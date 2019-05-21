新建项目编译时一直在 AndroidStudio 下方的 Build 窗口中提示：

当前的 NDK 编译方式已经被废弃，受影响的 module 有：xxx,xxx 

解决方案是：

Tools -- SDK Manager -- SDK Tools -- 勾选 Cmake -- 然后点击窗口底部的 apply 和 ok , 安装完成以后即可清除该错误提示