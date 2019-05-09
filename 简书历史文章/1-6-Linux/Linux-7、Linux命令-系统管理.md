###1、查看当前日历：cal
> * cal      `命令用于查看当前月份的日历，`
> * cal -y  `显示当前年份的日历`


![查看日历](http://upload-images.jianshu.io/upload_images/2551993-369e2633615d3e15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2、显示或设置时间：date
####（1）、显示时间
>date `该命令可以直接显示当前系统时间`

![查看当前系统时间](http://upload-images.jianshu.io/upload_images/2551993-1515099d78ef1770.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####（2）、按照指定格式显示当前系统时间
>date '+%y,%m,%d,%H,%M,%S'  **`注意：前面有个 + ，格式字符之间用逗号间隔`**

字符|含义
--|--
%Y，%y | 年
%m | 月
%d | 日 
%H | 时
%M | 分 
%S | 秒


![按照指定格式显示当前系统时间](http://upload-images.jianshu.io/upload_images/2551993-cc3d0889b805957b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
**`在上图中，我们发现，在使用指定格式展示当前系统时间时，必须严格区分大小写，大写字母和小写字符代表的含义是完全不一样的。`**

####（3）、设置时间（需要管理员权限）：
> date [MMDDhhmm[[CC]YY][.ss]] +format **`注意：ss前面有个小圆点`**

在上面的参数中各字符及其含义如下：

字符|含义
--|--
MM | 表示月份
DD | 表示天
hh | 表示小时
mm | 表示分钟
CC | 为年前两位
yy | 为年的后两位
ss |为秒。

如： **`date 101112132017.59`** 表示：2017年10月11日12时13分59秒

![设置系统时间](http://upload-images.jianshu.io/upload_images/2551993-074425d636bad51d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![显示时间，按格式展示时间，设置时间的示例代码](http://upload-images.jianshu.io/upload_images/2551993-ea1a773265989868.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、查看进程信息：ps
进程是一个具有一定独立功能的程序，它是操作系统动态执行的基本单元。

ps命令可以查看进程的详细状况，常用参数如下：

选项|含义
--|--
-a | 显示终端上的所有进程，包括其他用户的进程
-u |显示进程的详细状态
-x | 显示没有控制终端的进程
-w | 显示加宽，以便显示更多的信息
-r | 只显示正在运行的进程

>**ps 命令的参数前可以不加“-”**
>tar 打包命令的参数前也可以不加 - 。


![查看进程信息](http://upload-images.jianshu.io/upload_images/2551993-d3df3014d314ec82.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###4、动态显示进程：top
top命令用来动态显示运行中的进程。

top命令能够在运行后，在指定的时间间隔更新显示信息。

>**可以在使用top命令时加上 -d  来指定显示信息更新的时间间隔。**

在top命令执行后，可以按下按键得到对显示的结果进行排序。按键及对应的排序规则如下：

按键|含义
--|--
M | 根据内存使用量来排序
P | 根据CPU占有率来排序
T | 根据进程运行时间的长短来排序
U | 可以根据后面输入的用户名来筛选进程
K | 可以根据后面输入的PID来杀死进程。
q | 退出
h | 获得帮助


![top动态查看进程](http://upload-images.jianshu.io/upload_images/2551993-eecc1e853890b04a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###5、终止进程：kill
kill命令会终止指定进程号的进程，需要配合 ps 使用。
使用格式：
>kill [-signal] pid

信号值从0到15，其中9为绝对终止，可以处理一般信号无法终止的进程。
``kill 9133`` ：9133 为应用程序所对应的进程号

![终止进程](http://upload-images.jianshu.io/upload_images/2551993-e7e08535c33c6fc4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

有些进程不能直接杀死，这时候我们需要加一个参数“ -9 ”，**“ -9 ” 代表强制结束**：

![强制终止进程](http://upload-images.jianshu.io/upload_images/2551993-6ece85877736acc2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###6、关机重启：reboot、shutdown、init

命令|含义
--|--
reboot | 重新启动操作系统
shutdown –r now | 重新启动操作系统，shutdown会给别的用户提示
shutdown -h now | 立刻关机，其中now相当于时间为0的状态
shutdown -h 20:25 | 系统在今天的20:25 会关机
shutdown -h +10 | 系统再过十分钟后自动关机
init 0 | 关机
init 6 | 重启

###7、检测磁盘空间：df
df命令用于检测文件系统的磁盘空间占用和空余情况，可以显示所有文件系统对节点和磁盘块的使用情况。

选项 | 含义
--|--
-a | 显示所有文件系统的磁盘使用情况
-m | 以1024字节为单位显示
-t | 显示各指定文件系统的磁盘空间使用情况
-T | 显示文件系统

![image.png](http://upload-images.jianshu.io/upload_images/2551993-b06a1229553d2d67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![image.png](http://upload-images.jianshu.io/upload_images/2551993-a29aaf8a6781feec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###8、检测目录所占磁盘空间：du
du命令用于统计目录或文件所占磁盘空间的大小，该命令的执行结果与df类似，du更侧重于磁盘的使用状况。

du命令的使用格式如下：
> du [选项] 目录或文件名

选项|含义
--|--
-a | 递归显示指定目录中各文件和子目录中文件占用的数据块
-s | 显示指定文件或目录占用的数据块
-b | 以字节为单位显示磁盘占用情况
-l | 计算所有文件大小，对硬链接文件计算多次

![du 查看所占磁盘空间](http://upload-images.jianshu.io/upload_images/2551993-0009a7fb83f09055.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###9、查看或配置网卡信息：ifconfig
> * **`ifconfig`** 查看全部网卡信息
> * **`ifconfig 网卡名`** 查看指定网卡名的网卡信息，网卡名需要先通过 ifconfig 获取
>* **`sudo ifconfig 网卡名 新的ip地址 `** 重置IP地址

![ifconfig查看网卡信息](http://upload-images.jianshu.io/upload_images/2551993-3ae14b28a9b57f0e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###10、测试远程主机连通性：ping

![image.png](http://upload-images.jianshu.io/upload_images/2551993-cfecca0fb9e62a42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
