# 1. 43-git推送内容到远端报错The remote end hung up unexpectedly

通常，在 clone 项目到本地或者将本地内容推送到远端时，可能会出现报错：`The remote end hung up unexpectedly`

出现该问题一般有两种可能，一种是网络不好，一种是远端服务端对推送文件体积有限制。


## 1.1. clone 时报错的解决

clone 时报错，通常是因为网络不稳定，可以通过如下命令修改相关配置：

* 增加低速响应时间

```
git config --global http.lowSpeedLimit 0
git config --global http.lowSpeedTime 999999
```

* 增大 httpBuffer

```
git config --global http.postBuffer 524288000
```

* 压缩配置

```
git config --global core.compression -1    
```

## 1.2. push 时报错的解决

* push 时报错，可能是因为 push 的内容体积太大了，超过了代码仓库的单次推送上限。
    * 如果代码仓库是公司自己搭建的，请联系运维管理人员增大单次推送上限
    * 如果不增大推送上限，可以尝试使用内网代码仓库地址进行 push 操作
* 也可能是因为push的分支被管理员设置成了只读模式，这种情况下解除只读模式即可。


## 1.3. 参考

[Git 克隆问题-The remote end hung up unexpectedly](https://blog.csdn.net/weixin_43834742/article/details/109509987)