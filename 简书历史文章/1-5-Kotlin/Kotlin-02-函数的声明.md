##一、函数声明的基本格式
在上一篇文章中，我们在创建Intellij IDEA 和 Eclipse 的编译环境时已经写过 HelloWorld 代码，具体代码为：
```
fun main(args: Array<String>) {
    println("HelloWorld")
}
```

那么，接下来我们来解析一下这个main函数（也可叫做 main方法）的格式，具体如下：

字段|含义
--|--
fun | 用来声明一个函数，表示它后面的内容是一个函数，
main | 函数的具体名称。名称自定义，你想叫啥就叫啥
args | 该函数的参数。如果某个函数有多个参数，使用逗号间隔
：| 声明参数类型的声明符。参数和类型之间必须用：链接
Array<String>|参数类型，这里表示args 的类型是 String类型的Array数组
{ } | 用来包裹函数的主体内容
println ("HelloWorld") | 这是函数主体，你想让函数实现什么功能就写出对应的代码即可

以上就是 函数声明的基本格式，以后我们在声明函数的时候也需要遵守上面的格式。

##二、函数的返回值类型

我们在定义一个函数之后，有时候是需要通过函数返回一些数据，那么这些数据会有对应的数据类型（具体下一节会有介绍），这就是函数的返回值类型。

###1、函数无返回值
在kotlin中，如果某个函数不需要返回数据，那么这个函数的类型就是 **Unit**。也就是说，我们前面说的 main 函数的完整写法应该是：
```
fun main(args: Array<String>): Unit {
    println("HelloWorld")
}
```
但是，通常情况下， 如果某个函数没有返回值，那么 **Unit** 可以省略。

###2、函数有返回值
####（1）、显示声明返回值类型
如果某个函数有返回值，那么就需要在函数声明中声明其返回值类型，示例代码如下：
```
fun sum(a: Int, b: Int): Int {
    return a + b
}
```
在上面的代码中，我们定义了一个 求和的函数，接收两个 Int 类型的参数，返回值是两个参数的和，由于得到的和  是 Int 类型的数据，所以该函数的返回值类型就是Int，这个返回值类型就需要在函数中声明，否则会报错，报错状态如下：
![返回值类型不匹配](http://upload-images.jianshu.io/upload_images/2551993-b26ce73fea090174.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）、隐式声明返回值类型
对于上面 显示声明返回值类型 中的示例代码，还有一种简写方式，如下：
```
fun sum(a: Int, b: Int) = a + b
```
在上面的示例代码中，直接将表达式作为函数体，然后kotlin会自动根据 a+b 的类型来确定 sum 函数的返回值类型，这也就是 类型推断。

函数调用示例：
```
fun main(args: Array<String>) {
    println(sum(5, 6))
}

fun sum(a: Int, b: Int) = a + b
```
运行上面代码之后我们就会得到 5+6 的和。

##三、参数长度可变的函数
所谓参数长度可变的函数，就是说，在调用这个函数的时候我们可以根据实际需要传入参数，参数的个数由我们的具体需要决定，可能是1个，也可能是2 个或3个等等。

参数长度可变的函数在声明的时候需要使用关键字 **vararg**，示例如下：
```
fun varList(vararg vars: Int) {  //关键在vararg
    for (v in vars) {             //遍历输入的内容
        println(v)
    }
}
```
完整实例代码及运行结果：

```
fun main(args: Array<String>) {

    varList(1)
    println("上面调用时传入一个参数，下面调用时传入多个参数")
    varList(1, 2, 3, 4)
}

fun varList(vararg vars: Int) {
    for (v in vars) {
        println(v)
    }
}
```
![运行结果](http://upload-images.jianshu.io/upload_images/2551993-b2f0be8ce9157be6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##补充：Kotlin中的注释
kotlin支持单行注释和多行注释。
// 表示单行注释  ，上面在参数可变的函数示例中就使用了单行注释
/*  */ 表示多行注释

=================================================

**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注！**
![扫码关注](http://upload-images.jianshu.io/upload_images/2551993-da7a61b3bf8be04d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




