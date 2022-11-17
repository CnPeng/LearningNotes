原文链接：[《在cmd中输入ls命令出现“ls不是内部或外部命令解决》](https://blog.csdn.net/qq_33413264/article/details/77018528)

解决办法是： 在 `C:/windows` 目录下新建 `ls.bat` 文件，文件内容为：

```
@echo off
dir
```

![](pics/2-1-ls命令无法使用的解决.png)
