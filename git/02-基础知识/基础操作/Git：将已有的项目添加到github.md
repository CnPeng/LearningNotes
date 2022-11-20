# 1. 将已有仓库上传到 GitHub

## 1.1. 目标：

把本地已经存在的项目，推送到 github 服务端（或者其他远程服务端），实现共享。

## 1.2. 实现步骤：

### 1.2.1. 从 github 创建空仓库

先从github创建一个空的仓库，并复制链接地址

 ![创建仓库](http://upload-images.jianshu.io/upload_images/2551993-b93f1c7924ddf177?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

 ![复制链接](http://upload-images.jianshu.io/upload_images/2551993-02b34ee0f9ec35f2?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 1.2.2. 初始化本地仓库，并提交内容到本地

打开终端（即命令行工具），通过 `cd` 命令切换到需要添加到 github 的项目的目录下，然后依次执行如下命令， 具体命令及其含义如下：

命令 | 含义
---|---
`touch README.md`  | 创建说明文档，
`git init`  | 初始化本地仓库。<br> 初始化之后在项目目录下会有一个 `.gitignore` 隐藏文件，<br>该文件用于过滤不需要上传到服务端的内容，可自行编辑。
`git add .`  | 添加全部已经修改的文件，准备 commit 提交。<br> 该命令效果等同于 `git add -A`。
`git commit -m ‘提交说明’` | 将修改后的文件提交到本地仓库，<br> 如：`git commit -m ‘增加README.md说明文档’`

### 1.2.3. 连接远程仓库，创建流并上传


#### 1.2.3.1. 关联远程仓库

`git remote add origin 远程仓库地址`。

该命令表示：连接到远程仓库并为该仓库创建别名 , 别名为 `origin` 。 这个别名是自定义的，通常用 `origin` ; 

上述命令中的 `远程仓库地址`，就是你自己新建的那个仓库的地址，复制地址的方法参考 第二张图。 

示例：`git remote add origin https://github.com/CnPeng/MyCustomAlertDialog.git`

这段代码的含义是： 连接到 github 上 https://github.com/CnPeng/MyCustomAlertDialog.git 这个仓库，并创建别名为 origin . （之后 `push` 或者 `pull` 的时候就需要使用到这个 origin 别名）

#### 1.2.3.2.  创建上传流

`git push -u origin master`

该命令表示：创建一个 upStream （上传流），并将本地代码通过这个 upStream 推送到 别名为 `origin` 的仓库中的 `master` 分支上

`-u` ，就是创建 upStream 上传流，如果没有这个上传流就无法将代码推送到 github；同时，这个 upStream 只需要在初次推送代码的时候创建，以后就不用创建了

另外，在初次 `push` 代码的时候，可能会因为网络等原因导致命令行终端上的内容一直没有变化，耐心等待一会就好。

### 1.2.4. 继续修改代码，提交并推送

做完上面三个步骤之后，就实现了将本地代码同步到 github 的功能，接下来要做的事情就是继续修改代码，然后提交并推送到 github 。核心步骤及命令如下：


命令 | 含义
---|---
`git add .` | 添加全部修改的代码，准备提交
`git commit -m '提交说明'` | 将修改后的代码先提交到本地仓库
`git pull` | 拉取远程仓库的内容到本地。<br> 多人协作开发时，一定要先 pull ，确保本地代码和远端没有冲突。<br> 默认拉取到 `master` 分支（或 `main` 分支）。
`git push` | 将代码推送到远端仓库 , <br> 默认推送到别名为 `origin` 的仓库中的 `master` 分支（或 `main` 分支）上。

**注意事项：**

如果有多个远程仓库或者多个分支， 并且需要将代码推送到指定仓库的指定分支上，那么在 `pull` 或者 `push` 的时候，就必须明确指定仓库和分支名称。如下：

* `git pull 仓库别名 仓库分支名`
* `git push 仓库别名 仓库分支名`
