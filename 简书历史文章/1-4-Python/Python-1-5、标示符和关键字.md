##一、标示符
###1、标示符
什么是标示符，看下图

![image.png](http://upload-images.jianshu.io/upload_images/2551993-45d68c05fdfb274a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>开发人员在程序中自定义的一些符号和名称

标示符是自己定义的,如变量名 、函数名等

###2、标示符的规则
>标示符由字母、下划线和数字组成，且数字不能开头

思考：下面的标示符哪些是正确的，哪些不正确为什么
```
   fromNo12
   from#12
   my_Boolean
   my-Boolean
   Obj2
   2ndObj
   myInt
   test1
   Mike2jack
   My_tExt
   _test
   test!32
   haha(da)tt
   int
   jack_rose
   jack&rose
   GUI
   G.U.I
```
>python中的标识符是区分大小写的

![image.png](http://upload-images.jianshu.io/upload_images/2551993-1e7defac61138eb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###3、命名规则
####（1）、见名知意
起一个有意义的名字，尽量做到看一眼就知道是什么意思(提高代码可 读性) 比如: 名字 就定义为 name , 定义学生 用 student

####（2）、驼峰命名法

![image.png](http://upload-images.jianshu.io/upload_images/2551993-5d99bc03bca48bfc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#####1）、小驼峰式命名法（lower camel case）：
 第一个单词以小写字母开始；第二个单词的首字母大写，例如：myName、aDog

#####2）、大驼峰式命名法（upper camel case）：
 每一个单字的首字母都采用大写字母，例如：FirstName、LastName

>不过在程序员中还有一种命名法比较流行，就是用下划线“_”来连接所有的单词，比如send_buf

##二、关键字
###（1）、什么是关键字
python中一些具有特殊功能的标示符，已经被系统使用的了字符称为关键字。
所以不允许开发者自己定义和关键字相同的名字的标示符

查看关键字:

![image.png](http://upload-images.jianshu.io/upload_images/2551993-1cdc3f685bb973a9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
