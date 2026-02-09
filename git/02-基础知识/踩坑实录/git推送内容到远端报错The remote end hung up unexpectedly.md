# 1. 43-git推送内容到远端报错The remote end hung up unexpectedly

通常，在 clone 项目到本地或者将本地内容推送到远端时，可能会出现报错：`The remote end hung up unexpectedly`

出现该问题一般有两种可能，一种是网络不好，一种是远端服务端对推送文件体积有限制。


## 1.1. clone 时报错的解决

clone 时报错，通常是因为网络不稳定，可以通过如下命令修改相关配置：

### 1.1.1. 增加低速响应时间

```
git config --global http.lowSpeedLimit 0
# 单位：秒
git config --global http.lowSpeedTime 999999
```

### 1.1.2. 增大 httpBuffer 缓存

```
# 设置缓存区为 512M。
git config --global http.postBuffer 524288000
```


### 1.1.3. 压缩配置

```
git config --global core.compression -1    
```

## 1.2. push 时报错的解决

### 1.2.1. 方案1

* push 时报错，可能是因为 push 的内容体积太大了，超过了代码仓库的单次推送上限。
    * 如果代码仓库是公司自己搭建的，请联系运维管理人员增大单次推送上限
    * 如果不增大推送上限，可以尝试使用内网代码仓库地址进行 push 操作

* 也可能是因为push的分支被管理员设置成了只读模式，这种情况下解除只读模式即可。

### 1.2.2. 方案2

如果代码体积不大，但依旧报错 `The remote end hung up unexpectedly` 或 `send-pack: unexpected disconnect while reading sideband packet
`，报错信息如下：


```bash
cnpeng@CnPeng wxpush % git psoma
Enumerating objects: 42, done.
Counting objects: 100% (42/42), done.
Delta compression using up to 10 threads
Compressing objects: 100% (20/20), done.
send-pack: unexpected disconnect while reading sideband packet
Writing objects: 100% (23/23), 8.77 MiB | 6.97 MiB/s, done.
Total 23 (delta 15), reused 0 (delta 0), pack-reused 0
fatal: the remote end hung up unexpectedly
Everything up-to-date
```

此时也可以尝试先修改 httpBuffer 缓存区域大小：

```cmd
# 设置缓存区为 512M。单位是Byte, 1048576000 表示 1G。
git config --global http.postBuffer 524288000
```

如果修改后依旧不行，可尝试替换 remote 地址为 ssh 形式，然后再提交

```cmd
git remote set-url origin ssh://xxx@github.org/xxx/仓库名.git
```


## 1.3. 参考

[Git 克隆问题-The remote end hung up unexpectedly](https://blog.csdn.net/weixin_43834742/article/details/109509987)