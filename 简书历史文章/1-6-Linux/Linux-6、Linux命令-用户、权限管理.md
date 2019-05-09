用户是Unix/Linux系统工作中重要的一环，用户管理包括用户与组账号的管理。

在Unix/Linux系统中，不论是由本机或是远程登录系统，每个系统都必须拥有一个账号，并且对于不同的系统资源拥有不同的使用权限。

Unix/Linux系统中的root账号通常用于系统的维护和管理，它对Unix/Linux操作系统的所有部分具有不受限制的访问权限。在大多数版本的Unix/Linux中，都不推荐直接使用root账号登录系统。

在Unix/Linux安装的过程中，系统会自动创建许多用户账号，而这些默认的用户就称为“标准用户”。


###1、查看当前用户：whoami
* **``whoami``**该命令用户查看 **当前系统当前账号**的用户名。

![whoami--查看当前用户](http://upload-images.jianshu.io/upload_images/2551993-9757057d69dbc2d0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* 可通过 **``cat /etc/passwd``**查看系统用户信息。

![cat /etc/passwd查看系统用户信息](http://upload-images.jianshu.io/upload_images/2551993-7cfca76313f45c30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

由于系统管理员通常需要使用多种身份登录系统，例如通常使用普通用户登录系统，然后再以su命令切换到root身份对传统进行管理。这时候就可以使用whoami来查看当前用户的身份。

###2、查看登录用户：who
who命令用于查看当前 **所有登录系统的用户** 信息。
常用选项：

选项|含义
--|--
-m或am I |只显示运行who命令的用户名、登录终端和登录时间
-q或--count | 只显示用户的登录账号和登录用户的数量
-u | 在登录时间后显示该用户最后一次操作到当前的时间间隔
-u或--heading | 显示列标题

![who--查看登录用户](http://upload-images.jianshu.io/upload_images/2551993-e6a18ec0b95569ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、退出登录账户： exit
* 如果是图形界面，退出当前终端；
* 如果是使用ssh远程登录，退出登陆账户；
* 如果是切换后的登陆用户，退出则返回上一个登陆账号。

###4、添加用户账号：useradd

在Unix/Linux中添加用户账号可以使用 **adduser** 或 **useradd** 命令，因为adduser命令是指向useradd命令的一个链接，因此，这两个命令的使用格式完全一样。

useradd命令的使用格式如下： 
>useradd [参数] 新建用户账号
adduser [参数] 新建用户账号

参数|含义
--|--
-d | 指定用户登录系统时的主目录，如果不使用该参数，系统自动在/home目录下建立与用户名同名目录为主目录
-m | 自动建立目录
-g | 指定组名称

相关说明：
* ``Linux每个用户都要有一个主目录，主目录就是第一次登陆系统，用户的默认当前目录(/home/用户)；``
* ``每一个用户必须有一个主目录，所以用useradd创建用户的时候，一定给用户指定一个主目录；``
* ``用户的主目录一般要放到根目录的home目录下，用户的主目录和用户名是相同的；``
* ``如果创建用户的时候，不指定组名，那么系统会自动创建一个和用户名一样的组名。``

命令|含义
--|--
useradd -d /home/abc abc -m | 创建abc用户，如果/home/abc目录不存在，就自动创建这个目录，同时用户属于abc组
useradd -d /home/a a -g test -m |创建一个用户名字叫a，主目录在/home/a，如果主目录不存在，就自动创建主目录，同时用户属于test组
cat /etc/passwd | 查看系统当前用户名

>**注意：**
> **在ubuntu平台，当某些操作需要管理员权限时，无需切换到root用户，只需在命令前加“sudo”即可。**
>* `sudo是ubuntu平台下允许系统管理员让普通用户执行一些或者全部的root命令的一个工具，减少了root 用户的登陆和管理时间，提高了安全性。`
>* `增加用户，删除用户，修改用户密码，都需要使用管理员权限`


![ubuntu下增加用户的示例代码](http://upload-images.jianshu.io/upload_images/2551993-341b9fc672ebe977.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>ubuntu中通过-d参数指定主目录时命令会报错，不能使用-d 指定主目录


###5、设置用户密码：passwd
在Unix/Linux中，
* 超级用户可以使用passwd命令为普通用户设置或修改用户口令。（需要使用管理员权限）
* 普通用户也可以直接使用该命令来 **修改自己的口令**，而无需在命令后面使用用户名。

![更新用户密码](http://upload-images.jianshu.io/upload_images/2551993-d26420d37732c58a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![ubuntu下更新其他用户的密码](http://upload-images.jianshu.io/upload_images/2551993-7ed6f4b0abb74070.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###6、删除用户：userdel
需要使用管理员权限

命令|含义
--|--
userdel abc(用户名) | 删除abc用户，但不会自动删除用户的主目录 
userdel -r abc(用户名) | 删除用户，同时删除用户的主目录

###7、切换用户：su
可以通过su命令切换用户。

su后面可以加“-”。

`su`和`su –`命令不同之处在于，`su -`切换到对应的用户时会将当前的工作目录自动转换到切换后的用户主目录。
![su 与 su - 区别](http://upload-images.jianshu.io/upload_images/2551993-86130b4f89f7b83f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![su 与 su - 的区别--在ubuntu中的示例](http://upload-images.jianshu.io/upload_images/2551993-c26944a0d15175a3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


命令|含义
--|--
su | 切换到root用户
su root | 切换到root用户
su - | 切换到root用户，同时切换目录到/root
su - root | 切换到root用户，同时切换目录到/root
su 普通用户 | 切换到普通用户
su - 普通用户 | 切换到普通用户，同时切换普通用户所在的目录

###8、添加、删除组账号：groupadd、groupdel
>* groupadd 新建组账号 
>* groupdel 组账号 
>* cat /etc/group 查看用户组

![添加、删除组账号](http://upload-images.jianshu.io/upload_images/2551993-a33d1780e4b3d3ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>添加、删除组账号需要使用管理员权限，在ubuntu下使用sudo也不好使，具体如下
![ubuntu下使用sudo命令添加群组的示例--未成功](http://upload-images.jianshu.io/upload_images/2551993-127b38bb5bd80b39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###9、修改用户所在组：usermod
使用方法：
>usermod -g 用户组 用户名

![修改用户所在组](http://upload-images.jianshu.io/upload_images/2551993-dfb5a7464c93b8fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###10、修改文件权限：chmod
chmod 修改文件权限有两种使用格式：字母法与数字法。
####（1）、字母法：
>chmod u/g/o/a +/-/= rwx 文件

**[ u/g/o/a ]各个字符的含义：**

字符|含义
--|--
u | user 表示该文件的所有者
g | group 表示与该文件的所有者属于同一组( group )者，即用户组
o | other 表示其他以外的人
a | all 表示这三者皆是

**[ +-= ]的含义**

符号|含义
--|--
+ | 增加权限
- | 撤销权限
= | 设定权限

**rwx 的含义**

字符|含义
--|--
r | read 表示可读取，对于一个目录，如果没有r权限，那么就意味着不能通过ls查看这个目录的内容。
w | write 表示可写入，对于一个目录，如果没有w权限，那么就意味着不能在目录下创建新的文件。
x | excute 表示可执行，对于一个目录，如果没有x权限，那么就意味着不能通过cd进入这个目录。

**示例：**
**`chmod o+w file`** 给文件file的其它用户增加写权限：

![增加权限](http://upload-images.jianshu.io/upload_images/2551993-960c9208f94ff241.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`chmod u-r file`** 给文件file的拥有者减去读的权限：

![减去权限](http://upload-images.jianshu.io/upload_images/2551993-2626a9ffb5804a89.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`chmod g=x file`**设置文件file的同组用户的权限为可执行，同时去除读、写权限：

![设置权限](http://upload-images.jianshu.io/upload_images/2551993-f7fc0ddde431fedb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果需要同时进行设定拥有者、同组者以及其他人的权限，参考如下：

![一次设置多种用户权限1](http://upload-images.jianshu.io/upload_images/2551993-092fce284a3337b1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![一次设置多种用户权限2](http://upload-images.jianshu.io/upload_images/2551993-edefb1c2346bb84c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）、数字法：
“rwx” 这些权限也可以用数字来代替，具体对应关系如下：

字母|说明
--|--
r | 读取权限，数字代号为 "4"
w | 写入权限，数字代号为 "2"
x | 执行权限，数字代号为 "1"
- | 不具任何权限，数字代号为 "0"

>如执行：**`chmod u=rwx,g=rx,o=r filename `** 就等同于：**`chmod u=7,g=5,o=4 filename`**

**示例代码：**
**`chmod 751 file`**
* 文件所有者：读、写、执行权限
* 同组用户：读、执行的权限
* 其它用户：执行的权限

![chmod 751 file](http://upload-images.jianshu.io/upload_images/2551993-fab38d817cd0a6db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**`chmod 777 file`**
* 所有用户拥有读、写、执行权限

![chmod 777 file](http://upload-images.jianshu.io/upload_images/2551993-c3fe9367af04dc18.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：
**如果想递归所有目录加上相同权限，需要加上参数“ -R ”。** 
如：**`chmod 777 test/ -R `** 递归 test 目录下所有文件加 777 权限

###11、修改文件所有者：chown
>chown 新的所有者 文件名或目录名

![修改文件所有者](http://upload-images.jianshu.io/upload_images/2551993-e705b704f4fa34b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###12、修改文件所属组：chgrp
>chgrp 新的所属组名 文件名或目录名

![修改文件所属组](http://upload-images.jianshu.io/upload_images/2551993-eacbc438f7fbc0fa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
