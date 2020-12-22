Parallels Desktop 16 是 MAC 上一款比较好用的虚拟机软件。

但是，当我们把系统更新到 `macOS Big Sur 11.0` 之后，出现了 Parallels Desktop 中的虚拟机无法联网的问题。具体提示为：“您的虚拟机将继续正常运作 但将无法连接网络。”  

解决步骤如下:

1、安装 Parallels Desktop 16 软件

2、打开终端

3、输入如下命令

```
sudo -b /ApplicationsParallels\ Desktop.app/Contents/MacOS/prl_client_app
```

4、输入开机密码，并回车；

5、上述命令将会启动 Parallels Desktop 16，在 Big Sur 下，随后会提示你打开权限，按指示打开权限后，再运行或重新安装 Windows ，这样的打开方式下可以进行联网。

> 我是执行完命令之后，直接重新安装的 windows 虚拟机，安装完成之后，联网正常，重启或关闭再打开虚拟机之后也不需要再次输入相关密码。