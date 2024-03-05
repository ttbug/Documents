# Linux

#### netcat安装

安装 `openbsd-netcat` 而不是 `gnu-netcat` ，`gnu-netcat` 会存在问题，发送成功后，接收端不能正常关闭。对应的发送和接收命令：
```shell
nc -l 12345 > testfile
nc 1.2.3.4 12345 -q1 < testfile
```
这里加`-q1` 参数是为了兼容旧版本，表示发送完成1秒后退出，如果不加这个参数，在接收端为旧版本的netcat时，会导致发送端不能正常退出。

#### 查看非root用户连接信息

```Shell
$ lsof -u ^root| grep ESTABLISHED
```

#### neovim配置

[我的Neovim配置](https://github.com/ttbug/nvimconf/tree/feat/new)

配置常用快捷键说明, leader为空格键：  
- F12：打开FloatTerm
- F4：打开侧边栏
- g：打开GitUi
- j：格式化json代码
- ff：打开文件搜索
- fp：打开项目搜索

- Debug
  - F8：添加断点
  - F7：停止调试
  - F3：Step Over
  - F9：Step Into
  - F10：Step Out
  - F6：Run/Continue
  - dc：运行到光标处
  - do：打开REPL
  - dt：开始调试当前单元测试函数，调试开始前，可先在测试函数中添加断点，然后在被调试函数中添加断点

- Lazygit相关操作
  - 在分支上按空格，用来切换分支
  - 在修改的文件上按空格，表示git add
  - 添加后，按c表示git commit，可以输入提交信息
  - P表示git push
  - p表示git pull
  - 在选定分支上回车，可以查看commit信息
  - 在选定commit上，按c表示cherry-commit，按C表示range cherry-pick
  - 到当前分支的commit窗口，按v表示把选中commit cherry-pick到当前分支