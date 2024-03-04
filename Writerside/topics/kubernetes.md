# k8s二三事

## client-go
使用client-go实现主从选举功能
```Go
func election() error {
	ctx, cancel := context.WithCancel(context.Background())
	quit := make(chan os.Signal, 1)
	signal.Notify(quit, syscall.SIGINT, syscall.SIGTERM)
	stopCh := make(chan struct{}, 1)

	go func() {
		<-quit
		slog.Info("See you next time!")
		cancel()
	}()

	cli := client.Client()

	lock := &resourcelock.LeaseLock{
		LeaseMeta: metav1.ObjectMeta{
			Name:      leaseLockName,
			Namespace: leaseLockNamespace,
		},
		Client: cli.CoordinationV1(),
		LockConfig: resourcelock.ResourceLockConfig{
			Identity: identity,
		},
	}

	// start the leader election code loop
	cfg := leaderelection.LeaderElectionConfig{
		Lock:            lock,
		ReleaseOnCancel: true,
		LeaseDuration:   15 * time.Second,
		RenewDeadline:   5 * time.Second,
		RetryPeriod:     3 * time.Second,
		Callbacks: leaderelection.LeaderCallbacks{
			OnStartedLeading: func(ctx context.Context) {
				// 主开始要做的工作	
			},
			OnStoppedLeading: func() {
				// 丢失主权限后，要做的清理工作
			},
			OnNewLeader: func(id string) {
				// we're notified when new leader elected
				if identity == id {
					// I just got the lock
					return
				}
				slog.Info("new leader elected", "cur_leader", id, "my_id", identity)
			},
		},
	}

	elector, err := leaderelection.NewLeaderElector(cfg)
	if err != nil {
		slog.Error("failed to create leader elector: %s", err.Error())
		return err
	}

election:
	leaderCtx, leaderCancel := context.WithCancel(ctx)
	elector.Run(leaderCtx)
	leaderCancel()

	select {
	case <-ctx.Done():
		slog.Info("ctx done, quit apisix-controller")
	default:
		// 走到这里说明主从发生了切换
		stopCh = make(chan struct{}, 1)
		goto election
	}

	return nil
}
```