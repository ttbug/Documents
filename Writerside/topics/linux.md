# Linux

#### netcat安装

安装 `openbsd-netcat` 而不是 `gnu-netcat` ，`gnu-netcat` 会存在问题，发送成功后，接收端不能正常关闭。对应的发送和接收命令：
```shell
nc -l 12345 > testfile
nc 1.2.3.4 12345 -q1 < testfile
```
这里加`-q1` 参数是为了兼容旧版本，表示发送完成1秒后退出，如果不加这个参数，在接收端为旧版本的netcat时，会导致发送端不能正常退出。