# 001-redis安装

[参考：官方文档](https://redis.io/docs/getting-started/installation/)

本文以 [Mac 下的安装](https://redis.io/docs/getting-started/installation/install-redis-on-mac-os/)为例：

* 需要借助 HomeBrew 安装，先确认本机是否已安装 HomeBrew:

```bash
$ brew --version
```

执行上述命令后会显示本机已安装的 HomeBrew 信息，如果命令执行失败，则需要前往 [brew.sh](https://brew.sh/) 安装 HomeBrew。

* 如果本机已经安装 HomeBrew , 在终端中执行如下命令安装 Redis :

```bash
brew install redis
```

在执行上述命令是，如有条件，最好使用梯子，否则会一直超时。

* 安装完成后，通过如下命令可以启动 redis 服务：

```bash
redis-server
```

通过 `Ctrl + C` 可以终止服务。


更多内容可查看官网文档。