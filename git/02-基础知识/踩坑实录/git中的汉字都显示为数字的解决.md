# 1. git中的汉字都显示为数字的解决

## 1.1. 现象

通过 `git status` 查看文件变更时，中文名称全都显示成了数字（），如下：

![](pics/20221113193306644_894948210.png)

## 1.2. 解决

执行如下命令：

```shell
git config --global core.quotepath false
```