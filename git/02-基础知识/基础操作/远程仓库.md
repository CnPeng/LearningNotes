# 1. 远程仓库

>注：下面的shortname 和 reomtename都是指的远程仓库的名称

## 1.1. 1 作用

使用Git的远程仓库可以实现团队协作开发。

## 1.2. 2 使用介绍

假设我们已经有了一个远程仓库，地址是：https://github.com/CnPeng/test.git


### 1.2.1. 克隆远程仓库到本地：

* `git clone https://github.com/CnPeng/test.git`

将https://github.com/CnPeng/test.git版本库中的代码克隆到本地

* `git clone -b dev https://github.com/CnPeng/test.git`

将远程仓库中的 dev 分支克隆到本地

### 1.2.2. 查看远程仓库的信息

`git remote -v`

执行该命令后，会列出当前程序对应的所有远程版本仓库的信息，含仓库名和仓库地址 . （ v 是verbose 的缩写，冗余 ）如下图：

![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMTI0MDExNDQ1ODI2?x-oss-process=image/format,png)

* `git remote`

执行 git remote 命令之后 ，只是会单纯的列出所有远程仓库的名字，不会展示远程仓库的地址

### 1.2.3. 将远程版本库中的最新代码同步到本地

Git 提供了两种命令来完成此功能，分别是 fetch 和 pull，它们区别及用法如下所示: 

* `git fetch origin master`

执行这个命令后，就会将远程版本库上的代码同步到本地，不过同步下来的代码并不会 合并到任何分支上去，而是会存放在到一个 `origin/master` 分支上,之后再调用 merge 命令将 `origin/master` 分支上的修改合并到主分支上即可   `git merge origin/master`

* `git pull origin master`

pull 命令则是相当于将 fetch 和 merge 这两个命令放在一起执行了，它可以从远程版 本库上获取最新的代码并且合并到本地，

>将远程的某个分支上的代码拉到本地 : `git pull [remote-name] [branch-name]`，这个 remote-name 是远程仓库的代名称，branch-name 是分支名

**实际使用中，需要先通过pull将代码拉到本地，处理完冲突之后，再执行push**

### 1.2.4. 将修改后的代码同步到远程版本库

* `git push origin master`

其中 origin 是远程版本库的 Git 地址的代名称，master 部分指定的是同步到哪一个分 支上，上述命令就完成了将本地代码同步到 https://github.com/CnPeng/test.git这个版本库的 master 分支上的功能。 

>将远程的某个分支上的代码拉到本地：`git pull [remote-name] [branch-name]`，这个 remote-name 是远程仓库的代名称，branch-name 是分支名

### 1.2.5. 添加远程仓库

`git remote add [shortname] [url]`

如：`git remote add dev git://github.com/CnPeng/test.git` ,这样，就可以用字符串 dev 来代指该仓库地址。在使用 `git  remote -v` 查看仓库列表的时候，就会有一个 dev 的仓库列表。添加成功之后查看全部远程仓库的效果如下图：


 ![这里写图片描述](https://imgconvert.csdnimg.cn/aHR0cDovL2ltZy5ibG9nLmNzZG4ubmV0LzIwMTYxMTI2MjI0MDUyMDU4?x-oss-process=image/format,png)

### 1.2.6. 查看远程仓库信息

* `git remote show [shortname]`

查看某个远程仓库的详细信息,如：`git remote show origin`  将会显示出 shortname 为 origin 的远程仓库的详信息

### 1.2.7. 重命名远程仓库

* `git remote rename [old-name] [new-name]`

将远程仓库重新命名，如：`git remote dev cnpeng` ,执行这句代码之后，就会将 shortname 为 dev 的远程仓库重新命名为 cnpeng

### 1.2.8. 删除远程仓库（解除与远程仓库的关系）

* `git remote rm [shortname]`

删除指定名称的不在使用的远程仓库，如：`git remote rm cnpeng` ,执行这句代码之后，就会将 shortname 为 cnepng 的远程仓库删除掉


