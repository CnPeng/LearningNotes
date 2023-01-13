# 1. 002-配置node环境变量

> 基于 macOS Ventura 13.0.1 

## 1.1. 配置环境变量

* `cd ~`  切换到系统根目录。
* `ls -a` 查看该目录下是否有 `.bash_profile` 文件。
    * 如果没有则执行 `touch .bash_profile` 创建该配置文件。
* `npm -g bin` 查看 npm 全局包可执行文件路径。
* `open -e .bash_profile` 打开配置文件。
    * 也可以使用 `vim .bash_profile` 打开，但推荐使用 `open` 命令。
    * `open` 命令允许我们以普通文件编辑器的形式编辑配置文件；`vim` 则需要了解 vim 命令的基础操作。
* 在配置文件中添加内容：`export PATH=$PATH:/usr/local/bin/`，保存并退出。
* `source .bash_profile` 让配置文件中修改的内容立即生效。

## 1.2. 补充

* `which node` 查看 node 的安装目录
* `ls` 命令用于查看目录下的文件，但不包含隐藏文件；`ls -a` 包含隐藏文件
