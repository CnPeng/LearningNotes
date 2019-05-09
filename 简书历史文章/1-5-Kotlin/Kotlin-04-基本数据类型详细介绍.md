![kotlin基本数据类型](http://upload-images.jianshu.io/upload_images/2551993-abb728ef8c7d501e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


在上面的图中，我们能够清晰的看到，kotlin 的基本数据类型包括 数值、字符、字符串、布尔类型、数组。接下来，我们就一一了解这些类型。

##一、数值

在开篇的图中我们可以知道，数值的具体类型有如下几种：

  
类型|含义|位宽|取值范围(最小值~最大值)
--|--|--|--
Byte| 整数| 8|-128 ~ 127
Short|整数|16|-32768 ~ 32767
Int |整数|32|-2147483648 ~ 2147483647
Long|整数|64|-9223372036854775808 ~ 9223372036854775807
Float|单精度小数|32|1.4E-45  ~  3.4028235E38
Double|双精度小数|64|4.9E-324 ~ 1.7976931348623157E308

###1、数值的表示方式
  * Long 类型数据通常会在末尾加 L 或 l 后缀，如 123L，123l，**建议用L**

  * Float 类型数据通常会在末尾加 F 或 f 后缀，如 12.3F，12.3f，  如果一个小数 **不加后缀 F 或 f , 则默认为是 Double类型**

 * kotlin中的数值可以用二进制、十进制、十六进制表示，但 **不支持八进制！！**。所以 十进制的 3 可以用如下三种方式表示：
   * `二进制: 0b00000011`
   * ` 十进制: 3` 
   * `  十六进制: 0x3`


###2、用下划线增加大数据的易读性

先来看这么一个数据， 1234500000，这是 十二亿三千四百五十万， 我们看到这么一个数据的时候，基本都会去挨个的从左到右的数，个十百千万十万百万千万亿，然后才知道这个数据到底是多少。

对于例子中这种值比较大的数据，我们读起来很费劲，但是，**kotlin 1.1 版本之后我们可以使用 下划线 _ 来链接较大的数值** ，我们可以每隔三位或者四位加一个下划线，这样，我们在读数据的时候就能一路了然了。
        
示例如下：
```
val oneMillion = 1_000_000
val creditCardNumber = 1234_5678_9012_3456L
val socialSecurityNumber = 999_99_9999L
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```
###3、数值的类型转换

####（1）、显示类型转换
先来看一段代码：

![将Byte 值直接赋值给 Int类型](http://upload-images.jianshu.io/upload_images/2551993-08c2dca779bdc93a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中，我们看到，当我们将 Byte 类型的 a 赋值给 Int 类型的 b 时程序爆红了，那么我们该怎么解决呢？很简单：a.toInt( )，参考下图：

![Byte 转为 Int型](http://upload-images.jianshu.io/upload_images/2551993-783bb86d531c00ec.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

那么，如果我将 Int 数据赋值给 Byte 呢？

![Int 数据赋值给 Byte](http://upload-images.jianshu.io/upload_images/2551993-5881d5849ed687f9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

瞧，还是报错，那么怎么解决呢？a.toByte() 

![Int 转为Byte](http://upload-images.jianshu.io/upload_images/2551993-793079caa56b24be.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过上面的示例我们可以得出如下结论：
> **当不同数据类型的变量之间进行赋值时，必须进行类型转换。**
所以，kotlin给我们提供了如下转换方法：

方法名|作用
--|--
 toByte()|将数据转为Byte类型
 toShort()| 将数据转为Short
 toInt()|将数据转为Int
 toLong()|将数据转为 Long
 toFloat()| 将数据转为Float
 toDouble()| 将数据转为Double 
 toChar()| 将数据转为Char
 toString()|将数据转为String

####（2）、隐式类型转换
在上面的代码中，我们在代码中显示声明了 b 的类型，所以在给他赋值时，如果类型不一致则必须进行显示类型转换，但是，如果我们没有给 b 声明具体的类型呢？

![未声明 b 的类型](http://upload-images.jianshu.io/upload_images/2551993-bd271cf15257862b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

瞧，没有在报错，这是因为类型推断的作用，这种情况下，b 的类型就是 a 的类型。

另外，在下面的代码中，也是一种隐式类型转换的模式。
```
val l = 1L + 3   // Long + Int => Long
```
> 其实，在这里的这个隐式类型转换，就是类型推断。


##二、字符
* kotlin中字符用 **Char** 类型表示。
* 用 **单引号 ‘ ’** 括起来的内容都是字符。

**注意：**
**在 kotlin 中不能直接当作数字参与算术运算！！**看下图：
![字符不能直接参与与数值的算术运算](http://upload-images.jianshu.io/upload_images/2551993-86316dff02b5d63b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中，我们让 数值 3 和 字符 ‘1’ 直接进行算术运算，直接在 + 下面爆红了！！！意思就是说：char 不能直接参与算术运算，`这点与 java 中的char 不同，java 中 每个 char 都有一个对应的数值，可以直接参与算术运算 `

但是，**在kotlin中，如果我们想让 char 参与算术运算，需要先做类型转换**，示例代码如下：
```
fun main(args: Array<String>) {
    val b = 3 + '1'.toInt()
    print(b)
}
```
运行结果：

![将 char 转换类型之后参与运算的结果](http://upload-images.jianshu.io/upload_images/2551993-46667ca1f803f171.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


##三、字符串
###1、字符串的类型
* kotlin 中字符串用 **String** 类型表示。
* 用 **一对双引号 “ ”** ，或者 **一对的三个引号 “”“  ”“”** 括起来的内容就是字符串。

那么，用 ***“ ”*** 括起来的字符串和 **“”“  ”“”** 括起来的内容有什么区别呢？他们其实分别代表了两种字符串类型：**转义型字符串 和 原样字符串（也可以叫原生或者原始字符串）**，
* 用 “ ” 括起来的内容称为 转义字符串，
* 用 “”“  ”“” 括起来的内容称为 原样字符串

####（1）、原样字符串
原样字符串也可以叫做原始字符串或者原生字符串，其根本特点在于：
>**在打印原样字符串时，你输入的字符串是什么样的，它打印出来就是什么样的！**

看示例代码：
```
fun main(args: Array<String>) {
    val str = """        //这里使用 “”“  ”“” 创建了一个原生字符串
        床前明月光，
        疑是地上霜。
        举头望明月,
        低头思故乡。
    """
    println(str)
}
```
运行结果：

![原样字符串](http://upload-images.jianshu.io/upload_images/2551993-0f23525879306ae1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

瞧，我们在创建字符串时使用了换行符让内容换行，打印的时候这些格式被保留了！这就是 原样字符串。

>原样字符串的前导符

####（2）、转义字符串
转义字符串就是支持转义字符的字符串。

还是 李白的《静夜思》，我们直接使用双引号加换行的方式创建一个字符串，如下：
```
fun main(args: Array<String>) {
    val str2 = "" +  
            "床前明月光，" +    //使用换行时，系统自动用 + 将字符串链接了
            "疑是地上霜，" +
            "举头望明月，" +
            "低头思故乡。"

    println(str2)
}
```
查看运行结果：
![转义字符串](http://upload-images.jianshu.io/upload_images/2551993-4cd89ceb0f024101.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你看，虽然我们在创建字符串的使用手动敲击了键盘的回车，从外表看起来也像是换行了，但是打印出来其实是在一行的！！

那么，如果我们想用 转义字符串打印出 原始字符串的样子该怎么办呢？——**用转义字符啊！**，代码如下：
```
fun main(args: Array<String>) {
    val str3 = " 床前明月光，\n 疑是地上霜，\n 举头望明月，\n 低头思故乡。"
    println(str3)
}
```
运行结果：

![image.png](http://upload-images.jianshu.io/upload_images/2551993-ec013af2ce301a37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

好了，到这里我想你就应该明白为什么它叫 转义字符串了吧？——**支持转义字符，通过转义字符可以实现特殊的效果**

但是，你也可能会想，原样字符串不支持转义字符么？**是的，原样字符串不支持转义字符**，不信你就看下面的代码：
```
fun main(args: Array<String>) {
    val str = """
        床前明月光，\n
        疑是地上霜。\n
        举头望明月, \n
        低头思故乡。

    """
    println(str)      //原样字符串不支持转义字符
}
```
运行结果：
![](http://upload-images.jianshu.io/upload_images/2551993-70d04ca7ac842a3b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

你看，换行符的转义符 \n 直接被打印出来了。。。

###2、获取字符串元素

字符串是由一个个的字符元素组成的，如果我们需要获取某个字符串中的元素有两种方式：

**方式A：使用索引运算符访问**
* 字符串中元素的索引 从 0 开始计数。
* 使用索引运算符获取元素的格式为： 字符串[i] 
示例代码：
```
fun main(args: Array<String>) {
    val str = "123456789"
    val a = str[1]  //使用索引运算符获取字符串中的元素
    println(a)
}
```

**方式B：用 for 循环迭代字符串**
在 kotlin 中字符串也支持 for 循环，通过 for 循环我们也可以获取字符元素.

>kotlin 中 for 循环的格式为 ：
>```
>for (a in str){
>        //TODO sth   
>}
>```

使用 for 循环迭代字符串的示例代码：
```
fun main(args: Array<String>) {
    val str = "123456"

    //遍历 字符串中的内容 并打印出来
    for (a in str) {
        println(a)
    }
}
```
运行结果：
![for 循环遍历字符串中的元素](http://upload-images.jianshu.io/upload_images/2551993-f846cbeec24983a6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

###3、字符串模板
####（1）、字符串模板的标准写法
所谓的字符串模板，可以理解为一个占位符，这个占位符的值为实际传入的数据。

字符串模板 以 **$** 开头，后面跟一个 **{ }** ，**{ }** 中的内容可以是一个变量、方法或者一个运算式。

示例代码：
```
fun main(args: Array<String>) {
    inputSth("abc")
}

fun inputSth(content: String) {
    println("传入方法的数据是：${content}")    //字符串模板的使用
}

```
运行结果：
![字符串模板的使用](http://upload-images.jianshu.io/upload_images/2551993-9cd824e3a670853e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）、字符串模板的简写方式
我们再来看下面的代码：
```
fun main(args: Array<String>) {
    inputSth("abc")
}

fun inputSth(content: String) {
    println("传入方法的数据是：$content，它的长度是 ${content.length}")    //字符串模板的使用
}

```
细心的你可能已经发现了，这次的代码和上次的明显有区别，**$content** 省略了大括号， 而后面的 **${content.length}** 并没有省略大括号，这是因为：
>* **如果字符串模板中包裹的内容是单一的变量（或常量）可以省略大括号{ }**
>* **如果字符串模板中包裹的是表达式，则不能省略大括号**

上面代码的运行结果：
![](http://upload-images.jianshu.io/upload_images/2551993-dda0385f1aa486e8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###4、字符串比较

####（1）、== 和 equals(xx)
kotlin 中比较字符串有两种方式: **==** 和 **.equals()**

这两种方式都能比较字符串是否相同。看代码：
```
fun main(args: Array<String>) {
    var str1 = "a"
    var str2 = "a"
    println(str1 == str2)          //使用 == 判断字符串是否一致  
    println(str1.equals(str2))  // 使用 equals 判断字符串是否一致
}
```
运行结果：
![](http://upload-images.jianshu.io/upload_images/2551993-1692188784f92329.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通过上面的代码可知 ：
> kotlin 中 **== 具有 与 .equals(XX) 相同的功能** 

####（2）、equals(xx) 和 equals(xx , Boolean)
**equals( xx )** 有一个重载方法 **equals(xxx , Boolean)** ，后面的Boolean表示 **是否忽略大小写** ，true 忽略，false不忽略。equals(xx) 内部将Boolean赋值为false。看代码：
```
fun main(args: Array<String>) {
    var str1 = "a"
    var str2 = "A"
    println(str1.equals(str2))
    println(str1.equals(str2,true))
}
```
运行结果：

![](http://upload-images.jianshu.io/upload_images/2551993-1d23fb67e8d315ad.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##四、 布尔
布尔类型的关键字为 **Boolean** ，它有两个值:true 和 false。 

布尔运算符有:

布尔运算符|含义
--|--
ll | 短路逻辑或 
&& | 短路逻辑与 
! |逻辑非

>关于 与&、或 | 、非！不做过多解释了

##五、数组

 * Kotlin 中使用 **Array** 类来表示数组类型
* 它定义了 get() 和 set() 函数和 size 属性，以及一些其他有用的成员 函数,如：
```
class Array<T> private constructor() { val size: Int
    operator fun get(index: Int): T
    operator fun set(index: Int, value: T): Unit
    operator fun iterator(): Iterator<T>
    // ......
}
```
###1、创建数组

####（1）、使用 arrayOf() 创建数组并初始化元素
**使用该方式创建的数组可以通过 set 方法修改元素，但是不能增加元素！**
```
fun main(args: Array<String>) {
    var arr1 = arrayOf(1, 2, 3, 4, 5)   //创建一个指定元素的数组
    println(arr1[0])        //打印0索引元素
}
```

####（2）、 arrayOfNulls() 创建指定大小的空数组
**使用该方式创建数组时需要指定数组元素个数以及元素类型，然后可以通过set 方法设置元素，但是元素个数不能超过设定的值**
```
fun main(args: Array<String>) {
    var arr2 = arrayOfNulls<Int>(5)  //创建一个空数组，该数组接收的数据类型为 Int，最多能放入5个元素
    arr2.set(0, 1)
    arr2.set(1, 2)
    arr2.set(2, 3)
    arr2.set(3, 4)
    arr2.set(4, 5)
    println(arr2[1])
}
```
###2、获取数组元素
获取数组元素的方式有三种：
* arr[index]  ——角标方式
* arr.get(index) ——get方法
* for (a in arr){ } ——for 循环方式

示例代码：
```
fun main(args: Array<String>) {
    var arr1 = arrayOf(1, 2, 3, 4, 5)   //创建一个指定元素的数组
    println(arr1.get(1))        //使用 get() 方法获取元素
    println(arr1[1])            //使用 索引角标 获取元素

    for (a in arr1) {           //使用 for 循环遍历获取元素
        println(a)
    }
}
```
运行结果：
![](http://upload-images.jianshu.io/upload_images/2551993-26c70eb24a9763f3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3、ByteArray 、ShortArray 、IntArray 
ByteArray 、ShortArray 、IntArray 和 Array 并没有继承关系，但是它们有同样的方法属性集。使用这三个数组类，避免了数据的装箱操作。
```
fun main(args: Array<String>) {
    var arr1 = intArrayOf(1, 2, 3, 4, 5)   //创建一个指定元素的数组
    println(arr1.get(1))        //使用 get() 方法获取元素
    println(arr1[1])            //使用 索引角标 获取元素

    for (a in arr1) {           //使用 for 循环遍历获取元素
        println(a)
    }
}
```


本文到此结束，谢谢观看！

---

**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注!**

![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


