## 一、问题现象：

从远端拉取项目代码，项目中使用了 NDK ，在 AndroidStudio 中编译时报如下错误：

![](pics/14-没有so时的报错.png)

## 二、解决方案

* 出现这个情况是因为 `tess-two` 编译时缺少了 `.so` 文件，之所以少了，是因为没有将 `.so` 文件提交到远程仓库！！

* 也有可能是 Project Structure 中选择了默认的 NDK —— 实际上我们的 `tess-two` 使用的是 ndk17