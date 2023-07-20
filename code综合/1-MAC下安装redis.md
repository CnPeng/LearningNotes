[原文：Mac下安装redis](https://www.jianshu.com/p/299580dd37c8)


我们使用 brew 命令安装，使用 brew 之前需要安装 Homebrew 并换源，可以加快速度

## 1.1 检查可安装版本

使用命令 `brew search redis`
我们可以看到 redis 的版本

```
$ brew search redis
==> Formulae
hiredis                redis                  redis-leveldb          redis@3.2              redis@4.0
==> Casks
another-redis-desktop-manager          redis                                  redisinsight
(base)
```

上面结果中的 `redis@3.2` 中 `@` 后面的是版本号

## 1.2 安装redis

命令 `brew install redis`，也可以使用 `brew install redis@3.2` 的形式指定安装版本，不指定则安装最新版。

```
$ brew install redis
==> Downloading https://mirrors.ustc.edu.cn/homebrew-bottles/bottles/redis-6.0.8.catalina.bottle.tar.gz
######################################################################## 100.0%
==> Pouring redis-6.0.8.catalina.bottle.tar.gz
==> Caveats
To have launchd start redis now and restart at login:
  brew services start redis
Or, if you don't want/need a background service you can just run:
  redis-server /usr/local/etc/redis.conf
==> Summary
🍺  /usr/local/Cellar/redis/6.0.8: 13 files, 3.8MB
(base)
```

安装成功后给了提示信息，

```
To have launchd start redis now and restart at login:
brew services start redis
```
  
这就是启动命令了

## 1.3 启动redis

* 启动redis服务

```
brew services start redis
```

* 关闭redis服务

```
brew services stop redis
```

* 重启redis服务

```
brew services restart redis
```

* 通过bin目录启动

```
cd /usr/local/Cellar/redis/6.0.8/bin
./redis-server
```

* 配置开机启动redis

```
ln -sfv /usr/local/opt/redis/*.plist ~/Library/LaunchAgents
```

## 1.4 查看 redis 是否启动？

使用命令 `redis-cli`，进入 redis 命令窗口，`exit` 退出，6379 是 redis 服务器的端口号，和mysql 的 3306 应该是一个意思

```
$ redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> exit
(base)
```

如果 cli 界面中出现乱码，可以使用 `redis-cli --raw` 解决中文乱码

## 1.5. 补充

### 1.5.1 brew 的常用命令

命令 | 含义
---|--- 
brew search xxxx  | 查找指定的软件
brew list     | 列出已经安装的软件
brew install xxxx  | 安装指定软件,默认安装的是稳定版本（指定安装版本 `@+版本号`)
brew uninstall xxxx  | 卸载指定软件
brew upgrade xxxx  | 更新某个软件
brew info xxxx  | 查看指定软件包的说明
brew cache clean  | 清理缓存

如：`brew install redis@3.2（版本）` .表示指定安装 redis 3.2版本


## 1.6 其他参考

[MAC下安装redis](https://blog.csdn.net/ding_bao/article/details/105519175)