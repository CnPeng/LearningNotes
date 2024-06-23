# 1. stash-暂存

## 1.1. 使用场景

在 dev 分支修改代码未完成时，master 分支有 bug 需要修改，我们又不想新增一个 `commmit` 提交记录，此时就可以使用 `git stash` 将 dev 分支正在修改的内容进行暂存。暂存之后就可以切换到 master 分支，待 bug 修复完成后，再切换到 dev 分支，并将暂存的内容取出即可。

## 1.2. 核心命令

命令 |  作用 | 备注
---|---|---
`git status` | 查看当前状态，确认当前修改了哪些文件 |  | 
`git stash` | 将修改暂存到 Git 仓库中 | 该操作将一个新的堆栈记录添加到暂存区 | 
`git stash apply` | 恢复暂存区中的内容 | 恢复之后即可继续编辑 | 
`git stash drop` | 删除暂存区中的内容 | 将删除暂存区中的最后一个堆栈记录，<br>并从 Git 仓库中删除该记录 | 


注意，使用 `git stash` 命令将修改**暂存**到 Git 仓库中，而**不是提交**到 Git 仓库中。如果想提交修改，需要先使用 `git add` 命令将修改暂存到暂存区，然后再使用 `git commit` 命令提交暂存区的修改。


## 1.3. 使用步骤简述

假设正在 dev 分支修改内容，临时需要切换到 master 修改 bug，此时，使用 `git stash` 的步骤如下：

在 dev 分支暂存，并切换到 master 分支：

* 在 dev 执行 `git status` 查看修改的内容
* 在 dev 分支执行 `git stash` 将修改的内容暂存。
* 在 dev 分支执行 `git status` 确认内容都已经成功暂存（若上一步成功暂存，执行命令后，会提示无需要暂存的内容）。
* `git checkout master` 切换到 master 分支，并完成 bug 修改。

切换到 dev 分支，并取回最后一次暂存的内容：

* `git checkout dev` 切换到 dev 分支。
* 在 dev 分支执行 `git stash apply`，恢复暂存区的内容。