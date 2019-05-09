![变量与常量](http://upload-images.jianshu.io/upload_images/2551993-170018c4ede9d99d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##一、变量与数据类型
###1、变量的基本概念
**变量** ：是用来存储数据的一个容器，它其中存储的数据可以在一定的范围内进行变化，这个范围就是数据类型（数据类型后面会有介绍）

Kotlin中变量用 **var** 来标识，var 的全拼是：variable ['vεəriəbl]

###2、变量的声明与使用
```
fun main(args: Array<String>) {
    var name = "CnPeng"
    println(name)
}
```
在上面的代码中，我们通过 `var name="CnPeng"` 声明了一个变量name ,并给他赋值 “CnPeng” ，这样，我们再次执行该main函数时，输出的结果就是 “CnPeng”
![运行结果](http://upload-images.jianshu.io/upload_images/2551993-20ef43826cab72b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

关于上图中的bug , 请参考：https://stackoverflow.com/questions/18794573/class-javalaunchhelper-is-implemented-in-both-libinstrument-dylib-one-of-th 

###3、类型推断与数据类型
```
//这是一个错误的示例代码：
fun main(args: Array<String>) {
    var name = "CnPeng"
    name=123
    println(name)
}
```
在上面的代码中，我们给 name 重新赋值123 ，这时候IDEA会爆红，我们将光标移动到表红线的位置会得到一个弹窗提示，如下：
![数据类型错误](http://upload-images.jianshu.io/upload_images/2551993-a4c94ebeffd43234.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这个弹窗的意思是说：**String 类型的变量不能接收 integer 类型的数据！！**
这就牵涉到了两个概念，一个是类型推断，一个是数据类型。

####（1）、Kotlin常见数据类型简介

类型|含义|位宽
--|--|--
Byte| 整数| 8
Short|整数|16
Int |整数|32
Long|整数|64
Float|单精度小数|32
Double|双精度小数|64
String|字符串|
Char |字符|
Boolean|布尔|

* Byte、Short、Int、Long 都表示整数，但是他们的取值范围不一样，从左到右依次变大。
* Float、Double 都用来表示小数，但是范围也不一样，Double的范围大于Float。
* Float类型数据通常会带有后缀 **f**或者 **F** 
* 用 双引号包裹起来的内容都是字符串
* 用单引号括起来的是字符，在kotlin中，字符类型不能直接和数值类型进行操作
* Boolean类型取值：true， false 


>上面几种数据类型的具体取值不列举了
>* ***关于数据类型，后面会有详细介绍***

####（2）、类型推断
`var name="CnPeng"`

在上面这句代码中，我们将字符串“CnPeng” 赋值给 name , 那么 kotlin 就认为name 就是一个字符串类型的变量了，不能在接收字符串类型之外的值，所以当我们将没加引号的123 赋值给 name 时会报错，这就是类型推断。

除了依靠类型推断让 kotlin 系统自己去确定变量的数据类型之外，我们还可以显示的声明一个变量的类型，代码如下：

```
fun main(args: Array<String>) {
    var name: String = "CnPeng"  //显示声明数据类型
    println(name)
}
``` 

>TIPS : 与java 的区别
>A : 语句后面不需要写 分号 
>B : 声明变量的写法不一样，kotlin中变量名在前，类型名在后，变量名与类型之间使用 ：链接

##二、常量
###1、常量的定义
在上一小结中我们知道了什么是变量，那么常量又是什么呢？

**常量** 也是一个存储数据的容器，它存储的数据是固定的、不会发生变化的。也就是说，常量只能在初始化的时候被赋值。常量是变量的一种特殊形式。

常量 用 **Val** 来标识，当一个变量通过Val 声明为常量之后，就不能再进行赋值操作，否则会报错，示例代码如下：
![常量值不能被改变](http://upload-images.jianshu.io/upload_images/2551993-d1c025da15adba56.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###2、数字常量的命名
在命名数字常量时可以使用下划线间隔，让数值更易读，示例如下：

```
val oneMillion = 1_000_000                   //普通int数据
val creditCardNumber = 1234_5678_9012_3456L  //Long类型数据
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E              //十六进制类型
val bytes = 0b11010010_01101001_10010100_10010010  //二进制类型

```
##三、补充：打印数值类型变量的取值范围
实际使用中，我们可能需要知道某个数据类型的最大值或者最小值是多少，但是我们不可能完全记住，所以，我们就可以通过调用 MAX_VALUE 或者 MIN_VALUE 来获取，示例代码如下：
```
fun main(args: Array<String>) {
    var intMax = Long.MAX_VALUE
    var intMin = Long.MIN_VALUE
    println(intMax)
    println(intMin)
}
```
运行结果：
![运行结果](http://upload-images.jianshu.io/upload_images/2551993-0074d58153adb309.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

=================================================


**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注！**
![扫码关注](http://upload-images.jianshu.io/upload_images/2551993-da7a61b3bf8be04d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

























