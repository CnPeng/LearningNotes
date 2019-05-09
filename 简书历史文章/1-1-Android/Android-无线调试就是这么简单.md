##一、啥是无线调试？为啥需要无线调试
平时我们调试APP的时候，会将手机连接到USB数据线，然后再将数据线连接到电脑上。但是，由于长期插拔或者USB线材材质的问题，经常会出现无法连接的问题；而且，通常公司内会有手机多而线不够用的问题；还有就是，桌面上一堆手机一堆线是不是感觉很乱？就像下图一样

![](http://upload-images.jianshu.io/upload_images/2551993-f078e70fb4e20d09.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

此时，我们就需要用到无线调试了。那么，什么是无线调试呢？这还用问，就是没有线呗——手机和电脑连接的时候不需要用数据线了，我们部署调试APP时再也不用到处找USB线了，再也不用担心线材磨损导致的无法连接了！！


##二、如何开启无线调试？

###(一)、前提条件
* 一部手机
* 一条USB数据线
* 一部电脑
* 手机和电脑连接到同一个 Wifi 
* 开启手机的USB调试功能

###(二)、操作步骤

1、 将手机和电脑用USB数据线连接
2、 打开 终端，输入 `adb devices` , 查看手机和电脑是否连接成功。如下图：
> **adb devices** `查看当前可以连接到adb 的设备`
![](http://upload-images.jianshu.io/upload_images/2551993-999ecd5509ca2fcf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

3、为手机设置一个Tcp/Ip 的侦听端口
>**adb tcpip 端口号**    `端口号是一个任意数值, 想输入啥就输入啥，但尽量输入4位   ` 
![](http://upload-images.jianshu.io/upload_images/2551993-bf91de140707ad99.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



4、将手机与电脑断开连接（不断也行），并查看手机的 ip 地址
>不同手机的查看方法不一致，此处不做过多说明。以 Nexus 5X 为例：
![](http://upload-images.jianshu.io/upload_images/2551993-768f0c106a00ed8a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

5、执行 `adb connect ip地址：侦听端口` 命令，开启无线连接
> **adb connect ip地址：端口号**  `ip地址就是刚才查看的手机ip地址，端口号就是之前设置的侦听端口号，两者之间用冒号 ： 连接`
![](http://upload-images.jianshu.io/upload_images/2551993-cd4a0da0561289b5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

6、查看连接设备
>此时看到的连接设备号码就是 `ip:端口号`
![](http://upload-images.jianshu.io/upload_images/2551993-56aef56ee44e9310.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

7、通过无线连接部署应用到APP
>部署时看到的还是真实的手机型号
![](http://upload-images.jianshu.io/upload_images/2551993-7a3f5cbfcd74089f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

到此，无线调试已经搭建成功。

###(三)、电脑同时连接多个设备时如何开启无线调试
>前提条件不变，和 **如何开启无线调试** 中的整体步骤和思路无区别，重点是**在设置侦听端口时指定一下设备号**。命令如下：
>* **adb -s 设备号 tcpip 端口号**  `设备号就是执行 adb devices 命令之后展示的那一串编号；-s 的含义就是指定要执行命令的设备，当有多个设备时，-s 非常有用`
>
>![](http://upload-images.jianshu.io/upload_images/2551993-5b1c87dd24429e26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##三、注意事项
1、连接成功之后，不论是手机还是电脑，如果中间出现了断网现象（比如，重启手机，重启电脑，重启路由器等），需要重新走一遍流程
2、由于网络防火墙或者网络代理等因素，无线连接可能不会成功
3、连接不成功时，可以尝试通过 `adb kill server` 杀死 adb 服务，然后再次走一遍流程

>除了使用 命令方式开启无线调试之外，也可以为AndroidStudio添加相关插件来实现。比如：ADB WIFI 插件，具体步骤不再说明。

##四、参考资料
Android 调试桥(官方文档，含有完整的adb 命令介绍)：
https://developer.android.com/studio/command-line/adb.html?hl=zh-cn




---
本文到此结束，谢谢观看！
**如有不足，敬请指正！**

