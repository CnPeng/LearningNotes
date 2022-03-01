# 1. tag-标签

## 1.1. 什么是标签？

标签就是一个标记。

通常我们会在发布版本的时候，对最后一次的commit 提交打上一个标签，用来区分不同的app版本。（当然，标签也有其他用途）

## 1.2. 为什么需要标签？

你可能会想，既然标签只是一个标记，那么我们在 commit 的时候会有一个 `-m` 的提交说明，在发布 app 版本的时候为了区分不同的版本的我们也可以创建不同的branch分支，它们都能起到标记的作用，我们为什么还需要标签呢？？

打个比方，你有一本很厚的书，书会有页码（相当于我们commit时的说明），也会有目录（相当于我们的branch分支），但是我觉的你最好有一些书签（相当于我们这里要说的tag标签），有了这书签之后，你就能更快速的找到你之前的阅读位置。也就是说，有了tag之后，你不用checkout分支，也不用逐条的翻阅commit提交记录，你只需要 `git checkout 标签名` 就能快速的切换到你需要的版本位置。

## 1.3. 标签的基本使用

### 1.3.1. 查看标签

#### 1.3.1.1. 基本使用

- `git tag`

该命令的作用是，列出当前已有的全部标签名，也可以使用 `git tag -l` 。如下图：

![](https://img-blog.csdnimg.cn/img_convert/32d3c1a7402f7ee869014f1d803cd24b.png)

#### 1.3.1.2. 通配符

查看标签时也可以使用通配符  `*` , 具体如下：

 - `git tag -l  '2*'`

列出以 2 开头的所有标签名，如下图：

![](https://img-blog.csdnimg.cn/img_convert/030d97dbd987cc2ad34bb0970f46adb1.png)

- `git tag -l  '*2'`

列出以 2 结尾的所有标签名，如下图：

![](https://img-blog.csdnimg.cn/img_convert/56822d3e26d706c5daa70cdc8e7ef9f2.png)

- `git tag -l '* 版本*'`

列出包含  版本  字样的所有标签名，如下图：

![](https://img-blog.csdnimg.cn/img_convert/2379221e6e20daa8c5b0fabb18471126.png)

### 1.3.2. 新建标签

Git 标签有两种类型： Annotated (有附注的标签)，LightWeight(轻量级没有附注的标签)。

* LightWeight 只是一个指向了特定commit 提交记录的引用。
* Annotated 则是存储在仓库中的一个独立对象，它有自身的校验和信息，包含打标签者的名字，电子邮件地址和日期，以及标签说明等信息。

标签本身也允许使用 GNU Privacy Guard (GPG) 来签署或验证。

通常我们使用含附注型的标签，以便保留相关信息；但如果只是临时性加注标签，或不需要描述信息，就用轻量级标签。

#### 1.3.2.1. 新建含附注的标签

* `git tag -a 标签名称 -m '标签描述'`

如：`git tag -a v1.1 -m '新建含附注的v1.1标签'`

如下图：

![](https://img-blog.csdnimg.cn/img_convert/437f28b15da3536d3674701631e275bc.png)

如果在创建含有附注的标签时，并没有写 `-m` 及其后面的描述内容，就会跳转到 vi/vim 编辑器中。这样就需要在 vi/vim 编辑器中完成 描述内容的编辑。进入编辑器中的效果如下：

  ![](https://img-blog.csdnimg.cn/img_convert/7a5722685ae334b1f83d10bca389d301.png)

[点击跳转到：Vi/Vim编辑器的基本使用介绍](http://blog.csdn.net/north1989/article/details/53367461)

#### 1.3.2.2.  新建轻量级标签

* `git  tag 标签名`

如：`git tag  v2.1.9`，执行该命令后，会新建一个没有描述信息的标签 ; 与创建含附注的标签相比，不需要 `-a` , 也不需要 `-m`

### 1.3.3. 查看标签信息

* `git show 标签名`

如：`git show  v2.1.8`，查看 v2.1.8 这个标签的相应内容，如下图：

![](https://img-blog.csdnimg.cn/img_convert/a627ffd3c8f5e6b61e585392e522ce42.png)

### 1.3.4. 重命名标签

* `git tag 新标签名 旧标签名`

执行上述命令之后，就可以获得一个新标签名的 tag , 但是旧标签名的 tag 还存在，需要手动删除旧标签（`git tag -d 标签名`，删除标签的更多信息在后面小节中有介绍）。

### 1.3.5. 后期加注标签

* `git tag -a 标签名  提交对象的校验和  -m '标签描述'`

默认情况下，标签是打在最新提交的 commit 上，如果忘记了打标签，也可以在后期加注标签，只需要在加注标签的时候指定 提交对象的校验和（这个校验和就是用 `git log` 命令查看提交历史时展示的 commit 后面的那一串字符串），实际使用的时候，校验和不需要使用完整的，只需要使用前几位即可。

![](https://img-blog.csdnimg.cn/img_convert/99febb50df0259ebb1652f1a1ea1e404.png)

补注标签的代码图示：

![](https://img-blog.csdnimg.cn/img_convert/bbd21ba80c48ba511a8c8a23122882e7.png)

### 1.3.6. 推送到远程

* `git push 远程仓库名称 标签名`

默认情况下，直接使用 `git push` 并不会把标签传送到远端服务器上，还必须指定需要推送到的远程仓库 

如：`git push origin v1.5`  通过这个命令，就可以名称为 v1.5 的这个标签推送到名称为 origin 的这个仓库。

如果想推送全部标签到远程仓库，只需要执行下列命令：

`git push origin --tags`

### 1.3.7. 查看远程标签

将标签推送到远程服务器之后，可以打开项目进行查看，如下图：releases 就表示被推送的标签

![](https://img-blog.csdnimg.cn/img_convert/6e4b419ba10d6c22f0216548ed2aa7fd.png)

### 1.3.8. 删除标签

#### 1.3.8.1. 删除本地标签

* `git tag -d 标签名`

如：`git tag -d v1.4补注标签 `  执行该命令后，就会将名称为 `v1.4补注标签` 的标签删除，如下图：

![](https://img-blog.csdnimg.cn/img_convert/7ccd96abb9f9a8da2b5edf59e73e2eaa.png)

#### 1.3.8.2. 删除远程标签

默认情况下，标签会存放在本地，不会主动推送到远程服务器。上面的 `-d` 命令也就是删除本地的标签

如果标签已经推送到远程服务端，要删除的话，需要先从本地删除：`git tag -d v1.5`

然后再通过 push 删除服务端的标签 ：`git push origin :refs/tags/v1.5`

执行完上面两个命令之后，就会将本地和远程的名称为 v1.5 的标签删除。

### 1.3.9. 获取远程标签

 - `git pull origin`

执行该命令后，可以从服务器将本地不存在标签拉取到本地，如下图：

![](https://img-blog.csdnimg.cn/img_convert/d04acb05dcd00556f91fab8ec5f10cfa.png)

 - `git pull origin tag 标签名`

该命令的作用是从远端服务器拉取指定标签名的标签到本地

### 1.3.10. 切换标签

* `git checkout 标签名`

如同切换分支，执行该命令后，就会切换到指定名称的标签。

如：`git checkout v1.5` 执行完该命令后，会切换到 名称为 v1.5 的标签上，同 branch 类似，你当前操作的代码也会回退到 v1.5 标签对应的最后一次commit时的状态


* `git checkout -b 分支名`

基于被切换到的标签创建一个新的分支

使用 `git checkout 标签名` 切换了标签之后，代码会处于一个临时分支中，当你切换到其他分支后，临时分支会消失。如果我们希望能够基于被切换到的标签创建一个新的分支就可以使用该命令。

![](https://img-blog.csdnimg.cn/img_convert/b6ff8083feed7b190d35dc7b8ca1fe7e.png)

