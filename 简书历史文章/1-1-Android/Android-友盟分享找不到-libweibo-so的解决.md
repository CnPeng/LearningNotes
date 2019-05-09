## 问题现象：
升级友盟分享到 6.9.4 之后爆出如下错误：

![image.png](https://upload-images.jianshu.io/upload_images/2551993-e7b8af7ad71e9c2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 问题原因：
找不到对应的微博so文件

## 解决方案：
在友盟提供的SDK包中，找到微博目录下的 jniLibs目录，然后把你APP支持的平台拷贝到自己项目的 jniLibs 目录：

* 新浪SDK jniLibs 目录

![](https://upload-images.jianshu.io/upload_images/2551993-3545c9a5df94960b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 将自己APP支持的平台对应的so文件拷贝到自己的jniLibs
 
![](https://upload-images.jianshu.io/upload_images/2551993-d102c1c5c2eb831e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
