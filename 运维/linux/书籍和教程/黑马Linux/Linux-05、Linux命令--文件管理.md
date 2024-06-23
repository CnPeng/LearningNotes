>181217补充：查看隐藏文件——ls -d .*

文件管理系列命令

### 1、查看文件信息：ls

ls是英文单词list的简写，其功能为列出目录的内容，是用户最常用的命令之一，它类似于DOS下的dir命令。

>* Linux文件或者目录名称最长可以有265个字符，
>* “.”代表当前目录，“..”代表上一级目录，
>* **以“.”开头的文件为隐藏文件，需要用 -a 参数才能显示。**

ls常用参数：

参数|含义
--|--
-a | 显示指定目录下所有子目录与文件，包括隐藏文件
-l | 以列表方式显示文件的详细信息
-h | 配合 -l 以人性化的方式显示文件大小
-R | 递归查看某个目录中内容

![ls使用示例](http://upload-images.jianshu.io/upload_images/2551993-5003d24edaff6513.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

图中列出的信息含义如下图所示：
![各字段的含义](http://upload-images.jianshu.io/upload_images/2551993-8798bccb843758f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### （1）、通配符的使用

与DOS下的文件操作类似，在Unix/Linux系统中，也同样允许使用特殊字符来同时引用多个文件名，这些特殊字符被称为通配符。

通配符|含义
--|--
* | 文件代表文件名中所有字符。
？| 代表文件名中任意一个字符。
[ ] | [”和“]”将字符组括起来，表示可以匹配字符组中的任意一个。“-”用于表示字符范围。`[abc]  匹配a、b、c中的任意一个。[a-f] 匹配从a到f范围内的的任意一个字符`
\ | 如果要使通配符作为普通字符使用，可以在其前面加上转义字符。`“?”和“*”处于方括号内时不用使用转义字符就失去通配符的作用 ` 

**通配符使用示例**

命令|含义
--|--
ls te*  | 查找以te开头的文件`
ls *html | 查找结尾为html的文件`
 ls ?.c | 只找第一个字符任意，后缀为.c的文件
ls a.? | 只找只有3个字符，前2字符为a.，最后一个字符任意的文件`
ls [a-f]* | 找到从a到f范围内的的任意一个字符开头的文件 
ls a-f | 查找文件名为a-f的文件,当“-”处于方括号之外失去通配符的作用
ls \*a | 查找文件名为*a的文件

#### （2）、其他用法

* **查看文件和权限**：ls -la或ll -a
* **显示隐藏文件**：ls -d .*

### 2、输出重定向命令：>

Linux允许将命令执行结果重定向到一个文件，将本应显示在终端上的内容保存到指定文件中。

如：`ls > test.txt`  （**test.txt 如果不存在，则创建；存在则覆盖其内容** ）

![输出重定向的使用示例](http://upload-images.jianshu.io/upload_images/2551993-f2b9a6f9b8816855.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：
>*  `\>` 输出重定向会覆盖原来的内容，
>* `\>>` 输出重定向则会追加到文件的尾部。

### 3、分屏显示：more

查看内容时，如果信息过长无法在一屏上显示，会出现快速滚屏，使得用户无法看清文件的内容，此时可以使用more命令。

**使用more命令后，每次只显示一页，按下空格键可以显示下一页，按下q键退出显示，按下h键可以获取帮助。**


![分屏显示--more的使用](http://upload-images.jianshu.io/upload_images/2551993-a094666b715491d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、管道：|

管道：一个命令的输出可以通过管道做为另一个命令的输入。

管道我们可以理解现实生活中的管子，管子的一头塞东西进去，另一头取出来，这里“ | ”的左右分为两端，左端塞东西(写)，右端取东西(读)。

![管道 | 的使用](http://upload-images.jianshu.io/upload_images/2551993-a58dfad217968d8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5、清屏：clear

clear作用为清除终端上的显示(类似于DOS的cls清屏功能)，也可使用快捷键：**Ctrl + L** 

![clear清屏的使用](http://upload-images.jianshu.io/upload_images/2551993-1a7d46613ef763c0.gif?imageMogr2/auto-orient/strip)

>注意：
在上面的动图中，我们执行了 clear 命令之后，滚动屏幕之后依旧能看到之前执行的命令及其结果。
`也就是说，clear 命令并不是把之前的命令和执行结果直接删除，而是让之前的命令滚动到不可见的范围了，向下滚动之后依旧可见`

### 6、切换工作目录： cd
在使用Unix/Linux的时候，经常需要更换工作目录。cd命令可以帮助用户切换工作目录。

>* **Linux所有的目录和文件名严格区分大小写**
>* cd后面可跟绝对路径，也可以跟相对路径。
>* **cd 后面如果省略目录，则默认切换到当前用户的主目录。**

命令|含义
--|--
cd | 切换到当前用户的主目录(/home/用户目录)，用户登陆的时候，默认的目录就是用户的主目录。
cd ~ | 切换到当前用户的主目录(/home/用户目录)
cd . | 切换到当前目录 
cd .. | 切换到上级目录
cd - | 可进入上次所在的目录

![cd 命令使用示例](http://upload-images.jianshu.io/upload_images/2551993-07d400e1df61d4f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>注意：
如果路径是从根路径开始的，则路径的前面需要加上 “ / ”，如 “ /mnt ”，通常进入某个目录里的文件夹，前面不用加 “ / ”。
![进入绝对路径或相对路径](http://upload-images.jianshu.io/upload_images/2551993-2603a0d42ea7f7db.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7、显示当前路径：pwd

使用pwd命令可以显示当前的工作目录，该命令很简单，**直接输入pwd即可，后面不带参数**。

![查看当前所处路径](http://upload-images.jianshu.io/upload_images/2551993-b7aa5b0e5f4a2907.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 8、创建目录：mkdir

通过mkdir命令可以创建一个新的目录。**参数-p可递归创建目录。**

>注意
>* **新建目录的名称不能与当前目录中已有的目录或文件同名**，
>* 并且目录创建者必须对当前目录具有写权限。

![创建目录--mkdir](http://upload-images.jianshu.io/upload_images/2551993-1d9246594cceb101.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 9、删除目录：rmdir

可使用 rmdir 命令删除一个目录。

>删除时 **必须离开目录，并且目录必须为空**，否则提示删除失败。

如何删除非空目录？参考下面的 rm


### 10、删除文件：rm

可通过rm删除文件或目录。使用rm命令要小心，因为文件删除后不能恢复。为了防止文件误删，可以在rm后使用-i参数以逐个确认要删除的文件。

常用参数及含义如下表所示：

参数|含义
--|--
-i | 以进行交互式方式执行
-f | 强制删除，忽略不存在的文件，无需提示
-r | 递归地删除目录下的内容，**删除文件夹时必须加此参数**

![image.png](http://upload-images.jianshu.io/upload_images/2551993-40dc5c4664d90aff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 11、建立链接文件：ln

Linux链接文件类似于Windows下的快捷方式。

#### （1）、链接文件分类及其特点：

链接类型|特点
--|--
软链接 |软链接不占用磁盘空间，源文件删除则软链接失效。（类似于windows下的快捷方式）
硬链接 |硬链接只能链接普通文件，不能链接目录。（类似于将源文件拷贝了一份）

#### （2）、创建链接文件

命令|含义
--|--
ln 源文件 链接文件  | 创建硬链接
ln -s 源文件 链接文件 | 创建软连接

如果没有-s
选项代表建立一个硬链接文件，两个文件占用相同大小的硬盘空间，即使删除了源文件，链接文件还是存在，所以-s选项是更常见的形式。

>注意：
如果软链接文件和源文件不在同一个目录，源文件要使用绝对路径，不能使用相对路径。

![创建链接文件](http://upload-images.jianshu.io/upload_images/2551993-6d577f48934240df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中我们可以看到，通过ls查看文件时，软连接文件第一位是 l, 基本不占用磁盘空间，同时会指明该软连接所指向的源文件 ； 而硬链接文件第一位则用 - 表示，体积与源文件大小一致

### 12、查看或者合并文件内容：cat

![cat的使用--查看或者合并查看文件内容](http://upload-images.jianshu.io/upload_images/2551993-f7977132dcaf9a52.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 13、文本内搜索：grep
Linux系统中grep命令是一种强大的 **文本内搜索工具**，grep允许对文本文件进行模式查找。如果找到匹配模式， grep打印包含模式的所有行。

#### （1）、grep命令格式

grep 的命令格式为：
>grep [-选项] ‘被搜索的内容串’ 文件名

>注意：
在grep命令中输入被搜索的字符串时，最好用单引号或双引号括起来。
例如：`grep‘a ’1.txt` 。

#### （2）、grep常用选项说明：

选项|含义
--|--
-v | 显示不包含匹配文本的所有行（相当于求反）
-n | 显示匹配行及行号
-i | 忽略大小写

#### （3）、grep搜索内容时使用正则表达式
grep搜索内容串可以是正则表达式。

`正则表达式是对字符串操作的一种逻辑公式，就是用事先定义好的一些特定字符、及这些特定字符的组合，组成一个“规则字符串”，这个“规则字符串”用来表达对字符串的一种过滤逻辑。`

grep常用正则表达式：

参数|含义
--|--
^a | 行首,搜寻以 a 开头的行：``grep -n '^a' 1.txt``
b$ | 行尾,搜寻以 b结束的行：``grep -n 'b$' 1.txt``
[Ss]igna[Ll] | 匹配 [ ] 里中一系列字符中的一个；搜寻匹配单词signal、signaL、Signal、SignaL的行：``grep -n '[Ss]igna[Ll]' 1.txt``
**.** (点) | 匹配单个非换行符的字符；匹配 e 和 e 之间有任意单个字符的情况，如匹配 eee，eae，eve等，但是不匹配 ee（无字符），eaae（多个字符） : ``grep -n 'e.e' 1.txt``

![grep搜索范例](http://upload-images.jianshu.io/upload_images/2551993-3ec2d263fd081b9a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 14、查找文件：find

find命令功能非常强大，通常用来 **在特定的目录下搜索符合条件的文件**，也可以用来搜索 **特定用户属主的文件**。

常用用法示例：

命令|含义
--|--
`find ./ -name test.sh` | 查找当前目录下所有名为test.sh的文件
`find ./ -name '*.sh' `| 查找当前目录下所有后缀为.sh的文件
`find ./ -name "[A-Z]*" `| 查找当前目录下所有以大写字母开头的文件
`find /tmp -size 2M` | 查找在/tmp 目录下等于2M的文件
`find /tmp -size +2M` | 查找在/tmp 目录下大于2M的文件
`find /tmp -size -2M` | 查找在/tmp 目录下小于2M的文件
`find ./ -size +4k -size -5M` | 查找当前目录下大于4k，小于5M的文件
`find ./ -perm 0777` | 查找当前目录下权限为 777 的文件或目录

### 15、拷贝文件：cp

cp命令的功能是 **将给出的文件或目录复制到另一个文件或目录中**，相当于DOS下的copy命令。

常用选项说明：

选项|含义
--|--
-a | 该选项通常在复制目录时使用，它保留链接、文件属性，并递归地复制目录，简单而言，保持文件原有属性。
-f | 已经存在的目标文件而不提示
-i | 交互式复制，在覆盖目标文件之前将给出提示要求用户确认 
**-r** | 若给出的源文件是目录文件，则cp将递归复制该目录下的所有子目录和文件，**目标文件必须为一个目录名**。
-v | 显示拷贝进度

如：``cp vim_configure/ code/ -ivr `` 把文件夹 vim_configure 拷贝到 code 目录里。

![cp示例代码--拷贝目录](http://upload-images.jianshu.io/upload_images/2551993-69489784ecb38fe0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![cp示例代码--拷贝文件](http://upload-images.jianshu.io/upload_images/2551993-aae4be852ee92f00.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 16、移动文件：mv

用户可以使用mv命令来 **移动文件或目录，也可以给文件或目录重命名**。

常用选项说明：

选项|含义
--|--
-f | 禁止交互式操作，如有覆盖也不会给出提示
-i | 确认交互方式操作，如果mv操作将导致对已存在的目标文件的覆盖，系统会询问是否重写，要求用户回答以避免误覆盖文件
-v | 显示移动进度


![mv使用示例1](http://upload-images.jianshu.io/upload_images/2551993-0cad925fd150584d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 17、归档/打包管理：tar

计算机中的数据经常需要备份，**tar是Unix/Linux中最常用的备份工具**，
此命令可以把一系列文件归档到一个大文件中，也可以把档案文件解开以恢复数据。

#### （1）、tar的命令格式
> tar [参数] 打包文件名 文件

>注意：
>* **tar命令很特殊，其参数前面可以使用“-”，也可以不使用。**
>* 打包文件名必须以 **.tar** 结尾

#### （2）、tar命令的常用参数
常用参数：

参数|含义
--|--
-c | 生成档案文件，创建打包文件
-v | 列出归档解档的详细过程，显示进度
**-f** | **指定档案文件名称，f后面一定是.tar文件，所以必须放选项最后**
-t | 列出档案中包含的文件
-x | 解开档案文件

注意：除了f需要放在参数的最后，其它参数的顺序任意。

![tar打包与解包示例](http://upload-images.jianshu.io/upload_images/2551993-8a3d2a7db266a45a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 18、文件压缩解压：gzip

tar与gzip命令结合使用实现文件打包、压缩。

tar只负责打包文件，但不压缩。
用gzip压缩tar打包后的文件，其扩展名一般为 **``xxxx.tar.gz``**。

#### （1）、gzip使用格式如下：

>gzip [选项] 被压缩文件

#### （2）、gzip常用选项及其含义

常用选项：

选项|含义
--|--
-d | 解压
-r | 压缩所有子目录

![gzip压缩和解压缩](http://upload-images.jianshu.io/upload_images/2551993-e6b545883af91b1c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### （3）、tar调用 -z 参数实现压缩与解压缩

tar这个命令并没有压缩的功能，它只是一个打包的命令，但是在tar命令中增加一个选项(-z)可以调用gzip实现压缩或解压缩的功能。

``-z ：指定压缩包的格式为：xxxx.tar.gz``


##### 1）、压缩用法：
>tar cvzf 压缩包包名 文件1 文件2 ...

![tar 调用 -z 参数实现压缩](http://upload-images.jianshu.io/upload_images/2551993-e24823366d8349bd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 2)、查看压缩包内容

>tar tvf 压缩包名

![使用 tar tvf 查看压缩包内容](http://upload-images.jianshu.io/upload_images/2551993-38d737d71ff463df.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##### 3）、解压用法

> tar zxvf 压缩包包名

或 
> tar xvf 压缩包名

![tar 调用 -z 参数实现解压缩](http://upload-images.jianshu.io/upload_images/2551993-0e83c7e6c8729ea7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![tar未增加 -z 直接实现解压缩](http://upload-images.jianshu.io/upload_images/2551993-5e33d3a1820a5426.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 4）、tar 调用 -C 参数实现解压到指定目录

解压到指定目录：-C （大写字母“C”）
> tar xvf 压缩包名 -C 指定目录

![解压到指定目录](http://upload-images.jianshu.io/upload_images/2551993-b414cc0b9dcfc8a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 19、文件压缩解压：bzip2

tar与bzip2命令结合使用实现文件打包、压缩(用法和gzip一样)。

tar只负责打包文件，但不压缩，
用bzip2压缩tar打包后的文件，其扩展名一般用 **`xxxx.tar.bz2`**。

在tar命令中增加一个选项(-j)可以调用bzip2实现了一个压缩或解压缩的功能。

>* **压缩用法：**
>tar jcvf 压缩包包名 文件...
如：``tar jcvf bk.tar.bz2 *.c``
>* **解压用法：**
>tar jxvf 压缩包包名 
如：``tar jxvf bk.tar.bz2``


### 20、文件压缩解压：zip、unzip

通过zip压缩文件的目标文件 **不需要指定扩展名，默认扩展名为zip**。

>* **压缩文件：**
zip [-r] 目标文件(没有扩展名) 源文件
>* **解压文件：**
unzip -d 解压到的目录 压缩文件

![zip 与 unzip的使用](http://upload-images.jianshu.io/upload_images/2551993-5601ededc5e14b2b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



### 21、查看命令位置：which

查看指定命令对应的可执行程序在本地的位置
![which查看命令所在位置](http://upload-images.jianshu.io/upload_images/2551993-d57a5d27f98ca28c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
