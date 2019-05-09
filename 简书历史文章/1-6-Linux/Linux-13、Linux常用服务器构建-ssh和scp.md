##一、ssh
###1、ssh介绍
SSH为Secure Shell的缩写，由 IETF 的网络工作小组（Network Working Group）所制定；SSH 为建立在应用层和传输层基础上的安全协议。

SSH是目前较可靠，**专为远程登录会话和其他网络服务提供安全性的协议。常用于远程登录，以及用户之间进行资料拷贝。**

利用SSH协议可以有效防止远程管理过程中的信息泄露问题。SSH最初是 UNIX 系统上的一个程序，后来又迅速扩展到其他操作平台。SSH 在正确使用时可弥补网络中的漏洞。

SSH 客户端适用于多种平台。几乎所有 UNIX 平台—包括 HP-UX、Linux、AIX、Solaris、Digital UNIX、Irix，以及其他平台，都可运行SSH。

使用SSH服务，需要安装相应的服务器和客户端。客户端和服务器的关系：如果，A机器想被B机器远程控制，那么，A机器需要安装SSH服务器，B机器需要安装SSH客户端。

###2、安装ssh
安装ssh服务器：
>sudo apt-get install openssh-server


![image.png](http://upload-images.jianshu.io/upload_images/2551993-f9a3a7884c1b1496.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、使用ssh连接服务器，实现远程登录

远程登陆命令：
>ssh 服务器用户名@IP

使用ssh访问，如访问出现错误，**可查看是否有该文件 ～/.ssh/known_ssh 尝试删除该文件解决**。


####（1）、连接步骤1：查看ssh服务器地址和用户名
先在服务器端运行 ifconfig 命令，获取服务器ip地址，具体如下：

![查看ssh服务器地址和用户名](http://upload-images.jianshu.io/upload_images/2551993-f4f556c92e145e10.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）、连接步骤2：客户端发起链接
![客户端链接命令](http://upload-images.jianshu.io/upload_images/2551993-e50800ce16e68280.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![链接成功](http://upload-images.jianshu.io/upload_images/2551993-acadb80f336ba59a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![在客户端查看服务端内容](http://upload-images.jianshu.io/upload_images/2551993-cfebf238e7a9676a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：
>![客户端初次链接服务器时的提示](http://upload-images.jianshu.io/upload_images/2551993-357679fde7be5e38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
从客户端初次连接到服务器时可能会出现上图中的提示，其含义是：SSH 告知用户，这个主机不能识别，这时键入"yes"，SSH 就会将相关信息，写入" ~/.ssh/know_hosts" 中，再次访问，就不会有这些信息了。然后输入完口令,就可以登录到主机了。




##二、scp
远程拷贝文件 `scp -r ` 的常用方法：
###1、scp的使用前提
使用该命令的前提是：目标主机已经成功安装openssh-server
如没有安装使用 `sudo apt-get install openssh-server `来安装

###2、标准使用格式：

服务器端文件复制到客户端的标准格式如下
>`scp -r 目标用户名@目标主机IP地址：/目标文件的绝对路径 /保存到本机的绝对/相对路径`

举例：
`scp -r cnpneg@172.16.71.129:/home/cnpeng/003 /Users/CnPeng`
上述代码含义是：将服务端/home/cnpeng/目录中的003拷贝到客户端机器的 /Users/CnPeng 目录下，服务端的用户名是cnpeng，服务端的地址是172.16.71.129。具体如下图：

![拷贝服务端文件到本地](http://upload-images.jianshu.io/upload_images/2551993-c646c540ad39528f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


>**注意：**
>* **在客户端初次运行上述命令时，可能会提示输入“yes”确认拷贝，此时必须输 “yes” 而不能简单输入“Y”**
>* **拷贝单个文件可以不加 -r 参数，拷贝目录必须要加。**
>* **在客户端拷贝的时候，一定要切换到客户端本机的用户后才能进行拷贝操作！**`我在这儿吃了老大亏了，一开始从客户端链接到服务端之后，客户端会默认切换到服务端账户下（上面链接步骤2的图中有展示），然后我就直接运行了这个拷贝命令，然后就一直报错，一直提示/Users/CnPeng 不是一个目录或文件。最后我才反应过来，应该需要切换到客户端本机的用户后再运行该命令，然后就一切ok了`

###3、格式演化
>在使用各拷贝命令时， `服务器用户名@服务器IP地址` 最好写全！！

####（1）、远程目录复制到本地：
>* `scp -r RemoteUserName@RemoteHostIp:RemoteFolder FolderName`
>* `scp -r RemoteHostIp:RemoteFolder FolderName`

![image.png](http://upload-images.jianshu.io/upload_images/2551993-f2caec21e09a9e32.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-c10369b6e4d6249e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



####（2）、远程文件复制到本地：
>* `scp RemoteUserName@RemoteHostIp:RemoteFile FileName`
>* `scp RemoteHostIp:RemoteFolder FileName`
>* `scp RemoteHostIp:RemoteFile FileName`


![拷贝服务端文件到本地当前目录并指定在本地的名称](http://upload-images.jianshu.io/upload_images/2551993-e43bc2efcc2d3bbb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![拷贝服务端文件到本地2](http://upload-images.jianshu.io/upload_images/2551993-d2c2a8462b961e47.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（3）、本地目录复制到远程：
>* `scp -r FolderName RemoteUserName@RemoteHostIp:RemoteFolder`
>* `scp -r FolderName RemoteHostIp:RemoteFolder`


![image.png](http://upload-images.jianshu.io/upload_images/2551993-effe927314d261fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![image.png](http://upload-images.jianshu.io/upload_images/2551993-68318146d185a2ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####（4）、本地文件复制到远程：
>* `scp LocalFileName RemoteUserName@RemoteHostIp:RemoteFile`
>* `scp LocalFileName RemoteHostIp:RemoteFolder`
>* `scp LocalFileName RemoteHostIp:RemoteFile`


![image.png](http://upload-images.jianshu.io/upload_images/2551993-bb1720ce12d4008e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


拷贝远程的文件到本地当前目录：

![拷贝远程的文件到本地当前目录](http://upload-images.jianshu.io/upload_images/2551993-74d8551773db9560.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**其他示例代码：**
拷贝远程的文件可以任意修改其名字：

![拷贝远程的文件可以任意修改其名字：](http://upload-images.jianshu.io/upload_images/2551993-6a71cde4465c255c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

拷贝远程的文件可以指定存放路径：

![拷贝远程的文件可以指定存放路径](http://upload-images.jianshu.io/upload_images/2551993-1f9888bed78b563c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##三、思考
>使用Vmware或者VmFusion搭建了虚拟机之后，如果需要在虚拟机和宿主机之间实现文件的共享，那么就有两种方案可以选择了：
>* 1、安装 VmTools，使用VmTools 实现文件共享
>* 2、搭建SSH服务器，然后利用 scp命令实现两端的文件传输和共享

上面的这种思路目前在 MAC + VmFusion 中实现了，VmFusion中安装的是ubuntu ，文中的截图也是基于这种方案。

在Windows + Wmware 的环境下这种思路是否能 实现，后期再尝试
