`因当前使用的是最新16.04LTS版本，文中的内容暂未测试。`

##一、更新Ubuntu软件下载地址
###1、 寻找国内镜像源
所谓的镜像源：可以理解为提供下载软件的地方，比如Android手机上可以下载软件的91手机助手；iOS手机上可以下载软件的AppStore

![image.png](http://upload-images.jianshu.io/upload_images/2551993-a75aec8e23c0416c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-d41e9e6da593b362.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-07ce0bdc37845ffa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-0956ab2f29a5b7c8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2、 备份Ubuntu默认的源地址
>sudo cp /etc/apt/sources.list /etc/apt/sources.list.backup

![image.png](http://upload-images.jianshu.io/upload_images/2551993-220bb69ebfda0b15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3、 更新源服务器列表

![image.png](http://upload-images.jianshu.io/upload_images/2551993-f74355a0413de240.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-a5569c6108df47f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-9fbea30da1ff981d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4、 更新源
做完此步骤之后，就可以进行apt-get install 下载了

![image.png](http://upload-images.jianshu.io/upload_images/2551993-e853114a6ee03a42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-cdbf745675c1aa08.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-4e3e5d7eb7047d31.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、Ubuntu软件操作的相关命令

命令|含义
--|--
sudo apt-get update | 更新源
sudo apt-get install package | 安装包
sudo apt-get remove package | 删除包
sudo apt-cache search package | 搜索软件包
sudo apt-cache show package | 获取包的相关信息，如说明、大小、版本等
sudo apt-get install package --reinstall | 重新安装包
sudo apt-get -f install | 修复安装
sudo apt-get remove package --purge | 删除包，包括配置文件等
sudo apt-get build-dep package | 安装相关的编译环境
sudo apt-get upgrade | 更新已安装的包
sudo apt-get dist-upgrade | 升级系统
sudo apt-cache depends package | 了解使用该包依赖那些包
sudo apt-cache rdepends package | 查看该包被哪些包依赖
sudo apt-get source package | 下载该包的源代码
sudo apt-get clean && sudo apt-get autoclean | 清理无用的包
sudo apt-get check | 检查是否有损坏的依赖

其他参考：
http://blog.csdn.net/zyz511919766/article/details/7574040

http://blog.csdn.net/qinkang1993/article/details/54631606
