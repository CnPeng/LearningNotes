## 一、问题现象：

在 AndrodiStudio 的 .gitignore 文件中已经添加了对 .idea 和 .gradle 目录的过滤，但是，每次修改内容后，通过 `git status` 查看状态时依旧会显示这两个目录中的内容，然后 `git add .` 的时候就会把他们一起暂存/提交。

## 二、问题原因：

通常是因为这两个目录已经在 git 中提交过了（或者 暂存 过了），然后又添加了对它们的过滤。

（正常情况下，我们应该先添加 .gitignore 文件，然后再执行 `git add ` 和 `git commit `）

## 三、解决方案：

### 1、方案一：手动删除目录

直接右击这两个目录，然后删除它们。(或者在 terminal 中直接执行 `rm 目录名` 命令)

这样就可以了，以后再修改代码内容时，它们就不会再被提交了。

**注意：这种方案虽然简单，但是很多时候并不好使**，因为我们接着运行 `git status` 时可能依旧会出现 “Changes not staged for commit” 的提示，大致如下:

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add/rm <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)
 
        deleted:   .gralde
 
no changes added to commit (use "git add" and/or "git commit -a")
```


### 2、方案二：使用`git rm` 命令

该方案是基于 [《git - 移除文件以及取消对文件的跟踪》](https://blog.csdn.net/leedaning/article/details/44976319) 的二次整理

`git rm` 命令表示 从已跟踪文件清单中移除（确切地说，是从暂存区域移除）指定目录或文件。根据是否删除本地文件又可以分为两种方案：

- 直接删除暂存区和本地文件；
- 只删除暂存区，然后手动修改 .gitignore 文件中的过滤规则

**注意：如下内容是针对不同情况的不同方案的详细介绍，并不是操作步骤。可根据个人实际情况决定使用如下哪种方案。**

#### （1）、删除本地文件

删除本地文件又分为如下两种情况：

- 暂存区中没有目标文件的提交记录
- 暂存区中有目标文件的提交记录

##### A、暂存区中没有该文件的记录

如果文件修改了，但还没有提交过（也就是说暂存区中没有该文件的提交记录），此时，直接执行  `git rm` 移除该文件, 这样该文件就不会纳入版本管理了。运行 `git status` 时也就不会再出现 `Changes not staged for commit` 的提示了，如下图：

```
$ git rm grit.gemspec
rm 'grit.gemspec'
$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
 
        deleted:    grit.gemspec
```
        
##### B、暂存区中有该文件的提交记录

<font color="red">如果目标文件已经提交过（即 暂存区有提交记录），则必须要用强制删除选项 `-f`</font>, 示例如下：

```
// 强制删除本地 .idea/ 目录下的全部内容，并移出暂存区
git rm -f .idea/\*

// 强制删除本地 build/ 或 xx/build/ 目录下的全部内容，并移出暂存区
git rm -f */build/\*

// 强制删除本地 .gradle/ 目录下的全部内容，并移出暂存区
git rm -f .gradle/\*
```

<font color="red">**`星号 *` 之前加了 `反斜杠 \`，其作用是 递归删除 .gradle/ 目录下的全部子目录和文件**</font>，如果去除这个 `反斜杠 \`, 就只会删除该目录下的文件，不会递归删除子目录及子目录中的文件。

#### （2）、不删除本地文件（推荐使用这种方案）

如果我们只是想从 git 暂存区中删除文件或目录，并不想删除本地文件或目录（也就是说 只从 git 的跟踪清单中删除），那么我们可以 **使用 `git rm --cached xx文件名或目录名` ，然后再手动在 `.gitignore` 文件中添加对该文件或目录的过滤**。示例如下：

```
//从暂存区中移除 readme.txt 的跟踪
$ git rm --cached readme.txt
```

```
// 清除暂存区中的全部内容
$ git rm -r --cached .
```

取消对文件的跟踪还可以使用：`git update-index --assume-unchanged <取消跟踪的文件>`,但是该命令只能取消提交到暂存区之前的文件，可以先用 `git reset <文件名>` 将暂存区的文件回退到暂存区之前，然后再取消跟踪。


## 其他相关

[Git忽略规则.gitignore梳理](https://www.kancloud.cn/liqingbo27/git/602878)
