原文链接：https://blog.csdn.net/qq_21996633/article/details/51079380

这几天，我的android studio出了毛病，在android studio 的System setting选项怎样都找不到android sdk设置路径的地方。

最后无奈之下（技术不够厉害）强行删除android studio打算重新安装，由于第一次使用mac还不熟，原来直接把android studio拉到垃圾箱是不能完全删除的，

还有很多配置文件残留在系统上，导致你想重新配置android studio都不行。最后在网络上找了一堆方法去实现都不行，甚至考虑过时间关系，想过直接重装（好傻逼的感觉），

然后去重装系统，谁知道要200多小时（网速慢），最终我放弃了。原本打算就直接放弃的，结果在网上看到一篇博文。真的炒鸡有用直接食用命令行轻松清除残余文件

（看到的博客也是没有些博文出自，我就省略这一部分吧）



### 1／执行这些命令在命令行

rm -Rf /Applications/Android\ Studio.app

rm -Rf ~/Library/Preferences/AndroidStudio*

rm ~/Library/Preferences/com.google.android.studio.plist

rm -Rf ~/Library/Application\ Support/AndroidStudio*

rm -Rf ~/Library/Logs/AndroidStudio*

rm -Rf ~/Library/Caches/AndroidStudio*

### 2／如果你想删除全部项目
rm -Rf ~/AndroidStudioProjects

### 3／删除gradle关联文件 (caches & wrapper)
rm -Rf ~/.gradle

### 4／删除模拟器
rm -Rf ~/.android

### 5／删除android 工具
rm -Rf ~/Library/Android*

如果你想要完全清除的话，除了4，全部敲一下。
