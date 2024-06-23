##一、Linux命令格式
Linux命令格式如下：
>command [-options] [parameter1] …

说明：
* command: 命令名,相应功能的英文单词或单词的缩写
* [-options]：选项,可用来对命令进行控制，也可以省略，[]代表可选
* parameter1 …：传给命令的参数：可以是零个一个或多个

例：

![linux命令示例--查看/home/cnpeng 目录下的全部内容](http://upload-images.jianshu.io/upload_images/2551993-e2311fd3d17b2493.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、查看帮助文档
###1、--help
一般是linux命令自带的帮助信息 , 如：
>ls --help

###2、man(有问题找男人，manual)
man是linux提供的一个手册，包含了绝大部分的命令、函数使用说明。

该手册分成很多章节（section），使用man时可以指定不同的章节来浏览。
例：man ls ; man 2 printf

####（1）、man中各个section意义如下：
* Standard commands（标准命令）
* System calls（系统调用，如open,write）
* Library functions（库函数，如printf,fopen）
* Special devices（设备文件的说明，/dev下各种设备）
* File formats（文件格式，如passwd）
* Games and toys（游戏和娱乐）
* Miscellaneous（杂项、惯例与协定等，例如Linux档案系统、网络协定、ASCII 码；environ全局变量）
* Administrative Commands（管理员命令，如ifconfig）

man是按照手册的章节号的顺序进行搜索的。

####（2）、man设置了如下的功能键：
功能键|功能
--|--
空格键 | 显示手册页的下一屏
Enter键 | 一次滚动手册页的一行
b | 回滚一屏
f | 前滚一屏
q | 退出man命令
h | 列出所有功能键
/word | 搜索word字符串


![man的使用示例](http://upload-images.jianshu.io/upload_images/2551993-44d6b1736ff78085.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>**注意：实际使用的时候，不用指定第几个章节也可以查看，如：man ls ，执行该命令之后得到的内容和上图一致**

##三、自动补全：
在敲出命令的前几个字母的同时，按下tab键，系统会自动帮我们补全命令


##四、历史命令：
当系统执行过一些命令后，我们可以通过如下两种方式查看以前执行的命令。

* 可按上下键翻看以前的命令，
* 也可以通过history将执行过的命令列举出来。
![history使用示例](http://upload-images.jianshu.io/upload_images/2551993-bddae0f5ed8e8d34.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
