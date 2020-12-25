新建项目编译时一直在 AndroidStudio 下方的 Build 窗口中提示：

当前的 NDK 编译方式已经被废弃，受影响的 module 有：xxx,xxx 


解决方案如下：

![](pics/1-去除ndk被废弃的提示.png)

之前曾以为下面的方法能解决，但实际并不好使：

Tools -- SDK Manager -- SDK Tools -- 勾选 Cmake -- 然后点击窗口底部的 apply 和 ok