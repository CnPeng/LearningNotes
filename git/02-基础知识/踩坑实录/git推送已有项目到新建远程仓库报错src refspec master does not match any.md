# 1. 42-git推送已有项目到新建远程仓库报错src refspec master does not match any

## 1.1. 问题现象

将本地已有的项目推送到远程新建空白仓库时，会报错 `src refspec master does not match any`


## 1.2. 解决方案

首先确保本地已有项目目录非空，且已经通过 `git init` 执行过初始化。

然后确认本地项目的当前分支名，假设分支名为 master , 然后执行 `git push -u origin master` 。

上述命令中，origin 表示远程仓库的别名，master 表示远程仓库的分支名。

假设，本地项目的当前分支名为 dev, 当我们执行 `git push -u origin master` 时也是会报上面的错误。

也就是说，**我们初次向远端推送本地仓库时，远程仓库的分支名需要和本地当前分支名一致，不一致则报错**。第一次推送成功之后，名称不一致时也不会报错。