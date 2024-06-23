##一、FTP服务器
FTP 是File Transfer Protocol（文件传输协议）的英文简称，而中文简称为“文传协议”。

用于Internet上的控制文件的双向传输。

同时，它也是一个应用程序（Application）。基于不同的操作系统有不同的FTP应用程序，而所有这些应用程序都遵守同一种协议以传输文件。

在FTP的使用当中，用户经常遇到两个概念："下载"（Download）和"上传"（Upload）。
`"下载"文件就是从远程主机拷贝文件至自己的计算机上；`
`"上传"文件就是将文件从自己的计算机中拷贝至远程主机上。`
用Internet语言来说，用户可通过客户机程序向（从）远程主机上传（下载）文件。

![FTP架构](http://upload-images.jianshu.io/upload_images/2551993-67470b13d7d8b8a4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、FTP服务器的构建

###1、安装vsftpd服务器
>sudo apt-get install vsftpd


![image.png](http://upload-images.jianshu.io/upload_images/2551993-faea4b6d84b2a4e3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###2、配置vsftpd.conf文件
>sudo vi /etc/vsftpd.conf

![image.png](http://upload-images.jianshu.io/upload_images/2551993-8e94e1714b5cd18e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-30b05ac1894b1ea7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-40e8a97357465dc5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-03f5491d5820ba7f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-cf93ecb5dea90734.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-2ce82eca0623d95a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-daa31652f4250b92.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-d0757b5af862e9a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-69e8e31c252822fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-0a98cf32f75c7130.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-6a35819a6891d47d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、测试上传功能，登陆ftp服务器
>ftp IP地址

![image.png](http://upload-images.jianshu.io/upload_images/2551993-5038034e71470636.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4、上传命令，可以把文件上传到ftp服务器
>put somefile

###5、下载命令，可以把ftp服务器上的文件下载到本地
>get somefile

##二、图形界面的ftp客户端(filezilla)

![image.png](http://upload-images.jianshu.io/upload_images/2551993-d214677a330e6e32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
