《Kotlin从零到精通Android开发——欧阳燊（shen）》读书笔记

* 原书作者的CSDN地址：https://blog.csdn.net/aqi00/article/details/80731717
* 随书源码：https://github.com/aqi00/kotlin

> 下列内容以书中内容为主，穿插了其他资料中的知识以及自己理解的内容。其他资料的引用会有来源说明，自己的理解或者DEMO会用CnPeng字样标记，无相关标记的均为原书内容。
>
> 其他参考资料主要包括：《Kotlin实战》、Kotlin官方文档、传智播客《Kotlin基础入门视频》等。
>
>—— CnPeng 181129 起





## 一：环境搭建

### 1、配置本地Gradle

* 先从网络下载gradle，地址为：[https://services.gradle.org/distributions/](https://services.gradle.org/distributions/)
* 下载完成后解压到自定义目录
* 然后按照下图操作：
 `Gradle Home 指向上一步中的自定义目录。`
 
![](https://images.gitee.com/uploads/images/2018/1129/214440_8c733db9_930142.png "屏幕截图.png")


### 2、手动升级Kotlin插件

虽然 AndroidStudio 集成了 Kotlin ，但可能不是最新，所以需要手动升级

 **方法1** 
 
![](https://images.gitee.com/uploads/images/2018/1129/215104_2797e07b_930142.png "屏幕截图.png")

 **方法2**
 
![](https://images.gitee.com/uploads/images/2018/1129/215320_d86b7099_930142.png "屏幕截图.png") 


### 3、查看/修改 Kotlin 的编译版本
 
 * **在设置中查看** 
 
![](https://images.gitee.com/uploads/images/2018/1129/215519_d36ec771_930142.png "屏幕截图.png")

* **在 `.gradle` 文件中查看**

![](https://images.gitee.com/uploads/images/2018/1129/215834_4e884a86_930142.png "屏幕截图.png") 


### 4、引用 Anko 库

[**点击查看更多 Anko 库相关内容 https://github.com/Kotlin/anko**](https://github.com/Kotlin/anko)

*  **步骤1：修改 Project 的 .gradle 文件** 

如下图：新增`ext.anko_version="0.10.7"`

![](https://images.gitee.com/uploads/images/2018/1130/120136_5f22de76_930142.png "屏幕截图.png")

* **步骤2：修改module的.gradle文件** 

在 dependencies 节点中新增 ` implementation "org.jetbrains.anko:anko-common:$anko_version"`

![](https://images.gitee.com/uploads/images/2018/1130/120034_02410a2d_930142.png "屏幕截图.png")

---

## 二：数据类型

### 1、基本数据类型

* 基本类型

Int、Long、Float、Double、Boolean、Char、String

* 类型之间的转换

toInt()、toLong()、toFloat()、toDouble()、toBoolean()、toChar()、toString()

### 2、数组

#### (1)、数组的声明

* 基本数据类型数组的声明：

intArrayOf(...)、longArrayOf(...)、floatArrayOf(...)、booleanArrayOf(...)、charArrayOf(...)


* String 类型数组的声明

String 是一个特殊的基本数据类型，使用 `arrayOf(...）`方法初始化数组。

```kotlin
val strArray : Array<String> = arrayOf("中国","山东","济南")
```

其他基本数据类型也可以使用 arrayOf(...) 初始化对应的数组

#### (2)、数组元素操作

* 数组长度：

通过 `array.size()` 获取

* 获取/修改元素

除了 `array[index]` 模式，也可以通过 `set()/get()` 获取和修改元素


### 3、字符串

#### (1)、字符串操作

* 查找——indexOf()
* 截取——substring()
* 替换——replace()
* 分割——split()

 **注意：** 
Java 中 split() 返回的是一个 String[], 而 **kotlin中返回的是一个 List<String>** 

#### (2)、字符串模板和拼接

* 占位符 `${...}`

```kotlin
val str = "用户的昵称是${user.name}"

val str2 = "str的值是$str"
```

#### (3)、如何显示货币符号$

* 方式1：`${‘＄’}` 

${} 还是字符串占位格式，其中包含的`＇＄＇`表示 ＄ 符号

```kotlin
val str = "人民币转换后的美元金额为${'$'}$rmbNum"
```

* 方式2：`＼$`

\表示转义，`＼＄` 转义后得到 ＄

```kotlin
//\S 表示转义获取$符号，$rmbNum 为字符串占位符
val str = "人民币转换后的美元金额为\$ $rmbNum"
```


### 4、容器

#### (1)、容器的基本操作

##### A: 分类：

分为可变和不可变，可变就是能执行增删改查操作的。

* 不可变的容器：Set、List、HashMap
* 可变的容器：MutableSet、MutableList、MutableHashMap


##### B: 通用方法

* isEmpty —— 是否为空
* isNotEmpty —— 是否非空
* clear —— 清空容器
* contains —— 是否包含指定元素
* iterator —— 获取迭代器
* count —— 获取元素个数/ 也可以通过 size 获取

##### C: 容器初始化

kotlin中的容器可以在定义的时候就执行初始化赋值操作。

容器初始化的函数如下：

* listOf()、mutableListOf()
* setOf()、mutableSetOf()
* mapOf()、mutableMapOf()


#### (2)、Set/MutableSet

##### A: 特点

* 内部元素无序
* 内部元素唯一（通过哈希值判断是否唯一，重复则覆盖）
* set不可变，MutableSet可变（所以，后面的几个特点是针对MutableSet的）
* MutableSet 中的 add 方法仅添加元素，但不知道添加到了哪个位置
* MutableSet 没有修改元素值的方法，元素被添加之后不可被修改
* MutableSet 的 remove 方法仅接收元素作为参数，不能接收索引——因为Set无序没有索引

##### B: 遍历

有三种遍历方式：for-in 、 迭代器、 forEach

* for-in

```kotlin
for(user in userSet){
    ...
}
```

* 迭代器

```kotlin
val iterator=userSet.iterator()
while(iterator.hasNext()){
    val user=iterator.next()
    ...
}
```

迭代器中存储的并非真实的元素，而是元素的地址，类似于C中的指针。

* forEach

使用默认的  **it** 代表内部元素。 

```kotlin
userSet.forEache{"用户的姓名为${it.name}"}
```

#### (3)、List/MutableList

##### A: 特点

* 有序有索引
* 能够通过set修改元素值，能够通过get获取元素值
* 通过add方法添加元素
* removeAt可以删除指定索引位置的元素

##### B: 遍历方式

除了 for-in 、迭代器、forEach, 还有一种根据索引遍历的方式：

```kotlin
// indices 表示userList的索引数组。这种方式本质上也是采用了for-in, 但遍历的是索引
for(i in userList.indices){
    val user=userList[i]
    ...
}
```

##### C: 排序

MutableList 可以执行排序的操作：

* sortBy 按照规则升序排列
* sortByDescending 按规则降序排列

```kotlin
userNameList.sortBy{it.length}
```



#### (4)、Map/MutableMap

##### A: 特点

* 以键值对的形式存储元素，键唯一

##### B: 初始化

调用 mapOf / mutableMapOf 执行初始化，在组织键值对元素时都有如下两种方式：

* 键 to 值
* Pair(键,值)

```kotlin
val userMap:Map<String,String>=mapOf("姓名" to “张三” , “性别” to "女")

val userMAp2:MutableMap<String,String>=mutableOf(Pair("姓名",“张三”), Pair("性别",“女"))
```

to 模式底层也是使用 Pair()对象构建的。


##### C: 常用方法

* containsKey 是否包含某个键
* containsValue 是否包含某个值
* put 方法用来修改或添加元素
* remove 根据键名删除值——删除元素


##### D: 遍历

三种模式：for-in 、 迭代器、 forEach

由于元素是一个键值对，所以，在遍历获取到元素之后可以视需要根据元素取出对应的key 和 value

```kotlin
for(item in userMap){
   val str="用户的${item.key} 为 ${item.value}"
}
```

---


## 三：控制语句

### 1、条件分支

#### (1)、简单分支--if...else

kotlin 中 if...else 语句具有返回值，类似于Java中的三目运算

```kotlin
//传入两个值 a 和 b , 将大值显示在TextView控件中
nameTextView.text=if(a>b) a else b
```

#### (2)、多分支

##### A: 特点
kotlin 中没有 switch/case ，

* **使用 when/else 替代** 
* when/else 也有返回值
* 执行完一个节点之后就会终止循环
* 分支中判断条件可以是常量/变量/表达式——Java中的switch/case 中 case 节点只能是常量
* 为了确定所有情况都做了判断，通常不能省略else

##### B: 基本使用

```kotlin
var count:Int = 0
btn_when_simple.setOnClickListener {
  tv_answer.text = when (count) {
        0 ->  "值为0"
        1 ->  "值为1" 

        else -> "其他值"
    }
    count = (count+1) % 3
}
```

##### C: 穿透

java 中 switch/case 需要穿透时需要列出每一个常量条件，但 kotlin中可以简化——

* 多个条件直接写在一行，使用 ”,“ 间隔
* 如果条件连续数值，可以通过  `in 起始值..结束值`  指定区间范围
* 如果条件是连续数值，并且需要判断不在区间范围内，则使用 `!in 起始值..结束值`

```kotlin
btn_when_region.setOnClickListener {
    tv_answer.text = when (count) {
        1,3,5,7,9 -> "取值为13579中的一个" 
        in 13..19 -> "取值为13到19中的一个" 
        !in 6..10 -> "取值不再6到10之间" 
        else -> "其他值"
    }
    count = (count+1) % 20
}
```

##### D、以表达式为分支条件

kotlin 中 通过  **is**  关键字用来判断 A 是否为 B 的实例——`A is B `，等同于 Java中的 `A instance B` .

```kotlin
var countType:Number;
btn_when_instance.setOnClickListener {
    count = (count+1) % 3
    countType = when (count) {
        0 -> count.toLong();
        1 -> count.toDouble()
        else -> count.toFloat()
    }
        
    tv_answer.text = when (countType) {
        is Long -> "Long类型"
        is Double -> "Double类型"
        else -> "其他类型"
    } 
}
```


### 2、循环处理

#### (1)、遍历循环

即 for-in 、forEach、 迭代器的使用


#### (2)、条件循环
满足某个条件时执行/终止循环。


##### A: 使用关键字

为此，新增了多个关键字：until、setp、downTo

```kotlin
// 遍历11到66之间的数值，until 声明了一个左闭右开的区间——不包含66，包含11 
for (i in 11 until 66) { 
    ...
}

// 遍历23-89之间的数值，步进为4——每次+4。23..89声明一个左右均闭合的区间
for (i in 23..89 step 4) {
    ... 
}

//倒序遍历从50到7
for (i in 50 downTo 7) {
    ...
}
```

##### B: 使用while、do/while

由于关键字有限，能实现的效果有限，所以，更复杂的逻辑可以使用 while 或 do/while实现

```kotlin
btn_repeat_begin.setOnClickListener {
    var poem:String=""
    var i:Int = 0
    while (i < poemArray.size) {
        if (i%2 ==0) {
            poem = "$poem${poemArray[i]}\n"
        } else {
            poem = "$poem${poemArray[i]}\n"
        }
        i++ 
    }
    poem = "${poem}该诗歌一共有${i}句"
    tv_poem_content.text = poem
}
```

```kotlin
btn_repeat_end.setOnClickListener {
    var poem:String=""
    var i:Int = 0
    do {
        if (i%2 ==0) {
            poem = "$poem${poemArray[i]}\n"
        } else {
        poem = "$poem${poemArray[i]}\n"
        }
        i++    
    } while (i < poemArray.size)

    poem = "${poem}该诗歌一共有${i}句" 
    tv_poem_content.text = poem
}
```


#### (3)、中断循环

使用 break——中断循环、continue——跳过本次循环，基本用法同Java，

另外，当嵌套循环时，还可以通过 `@循环标签名` 指定要中断的循环。

```kotlin
btn_repeat_break.setOnClickListener {
    var i:Int = 0
    var is_found = false 
    // outsize@ 表示为外层循环添加标签，名称为 outside
    outside@ while (i < poemArray.size) {
        var j:Int = 0
        var item = poemArray[i];
        while ( j < item.length) {
            if (item[j] == '一') { 
                is_found = true
                //中断标签名为outside的循环
                break@outside
            }
        j++ 
    }
    i++ 
}
```

### 3、空处理

#### (1)、字符串的有效性判断
校验空字符串的方法：

* isNullOrEmpty —— 为null或长度为0时返回true。
* isNullOrBlank —— 为null或长度为0或者全是空格，返回true。
* isEmpty —— 长度为0时返回true，必须先判断非null
* isBlank —— 长度为0或者全是空格返回true，必须先判断非null
* isNotEmpty —— 长度大于0时返回true，必须先判断非null
* isNotBlank —— 长度大于0并且非空格返回true，必须先判断非null

#### (2)、声明可空变量
kotlin中默认变量非空，如果需要声明一个可空的变量，则在类型后面追加一个问号？

```kotlin
var strCanNull:String?
```

#### (3)、校验空值的运算符——?、?:、!!

* ? 表示可空，如果空直接返回null
* ?: 表示一旦为空返回冒号后面的值，否则返回正常的值
* !! 表示断言非空(放弃非空判断)。但是，如果做了这个断言，但依旧为null，那么会报空指针。

```kotlin
var length_null:Int?
btn_question_dot.setOnClickListener {
    //strB后面跟了一个”?“表示可null，如果为null则直接返回null, 此时 length_null的值为null
    length_null = strB?.length
    tv_check_result.text = "追加 ? 调用对象属性时得到的长度为 $length_null"
}
```

```kotlin
btn_question_colon.setOnClickListener { 
    //如果strB非null，返回正常的 strB.length; 如果strB为null，则返回-1
    length = strB?.length ?: -1
    tv_check_result.text = "使用 ?: 调用对象属性时得到的长度为 $length"
}

```

```kotlin
btn_exclamation_two.setOnClickListener { 
    strB = "ABCDE"
    //只有百分百确定非null时，才使用!!,否则依旧会报空指针
    length = strB!!.length
    tv_check_result.text = "使用!!断言时得到的属性值长度为$length"
}
```


### 4、等式判断

#### (1)、结构相等
* kotlin中使用  **==** 判断两个数据是否相等，使用  **!=** 判断是否不相等。

* kotlin中的String也是使用这种方式。Java中使用equals()  

 **kotlin中比较字符串时不再比较在内存中的存储地址，而是直接比较变量值** ，这种方式就被叫做 结构相等，即模样相同/外观相同。

#### (2)、引用相等
如果需要判断引用是否相等，也就是判断内存地址是否一致，kotlin中使用  **===**，如果判断不一致，则使用 **！==**

* 对于基本数据类型(包括字符串)，结构相等和引用相等一致。
* 通过 clone 得到的对象即便属性值一样，但引用地址不一致。

#### (3)、is 和 in

* is 和 !is —— 判断A是否为B的实例,如：`对象名 is 类名 `
* in 和 !in —— 判断数组中是否包含某个元素，如 `变量名 in 数组名`

---

## 四：函数

Kotlin中设计师的初衷是把函数作为一个特殊的变量

### 1、函数基本用法

此处可参考 [传智Kotlin基础--张泽华](https://gitee.com/CnPeng_1/LearningNotes/wikis/2%E3%80%81%E4%BC%A0%E6%99%BAKotlin%E5%9F%BA%E7%A1%80--%E5%BC%A0%E6%B3%BD%E5%8D%8E?sort_id=364269) 中的函数内容

### 2、输入参数的变化


* 默认参数
* 具名参数
* 可变参数—— 关键字**vararg** 

此处可参考 [传智Kotlin基础--张泽华](https://gitee.com/CnPeng_1/LearningNotes/wikis/2%E3%80%81%E4%BC%A0%E6%99%BAKotlin%E5%9F%BA%E7%A1%80--%E5%BC%A0%E6%B3%BD%E5%8D%8E?sort_id=364269) 中的函数内容

### 3、特殊函数

#### (1)、泛型函数

* kotlin中使用 T 表示泛型。
* 在定义泛型函数时需要在 fun 后面加入 <T> , 然后指明某个参数的类型为 T 

泛型函数的定义：

```kotlin
//Kotlin中允许定义全局函数，将函数定义在kt文件中，然后全局可调用
fun <T> appendString(tag:String, vararg otherInfo: T?):String {
    var str:String = "$tag:" 
    for (item in otherInfo) {
        //遍历可变数组，然后拼接
        str = "$str${item.toString()},"
    }
    return str 
}
```

泛型函数的调用：

```kotlin
var count = 0
btn_vararg_generic.setOnClickListener {
    tv_function_result.text = when (count%3) {
        //在定义泛型函数时，需要指明T的类型
        0 -> appendString<String>("中国四大发明","造纸术","火药","印刷术","指南针")
        1 -> appendString<Int>("小于10的素数",2,3,5,7)
        else -> appendString<Double>("花钱的日子",5.20,6.18,11.11,12.12)
    }
    count++ 
}
```

#### (2)、内联函数

注意：《Kotlin从零到精通Android开发》书中并没有详细说明什么是内联函数，为什么需要内联函数，而是直接给出了一个具体化参数类型的内联函数——对应下面的B示例。

##### A: 内联函数(内容摘自《Kotlin实战》)

> 此处只是节选了《Kotlin实战》中内联函数的部分内容，详细内容还是参考《Kotlin实战》第八章内容

* 为什么需要内联函数？

lambda 表达式会被正常地编译成匿名类。这表示每调用一次 lambda表达式，一个额外的类就会被创建。

并且如果 lambda捕捉了某个变量, 那么每次调用的时候都会创建一个新的对象。 

这会带来运行时的额外开销，导致使用 lambda 比使用一个直接执行相同代码的函数效率更低 。

* 内联函数的作用是啥？

能够消除 lambda带来的性能开销， 还能够使 lambda 内的控制流更加灵活.

内联函数使用 inline 修饰符标记，内联函数在 **被使用的时候编译器并不会生成函数调用的代码，而是使用函数实现的真实代码替换每一次的函数调用** 

* 内联函数如何运作？

当一个函数被声明为 inline 时，它的函数体是内联的一一换句话说，函数体会被直接替换到函数被调用的地方，而不是被正常调用。来看一个例子以便理解生成的最终代码。

下面的函数用于确保一个共享资源不会并发地被多个线程访问。函数锁住一个 Lock 对象，执行代码块，然后释放锁。

```kotlin
//kotlin库中已经提供了一个synchronized(),此处定义的函数只做演示用
inline fun <T> synchronized(lock: Lock, action: () -> T): T {
    lock.lock()
    try {
        return action()
    } finally {
        lock.unlock()
    }
}

fun main(args: Array<String>) {
    println("执行 synchronized 之前")
    val lock: Lock = TestLock()
    synchronized(lock) {
        println("do cus action--inline")
    }
    println("执行 synchronized 之后")
}
```

因为 synchronized 函数是内联的，编译时不会构建匿名对象，所以，

上述main函数编译之后的字节码等同于：

```kotlin
fun main(args: Array<String>) {
    println("执行 synchronized 之前")
    val lock: Lock = TestLock()

    //内联函数体
    lock.lock()
    try {
        println("do cus action--inline")
    } finally {
        lock.unlock()
    }

    println("执行 synchronized 之后")
}
```

* 使用 inline 关键字只能提高带有 lambda 参数的函数的性能，

##### B: 具体化参数类型的内联函数
假设我们定义了下面一个方法：

```kotlin
fun setArrayNumber(array:Array<Number>) {
    var str:String = "数组元素依次排列" 
    for (item in array) {
        str = str + item.toString() + ", "
    }
    tv_function_result.text = str
}
```

Kotlin中Int、Long、Double都继承自Number,但是，如果我们在调用上面的函数时，传入了Array<Int>，这样是会报错的！！这一点与JAVA不一致。因为kotlin中在调用函数时要求参数类名必须完全一致！！

对于上面的函数，如果我们想既可以接收 `Array<Int>`, 也可以接收 `Array<Double>`, 那么就可以定义一个具体化类型参数的内联函数. **只有内联函数才可以具体化类型参数（限定参数类型）** 

函数定义：

```kotlin
//fun前面加了inline表示内联函数, <reified param:Number> 表示param需要是 Number的子类
inline fun<reified param:Number> setArrayNumber2(array:Array<param>) {
    var str:String = "数组元素依次排列"
    for (item in array) {
        str = str + item.toString() + ", "
    }

    println(str)
}
```

函数调用

```kotlin
var int_array:Array<Int> = arrayOf(1, 2, 3)
var float_array:Array<Float> = arrayOf(1.0f, 2.0f, 3.0f)
var double_array:Array<Double> = arrayOf(11.11, 22.22, 33.33)

btn_generic_number.setOnClickListener {
    when (count%3) {
         0 -> setArrayStr<Int>(int_array)
         1 -> setArrayStr<Float>(float_array)
         else -> setArrayStr<Double>(double_array)
    }
    count++ 
}
```

 **注意：** 

内联函数在调用的时候会在调用出把该函数的代码直接复制一份，调用10次就会复制10次，而并非普通函数一样直接引用函数地址。

#### (3)、简化函数

数学上存在求阶乘的函数，比如5的阶乘为：5! =5*4*3*2*1，在kotlin中使用函数表示为：

```kotlin
fun factorial(n:Int):Int {
    if (n <= 1) n
    else n*factorial(n-1)
}
```
由于kotlin中把函数也作为一种特殊的变量，所以允许给函数这个变量进行赋值，上述代码可以改写为:

```kotlin
fun factorial(n:Int):Int = if (n <= 1) n else n * factorial(n-1)
```

#### (4)、尾递归函数

* 在上一节中求阶乘的函数是一个递归函数。
* kotlin中，如果某个函数的末尾又调用了函数自身，这种就称为尾递归函数。
* 尾递归函数需要在 fun 前面添加 tailrec。
* 尾递归函数会使用循环的方式替代递归，从而避免栈溢出。


示例：求余弦不动点

```kotlin
tailrec fun findFixPoint(x: Double = 1.0): Double
            = if (x == Math.cos(x)) x else findFixPoint(Math.cos(x))
```

#### (5)、高阶函数

* 高阶函数就是以另 一个函数作 为参数或者返回值的函数。
* 在Kotiin中， 函数可以用lambda或者函数引用来表示

把A函数作为B函数的输入参数，此时，B函数就被称为高阶函数，A 函数则被称为函数类型的变量。

示例：求数组中的最大值

```kotlin
//greater函数接收两个参数，返回Boolean；
fun <T> maxCustom(array: Array<T>, greater: (T, T) -> Boolean): T? {
    var max: T? = null
    for (item in array)
        if (max == null || greater(item, max))
             max = item
    return max 
}
```

示例：求数组最大值的调用

```kotlin
//I’m 后面有一个空格
var string_array:Array<String> = arrayOf("How", "do", "you", "do", "I'm ", "Fine")
btn_function_higher.setOnClickListener {
    tv_function_result.text = when (count%4) {
        //string_array.max()返回的是you。该函数按首字母排序，最后的为最大值
        0 -> "字符串数组的默认最大值为:${string_array.max()}"
    
        //高阶函数maxCustom也是泛型函数，所以要在函数名称后面加上<String> 
        1 -> ”字符串数组按长度比较的最大值为:${maxCustom<String>(string_array, { a, b -> a.length > b.length })}"
        
        //string_array.max() 对应的高阶函数为 maxCustom(string_array, { a, b -> a > b })
        2 -> "字符串数组的默认最大值(使用高阶函数时)为${maxCustom(string_array, { a, b -> a > b })}"

        //因为系统可以根据string_array判断泛型函数采用了String类型，所以函数名称后面的<String>可以省略
        else -> "字符串数组去掉空格后再按长度比较大小：${maxCustom (string_array, { a, b -> a.trim().length > b.trim().length })}"
    }
    count++ 
}
```

 **补充：**

在上面调用高阶函数时，传入的第二个参数为： { a, b -> a.length > b.length }，这是Lambda表达式的匿名函数写法。 -> 前面的表示函数接收的参数，后面的表示函数体。

第二个参数的完整写法应该为：

```kotlin
fun anonymous(a:String, b:String):Boolean {
    var result:Boolean = a.length > b.length
    return result
}
```

高阶函数示例2：（摘自《Kotlin实战》）

```kotlin
//函数声明——过滤字符串中的内容，如果predicate函数在调用时返回true则追加
fun String . filter (predicate: (Char) -> Boolean): String {
    val sb = StringBuilder ()
    for (index in O until length) {
        val element= get (工ndex) 
        if (predicate(element))
            sb .append(element)
    }        
    return sb. toString ()

//函数调用（具体函数省略，只写调用体），最终输出abc
println(”ab1c" filter { it in ’a’··’z’}) 

```

高阶函数示例3：（摘自《Kotlin实战》）

```kotlin
//声明函数——该函数为 函数类型的参数 transform 赋予了默认值(默认函数体)——it.toString()
fun <T> Collection<T>. joinToString ( 
    separator: String =”,
    prefix: String =””,
    postfix: String =””,
    transform: (T) -> String = { it. toString () } ) : String {
        
    val result = StringBuilder(prefix)
    for ((index, element) in this.withindex()) 
        if (index > 0) 
            result.append(separator) 
            result.append(transform(element))

    result append(postfix) 
    return result. toString ()
}
//函数调用和输出
val letters = listOf ("Alpha”,”Beta”)

//输出：Alpha , Beta
println (letters.joinToString () )

//输出：alpha, beta
println(letters.joinToString{it.toLowerCase()}) 

//输出：ALPHA! BETA! 
println(letters.joinToString(
        separator =“! ”, 
        postfix =”! ”, 
        transform={it.toUpperCase()}
       )
    )
```

高阶函数示例4：callback?.invoke ()——（摘自《Kotlin实战》）

```kotlin
/**
 * 此处定义 函数类型的参数 transform 可空，并且默认为null。
 * 所以，在函数体内调用 transfrom 时需要判断非空：if(null!=transform)...
 * 但是，函数类型是一个包含 invoke 方法的接口的具体实现。
 * 作为一个普通方法, invoke 可以通过安全调用语法被调用 : callback?.invoke ()
 */
fun <T> Collection<T>.joinToString( 
    separator: String =”,”,
    prefix: String =””,
    postfix: String =””,
    transform: ( (T) - > String)? = null): String {
        val result = StrinqBuilder(prefix)
        for((index, element) in this.withlndex()) 
            if (index > 0) 
                result .append(separator) 
                //以安全调用的方式通过invoke调用tranform,这样就不用显示判读非空
                val str = transform?.invoke(element)?: element.toString() 
                result.append(str)
        }
        result.append(postfix) 
        return result.toString ()
}    
```

### 4、增强系统函数

#### (1)、扩展函数

##### A: 基本使用

```kotlin
//要扩展哪个类中的方法，被扩展的类名就是哪个
fun 被扩展的类名.扩展函数名(..参数..){
    函数体
}
```

示例：在Array<Int>中扩展一个元素交换的方法

```kotlin
fun Array<Int>.swap(pos1: Int, pos2: Int) {
    //this表示当前数组本身
    val tmp = this[pos1] 
    this[pos1] = this[pos2]
    this[pos2] = tmp
}
```

#### B: 配合泛型的扩展函数

* 在kotlin中也是使用  **T** 标记泛型

对于上面的扩展方法，只适用于Array<Int> ,如果想适用于 Double、Float等类型就需要配合泛型定义扩展函数：

```kotlin
fun <T> Array<T>.swap(pos1: Int, pos2: Int) {
    val tmp = this[pos1]
    this[pos1] = this[pos2]
    this[pos2] = tmp
}
```


#### (2)、扩展高阶函数

前面在介绍高阶函数时定义了一个求数组元素中最大值的方法，如果我们将该扩束扩展到Array<T> 类中，就可以省略第一个参数，写法如下：

```kotlin
fun <T> Array<T>.maxCustomize(greater: (T, T) -> Boolean): T? {
    var max: T? = null
    //this表示 Array<T>本身
    for (item in this)
        if (max == null || greater(item, max))
            max = item
    return max 
}
```


#### (3)、日期时间函数函数

示例：Java中获取日期的工具类

```kotlin
public class DateUtil {
     //获取当前完整的日期和时间
    public static String getNowDateTime() {
        SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        return sdf.format(new Date());
    }

    //获取完整的事件
    public static String getNowTime() {
        SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss");
        return sdf.format(new Date());
    }

    //获取当前时间——精确到毫秒
    public static String getNowTimeDetail() {
        SimpleDateFormat sdf = new SimpleDateFormat("HH:mm:ss.SSS");
        return sdf.format(new Date());
    }
}

```

关于格式化中的字符介绍：

* yyyy —— 表示4位年份
* MM   —— 两位数字的月份，如01，12
* dd   —— 两位日期数字，如08，15
* HH   —— 24小时制的时，如 19
* hh   —— 12小时制的时，如 下午8点就用8表示
* mm   —— 分钟数
* ss   —— 秒数
* SSS  —— 毫秒数

>CnPeng：时间格式化中的字符默认小写，会产生重复的改用大写。彼此之间的连接符可以自定义

使用kotlin中的函数Date函数：

```kotlin
//返回完整的年月日时分秒2017-10-01 10:00:00
fun Date.getNowDateTime(): String {
    val sdf = SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
    return sdf.format(this)
}

//返回年月日
fun Date.getNowDate(): String {
    val sdf = SimpleDateFormat("yyyy-MM-dd")
    return sdf.format(this)
}

//返回时分秒
fun Date.getNowTime(): String {
    val sdf = SimpleDateFormat("HH:mm:ss")
    return sdf.format(this)
}

//返回时分秒毫秒
fun Date.getNowTimeDetail(): String {
    val sdf = SimpleDateFormat("HH:mm:ss.SSS")
    return sdf.format(this)
}

//根据用户自定义的格式进行返回
fun Date.getFormatTime(format: String=""): String {
    var ft: String = format
    val sdf = if (!ft.isEmpty()) SimpleDateFormat(ft)
    else SimpleDateFormat("yyyyMMddHHmmss")
    return sdf.format(this)
}
```

调用上述扩展函数：

```kotlin
btn_extend_date.setOnClickListener {
    tv_function_result.text = "扩展函数" + when (count++%5) {
         //调用Date扩展函数时，需用对象调用
         0 -> "当前日期时间为：${Date().getNowDateTime()}"
         1 -> "当前日期为：${Date().getNowDate()}"
         2 -> "当前时间为：${Date().getNowTime()}"
         3 -> "当前毫秒时间为：${Date().getNowTimeDetail()}"
         else -> "当前中文日期时间为：${Date().getFormatTime("yyyy年MM月dd日HH时mm分ss秒")}" }
}
```


#### (4)、单例对象
* 使用 object 修饰类，替代 class . 类似于Java中的 static class ,外部可以不需要构建对象就可以直接调用其中的属性和函数

示例：将日期工具类改为单例

```kotlin
object DateUtil {
    //定义日期时间属性，外部调用DateUtil.nowDateTime时会主动调用nowDateTime附属的get()方法
    val nowDateTime: String 
        get() {
            val sdf = SimpleDateFormat("yyyy-MM-dd HH:mm:ss")
            return sdf.format(Date())
        }

    val nowDate: String
        get() {
            val sdf = SimpleDateFormat("yyyy-MM-dd")
            return sdf.format(Date())
    }

    val nowTime: String
        get() {
            val sdf = SimpleDateFormat("HH:mm:ss")
            return sdf.format(Date())
    }

    val nowTimeDetail: String
        get() {
            val sdf = SimpleDateFormat("HH:mm:ss.SSS")
            return sdf.format(Date())
    }

    fun getFormatTime(format: String=""): String {
        val ft: String = format
        val sdf = if (!ft.isEmpty()) SimpleDateFormat(ft)
            else SimpleDateFormat("yyyyMMddHHmmss")
        return sdf.format(Date())
    } 
}
```

外部调用单例DateUtil示例：

```kotlin
btn_object_date.setOnClickListener { 
    tv_function_result.text = "单例工具类" + when (count++%5) {
        0 -> "当前日期时间为：${DateUtil.nowDateTime}"
        1 -> "当前日期为：${DateUtil.nowDate}"
        2 -> "当前时间为：${DateUtil.nowTime}"
        3 -> "当前毫秒时间为：${DateUtil.nowTimeDetail}"
        else -> "当前中文时间为：${DateUtil.getFormatTime("yyyy年MM月dd日HH时mm分ss秒")}" 
    }
}
```

---

## 五、类和对象

### 1、类的构造
#### (1)、类的定义

与Java定义类相比，kotlin有如下修改：

* 省略public 修饰符，默认public
* 继承时使用  **:** 替代 Java中的extends
* 继承时，被继承方需要构造——也就是说继承的是父类对象 

```kotlin
class Animal { 
    //类的初始化函数。CnPeng:init代码块类似于构造，但不完全相同，顶多可以理解为构造的一部分
    init { 
        //Kotlin使用println替代Java中的System.out.println
        println("Animal：这是个动物类") 
    }
}
```

#### (2)、类的构造函数

kotlin把函数看做特殊的变量，那么，类也可以被看做一个特殊的函数。

构造部分可以参考：[Kotlin语言中文站](https://www.kotlincn.net/docs/reference/classes.html)

* Kotlin 中的类可以有一个主构造函数和一个或多个次构造函数。
* 主构造函数是类头的一部分:它跟在类名(和可选的类型参数)后。次构造函数定义在类中。
* 一个类中既可以没有主构造，也可以没有次构造，或者都没有
* 如果一个非抽象类没有声明任何(主或次)构造函数，它会有一个默认的不带参数的主构造函数。

##### A: 主构造函数
主构造函数示例：

```kotlin
 class Person constructor(firstName: String) { }
```

如果主构造函数没有任何注解或者可⻅性修饰符，可以省略 constructor 关键字。 

```kotlin
 class Person2(firstName: String) { }
```

如果主构造函数有注解或可⻅性修饰符，这个 constructor 关键字是必需的，并且这些修饰符在它前面:

```kotlin
class Customer public @Inject constructor(name: String) { ...... }
```

主构造函数中不能包含任何代码，相关初始化代码可以放在init关键字修饰的代码块中：

```kotlin
class Customer(name: String) { 
    init {
        logger.info("Customer initialized with value ${name}") 
    }
}
``` 

在上面的例子中，主构造中的参数可以在init代码块中调用，也可以被类的属性调用：

```kotlin
class Customer(name: String) {
    val customerKey = name.toUpperCase()
}
```

事实上，声明属性以及从主构造函数初始化属性，Kotlin 有简洁的语法:

```kotlin
//与普通属性一样，主构造函数中声明的属性可以是可变的(var)或只读的(val)。
class Person(val firstName: String, val lastName: String, var age: Int) { 
    // ......
}
```

##### B: 次构造

* 次构造定义在类内部，必须用 constructor 修饰 
* 如果某个类既有主构造也有次构造，那么次构造需要委托(继承)主构造
* 如果既有主构造也有次构造，那么调用次构造获取类的实例时，会优先调用主构造的init代码块


仅包含次构造的类：

```kotlin
class Person { 
    constructor(parent: Person) {
        parent.children.add(this) 
    }
}
```

如果类有主构造函数，那么次构造函数需要委托给主构造函数，可以直接委托或者通过别的次构造函数间接委托。委托到同一个类的另一个构造函数时用 this 关键字:

```kotlin
class Person(val name: String) {
    constructor(name: String, parent: Person) : this(name) {
        parent.children.add(this) 
    }
}

```
构造函数的可⻅性是 public。如果你不希望你的类有一个 公有构造函数，可以为主构造配置可见属性：

```kotlin
 class DontCreateMe private constructor () { }
```


#### (3)、带默认参数的构造函数
可以为构造中的参数配置默认值：

默认参数构造的声明：

```kotlin
class AnimalDefault (context: Context, name:String, sex:Int = 0) {
    init {
        var sexName:String = if(sex==0) "公" else "母"
        context.toast("这只${name}的性别是${sexName}") 
    }
}
```

调用：

```kotlin
btn_class_default.setOnClickListener {
     setAnimalInfo()
     when (count%2) {
         0 -> { var animal = AnimalDefault(this, animalName) }
         else -> { var animal = AnimalDefault(this, animalName, animalSex) }
        }
}
```

#### (4)、Java中调用Kotlin带默认参数的构造函数

如果Kotlin中定义了一个带有默认参数的函数(包括构造函数和其他函数)，该函数需要被Java代码调用，如果希望让该默认参数在Java中也生效，就需要 **展示 constructor ,并在其前面增加 @JvmOverloads 注解** 

比如，上面的 AnimalDefault 类中定义了一个带有默认sex=0的参数，如果不添加注解，那么Java中调用时必须完整传入三个参数，否则报错。添加注解的示例如下：


示例：

```kotlin
class AnimalDefault @JvmOverloads constructor(context: Context, name:String, sex:Int = 0) {
     init {
        var sexName:String = if(sex==0) "公" else "母" 
        context.toast("这只${name}的性别是${sexName}")
     } 
}
```

java中调用示例：

```kotlin
@Override
public void onClick(View v) {
    if (v.getId() == R.id.btn_class_seperate) {
        setAnimalInfo();
        if (count%2 == 0) { 
            AnimalDefault animal = new AnimalDefault(this, animalName);
        else
            AnimalDefault animal = new AnimalDefault(this, animalName, animalSex);
        }
    } 
}
```

### 2、类的成员

#### (1)、成员属性

* 非空的成员属性必须在声明时赋值，或者在构造中赋值

如果属性名和构造函数的参数名一致，可以按如下方式声明：

```kotlin
//该构造中的name、sex既是构造函数参数，也是该类的属性——前面加了可变属性var、val
class WildAnimal (var name:String, val sex:Int = 0) { }
```

如果属性名和构造函数的参数名不一致，可以按如下方式声明：

```kotlin
//该类有三个属性：name,sex,sexName。
class WildAnimalMember (var name:String, val sex:Int = 0) {
    var sexName:String
    init {
        sexName = if(sex==0) "公" else "母"
    } 
}
```


#### (2)、成员方法

就是在类内部定义一个函数，详细可以参考前面函数声明的部分。

#### (3)、伴生对象

* 伴生对象类似Java中的静态内部类。
* 伴生对象需要在类名前面添加 companion object

```kotlin
class MyClass {
    companion object Factory {
        fun create(): MyClass = MyClass() 
    }
}
```

调用伴生对象中的成员时，可以这么写：

```kotlin
MyClass.Factory.create()
```
但上面的调用方式，会提示：`Redundant Companion reference`,推荐的方式为：

```kotlin
MyClass.create()
```

伴生对象可以省略名称，此时，默认名称为  **Companion** ,外部调用的方式不变

```kotlin
class MyClass {
    companion object {
        fun create(): MyClass = MyClass() 
    }
}
```

#### (4)、静态属性

* 伴生对象中的属性就称为静态属性。

声明方式省略，调用方式参考上面伴生对象方法的调用


### 3、类的继承

#### (1)、开放性修饰符

##### A: 可继承关键字

* Kotlin中 open 关键字修饰类时表示类可以被继承，修饰成员(含变量和方法)时表示可以被重写
* Kotlin中的 **类默认是不能被继承的** 

```kotlin
//open修饰的类才可以被继承
open class Base { 
    //open修饰的方法才可以被重写
    open fun v() {}
    fun nv() {} 
}


class Derived() : Base() { 
    //被重写fun函数依旧是open的，依旧可以被Derived的子类重写
    override fun v() {}
}

//标记为 override 的成员本身是开放的，也就是说，它可以在子类中覆盖。如果你想禁止再次覆盖，使用 final 关键字
open class AnotherDerived() : Base() { 
    final override fun v() {}
}
```

##### B: 公开性修饰符

Kotlin中用来修饰成员的公开性修饰符有：

* public   —— 类、函数、变量默认就是public的
* internal —— 只对本模块内部开放。对于APP开发来说，本模块指本APP
* procted  —— 对自己和子类开放
* private  —— 私有

 **注意：** 
 
* **Kotlin中外部类不能访问内部类的private成员** 
* open 和公开性修饰符是平级关系
* open和private不能同时修饰一个成员，否则报错：Modifier 'open' is incompatible with 'private'


#### (2)、普通类继承

* 对比Java，继承时使用  **:** 替代 extends, 并且冒号后面跟的是 父类的构造 
* 默认情况下，重写的成员具有与父类中对应成员的相同可见属性。
* 重写的成员可以将可见属性升级，但不能降级


```kotlin
//open修饰的类才可以被继承
open class Base { 
    //open修饰的方法才可以被重写
    procted open fun v() {}
    fun nv() {} 
}


class Derived() : Base() { 
    //在子类中，可见属性可以升级为public,但不能降级为 private。如果不想被再次重写就定义为final
    public override fun v() {}
}
```


#### (3)、抽象类

* 抽象类定义的是事物的共性，接口定义的则是事物的特性
* 抽象类中可以包含非抽象的方法

关于抽象类，可以参考 [传智Kotlin基础--张泽华](https://gitee.com/CnPeng_1/LearningNotes/wikis/2%E3%80%81%E4%BC%A0%E6%99%BAKotlin%E5%9F%BA%E7%A1%80--%E5%BC%A0%E6%B3%BD%E5%8D%8E?sort_id=364269) 中的内容



#### (4)、接口

* 抽象类定义的是事物的共性，接口定义的则是事物的特性
* 由于接口没有构造，所以某个类实现接口时不需要在接口名后面添加“()”——括号表示构造
* 接口中可以定义抽象的成员变量，由实现类初始化
* kotlin中，继承和实现都是用冒号 **:** 表示
* 同Java，类可以单继承多实现 

关于接口类，可以参考 [传智Kotlin基础--张泽华](https://gitee.com/CnPeng_1/LearningNotes/wikis/2%E3%80%81%E4%BC%A0%E6%99%BAKotlin%E5%9F%BA%E7%A1%80--%E5%BC%A0%E6%B3%BD%E5%8D%8E?sort_id=364269) 中的内容

```kotlin
//可以被继承的类
open class Human(var name: String) {
    open fun printName() {
        println(name)
    }
}

//抽象类定义了共性
abstract class HumanCommonFeature(name: String, sex: String) : Human(name) {
    override fun printName() {
        println("每个人都有姓名，这个人的姓名为：$name")
    }

    //抽象方法默认是open的
    abstract fun printSex()

    fun printXXX() {
        println("抽象类中可以拥有非抽象方法")
    }
}

//接口定义特性 / 接口没有构造函数，
interface ManFrature {

    //接口内部的所有方法都是open的；接口中没有方法体的函数默认是abstract的
    fun beard()

    //接口内部可以包含非抽象的方法——有方法体的方法
    fun dick() {
        println("只有男人才有Dick")
    }

    //接口中允许声明属性，并且也是open，并且不能初始化——也就是抽象的，只能由实现类初始化
    var age: Int

    //这样写会报错
    //var height:Int=175
}

class Man(var manName: String) : HumanCommonFeature(manName, "男"), ManFrature {

    override fun printSex() {
        println("${name}性别为男")
    }

    override fun beard() {
        println("男人才有胡子")
    }

    override var age: Int = 10

}

fun main(args:Array<String>) {
    val man = Man("张三")
    println(man.printName())
    println(man.printSex())
    println(man.printXXX())
    println(man.beard())
    println("${man.manName}的姓名是${man.name},年龄是${man.age}")
}
```

#### (5)、接口代理

* 把自己不想做的事情交给别人做称为委托，做别人委托的事件称为代理
* 只有接口才能被代理
* 代理关键字 by

以下代理示例内容摘自： [传智Kotlin基础--张泽华](https://gitee.com/CnPeng_1/LearningNotes/wikis/2%E3%80%81%E4%BC%A0%E6%99%BAKotlin%E5%9F%BA%E7%A1%80--%E5%BC%A0%E6%B3%BD%E5%8D%8E?sort_id=364269)

##### A、场景说明

```
围裙妈妈只负责做饭，不负责洗碗
小头爸爸洗一次碗可以赚到10元
大头儿子洗一次碗可以赚到1元
小头爸爸承揽了洗碗的活，最终交给大头儿子做，中间赚了9元差价
```

##### B、代码实现——完全委托

* 定义洗碗的接口

```kotlin
interface IWashBow {    
    //定义一个洗碗接口，包含一个洗碗方法
    fun washBow()
}
```

* 大头儿子实现接口

```kotlin
//被实现的接口后面不需要加（）
class BigHeadSon:IWashBow {    
    override fun washBow() {
        println("我是大头儿子，每次洗碗赚1元钱")
    }
}
```

* 小头爸爸实现接口并委托事件给小头儿子

```kotlin
class SmallHeadFather:IWashBow by BigHeadSon(){     
    //委托关键字 by;被委托方(即代理方)如果不是单例类，则后面需要跟()
}

//也可以使用下面这种方式
class SmallHeadFather2(bigHeadSon:BigHeadSon):IWashBow by bigHeadSon{     
    //委托关键字 by;被委托方(即代理方)如果不是单例类，则后面需要跟()
}
```

* 程序调用及输出结果

```kotlin
fun main(args: Array<String>) {
    var father=SmallHeadFather()
    //小头爸爸已经将洗碗的操作委托为小头儿子了，所以，此处本质是调用的小头儿子的洗碗操作
    father.washBow()    
}
```

![](https://gitee.com/uploads/images/2018/0510/104919_b477339e_930142.png "屏幕截图.png")


### 4、特殊类

#### (1)、嵌套类
* A类中包含B类，则B类就是A的嵌套类
* 嵌套类不能直接访问外部类的成员
* 外部类访问嵌套类时，需要先构造嵌套类

示例代码在内部类中。


#### (2)、内部类

* A类中包含B类，并且B类用  **inner** 修饰， 则B类就是A的嵌套类
* 内部类可以直接访问外部类的成员
* 外部类访问内部类时，需要先构造内部类


嵌套类+内部类的示例代码：

```kotlin
package MyTempDemo

class UserA() {
    var nameA: String = "张三"

    //没有加inner前缀，表示普通嵌套类
    class UserB(var nameB: String) {
        var ageB = 15
        fun printName() {
            println("UserB的姓名为：$nameB")

            //嵌套类不能直接访问外部类的成员，此处会报错：unresolved reference
            //println("UserA的姓名为: $nameA ")

            //嵌套类和普通类一样，只能通过实例访问外部类的内容
            println("UserA的姓名为: ${UserA().nameA} ")
        }
    }

    //inner标记的为内部类
    inner class UserC(var nameC: String) {
        var ageC = 10
        fun printName() {
            println("UserC的姓名为：$nameC")

            //内部类可以直接调用外部类的成员
            println("UserA的姓名为：$nameA")
        }
    }

    fun printName() {
        //外部类访问嵌套类、内部类成员时必须通过实例
        println("UserA中获取UserB的年龄${UserB("李四").ageB}")
        println("UserA中获取UserC的姓名${UserC("王五").ageC}")
    }
}

fun main(args:Array<String>){
    //调用嵌套类中的方法
    println("调用嵌套类中的方法：外部类不需要构造，只需要构造嵌套类——从此处看，UserB类似于UserA的静态内部类")
    UserA.UserB("李四").printName()

    println("\n调用内部类的方法:必须同时构造外部类和内部类")
    UserA().UserC("王五").printName()
}
```

#### (3)、枚举类

* Kotlin中，枚举是一个类，关键字为 enum
* 枚举类中的成员默认定义了两个属性：ordinal——索引；name——名字
* 枚举类可以有构造，但该构造只对成员有效，不对外开放——外部不能调用其构造
* 枚举类不能被继承，也不能被实现。
* 更多内容可以参考 [官方文档](https://www.kotlincn.net/docs/reference/enum-classes.html)

```kotlin
package MyTempDemo

enum class Season {
    Spring,
    Summer,
    Autumn,
    Winter
}

enum class Color(var rgb: Int) {
    RED(0XFF0000),
    GREEN(0X00FF00),
    BLUE(0X0000FF)
}


fun main(args: Array<String>) {

    for (i in 0..Season.values().size) {
        when (i) {
        //枚举类中的成员默认具有 ordinal(索引)、name(名称) 两个属性
            Season.Spring.ordinal -> println(Season.Spring.name)
            Season.Summer.ordinal -> println(Season.Summer.name)
            Season.Autumn.ordinal -> println(Season.Autumn.name)
            Season.Winter.ordinal -> println(Season.Winter.name)
        }
    }

    for (i in 0..Color.values().size) {
        when (i) {
            Color.RED.ordinal ->
                println("${Color.RED}的索引为${Color.RED.ordinal},name为${Color.RED.name},rgb值为${Color.RED.rgb}")
            Color.GREEN.ordinal ->
                println("${Color.GREEN}的索引为${Color.GREEN.ordinal},name为${Color.GREEN.name},rgb值为${Color.GREEN.rgb}")
            Color.BLUE.ordinal ->
                println("${Color.BLUE}的索引为${Color.BLUE.ordinal},name为${Color.BLUE.name},rgb值为${Color.BLUE.rgb}")
        }
    }
    //枚举类不能被实例化,其构造只为内部成员使用
    //println("${Color(0x000000)}")
}
```

运行结果：

```
Spring
Summer
Autumn
Winter

RED的索引为0,name为RED,rgb值为16711680
GREEN的索引为1,name为GREEN,rgb值为65280
BLUE的索引为2,name为BLUE,rgb值为255
```


#### (4)、密封类/印章类
以下内容摘自 [Kotlin官方文档](https://kotlinlang.org/docs/reference/sealed-classes.html)

如果某个类的子类有限，则可以将该类定义为密封类。

CnPeng: 比如，按性别区分人的时候，只有两种可能：男人、女人。所以，人就可以被定义为印章类。

看起来密封类和枚举差不多，都是用来表示有限的内容。 **但枚举中的成员都是单例的，密封类的子类非单例。** 

* 声明密封类时，在 class 前面添加 sealed 关键字即可。
* 密封类默认是open可继承的
* 密封类的子类，在Kotlin 1.1 之前，必须声明在密封类内部（也就是作为密封类的嵌套类）；但是之后的版本中，只要确保他们在同一个kt文件中即可。
* 密封类本身是抽象的，不能直接被实例化，可以拥有抽象成员。
* 密封类的所有构造都必须是私有的。
* 密封类的孙子类，可以放置在任意位置，并非必须在同一个kt文件中。

```kotlin
sealed class Expr
data class Const(val number: Double) : Expr()
data class Sum(val e1: Expr, val e2: Expr) : Expr()
object NotANumber : Expr()
```

使用密封类的好处在于：

* 当使用when表达式判断分支并 **使用when返回值的时候，when表达式能够判断是否已经完全涵盖了密封类的全部子类分支** ，这样就不用再写else 分支了。少写了分支会直接爆红。

* 如果不使用when的返回值——也就是只是调用when语句，那么，上述作用无效。

* 枚举+when时，when语句无法获知是否已经覆盖了枚举内的全部情况。

```kotlin
fun eval(expr: Expr): Double = when(expr) {
    is Const -> expr.number
    is Sum -> eval(expr.e1) + eval(expr.e2)
    NotANumber -> Double.NaN
    // the `else` clause is not required because we've covered all the cases
}
```

CnPeng的示例代码：

```kotlin
package kotlininaction

//密封类可以被继承，但不能实例化，其子类可以实例化。密封类默认open
sealed class Season {
    //kotlin1.1之前子类必须放在密封类内部，1.1之后只要在同一个文件中即可
    open class Spring(var name: String) : Season()

    class Summer(var name: String) : Season()
    class Autumn(var name: String) : Season()
    class Winter(var name: String) : Season()
}

//密封类的孙子类可以放置在任意位置，可以不用放置在同一个文件中
class SpringSub : Season.Spring("春天")

/**
 * 这里将when表达式的返回值作为 getSeasonName的返回值，此时，when能自动判断出分支是否完全覆盖了密封类Season中的子类；
 * 缺少分支时会爆红，并提示补足分支——要么补足类型，要么补上else
 */
fun getSeansonName(season: Season): String = when (season) {
    is Season.Spring -> "春天"
    is Season.Summer -> "夏天"
    is Season.Autumn -> "秋天"
    is Season.Winter -> "冬天"
}

/**
 * 该函数中，when只作为一个普通的表达式，返回值并没有被引用，所以，不能自动判断是否涵盖了全部情况
 */
fun printSeasonName(season: Season) {
    when (season) {
        is Season.Spring -> "春天"
    }
}

fun main(args: Array<String>) {
    //输出“春天”
    println(getSeansonName(Season.Spring("Spring")))
    //输出“春天”
    println(getSeansonName(SpringSub()))

    //输出 kotlin.unit
    println(printSeasonName(Season.Summer("夏天")))
}
```

#### (5)、数据类
所谓数据类就是编码过程中的各种实体类, 数据类关键字 data

Kotlin中数据类的优点包括：

* 自动声明与构造函数入参同名的属性字段
* 自动实现每个属性的set/get方法
* 自动提供equals 方法，用来判断两个数据对象(的属性值)是否相等
* 自动提供copy方法，允许完整复制某个数据对象，也可以在复制之后单独修改部分字段的值
* 自动提供 toString 方法
* 自动提供hashCode 方法判断根据属性值生成的哈希值是否一致

 **注意：** 没有在主构造中声明的属性，不会加入到 equals 、hashCode的判断中去 

声明数据类：

```kotlin
/**
 * 数据类必须有主构造函数，并且至少传入一个参数
 * 并且要声明与入参同名的属性——简单一点就直接给入参加上var/val
 * 数据类不能是子类，不能是抽象类，不能是内部类，也不能是印章类——数据类就是一个单独的类 
 */
data class Plant(var name:String, var stem:String, 
    var leaf:String, var flower:String, 
    var fruit:String, var seed:String) {
    ...
}
```

调用数据类：

```kotlin
var lotus = Plant("莲", "莲藕", "莲叶", "莲花", "莲蓬", "莲子") 
    //数据类的copy方法不带参数时表示复制一个属性值一致的对象
    var lotus2 = lotus.copy()

    btn_class_data.setOnClickListener {
        lotus2 = when (count++%2) { 
            //copy方法带参数，表示为对应字段重新赋值
            0 -> lotus.copy(flower="荷花") 
            else -> lotus.copy(flower="莲花")
        } 

    //数据类自带equals方法，用来判断两个对象是否一致
    var result = if (lotus2.equals(lotus)) "相等" else "不等" 
    tv_class_secret.text = "两个植物的比较结果是：${result}\n" +
         "第一个植物的描述是：${lotus.toString()}\n" +
         "第二个植物的描述时：${lotus2.toString()}"
}
```

#### (6)、模板类/泛型类

更多内容可参考 [官方文档中的Generics](https://kotlinlang.org/docs/reference/generics.html)

下列内容摘自《Kotlin实战》
* Kotlin通过在类名称后加上一对尖括号，井把类型参数放在尖括号内来声明泛型类及泛型接口。
* 一旦声明之后，就可以在类的主体内像其他类型一样使用类型参数

List泛型接口的声明：

```kotlin
//List接口定义了类型参数T
interface List<T> {
    //在接口或类的内部， T 可以当作普通类型使用
    operator fun get (index: Int): T 
    //...
}
```

* 如果你的类继承了泛型类(或者实现了泛型接口)，你就得为基础类型的泛型形参提供一个类型实参——它可以是具体类型或者另一个类型形参 

```kotlin
class StringList: List<String> { 
    //这个类实现了 List，提供了具体类型实参: String
    override fun get (index: Int) : String = . . 
}

//现在 Arraylist 的泛型类型形参T就是List的类型实参
class ArrayList<T> : List<T> {
    override fun get (index: Int) : T = ... 
}
```

---

## 六、Kotlin使用简单控件

### 1、使用按钮控件
#### (1)、Button
实现点击事件的三种形式：

```kotlin
//匿名函数
btn_click_anonymos.setOnClickListener { v ->
    //as 为类型强转标记
    toast("点击了控件${(v as Button).text}")
}
```
其他两种形式：定义内部类、让当前activity实现OnclickListener 的代码省略

* 关于上面示例代码中匿名对象为什么这么写，可以参考[《KotlinForAndroid》读书笔记中的——简化setOnCliclListener](https://gitee.com/CnPeng_1/LearningNotes/wikis/1%E3%80%81KotlinForAndroid%E8%AF%BB%E4%B9%A6%E7%AC%94%E8%AE%B0?sort_id=365441)

#### (2)、CheckBox

略

#### (3)、RadioButton
略

### 2、使用页面布局

#### (1)、LinearLayout

* Anko中为Context扩展了dp和px互转的工具：dip(x) ——转换dp为px, px2dp(x)——转换px为dp

#### (2)、RelativeLayout
* 相对布局中，可以通过 RelativeLayout.LayoutParams 的 `addRule(int verb, int subject)` 方法手动在代码中指定视图的布局规则。

addRule 中前者表示布局规则，后者表示相对目标View的id

* Anko中为了简化addRule增加了 RelativeLayoutLayoutParamsHelpers.kt ,该文件中增加了部分封装好的布局规则，只需要传入视图id即可。如 rlLayoutParams.above(viewID)
 
#### (3)、ConstraintLayout

* ConstraintLayout 的LayoutParams 可以控制视图位置，ConstraintSet也可以控制视图位置
* 在为LayoutParams配置margin时，尽量使用marginStart、marginEnd,如果使用 leftMargin、rightnMargin,可能会出现不生效的情况

##### A: ConstraintSet 

是代码控制view之间约束条件的辅助类，ConstraintLayout.LayoutParams的高级版本。
* connect() 可以一次性指定存在约束关系的两个控件以及他们的间距
* setMargin() 可以指定单一方向上的margin值

使用constraintSet控制视图位置：

```kotlin
private fun moveView() {
    val margin = dip((if (isMoved) 200 else 20).toFloat())
    //低版本的constraint-layout库中不包含ConstraintSet
    val set = ConstraintSet()

    //复制当前控件父View的约束关系
    set.clone(cl_content)

    //清空控件的约束关系
    //set.clear(tv_first.getId()); 
    
    //设置控件的约束宽度
    //set.constrainWidth(tv_first.getId(), ConstraintLayout.LayoutParam s. WRAP_CONTENT);
    
    //设置控件的约束高度
    //set.constrainHeight(tv_first.getId(),ConstraintLayout.LayoutParam
s. WRAP_CONTENT); 

    //设置顶部约束关系以及间距
    //set.connect(tv_first.getId(), ConstraintSet.TOP, cl_content.getId (), ConstraintSet.BOTTOM, margin);

    //设置底部约束关系以及间距
    //set.connect(tv_first.getId(), ConstraintSet.BOTTOM, cl_content.ge
tId(), ConstraintSet.BOTTOM, margin); 

    //设置控件左侧的约束关系及间距
set.connect(tv_first.id, ConstraintSet.START, cl_content.id, Constr aintSet.START, margin)

    //右侧约束关系及间距
    //set.connect(tv_first.getId(), ConstraintSet.END, cl_content.getId
(), ConstraintSet.END, margin); 

    //LEFT和RIGHT的margin不生效，需要使用START和END
    //set.setMargin(tv_init.getId(), ConstraintSet.START, 200); 
    
    //启用新的约束关系
    set.applyTo(cl_content)

    isMoved = !isMoved
}
```

##### B: TransitionManager
当布局参数发生变化时提供重新布局的切换动画。

```kotlin
btn_move_soft.setOnClickListener { 
    //当视图的布局规则发生变化时，启用默认切换动画，TransitionManager是API19加入的类
    TransitionManager.beginDelayedTransition(cl_content)
    moveView() 
}
```

### 3、使用图文控件

#### (1)、TextView
* Kotlin中使用 or 替代Java中的 | ,如： 

```kotlin
//Kotlin写法
tv_adMob.gravity=Gravity.START or Gravity.CENTER

//java中的写法
tv_adMob.gravity=Gravity.START | Gravity.CENTER
```

kotlin中都有对应的关键字替代Java中的位运算，对应关系如下：

* or —— |  ——或运算
* and —— & —— 与运算
* xor —— ^ —— 异或运算
* shl —— << —— 按位左移
* shr —— >> —— 按位右移
* ushr —— >>> —— 无符号右移，高位补0


#### (2)、ImageView
#### (3)、EditText
intputType的类型：

- `TYPE_CLASS_TEXT` —— 普通文本
- `TYPE_TEXT_VARIATION_NORMAL` —— 正常显示
- `TYPE_TEXT_VARIATION_PASSWORD` —— 密文显示
- `TYPE_TEXT_VARIATION_VISIBLE_PASSWORD` —— 明文显示

更多类型可查看 InputType 类中的定义


### 4、使用Activity跳转
#### (1)、传送配对字段数据

* Kotlin中跳转Activity的普通写法：

```kotlin
//注意字节码的获取。传递数据时依旧使用putExtra的方式
val intent = Intent(this@MainActivity, LinearLayoutActivity::class.java )
startActivity(intent)
```

* Anko库中提供的Activity跳转方法

```kotlin
//<> 中包裹要跳转的目标页面，() 中传递的是键值对Pair组成的可变数组 vararg 
startActivity<ChipActivity>()
```
* 目标页面获取被传递数据的方式不变

#### (2)、传送序列化数据

Kotlin中如果需要序列化，则为数据类添加一个@Parcelize注解，并实现该接口即可。比Java中的序列化简单了太多太多。。。

```kotlin
//@Parcelize注解表示自动实现Parcelable接口的相关方法
@Parcelize
data class MessageInfo(val content: String, val send_time: String) : Parcelable {
   ...
}
```

此外，为了让该注解生效，必须修改Module 的 build.gradle文件 —— 在 android 节点增加 androidExtensions：

```kotlin
android {
    ...
    androidExtensions {
        experimental = true
    }
}
```
传递和接收的示例如下：
* 跳转时传递序列化数据


```kotlin
val request = MessageInfo(et_request.text.toString(), DateUtil.nowTime)
startActivity<ParcelableSecondActivity>("message" to request)
```
* 接收序列化数据


```kotlin
class ParcelableSecondActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) { 
        super.onCreate(savedInstanceState) 
        setContentView(R.layout.activity_parcelable_second) 

        //获取传入的序列化对象数据
        val request = intent.extras.getParcelable<MessageInfo> ("message")
        tv_response.text = "收到的数据\n时间为：${request.send_time}\n内容为：${request.content}" 
    }
}
```
#### (3)、跳转时指定启动模式

##### A: Anko中的 intentFor()
从其他页面跳转到Activity界面时，可能还会通过Intent对象设置Action、Flag等内容，Anko提供了构建Intent对象的 intentFor()函数。

```kotlin
//构建intent对象的时候，指定跳转的Activity并携带数据
val intent = intentFor<ActSecondActivity>(
           "request_time" to DateUtil.nowTime,
           "request_content" to et_request.text.toString())

//通过intent对象指定目标Activtiy的启动模式
intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
startActivity(intent)
```

##### B：清单文件中配置Activity启动模式

>1、此处内容摘自：《黑马程序员:阳哥面试宝典V3.0》中的启动模式部分。
>2、在Activity界面中调用 this.toString() 可以获取Activity的唯一标识，通过判断标识是否一致得知是否创建了新的实例。


```kotlin
<activity android:name=".ActSecondActivity" android:launchMode= "standard" />
```

从清单文件中配置的Activity启动模式有：
*  **standard**  —— 标准模式。重新创建页面实例并放入到栈顶。默认为该模式

下图中，FirstAct的启动模式为standard，点击FirstAct中的按钮再次打开FirstAct时会构建新的实例
![](https://images.gitee.com/uploads/images/2018/1228/092941_4f846a6e_930142.png "屏幕截图.png")

*  **singleTop**  —— 单顶模式。判断是否在栈顶，栈顶则重用；不再栈顶则重新构建实例并放到栈顶

下图中，FirstAct启动模式为singleTop，SecondAct为标准模式。FirstAct打开SecondAct,SecondAct再打开FirstAct时，由于First不在栈顶，所以会重新构建一个FirstAct
![](https://images.gitee.com/uploads/images/2018/1228/093047_00ced947_930142.png "屏幕截图.png")

*  **singleTask**  —— 单一任务(栈内单一)。栈中不存在，新建；存在则判断是否在栈顶，在栈顶则重用，不在栈顶则清除其上的全部现有实例。

下图中，FirstAct的启动模式为singleTask，SecondAct为标准模式。FirstAct启动SecondAct,再从SeconAct打开FirstAct时，由于FirstAct不在栈顶，所以，会移除SecondAct.
![](https://images.gitee.com/uploads/images/2018/1228/093226_d17cbcd2_930142.png "屏幕截图.png")

*  **singleInstance**  —— 单栈单实例。目标Activtiy会被放到单独的栈中，


修改 FirstActivity 的启动模式为 standard，SecondActivity 的启动模式为 singleInstance，由于涉及到了多个栈结构，我们需要在每个 Activity 中通过 `this.getTaskId()` 获取并显示当前栈结构的 id。

![](https://images.gitee.com/uploads/images/2018/1228/100417_dcc39cda_930142.png "屏幕截图.png") 
我们看到从 FirstActivity 跳转到 SecondActivity 时，重新启用了一个新的栈结构，来放置 SecondActivity 实例， 然后按下后退键，再次回到原始栈结构;

图中下半部分显示的在 SecondActivity 中再次跳转到 FirstActivity，这个时 候系统会在原始栈结构中生成一个 FirstActivity 实例，然后回退两次，注意，并没有退出，而是回到了 SecondActivity， 为什么呢?是因为从 SecondActivity 跳转到 FirstActivity 的时候，我们的起点变成了 SecondActivity 实例所在的栈 结构，这样一来，我们需要“回归”到这个栈结构。

如果我们修改 FirstActivity 的 launchMode 值为 singleTop、singleTask、singleInstance 中的任意一个，流程将会 如图所示:

![](https://images.gitee.com/uploads/images/2018/1228/100604_fc273f64_930142.png "屏幕截图.png")


##### C：代码中配置Activity启动模式——setFlags(Flag)

Flag的部分取值为：

*  **`Intent.FLAG_ACTIVITY_NEW_TASK`** 

等价于 launchMode="standard"


*  **`Intent.FLAG_ACTIVITY_SINGLE_TOP`** 

等价于 launchMode="singleTop"


*  **`Intent.FLAG_ACTIVITY_CLEAR_TOP`** 

栈内存在实例时，如果在栈顶，直接复用； **不在栈顶则清除原实例上方的全部，然后销毁该实例，接着再创建一个新的实例** 。

复用逻辑与 launchMode="singleTask" 类似。但 singleTask 使用 onNewIntent 启用原实例；CLEAR_TOP 会先销毁原有实例然后重新创建。


*  **`Intent.FLAG_ACTIVITY_NO_HISTORY`** 

每次打开会新建实例，使用完毕(跳到其他页面)就会销毁，栈内不保存实例。——类似于阅后即焚的逻辑。

*  **`Intent.FLAG_ACTIVITY_CLEAR_TASK`** 

会清空栈中现有的全部实例，然后新建该实例。需要配合 FLAG_ACTIVITY_NEW_TASK一起使用。示例如下：

```kotlin
//如果不加后面的 or Intent.FLAG_ACTIVITY_NEW_TASK, 则 CELAR_TASK不生效
intent.flags = Intent.FLAG_ACTIVITY_CLEAR_TASK or Intent.FLAG_ACTIVITY_NEW_TASK
```

##### D: 代码配置Flag的便捷方式
Anko中为我们定义了代码设置Flag的更加便捷的方式——预置配置Flag的方法。

这样，我们就不用再调用 intent.flag=xxx ,而直接在startActivity()函数中传参即可。

示例如下：

```kotlin
//使用Anko中提供的跳转函数。跳转时携带数据，并制定目标页面的启动模式
val ankoIntent = intentFor<MyDemoActivity>(Pair("KeyName", "keyValue"))
startActivity(ankoIntent.newTask())
```
上述代码中，ankoIntent.newTask() 本质是 Anko 为 Intent 扩展的函数，内部帮我们配置了FLAG，其具体实现为：

```kotlin
inline fun Intent.newTask(): Intent = apply { 
    addFlags(Intent.FLAG_ACTIVITY_NEW_TASK) 
}
```

Flag和Anko中扩展函数的对应关系为：

* `Intent.FLAG_ACTIVITY_NEW_TASK` —— newTask(）
* `Intent.FLAG_ACTIVITY_SINGLE_TOP` —— singleTop()
* `Intent.FLAG_ACTIVITY_CLEAR_TOP` —— clearTop()
* `Intent.FLAG_ACTIVITY_NO_HISTORY` —— noHistory()
* `Intent.FLAG_ACTIVITY_CLEAR_TASK` —— clearTask()

这些函数都定义在Anko库的 Intents.kt 文件中。


#### (4)、处理返回数据——startActivityForResult

Anko中也扩展了startActivityResult方法，示例如下：

```kotlin
val info = MessageInfo(et_request.text.toString(), DateUtil.nowTime) 
//打开ActResponseActivity时携带请求码0，并携带数据。ActResponseActivity关闭时需返回数据
startActivityForResult<ActResponseActivity>(0, "message" to info)
```

返回数据时的 setResult() 函数和JAVA中的一致。


### 5、实战：电商APP登录界面

#### (1)、展示AlertDialog

Anko库中为Context封装了一个alert函数，通过该函数可以极大的简化弹窗构建流程。格式为

`alert(消息内容, 消息标题){ ...按钮以及事件... }”`

示例：

```kotlin
alert("你确定要卸载么？", "亲爱的用户") {
    positiveButton("残忍卸载") { ... }
    negativeButton("我再想想") { ...} 
}.show()
```
---

## 七、Kotlin操作复杂控件

### 1、使用视图排列(列表类控件)

![类表类控件继承关系图1](https://images.gitee.com/uploads/images/2018/1228/144015_56b1c144_930142.png "屏幕截图.png")

#### (1)、Spinner


##### A: Spinner的基本使用：

>以下内容摘自之前总结的《Android读书笔记》.doc

Spinner 英文原意是下拉列表，表示一个可供用户选择的列表选择框。

Spinner和Gallery 都继承自AbsSpinner, AbsSpinner又继承自AdapterView，所以只要为Spinner 提供一个adapter即可显示列表内容。

*  **Spinner属性**
 
属性|含义
---|---
android:entries| Reference to an array resource that will populate the Spinner. 
android:dropDownHorizontalOffset|Amount of pixels by which the drop down should be offset horizontally. 
android:dropDownSelector|List selector to use for spinnerMode="dropdown" display. 
android:dropDownVerticalOffset|Amount of pixels by which the drop down should be offset vertically. 
android:dropDownWidth|Width of the dropdown in spinnerMode="dropdown". 
android:gravity|Gravity setting for positioning the currently selected item. 
android:popupBackground|Background drawable to use for the dropdown in spinnerMode="dropdown". 
android:prompt|当用dialog模式展示spinner的时候的提示标题，取值字符串，但是必须是引用类型的字符串，不能硬编码。对应方法：spinnser.setPrompt(charsquence).
android:spinnerMode|Spinner的展示模式，有两种取值，dialog，dropdown。默认是dropdown模式 


 **注意** 
 
* android:entries 属性并不是Spinner 定义的，而是在AbsSpinner中定义的，因此同样继承自AbsSpinner 的Gallery也支持该属性

* dropDownHorizontalOffset / dropDownVerticalOffset 两个属性必须手动的写完，AS不会自动补全


<br>

*  **SpinnerAdapter** 

展示Spinner的使用使用SpinnerAdapter。
SpinnerAdapter的继承关系可以参考下图:

![](https://images.gitee.com/uploads/images/2018/1228/145642_372f60b6_930142.png "屏幕截图.png")

这里单独说ArrayAdapter , 因为他里面有一个  setDropDownViewResource( resID ) , 参数是布局文件的id。

这个方法的作用是指定下拉列表中条目的布局。只针对ArrayAdapter 有效。

之所以使用这个方法，是因为， **默认情况下，下拉列表的条目布局与spinner展示的最终数据的条目布局一致** ，为了区分，就需要使用上面的这个setDropDownViewResource(resId )方法。

<br>

*  **setOnItemSelectedListener** 

点击并选中Spinner中下拉列表的条目时可以设置该方法

<br>

*  **Gallery**
 
Gallery与Spinner 组件有共同的父类：AbsSpinner ，也就是说Gallery和Spinner都是一个列表选择框。

二者的区别在于，Spinner显示的是一个垂直的列表选择框，而Gallery展示的是一个水平的列表选择框。
Gallery与Spinner还有一个区别：Spinner的作用是供用户选择，而Gallery则允许用户通过拖动来查看上一个下一个列表项。

android 目前已经不再推荐使用Gallery，而是推荐用HorizontalScrollView 和ViewPager 等可以水平滚动的组件来替代。


<br>

* **示例：让用户选择的下拉列表** 

如果下拉列表项中数据是固定的，直接使用 android:entries 属性指定即可。

如果需要在运行时动态的决定下拉列表的内容，或者需要对Spinner 的列表项进行定制，则可以使用Adapter为Spinner提供列表项。

```kotlin
public class SpinnerActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_spinner);

        //初始化数据，该数组是定义在res目录下的values目录中的arrays.xml中。正常可以直接定义新数组，这里主要演示如何获取res目录中的数据
        final String[] books = getResources().getStringArray(R.array.books);

        //1 Spinner 默认的情况下，展示出来的最终被选中的内容与 dropdown中的条目采用同样的布局，如果想自定义dropDown的条目布局，使用arrayAdapter的setDropDownViewResource，
        Spinner spinner = (Spinner) findViewById(R.id.spinner_adapter);
        ArrayAdapter arrayAdapter = new ArrayAdapter(this, android.R.layout.simple_list_item_1, books);
        //该方法只针对ArrayAdapter有效，用来指定下拉列表条目的布局文件
        arrayAdapter.setDropDownViewResource(R.layout.item_arrayadapter1);  
        spinner.setAdapter(arrayAdapter);

        //2 使用ArrayAdapter实现spinner。prompt属性只有在spinnerMode 为dialog时才起作用
        Spinner spinnser1 = (Spinner) findViewById(R.id.spinner_adapter1);
        spinnser1.setAdapter(arrayAdapter);
        // spinner.setPrompt("选择你喜欢的书"); 
        // spinnerMode 可以通过代码设置


        //3 使用自定义BaseAdapter封装数据
        Spinner spinner2 = (Spinner) findViewById(R.id.spinner_adapter2);
        MySpinnerAdapter mySpinnerAdapter = new MySpinnerAdapter(this, books);
        spinner2.setAdapter(mySpinnerAdapter);

        //4 给spinner 的条目加入 点击/选中 事件
        spinner2.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
                Toast.makeText(SpinnerActivity.this, "你喜欢的图书是：" + books[position], Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onNothingSelected(AdapterView<?> parent) {

            }
        });
    }
}
```

activity_spinner.xml

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">      
    
    <!--entries 实现-->
    <Spinner
        android:id="@+id/spinner_entries"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:dropDownHorizontalOffset="@dimen/dp300"
        android:dropDownVerticalOffset="@dimen/dp35"
        android:entries="@array/books" />      
    
    <!--dropdown模式展示Spinner，使用ArrayAdapter-->
    <Spinner
        android:id="@+id/spinner_adapter"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/dp35"
        android:layout_marginTop="@dimen/dp35" />               
    
    <!--注意这里的prompt 属性必须是引用类型的字符串，不能硬编码！！而且只有在spinnerMode 的取值模式是dialog的时候才有效.dialog模式展示spinnser，使用arrayAdapter-->
    <Spinner
        android:id="@+id/spinner_adapter1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/dp70"
        android:layout_marginTop="@dimen/dp70"
        android:prompt="@string/app_name"
        android:spinnerMode="dialog" />      
    
    <!--使用BaseAdapter实现的-->
    <Spinner
        android:id="@+id/spinner_adapter2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginLeft="@dimen/dp100"
        android:layout_marginTop="@dimen/dp100" />
</LinearLayout>
```

定义在res目录下 values文件夹中 的arrays.xml

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<resources>
<string-array name="listViewEntries">
    <item>张三1</item>
    <item>李四2</item>
    <item>王五3</item>
    <item>赵六4</item>
    <item>王二麻子5</item>
</string-array>
<string-array name="books">
    <item >西游记</item>
    <item >水浒传</item>
    <item >三国演义</item>
    <item >红楼梦</item>
</string-array>
</resources>
```


##### B: Anko中Dialog模式Spinner的替代

Dialog 模式的Spinner可以使用一个 TextView + 列表Dialog实现。


Anko中提供了selector()函数，该函数可以用来创建一个带有列表项，并且具有条目事件的dialog。格式如下：

```kotlin
selector(对话框标题, 备选列表项) {
     i -> 第i条目被选中时的事件处理 
}
```

示例代码：

```kotlin
val satellites = listOf("金星", "木星", "水星", "火星", "土星", "地球") 
tv_spinner.text = satellites[0]
tv_spinner.setOnClickListener { 
    selector("请选择行星", satellites) {
         i ->tv_spinner.text = satellites[i]
             toast("选择的行星是：${tv_spinner.text}") 
    }
}
```

CnPeng:selector()源码：

```kotlin
fun Context.selector(
        title: CharSequence? = null,
        items: List<CharSequence>,
        onClick: (DialogInterface, Int) -> Unit
) {
    with(AndroidAlertBuilder(this)) {
        if (title != null) {
            this.title = title
        }
        items(items, onClick)
        show()
    }
}
```
CnPeng:items()源码：

```kotlin
override fun items(
    items: List<CharSequence>, 
    onItemSelected: (dialog: DialogInterface, index: Int) -> Unit
) {
        builder.setItems(Array(items.size) { i -> items[i].toString() }) { 
            dialog, which ->onItemSelected(dialog, which)
        }
  }
```

#### (2)、ListView

##### A: lateinit
lateinit表示延迟初始化，放在var/val前面，表示属性不需要在声明时就初始化。而且，即便没有初始化，也不会为空！

这样，定义时未初始化的成员就不需要赋null值了。

#### (3)、GridView

略


#### (4)、RecyclerView


##### A: LayoutContainer

在Activity界面中，Anko库帮我们实现了findViewById的操作，我们使用控件时直接调用控件ID即可。

但是在适配器中 inflate 视图之后，通过填充起来的视图获取子View的时候，Anko库并没有主动实现findViewById操作。所以，我们就需要手动启用 LayoutContainer, 启用之后，获取填充视图中的子View时就可以直接调用子View的id了。

启用方式 同 启用 @Parcelize 序列化注解，在 module的gradle文件的anroid节点进行配置：

```kotlin
android{
    androidExtensions {
        experimental = true
    }
}
```

##### B：Kotlin下Rv的基本使用

* ExtractRvAdapterActivity.kt


```kotlin
//该界面的布局文件中仅有一个RV
class ExtractRvAdapterActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_extract_rv_adapter)

        initRv()
    }

    private fun initRv() {
        val linearLayoutManager = LinearLayoutManager(this)
        rv_extract.layoutManager = linearLayoutManager

        val itemList = mutableListOf<String>()
        for (i in 0..15) {
            itemList.add("这是第${i}个条目")
        }

        rv_extract.adapter = ExtractRvAdapter(itemList)
    }
}
```

* ExtractRvAdapter.kt


```kotlin
/**
 * 作者：CnPeng
 * 时间：2018/12/28
 * 功用：
 * 其他：条目中只是一个id为tv_extractItemDesc的TextView
 */
class ExtractRvAdapter(private var itemList: MutableList<String>) : RecyclerView.Adapter<RecyclerView.ViewHolder>() {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): RecyclerView.ViewHolder {
        val layoutInflater = LayoutInflater.from(parent.context)
        val itemView = layoutInflater.inflate(R.layout.item_extract, parent, false)
        return ExtractViewHolder(itemView)
    }

    override fun getItemCount(): Int {
        return itemList.size
    }

    override fun onBindViewHolder(holder: RecyclerView.ViewHolder, position: Int) {
        (holder as ExtractViewHolder).setDataToItem(itemList[position])
    }

    /**
     * - 《Kotlin从0到精通Android开发》在7.1.4中说此处不要加 inner 前缀，否则报错“java.lang.NoSuchMethodError: No virtual method _$_findCachedViewByID，实测未复现。
     * - 实现LayoutContainer接口后，主构造函数中的参数是自动加进去的——不能改变参数名称！！！默认自动加入时View后面会有？，但与Rv.ViewHolder构造冲突，去掉即可。
     * - 实现LayoutContainer的目的是省略findViewByID
     * - 要想使用LayoutContainer也需要从module的gradle的android节点中增加：androidExtensions {experimental = true}——启用@Parcelize 注解时也需要这个节点
     */
    inner class ExtractViewHolder(override val containerView: View) : RecyclerView.ViewHolder(containerView), LayoutContainer {
        fun setDataToItem(itemBean: String) {
            //因为实现了 LayoutContainer 接口，所以，不用findViewById
            itemView.tv_extractItemDesc.text = itemBean
        }
    }
}
```

##### C：抽取基类适配器（重点）

运用了模板类、高阶函数等内容。

* CommonRvAdapter.kt


```kotlin
/**
 * 作者：CnPeng
 * 时间：2018/12/28
 * 功用：通用的RvAdapter
 * 其他：
 *
 * @param itemLayoutId 条目视图对应的 layoutID
 * @param itemList 条目bean集合。考虑到更新的问题，所以定义为 MutableList
 * @param initItemView 函数参数，由外部提供具体实现. 这是kotlin高阶函数的表现形式
 */
class CommonRvAdapter<T>(var itemLayoutId: Int,
                         private var itemList: MutableList<T>,
                         private val initItemView: (View, T) -> Unit) : RecyclerView.Adapter<CommonRvAdapter.BaseItemHolder<T>>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): CommonRvAdapter.BaseItemHolder<T> {
        val layoutInflater = LayoutInflater.from(parent.context)
        val itemView = layoutInflater.inflate(itemLayoutId, parent, false)
        return BaseItemHolder(itemView, initItemView)
    }

    override fun getItemCount(): Int = itemList.size

    override fun onBindViewHolder(holder: CommonRvAdapter.BaseItemHolder<T>, position: Int) {
        holder.setDataToItem(itemList[position])
    }

    /**
     * CnPeng 2018/12/28 5:36 PM
     * 功用：
     * 说明：
     * - 因为setDataToItem的参数为条目Bean，类型不明确，所以定义为T，所以BaseItemHolder就必须是一个模板类，所以 CommonRvAdapter 也必须是一个模板类
     * - 不能声明为inner，否则 CommonRvAdapter 中引用该Holder的地方会报错，提示：One type argument excepted for class BaseItemHolder<T>
     * - 此处实现了LayoutContainer接口，那么 CommonRvAdapter 构造中传入的方法内部就可以直接通过 view.子View的ID 获取子View对象
     */
    class BaseItemHolder<in T>(override val containerView: View, val initItem: (View, T) -> Unit) : RecyclerView.ViewHolder(containerView), LayoutContainer {

        fun setDataToItem(itemBean: T) {
            initItem(containerView, itemBean)
        }
    }

    /**
     * CnPeng 2018/12/28 6:20 PM
     * 功用：更新列表中的数据
     * 说明：
     */
    fun updateData(newDataList: List<T>) {
        itemList.clear()
        itemList.addAll(newDataList)
        notifyDataSetChanged()
    }
}
```

* ExtractRvAdapterActivity.kt


```kotlin
class ExtractRvAdapterActivity : AppCompatActivity() {
    lateinit var commonRvAdapter: CommonRvAdapter<String>

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_extract_rv_adapter)

        initRv()
        initClickEvent()
    }

    private fun initRv() {
        val linearLayoutManager = LinearLayoutManager(this)
        rv_extract.layoutManager = linearLayoutManager

        val itemList = mutableListOf<String>()
        for (i in 0..15) {
            itemList.add("这是第${i}个条目——普通适配器")
        }

        //普通模式构造适配器
        rv_extract.adapter = ExtractRvAdapter(itemList)
    }


    private fun initClickEvent() {
        tv_initByComAdapter.setOnClickListener {

            val itemList = mutableListOf<String>()
            for (i in 0..15) {
                itemList.add("这是第${i}个条目——通用适配器")
            }

            /**
             * - 通用适配器——构造CommonRvAdapter时，省略了其后的类型<String>
             * - 当函数的最后一个参数是lambda表达式时，需要挪到小括号外面。此处可以参考《KotlinForAndroid读书笔记》中的 "简化setOnCliclListener"
             */
            commonRvAdapter = CommonRvAdapter(R.layout.item_extract, itemList
            ) { view: View, s: String ->
                initRvItem(view, s)
            }

            rv_extract.adapter = commonRvAdapter
        }

        tv_updateRvData.setOnClickListener {
            val newItemList = mutableListOf<String>()
            for (i in 0..15) {
                newItemList.add("这是第${i + 10}个条目——通用适配器")
            }

            commonRvAdapter.updateData(newItemList)
        }
    }

    private fun initRvItem(view: View, s: String) {
        //因为 CommonRvAdapter 中的嵌套类 BaseItemHolder 实现了 LayoutContainer 接口，所以此处可以使用 view.子View的ID 获取子View对象
        view.tv_extractItemDesc.text = s

        // TODO: CnPeng 2018/12/28 6:03 PM 条目的点击、长按，都可以从这里实现。
    }
}
```

* activity_extract_rv_adapter.xml


```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".d_mine.a05_extract_rv_adapter.ExtractRvAdapterActivity">

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_extract"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintRight_toRightOf="parent"
        app:layout_constraintTop_toTopOf="parent" />

    <TextView
        android:id="@+id/tv_updateRvData"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:background="#ff5533"
        android:padding="@dimen/dp15"
        android:text="更新通用适配器列表数据"
        android:textColor="@color/white"
        app:layout_constraintBottom_toBottomOf="parent"
        app:layout_constraintRight_toRightOf="parent" />

    <TextView
        android:id="@+id/tv_initByComAdapter"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_marginBottom="@dimen/dp10"
        android:background="#ff5533"
        android:padding="@dimen/dp15"
        android:text="切换为通用适配器列表"
        android:textColor="@color/white"
        app:layout_constraintBottom_toTopOf="@id/tv_updateRvData"
        app:layout_constraintLeft_toLeftOf="@id/tv_updateRvData"
        app:layout_constraintRight_toRightOf="parent" />
</androidx.constraintlayout.widget.ConstraintLayout>
```

### 2、使用MaterialDesign

#### (1)、CoordinatorLayout基本使用

CoordinatorLayout继承自ViewGroup, 其效果类似于相对布局, 控制子视图位置时有如下方法：

* app:layout_gravity —— 子View在CoordinatorLayout中的对齐方式
* app:layout_anchor —— 子View以谁作为参照物
* app:layout_anchorGravity —— 子View相对于参照物的对齐方式
* app:layout_behavior —— 子View相对于其他子View的行为，当对方的位置放生变化时，该子View随之变化。


activity_coordinator_layout_test.xml

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/coor_parent"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".a_book1.chapter7_coordinatorlayout.CoordinatorLayoutTestActivity">

    <TextView
        android:id="@+id/tv_showSnackBar"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_gravity="bottom"
        android:background="#efa659"
        android:padding="@dimen/dp15"
        android:text="显示SnackBar"
        android:textColor="@color/white" />

    <com.google.android.material.floatingactionbutton.FloatingActionButton
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginBottom="@dimen/dp30"
        android:src="@drawable/start_on"
        app:layout_anchor="@id/tv_showSnackBar"
        app:layout_anchorGravity="right|bottom" />
    
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```
CoordinatorLayoutTestActivity.kt

```kotlin
class CoordinatorLayoutTestActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_coordinator_layout_test)

        showSnackBar()
    }

    private fun showSnackBar() {
        tv_showSnackBar.setOnClickListener {
            //点击之后显示SnackBar,此时FloatingActionButton上移；SnackBar自动移除之后，FloatingActionButton回到原位置
            Snackbar.make(coor_parent, "SnackBar显示了", Snackbar.LENGTH_SHORT)
                    .setAction("点我试一下") {
                        toast("SnackBar中的超链接被点击了")
                    }
                    .show()
        }
    }
}
```

#### (2)、工具栏ToolBar

##### A: 使用须知
* APP通常会有一个顶部导航栏，Android5.0之前，这个顶部导航栏为 ActionBar；但ActionBar不容易扩展，所以，5.0 之后推出了 ToolBar.

* 使用TooBar时需要先关闭 ActionBar —— 为当前界面或者应用引用 NoActionBar主题

* ToolBar是继承自ViewGroup, 允许有子View

* 所在Activity需要继承自AppCompatActivity

* 使用ToolBar时，需要调用 setSupportActionBar(toolBar) 函数，让toolBar对象替代actionBar

布局文件中的部分内容：

```kotlin
 <androidx.appcompat.widget.Toolbar
        android:id="@+id/toolbar2"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:background="#f57d5c"
        android:minHeight="?attr/actionBarSize"
        android:theme="?attr/actionBarTheme"
        app:logo="@drawable/start_on"
        app:navigationIcon="?attr/homeAsUpIndicator"
        app:subtitle="子标题定义为黄色"
        app:subtitleTextColor="#ff0"
        app:title="ToolBar的标题——CnPeng"
        app:titleTextColor="@color/white" />
```
 
绑定及导航按钮的点击事件：  
     
```kotlin
class ToolBarDemoActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_tool_bar_demo)

        setSupportActionBar(toolbar2)

        toolBarBaseUse()
    }

    private fun toolBarBaseUse() {
        toolbar2.setNavigationOnClickListener {
            //点击 navigationIcon 后: 隐藏toolBar并关闭当前页面
            supportActionBar?.hide()
            finish()
        }
    }
}
```

##### B：其他相关参考文章：
* [官方文档](https://developer.android.com/training/appbar/setting-up) 
* [CnPeng——ToolBar使用详解(暂未开始)](https://gitee.com/CnPeng_1/LearningNotes/wikis/*%2084%E3%80%81ToolBar%E4%BD%BF%E7%94%A8%E8%AF%A6%E8%A7%A3?sort_id=1215101)


#### (3)、AppBarLayout

* AppBarLayout继承自LinearLayout
* AppBarLayout可以实现协调滚动
* AppBarLayout 的 LayoutParams 提供了 `app:layout_scrollFlags` 属性，用来控制子View的滚动行为

AppBarLayout + ToolBar + CoordinaterLayout + Rv 实现滚动传递(ToolBar随着RV的滚动可以滚入或滚出)时，有如下要点：

* AppBarLayout 中包裹 ToolBar
* 被滚动方 ToolBar 定义 `app:layout_scrollFlags` 属性
* 滚动事件触发者 RV 定义 `app:layout_behavior="@string/appbar_scrolling_view_behavior`,

`app:layout_behavior` 的作用是将RV的滚动事件传递给 AppBarLayout


 **A: `APP：layout_scrollFlags`取值说明**

取值|含义
---|---
scroll| 头部与主体一起滚动
enterAlways|头部先与主体一起滚动，头部滚动完毕后，主体继续向上或向下滚动，需要与scroll同时声明
enterAlwaysCollapsed|该标记与enterAlways的区别在于有折叠操作，而enterAlways没有，需要与srcoll/enterAlways同时声明
exitUtilCollapsed|保证页面上至少能看到最小化的工具栏，需要与scroll同时声明
snap|用户手指松开时，系统自行判断接下来时


示例如下：

`activity_app_bar_layout.xml`

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".a_book1.chapter7.AppBarLayoutActivity">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <!--被滚动方定义 scrollFlags-->
        <androidx.appcompat.widget.Toolbar
            android:layout_width="match_parent"
            android:layout_height="?attr/actionBarSize"
            app:layout_scrollFlags="scroll|enterAlways"
            app:title="ToolBar要滚进滚出了"
            app:titleTextColor="@color/white" />
    </com.google.android.material.appbar.AppBarLayout>

    <!--滚动发起者定义 behavior-->
    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_appBarTest"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

AppBarLayoutActivity.kt 中初始化RV

```kotlin
class AppBarLayoutActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_app_bar_layout)

        rv_appBarTest.layoutManager = LinearLayoutManager(this)

        val mutableList = mutableListOf<String>()
        for (i in 0..15) {
            mutableList.add("${i}——ToolBar+AppBarLayout+Rv的滚动")
        }

        //此处直接调用了前面抽取的通用适配器
        rv_appBarTest.adapter = CommonRvAdapter<String>(R.layout.item_extract, mutableList) { view, str ->
            view.tv_extractItemDesc.text = str
        }
    }
}
```

#### (4)、CollapsingToolbarLayout
可折叠工具栏，可以实现视察特效——展开和收起工具栏

>该部分内容主要参考 [ **官方文档--CollapsingToolbarLayout** ](https://developer.android.com/reference/android/support/design/widget/CollapsingToolbarLayout)


##### A: 实现要点：

* 根布局为 CoordinaterLayout
* AppBarLayout中包裹 CollapsingToolBarLayout, CollapsingToolBarLayout中包裹 ToolBar
* CollapsingToolBarLayout 定义scrollFlags
* 定义RV作为滚动事件提供者，并配置 layout_behavior


##### B: CollapsingToolbarLayout.LayoutParams的属性

在 CollapsingToolbarLayout 中LayoutParams 中定义了如下两个重要属性：
* layout_collapseMode——子视图折叠模式
* layout_collapseParallaxMultiplier


##### C: CollapsingToolbarLayout的属性

CollapsingToolbarLayout是对ToolBar的包装，作为AppBarLayout的直接子类使用。

不要手动的在运行阶段为ToolBar添加子View。因为，为了给title预留足够的控件，ToolBar 中加入了一个'dummy view' , 它可能会妨碍手动添加View的布局。

属性|含义
---|---
collapsedTitleGravity | 标题收起时的对齐方式
collapsedTitleTextAppearance | 标题收起时的字体样式
contentScrim| 折叠之后ToolBar的背景（或者是从折叠到展开时的渐变起始色），取值Drawable/Clolr
expandedTitleGravity| 标题展开时的对齐方式
expandedTitleMargin| 标题展开时的margin值
expandedTitleMarginBottom|标题展开时距离底部的距离
expandedTitleMarginEnd|标题展开时距离末端（通常是右侧）的距离
expandedTitleMarginStart| 标题展开时距离起始端（通常是左侧）的距离
expandedTitleMarginTop|标题展开时距离顶部的距离
expandedTitleTextAppearance|标题展开时的字体样式
scrimAnimationDuration|scrim动画时长
scrimVisibleHeightTrigger|trigger扳机
statusBarScrim|状态栏的背景——测试无效，不知道是否需要与其他配合使用
title|标题（如果子View中包裹了ToolBar,那么该title会覆盖ToolBar中的title）
titleEnabled|是否启用标题


##### D: CollapsingToolbarLayout的方法

只列出没有属性对应的方法，对应的get方法省略

函数|含义
---|---
setExpandedTitleTypeface|标题展开时使用的字体.取值Typeface，null使用默认
setExpandedTitleTextColor|标题展开时的颜色. 取值 ColorStateList。26.1.0加入
setExpandedTitleColor|标题展开时的颜色.取值为ARGB格式的颜色。22.2.0加入
setScrimsShown|是否显示content scrim and/or status bar scrim 。垂直方向的任意滚动可能都会重写该值.该View任何可见的改变都会触发一个布局动画
setCollapsedTitleTypeface|标题收起时的字体
setCollapsedTitleTextColor|标题收起时的颜色——ARGB
setCollapsedTitleTextColor|标题收起时的颜色——ColorStateList

##### E: 基本使用

APP运行过程中，ToolBar 的高度是固定的，展开和收起的视觉效果实际是 CollapsingToolBarLayout的高度变化触发的。

在下面的示例代码中，为ToolBar设置一个红色背景之后，就能够清晰的得出上述结论！


```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.coordinatorlayout.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".a_book1.chapter7.CollapsingToolBarActivity">

    <com.google.android.material.appbar.AppBarLayout
        android:layout_width="match_parent"
        android:layout_height="160dp"
        android:background="#ff0">

        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            app:contentScrim="?attr/colorPrimary"
            app:expandedTitleGravity="left"
            app:expandedTitleMarginStart="10dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:scrimAnimationDuration="1000"
            app:title="CollapsingToolBar的标题">

            <!--&lt;!&ndash; 时差模式1：app:title="ToolBar的标题"/  android:background="#f00" &ndash;&gt;-->
            <!--<androidx.appcompat.widget.Toolbar-->
            <!--android:layout_width="match_parent"-->
            <!--android:layout_height="?attr/actionBarSize"-->
            <!--app:layout_collapseMode="parallax"-->
            <!--app:layout_collapseParallaxMultiplier="0.2"-->
            <!--app:titleTextColor="@color/white" />  -->

            <!--&lt;!&ndash; pin模式：app:title="ToolBar的标题"/   &ndash;&gt;-->
            <!--<androidx.appcompat.widget.Toolbar-->
            <!--android:layout_width="match_parent"-->
            <!--android:layout_height="?attr/actionBarSize"-->
            <!--android:background="#f00"-->
            <!--app:layout_collapseMode="pin"-->
            <!--app:layout_collapseParallaxMultiplier="0.2"-->
            <!--app:titleTextColor="@color/white" />-->

            <!-- app:title="ToolBar的标题"/ layout_collapseParallaxMultiplier的取值0-1，值越大，视差效果越明显 -->
            <androidx.appcompat.widget.Toolbar
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.1"
                app:titleTextColor="@color/white" />
        </com.google.android.material.appbar.CollapsingToolbarLayout>

    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_collapsing"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />

</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

#### (5)、仿支付宝首页标题栏展开和折叠
![输入图片说明](https://images.gitee.com/uploads/images/2019/0102/152329_cdb2b46d_930142.gif "1.gif")


完整示例代码在：[com.cnpeng.android2.a_book1.chapter7](https://github.com/CnPeng/CnPengAndroid2) 中

* `activity_scroll_alipay.xml`


```kotlin
<androidx.coordinatorlayout.widget.CoordinatorLayout 
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true"
    android:focusable="true"
    android:focusableInTouchMode="true">

    <!--此处慎用fitsSystemWindows="true"，可能会出问题 -->
    <com.google.android.material.appbar.AppBarLayout
        android:id="@+id/abl_bar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content">

        <!--注意这里的scrollFlags中加入了snap，可以实现松手自动回滚的动作——类似于fling的效果-->
        <com.google.android.material.appbar.CollapsingToolbarLayout
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:contentScrim="@color/blue_dark"
            app:layout_scrollFlags="scroll|exitUntilCollapsed|snap">

            <!--四大基本功能-->
            <include
                android:id="@+id/base_func"
                layout="@layout/life_pay"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/toolbar_height"
                app:layout_collapseMode="parallax"
                app:layout_collapseParallaxMultiplier="0.7" />

            <!--注意：contentInsetLeft/contentInsetStart 这两个属性-->
            <androidx.appcompat.widget.Toolbar
                android:layout_width="match_parent"
                android:layout_height="@dimen/toolbar_height"
                app:contentInsetLeft="0dp"
                app:contentInsetStart="0dp"
                app:layout_collapseMode="pin">

                <!--四大基本功能展开时的标题栏-->
                <include
                    android:id="@+id/tl_expand"
                    layout="@layout/toolbar_expand"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent" />

                <!--四大基本功能收起时的标题栏-->
                <include
                    android:id="@+id/tl_collapse"
                    layout="@layout/toolbar_collapse"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:visibility="gone" />
            </androidx.appcompat.widget.Toolbar>
        </com.google.android.material.appbar.CollapsingToolbarLayout>
    </com.google.android.material.appbar.AppBarLayout>

    <androidx.recyclerview.widget.RecyclerView
        android:id="@+id/rv_content"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```

* life_pay.xml


```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="160dp"
    android:background="@color/blue_dark"
    android:orientation="horizontal"
    android:paddingTop="35dp"
    android:paddingBottom="20dp">

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/big_scan" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="5dp"
            android:text="扫一扫"
            android:textColor="#fff"
            android:textSize="16sp" />
    </LinearLayout>


    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/big_pay" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="5dp"
            android:text="付一付"
            android:textColor="#fff"
            android:textSize="16sp" />
    </LinearLayout>


    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/big_chat" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="5dp"
            android:text="聊一聊"
            android:textColor="@color/white"
            android:textSize="16sp" />
    </LinearLayout>


    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:orientation="vertical">

        <ImageView
            android:layout_width="60dp"
            android:layout_height="60dp"
            android:layout_gravity="center_horizontal"
            android:src="@drawable/big_qrcode" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center_horizontal"
            android:layout_marginTop="5dp"
            android:text="咻一咻"
            android:textColor="#fff"
            android:textSize="16sp" />
    </LinearLayout>
</LinearLayout>
```

* toolbar_collapse.xml


```kotlin
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="@dimen/toolbar_height"
    android:background="@color/blue_dark">

    <ImageView
        android:id="@+id/iv_scan"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_alignParentLeft="true"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:src="@drawable/small_scan" />

    <ImageView
        android:id="@+id/iv_pay"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:layout_toRightOf="@+id/iv_scan"
        android:src="@drawable/small_pay" />

    <ImageView
        android:id="@+id/iv_charge"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:layout_toRightOf="@+id/iv_pay"
        android:src="@drawable/small_charge" />

    <ImageView
        android:id="@+id/iv_money"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_centerVertical="true"
        android:layout_marginLeft="20dp"
        android:layout_toRightOf="@+id/iv_charge"
        android:src="@drawable/small_money" />

    <ImageView
        android:id="@+id/iv_plus"
        android:layout_width="30dp"
        android:layout_height="30dp"
        android:layout_alignParentRight="true"
        android:layout_centerVertical="true"
        android:layout_marginRight="20dp"
        android:src="@drawable/icon_plus" />
</RelativeLayout>
```
* toolbar_expand.xml

```kotlin
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="@dimen/toolbar_height"
    android:background="@color/blue_dark"
    android:orientation="horizontal" >

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:layout_margin="10dp"
        android:layout_toLeftOf="@+id/iv_contact"
        android:background="@drawable/shape_search"
        android:paddingLeft="10dp"
        android:paddingRight="10dp" >

        <ImageView
            android:id="@+id/iv_search"
            android:layout_width="20dp"
            android:layout_height="20dp"
            android:layout_alignParentLeft="true"
            android:layout_centerVertical="true"
            android:src="@drawable/icon_search" />

        <EditText
            android:id="@+id/et_search"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_centerVertical="true"
            android:layout_toRightOf="@+id/iv_search"
            android:paddingLeft="10dp"
            android:background="@null"
            android:hint="搜索商品"
            android:textColorHint="@color/white"
            android:textSize="16sp" />
    </RelativeLayout>

    <ImageView
        android:id="@+id/iv_plus"
        android:layout_width="20dp"
        android:layout_height="20dp"
        android:layout_alignParentRight="true"
        android:layout_centerVertical="true"
        android:layout_marginRight="10dp"
        android:src="@drawable/icon_plus" />

    <ImageView
        android:id="@+id/iv_contact"
        android:layout_width="25dp"
        android:layout_height="25dp"
        android:layout_centerVertical="true"
        android:layout_marginRight="10dp"
        android:layout_toLeftOf="@+id/iv_plus"
        android:src="@drawable/icon_contact" />

    <View
        android:id="@+id/v_expand_mask"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/transparent" />

</RelativeLayout>
```

* MainAlipayActivity.kt


```kotlin
/**
 * CnPeng 2019/1/2 2:51 PM
 * 功用：仿支付宝首页顶部的滚动和折叠
 * 说明：
 */
class MainAlipayActivity : AppCompatActivity(), AppBarLayout.OnOffsetChangedListener {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_scroll_alipay)

        //给控件abl_bar注册一个位置偏移的监听器
        abl_bar.addOnOffsetChangedListener(this)

        rv_content.layoutManager = GridLayoutManager(this, 4)

        rv_content.adapter = CommonRvAdapter(R.layout.item_life, LifeItem.default
        ) { view, item ->
            view.iv_pic.setImageResource(item.pic_id)
            view.tv_title.text = item.title
        }
    }

    /**
     * CnPeng 2019/1/2 2:47 PM
     * 功用：重写AppBarLayout的滚动监听
     * 说明：以AppBarLayout滚动范围的1/2为基准，超过1/2时完全隐层一个，并逐步显示另一个。
     */
    override fun onOffsetChanged(appBarLayout: AppBarLayout, verticalOffset: Int) {

        val offsetAbs = Math.abs(verticalOffset)
        val totalScrollRange = appBarLayout.totalScrollRange
        val halfTotal = totalScrollRange / 2

        //标题栏的透明度渐变
        val titleAlpha: Float = (offsetAbs * 1.0f / halfTotal)
        AnkoLogger("AppBarLayout滚动信息").error {
            "verticalOffset:$verticalOffset,totalScrollRange:$totalScrollRange,titleAlpha:$titleAlpha"
        }
        //向上滚时，alpha从0-2，向下滚时从2-0
        if (titleAlpha in 0.0..1.0) {
            //渐渐不可见——0为完全透明，不可见；1为完全不透明可见
            tl_expand.alpha = 1 - titleAlpha
            tl_expand.visibility = View.VISIBLE
            tl_collapse.visibility = View.GONE
        } else {
            tl_collapse.visibility = View.VISIBLE
            tl_expand.visibility = View.GONE
            tl_collapse.alpha = titleAlpha - 1
        }

        //默认四大基本常用功能的透明度变化
        base_func.alpha = 1 - offsetAbs * 1.0f / totalScrollRange
    }
}

```

* LifeItem.kt

```kotlin
data class LifeItem(var pic_id: Int, var title: String) {
    companion object {
        val default: MutableList<LifeItem>
            get() {
                val itemArray = mutableListOf<LifeItem>()
                for (i in 0..39) {
                    itemArray.add(LifeItem(R.drawable.icon_transfer, "转账"))
                }
                return itemArray
            }
    }
}
```
### 3、实现页面切换
#### (1)、ViewPager

PageTabStrip也是ViewPager指示器。使用时必须在xml中嵌套在ViewPager内部

activity_view_pager.xml

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<androidx.constraintlayout.widget.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".a_book1.chapter7.ViewPagerActivity">

    <androidx.viewpager.widget.ViewPager
        android:id="@+id/vp_vpAct"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <androidx.viewpager.widget.PagerTabStrip
            android:id="@+id/pts_vpTest"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="top" />

    </androidx.viewpager.widget.ViewPager>

</androidx.constraintlayout.widget.ConstraintLayout>
```

ViewPagerActivity.kt

```kotlin
class ViewPagerActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_view_pager)

        initViewPager()
    }

    private fun initViewPager() {
        vp_vpAct.currentItem = 0
        vp_vpAct.adapter = VpActAdapter(AlipayBaseFuncItem.funcItemList, this)

        vp_vpAct.addOnPageChangeListener(object : ViewPager.OnPageChangeListener {
            override fun onPageScrollStateChanged(state: Int) {

            }

            override fun onPageScrolled(position: Int, positionOffset: Float, positionOffsetPixels: Int) {
            }

            override fun onPageSelected(position: Int) {
                toast("当前选中的页面标题为：${AlipayBaseFuncItem.funcItemList[position].title}")
            }
        })

        pts_vpTest.tabIndicatorColor = resources.getColor(R.color.colorAccent)
        pts_vpTest.setTextSize(TypedValue.COMPLEX_UNIT_SP, 16f)
        pts_vpTest.setTextColor(Color.BLUE)
    }
}
```

VpAdapter.kt

```kotlin
class VpActAdapter(private var funcList: MutableList<AlipayBaseFuncItem>, val context: Context) : PagerAdapter() {
    private val itemViews = mutableListOf<View>()

    init {
        //主构造函数只初始化被声明的成员，其他未从主构造中声明的变量可以放在 init 中初始化
        for (it in funcList) {
            val itemView = ImageView(context)
            itemView.setImageResource(it.picID)
            itemView.setBackgroundColor(Color.BLUE)
            itemView.scaleType = ImageView.ScaleType.CENTER_INSIDE
            itemViews.add(itemView)
        }
    }

    //判断页面是否已经加入到适配器
    override fun isViewFromObject(view: View, `object`: Any): Boolean = view === `object`

    override fun getCount(): Int = itemViews.size

    override fun instantiateItem(container: ViewGroup, position: Int): Any {
        container.addView(itemViews[position])
        return itemViews[position]
    }

    override fun destroyItem(container: ViewGroup, position: Int, `object`: Any) {
        //此处必须直接销毁view，如果使用container.removeViewAt(position)会出错
        container.removeView(itemViews[position])
    }

    override fun getPageTitle(position: Int): CharSequence? {
        return funcList[position].title
    }
}
```


#### (2)、Fragment

##### A: 基本使用
重点参考下面代码中 获取Framgent实例的实现方法

```kotlin
class VpChildFragment : Fragment() {
    private var param1: String? = null
    private var param2: String? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        arguments?.let {
            param1 = it.getString(ARG_PARAM1)
            param2 = it.getString(ARG_PARAM2)
        }
    }

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?,
                              savedInstanceState: Bundle?): View? {
        return inflater.inflate(R.layout.fragment_vp_child, container, false)
    }

    companion object {
        @JvmStatic
        fun newInstance(param1: String, param2: String) =
                VpChildFragment().apply {
                    arguments = Bundle().apply {
                        putString(ARG_PARAM1, param1)
                        putString(ARG_PARAM2, param2)
                    }
                }
    }
}

```

##### B: setUserVisibleHint()
对应原书 7.5.4

* Fragment界面对用户可见时做相关处理。

ViewPager+Fragment时，默认选中0索引Fragment，此时，该Fragment的声明周期为：onAttach->setUserVisibleHint->onCreateView。


界面可见时，发送改变界面背景的广播：

```kotlin
override fun setUserVisibleHint(isVisibleToUser: Boolean) {
    super.setUserVisibleHint(isVisibleToUser) 
    //ViewPager+Fragment中，默认选中的Fragment的声明周期中setUserVisibleHint先于onCreateView执行
    if (ctx != null) {
        val intent = Intent(ChannelPagerAdapter.EVENT)
        intent.putExtra("color", ctx!!.resources.getColor(R.color.pink)
        ctx!!.sendBroadcast(intent)
    }
}
```


#### (3)、TabLayout
参考内容：https://www.jianshu.com/p/58c168ef4fa8

参考内容2：https://www.jianshu.com/p/c1aa1056176b

### 4、收发广播——Broadcast


* val——运行时常量

* const val ——编译时常量

#### (1)、系统广播

##### A: 静态注册

* 适用于 开机启动、用户解锁、定时闹钟等APP未启动也可以接收的广播

在清单文件中增加 <receiver> 节点：

```kotlin
 <receiver android:name=".receiver.BootCompletedReceiver" >
        <intent-filter>
            <action android:name="android.intent.action.BOOT_COMPLETED" />
        </intent-filter>
</receiver>
```
##### B:动态注册

* registerReceiver（）
* unregisterReceiver（）

* 适用于 分钟变化、网络切换、电量变化等APP启动之后才可以接收的广播

```kotlin
timeReceiver = TimeReceiver() 
//分钟变化的广播
val filter = IntentFilter(Intent.ACTION_TIME_TICK)
registerReceiver(timeReceiver, filter)
```


#### (2)、本地广播

只能动态注册



### 5、实战：电商APP的商品频道

#### (1)、需求分析

略

#### (2)、SwipeRefreshLayout

函数|含义
---|---
setColorSchemeColors|进度圈的圆环颜色列表
setProgressBackgroundColorSchemeColor|进度圈的圆环背景颜色列表
setProgressViewOffset|第一个参数表示进度圈是否缩放，第二个参数表示进度圈开始时距离顶部的距离，第三个参数表示进度圈最大时距离顶部的距离

* SwipeRefreshLayout只能有一个子View，并且其子View必须是：ScrollView、ListView、GridView、RecyclerView、 NestedScrollView等可以滚动的View

```kotlin
refreshLayout.setColorSchemeResources(R.color.red, R.color.orange, R.color.green, R.color.blue)
```

---

## 八、Kotlin进行数据存储

### 1、SharedPreference

#### (1)、SP读写工具类

```kotlin
class Preference<T>(val context: Context, val name: String, val default: T) : ReadWriteProperty<Any?, T> {

    //通过属性代理初始化共享参数对象
    val prefs: SharedPreferences by lazy { context.getSharedPreferences("default", Context.MODE_PRIVATE) }

    //接管属性值的获取行为
    override fun getValue(thisRef: Any?, property: KProperty<*>): T {
        return findPreference(name, default)
    }

    //接管属性值的修改行为
    override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
        putPreference(name, value)
    }

    //利用with函数定义临时的命名空间
    private fun <T> findPreference(name: String, default: T): T = with(prefs) {
        val res: Any = when (default) {
            is Long -> getLong(name, default)
            is String -> getString(name, default)
            is Int -> getInt(name, default)
            is Boolean -> getBoolean(name, default)
            is Float -> getFloat(name, default)
            else -> throw IllegalArgumentException("This type can be saved into Preferences")
        }
        return res as T
    }

    private fun <T> putPreference(name: String, value: T) = with(prefs.edit()) {
        //putInt、putString等方法返回Editor对象
        when (value) {
            is Long -> putLong(name, value)
            is String -> putString(name, value)
            is Int -> putInt(name, value)
            is Boolean -> putBoolean(name, value)
            is Float -> putFloat(name, value)
            else -> throw IllegalArgumentException("This type can be saved into Preferences")
        }.apply() //commit方法和apply方法都表示提交修改
    }
}
```

ReadWriteProperty.kt

```kotlin
public interface ReadWriteProperty<in R, T> {

    public operator fun getValue(thisRef: R, property: KProperty<*>): T

    public operator fun setValue(thisRef: R, property: KProperty<*>, value: T)
}
```

调用方：

```kotlin
//以属性委托的形式存取变量值——此处为取。
private var name: String by Preference(ctx, "name", "")
private var age: Int by Preference(ctx, "age", 0)

//因为上面已经声明了属性委托，所以，此处为name赋值时，实际调用了属性委托的存储。
name="zhangsan"
```

#### (2)、属性代理等

##### A：T、Any、* 的区别

* T 是抽象的类型，在模板类中用来占位子，外部调用模板类时才能确定T的具体类型。
* Any是Kotlin的基本类型，所有类都是从Any派生而来，相当于Java中的Object
* “*” 表示一个不确定的类型，同样也是在外部调用时才能确定，这一点跟T比较像，但是，T出现在模板类的定义当中，而“* ”与模板类无关。它出现在单个函数定义的参数列表中，相当于Java中的“？”


##### B: 属性委托/属性代理

属性代理：该属性的类型不变，但是属性的读写行为被后面的类接管了。该类接管之后，通过自定义的set/get 进行存储和读取数据。

>属性代理有点类似于实现了 Observable 的 DataBinding


##### C: lazy

被lazy修饰的内容表示， **只在第一次使用时执行初始化。** 


Kotlin中初始化的几种情况：
* 声明时立即通过“=”进行赋值
* 通过lateinit延迟初始化：虽然没有马上赋值，但必然是一个非空变量，具体何时初始化由开发者自行决定
* 通过lazy修饰的首次使用时初始化：声明变量时就指定初始化操作，但只有第一次被调用时才执行这些操作。


##### D: with 函数
* 格式为：with(函数头){函数体}

* 函数头：位于紧跟with的小括号内，先于函数体执行，并且函数头会返回一个对象。

* 函数体：位于大括号内，要等函数头执行完毕之后才执行，同时函数体只在函数头返回对象的命名空间中运行。也就是说，函数体语句可以直接调用该对象的方法，而无须指定头部对象的实例名称。


#### (3)、记住密码功能
略


### 2、SQLite

#### (1)、SQLiteOpenHelper、SQLiteDatabase

##### A: SQLiteDatabase

SQLiteDatabase是SQLite的数据库管理类，开发者可以在任意能获取到Context对象的地方获取数据库的实例。

Java示例：

```kotlin
//创建或者打开数据路
SQLiteDatabase db = getApplicationContext().openOrCreateDatabase ("test
.db", Context.MODE_PRIVATE, null);

//删除数据库
getApplicationContext().deleteDatabase("test.db");
```

* SQLiteDatabase常用方法：

方法|含义
---|---
openDatabase|打开指定路径的数据库
isOpen|判断数据库是否已经打开
close|关闭数据库
execSQL|执行拼接好的SQL语句，通常用于建表、删表、修改表结构等
delete|删除符合条件的数据
update|更新符合条件的数据
insert|插入一条记录
query|查询数据，返回结果集Cursor
rawQuery|执行拼接好的查询语句，返回Cursor

insert、update可以直接使用ContentValues类。其内部是Map结构，键只能使用String。

获取查询后的单条数据时，需要遍历Cursor（Cursor也叫游标）.

* Cursor常用方法：

方法|含义
---|---
close|关闭游标
isClosed|判断游标是否已经关闭
isFirst|判断游标是否在开头
isLast|判断游标是否在末尾
moveToFirst|移动游标到开头
moveToLast|移动游标到末尾
moveToNext|移动游标到下一位
moveToPrevious|移动游标到前一位
move|往后移动若干位
moveToPosition|移动到指定位置
getCount|获取游标集的总数
getInt|获取指定字段的整型值
getFloat|获取指定字段的Float值
getString|获取指定字段的String值
getType|获取指定字段的字段类型。


##### B: SQLiteOpenHelper

SQLiteDatabase只是提供了数据库的DDL(数据库定义)和DML(数据库管理)的操作，并未提供完整的业务处理流程。

当需要处理业务流程时就需要使用SQLiteOpenHelper，它是数据库操作的辅助工具类。存取数据时，SQLiteOpenHelper的使用步骤如下：

* 新建一个继承自SQLiteOpenHelper的类，并重写其中的onCreate和onUpgrade。onCreate只在第一次打开数据库时执行，其中可执行创建表结构的操作；onUpgrade 是在数据库版本升级时使用。

* 封装保证数据库安全的相关方法：获取单例，打开/关闭数据库连接等
    * 获取单例：确保运行时数据库只被创建/打开一次，避免重复创建/打开
    * 打开数据库连接：SQLite也有锁机制，即读锁和写锁，所以，SQLiteOpenHelper获取的数据库连接也有两种：getReadableDatabase、getWritableDatabase
    * 关闭数据库连接：数据库操作完毕之后，调用SQLiteDatabase 的close方法关闭数据库
* 提供增删改查的方法


#### (2)、ManagedSQLiteOpenHelper

ManagedSQLiteOpenHelper是Anko提供的数据库帮助类。 **主动封装了数据库连接的开启和关闭操作** ，这样开发者就不用再手动的开关了。


Anko中数据库相关的内容在 anko-sqlite 包中，而 anko-common中并不包含，所以，还需要在gradle中导入：

```kotlin
implemation "org.jetbrains.anko:anko-sqlite:$anko_version"
```

ManagedSQLiteOpenHelper与SQLiteOpenHelper的用法基本一致，其区别在于：数据表的增删改查操作需要放在use代码块中。格式如下：

```kotlin
use {
    //insert(...)
    //update(...)
    //delete(...)
    //query(...)或rawQuery(...)
}
```

UserDBHelper.kt

```kotlin
class UserDBHelper(var context: Context, private var DB_VERSION: Int = CURRENT_VERSION) :
        ManagedSQLiteOpenHelper(context, DB_NAME, null, DB_VERSION) {

    companion object {
        private val TAG = "UserDBHelper"
        var DB_NAME = "user.db" //数据库名称
        var TABLE_NAME = "user_info" //表名称
        var CURRENT_VERSION = 1 //当前的最新版本，如有表结构变更，该版本号要加一
        private var instance: UserDBHelper? = null

        @Synchronized
        fun getInstance(ctx: Context, version: Int = 0): UserDBHelper {
            if (instance == null) {
                //如果调用时没传版本号，就使用默认的最新版本号
                instance = if (version > 0) {
                    UserDBHelper(ctx.applicationContext, version)
                } else {
                    UserDBHelper(ctx.applicationContext)
                }
            }
            return instance!!
        }
    }

    override fun onCreate(db: SQLiteDatabase) {
        Log.d(TAG, "onCreate")
        val drop_sql = "DROP TABLE IF EXISTS $TABLE_NAME;"
        Log.d(TAG, "drop_sql:" + drop_sql)
        db.execSQL(drop_sql)
        val create_sql = "CREATE TABLE IF NOT EXISTS $TABLE_NAME (" +
                "_id INTEGER PRIMARY KEY  AUTOINCREMENT NOT NULL," +
                "name VARCHAR NOT NULL," + "age INTEGER NOT NULL," +
                "height LONG NOT NULL," + "weight FLOAT NOT NULL," +
                "married INTEGER NOT NULL," + "update_time VARCHAR NOT NULL" +
                //演示数据库升级时要先把下面这行注释
                ",phone VARCHAR" + ",password VARCHAR" + ");"
        Log.d(TAG, "create_sql:" + create_sql)
        db.execSQL(create_sql)
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
        Log.d(TAG, "onUpgrade oldVersion=$oldVersion, newVersion=$newVersion")
        if (newVersion > 1) {
            //Android的ALTER命令不支持一次添加多列，只能分多次添加
            var alter_sql = "ALTER TABLE $TABLE_NAME ADD COLUMN phone VARCHAR;"
            Log.d(TAG, "alter_sql:" + alter_sql)
            db.execSQL(alter_sql)
            alter_sql = "ALTER TABLE $TABLE_NAME ADD COLUMN password VARCHAR;"
            Log.d(TAG, "alter_sql:" + alter_sql)
            db.execSQL(alter_sql)
        }
    }

    fun delete(condition: String): Int {
        var count = 0
        use {
            count = delete(TABLE_NAME, condition, null)
        }
        return count
    }

    fun insert(info: UserInfo): Long {
        val infoArray = mutableListOf(info)
        return insert(infoArray)
    }

    fun insert(infoArray: MutableList<UserInfo>): Long {
        var result: Long = -1
        for (i in infoArray.indices) {
            val info = infoArray[i]
            var tempArray: List<UserInfo>
            // 如果存在同名记录，则更新记录
            // 注意条件语句的等号后面要用单引号括起来
            if (info.name.isNotEmpty()) {
                val condition = "name='${info.name}'"
                tempArray = query(condition)
                if (tempArray.size > 0) {
                    update(info, condition)
                    result = tempArray[0].rowid
                    continue
                }
            }
            // 如果存在同样的手机号码，则更新记录
            if (info.phone.isNotEmpty()) {
                val condition = "phone='${info.phone}'"
                tempArray = query(condition)
                if (tempArray.size > 0) {
                    update(info, condition)
                    result = tempArray[0].rowid
                    continue
                }
            }
            // 不存在唯一性重复的记录，则插入新记录
            val cv = ContentValues()
            cv.put("name", info.name)
            cv.put("age", info.age)
            cv.put("height", info.height)
            cv.put("weight", info.weight)
            cv.put("married", info.married)
            cv.put("update_time", info.update_time)
            cv.put("phone", info.phone)
            cv.put("password", info.password)
            use {
                result = insert(TABLE_NAME, "", cv)
            }
            // 添加成功后返回行号，失败后返回-1
            if (result == -1L) {
                return result
            }
        }
        return result
    }

    @JvmOverloads
    fun update(info: UserInfo, condition: String = "rowid=${info.rowid}"): Int {
        val cv = ContentValues()
        cv.put("name", info.name)
        cv.put("age", info.age)
        cv.put("height", info.height)
        cv.put("weight", info.weight)
        cv.put("married", info.married)
        cv.put("update_time", info.update_time)
        cv.put("phone", info.phone)
        cv.put("password", info.password)
        var count = 0
        use {
            count = update(TABLE_NAME, cv, condition, null)
        }
        return count
    }

    fun query(condition: String): List<UserInfo> {
        val sql = "select rowid,_id,name,age,height,weight,married,update_time,phone,password from $TABLE_NAME where $condition;"
        Log.d(TAG, "query sql: " + sql)
        var infoArray = mutableListOf<UserInfo>()
        use {
            val cursor = rawQuery(sql, null)
            if (cursor.moveToFirst()) {
                while (true) {
                    val info = UserInfo()
                    info.rowid = cursor.getLong(0)
                    info.xuhao = cursor.getInt(1)
                    info.name = cursor.getString(2)
                    info.age = cursor.getInt(3)
                    info.height = cursor.getLong(4)
                    info.weight = cursor.getFloat(5)
                    //SQLite没有布尔型，用0表示false，用1表示true
                    info.married = if (cursor.getInt(6) == 0) false else true
                    info.update_time = cursor.getString(7)
                    info.phone = cursor.getString(8)
                    info.password = cursor.getString(9)
                    infoArray.add(info)
                    if (cursor.isLast) {
                        break
                    }
                    cursor.moveToNext()
                }
            }
            cursor.close()
        }
        return infoArray
    }

    fun queryByPhone(phone: String): UserInfo {
        val infoArray = query("phone='$phone'")
        val info: UserInfo = if (infoArray.size > 0) infoArray[0] else UserInfo()
        return info
    }

    fun deleteAll(): Int = delete("1=1")

    fun queryAll(): List<UserInfo> = query("1=1")

}
```

UserInfo.kt

```kotlin
data class UserInfo(var rowid: Long=0, var xuhao: Int=0, var name: String="", 
    var age: Int=0,var height: Long=0, var weight: Float=0f, 
    var married: Boolean=false,var update_time: String="", 
    var phone: String="", var password: String="") {
}
```

调用帮助类存储的示例:

```kotlin
var helper: UserDBHelper = UserDBHelper.getInstance(this) 
//存储信息
btn_save.setOnClickListener {
when (true) {
    et_name.text.isEmpty() -> toast("不能为空")
    et_age.text.isEmpty() -> toast("不能为空") 
    et_height.text.isEmpty() -> toast("不能为空") 
    et_weight.text.isEmpty() -> toast("不能为空")
    else -> {
        val info = UserInfo(name = et_name.text.toString(), 
            age = et_age.text.toString().toInt(),
            height = et_height.text.toString().toLong(),
            weight = et_weight.text.toString().toFloat(), 
            married = bMarried,
            update_time = DateUtil.nowDateTime) 
            helper.insert(info)
            toast("已经存储到SQLite")
        } 
    }
}

//读取数据 
private fun readSQLite() {
    val userArray = helper.queryAll()
    var desc = "查询到${userArray.size}条数据，具体如下：" 
    for (i in userArray.indices) {
        val item = userArray[i]
        desc = "$desc\n第${i+1}条信息为：" +
            "\n 姓名${item.name}"+
            "\n 年龄${item.age}"+
            "\n 升高${item.height}"+
            "\n 体重${item.weight}"+
            "\n 婚否${item.married}"+
            "\n 更新时间${item.update_time}"
     }
     if (userArray.isEmpty()) {
        desc = "查询记录为空" 
     }
     tv_sqlite.text = desc
}
```


#### (3)、优化记住密码功能

略

### 3、文件I/O
#### (1)、文件保存空间
* 获取公共存储空间：Environment.getExternalStoragePublicDirectory
* 获取当前APP的私有存储空间：getExternalFilesDir

```kotlin
class FilePathActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) { 
        super.onCreate(savedInstanceState) 
        setContentView(R.layout.activity_file_path) 
        //获取公共存储空间——输出：/storage/emulated/0/Download
        val publicPath = Environment.getExternalStoragePublicDirectory (Environment.DIRECTORY_DOWNLOADS).toString();
        //获取APP私有存储空间_输出：/storage/emulated/0/Android/data/完整包名/files/Downlaod
        val privatePath = getExternalFilesDir(Environment. DIRECTORY_DOWNLOADS).toString();
    } 
}
```


#### (2)、读写文本

##### A:Java版文本读写工具类

```kotlin
public class FileUtil {
    //存
    public static void saveText(String path, String txt) {
        try {
            FileOutputStream fos = new FileOutputStream(path);
            fos.write(txt.getBytes());
            fos.close();
        } catch (Exception e) {
            e.printStackTrace();
        } 
    }
    
    //取
    public static String openText(String path) {
        String readStr = "";
        try {
            FileInputStream fis = new FileInputStream(path);
            byte[] b = new byte[fis.available()];
            fis.read(b);
            readStr = new String(b);
            fis.close();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return readStr;
    }
}
```

##### B:Kotlin为File类扩展的文本存取工具方法

下列方法定义在 FileReadWrite.kt 中。

函数|含义
---|---
File(file_path).writeText(content)|把文本写入到文件（覆盖已有）
appendText（str）|追加文本内容
readText|读取文本形式的文件
readLines|按行读取文件内容，返回字符串List，文件有多少行，List中就有多少个元素

##### C:完整示例


```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:focusable="true"
    android:focusableInTouchMode="true"
    android:orientation="vertical"
    android:padding="10dp" >

    <Button
        android:id="@+id/btn_delete"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="删除所有文本文件"
        android:textColor="@color/black"
        android:textSize="20sp" />
    
    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="50dp" >

        <TextView
            android:id="@+id/tv_file"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_alignParentLeft="true"
            android:gravity="center"
            android:text="文件名："
            android:textColor="@color/black"
            android:textSize="17sp" />

        <Spinner
            android:id="@+id/sp_file"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_toRightOf="@+id/tv_file"
            android:gravity="left|center"
            android:spinnerMode="dialog" />

        <TextView
            android:id="@+id/tv_spinner"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_toRightOf="@+id/tv_file"
            android:gravity="center"
            android:drawableRight="@drawable/arrow_down"
            android:textColor="@color/black"
            android:textSize="17sp"
            android:visibility="gone" />

    </RelativeLayout>
    
    <TextView
        android:id="@+id/tv_text"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>

```

```kotlin
package com.example.storage

import java.io.File

import android.os.Bundle
import android.os.Environment
import android.support.v7.app.AppCompatActivity
import android.util.Log
import android.view.View

import kotlinx.android.synthetic.main.activity_text_read.*
import org.jetbrains.anko.selector
import org.jetbrains.anko.toast

/**
 * Created by ouyangshen on 2017/9/10.
 */
class TextReadActivity : AppCompatActivity() {
    private lateinit var mPath: String
    private var fileNames: MutableList<String> = mutableListOf()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_text_read)
        sp_file.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE
        btn_delete.setOnClickListener {
            fileNames.forEach { //循环删除文本文件
                val file = File(mPath + it)
                if (!file.delete()) {
                    Log.d(TAG, "file_path=$it, delete failed")
                }
            }
            refreshSpinner()
            toast("已删除临时目录下的所有文本文件")
        }
        mPath = getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + "/"
        refreshSpinner()
    }

    private fun refreshSpinner() {
        fileNames.clear()
        //在该目录下走一圈，得到文件目录树结构
        val fileTree: FileTreeWalk = File(mPath).walk()
        fileTree.maxDepth(1) //需遍历的目录层级为1，即无需检查子目录
                .filter { it.isFile } //只挑选文件，不处理文件夹
                .filter { it.extension == "txt" } //选择扩展名为txt的文本文件
                .forEach { fileNames.add(it.name) } //循环处理符合条件的文件
        if (!fileNames.isEmpty()) {
            tv_spinner.text = fileNames[0]
            tv_spinner.setOnClickListener {
                selector("请选择文本文件", fileNames) { i ->
                    tv_spinner.text = fileNames[i]
                    val file_path = mPath + fileNames[i]
                    //readText读取文本形式的文件内容，readLines按行读取文件内容
                    val content = File(file_path).readText()
                    tv_text.text = "文件内容如下：\n$content"
                }
            }
        } else {
            tv_spinner.text = ""
            tv_spinner.setOnClickListener { }
            tv_text.text = ""
        }
    }

    companion object {
        private val TAG = "TextReadActivity"
    }

}
```

#### (3)、读写图片

像图片等二进制格式的文件，可以通过字节数组的形式写入文件。

由于图像存储比较特殊，牵涉压缩格式和压缩质量，所以，依旧需要使用输出流进行处理。

* 图片保存示例：


```kotlin
fun saveImage(path: String, bitmap: Bitmap) {
    try {
        val file = File(path) 
        //outputStream获取文件输出流；writer获取Writer对象 ；printWriter获取PrintWriter对象
        val fos: OutputStream = file.outputStream() 
        bitmap.compress(Bitmap.CompressFormat.JPEG, 80, fos)
        fos.flush()
        fos.close()
    } catch (e: Exception) {
        e.printStackTrace()
    } 
}
```

* 读取图片文件到内存——字节数组的形式实现


```kotlin
//readBytes的形式读取文件内容
val bytes = File(file_path).readBytes()
//根据字节数组构建Bitmap对象
val bitmap = BitmapFactory.decodeByteArray(bytes, 0, bytes.size)
```

* 读取图片文件到内存——输入流的形式实现

```kotlin
val fis = File(file_path).inputStream() 
val bitmap = BitmapFactory.decodeStream(fis)
fis.close()
```

* 读取图片文件到内存——直接传入路径的形式实现

```kotlin
val bitmap = BitmapFactory.decodeFile(file_path)
```

* 保存截屏的完整示例：

```kotlin
//任意View都可以获取drawingCache对象
val bitmap = ll_info.drawingCache
val file_path = "$mPath${DateUtil.getFormatTime()}.png"

//该函数完整内容就是上面的图片保存示例中的代码
FileUtil.saveImage(file_path, bitmap)
tv_path.text = "用户注册信息图片的保存路径为：\n$file_path"
toast("图片已存入临时目录")
```

#### (4)、遍历文件目录

Kotlin中提供了 **FileTreeWalk** 用来遍历文件目录。通过File(path).walk()可以获取该对象。

```kotlin
var fileNames: MutableList<String> = mutableListOf()
//在该目录下走一圈，得到文件目录树结构
val fileTree: FileTreeWalk = File(mPath).walk()
fileTree.maxDepth(1) //需遍历的目录层级为1，即无需检查子目录
        .filter { it.isFile } //只挑选文件，不处理文件夹
        .filter { it.extension == "txt" } //选择扩展名为txt的文本文件
        .forEach { fileNames.add(it.name) } //循环处理符合条件的文件
```

如果需要过滤多种扩展名的文件类型，可以参考下列代码：

```kotlin
var fileNames: MutableList<String> = mutableListOf() 
val fileTree: FileTreeWalk = File(mPath).walk() 
fileTree.maxDepth(1) 
        .filter { it.isFile } 
        .filter { it.extension in listOf("png","jpg") } //过滤多种类型
        .forEach { fileNames.add(it.name) }
```


### 4、Application全局变量

#### (1)、Application单例化

##### A:Java示例

```kotlin
public class MainApplication extends Application {
    private static MainApplication mApp;
    
    public static MainApplication getInstance() {
        return mApp;
    }
    @Override
    public void onCreate() {
        super.onCreate();
        mApp = this; 
    }
}
```

##### B: 手动声明单例

```kotlin
class MainApplication : Application() {
   override fun onCreate() {
       super.onCreate()
       instance = this
   }

    companion object {
        //方式1：声明时可空，但返回时必须断言非空
        private var instance: MainApplication? = null
        fun instance() = instance!! 

        //方式2：延时初始化，必然非空
        //private lateinit var instance: MainApplication
        //fun instance() = instance
    }
}
```

##### C: 借助 Delegates 的委托属性实现单例

Delegates提供了 notNull 的非空校验方法，使用它之后，开发者不需要手动校验非空。

```kotlin
class MainApplication : Application() {
    override fun onCreate() {
        super.onCreate()
        instance = this
    }
    
    companion object {
        private var instance: MainApplication by Delegates.notNull()
        fun instance() = instance
    }
}
```

##### D:自定义代理行为的单例

前面几种方式虽然判断了非空，但不能保证真正的单例——有且只有一次赋值！

通过下面的自定义代理行为，可以校验重复赋值的行为。并接管委托属性的读写行为。

```kotlin
class MainApplication : Application() {

    override fun onCreate() {
        super.onCreate()
        instance = this
    }

    companion object {
        private var instance: MainApplication by NotNullSingleValueVar()
        fun instance() = instance
    }
}

private class NotNullSingleValueVar<T>() : ReadWriteProperty<Any?, T> {
    private var value: T? = null
    
    override fun getValue(thisRef: Any?, property: KProperty<*>): T{
        return value ?: throw IllegalStateException("application not initialized")
    }
        
    override fun setValue(thisRef: Any?, property: KProperty<*>, va lue: T) {
        this.value = if (this.value == null) value
                    else throw IllegalStateException("application already initialized") 
    }
} 
```



#### (2)、利用Application实现全局变量

适合在Application保存的全局变量包含如下内容：
* 会频繁读写的信息，如用户名，手机号等
* 从网络上获取的临时数据，如logo，商品图片等。可以节省流量，减少用户等待时间
* 容易因频繁分配内存而导致内存泄漏的对象， 如Handler、ThreadPool等



### 5、实战——购物车
#### (1)、需求描述

#### (2)、选项菜单——OptionsMenu

res/menu/menu_option.xml

```kotlin
<menu xmlns:android="http://schemas.android.com/apk/res/android" >

    <item
        android:id="@+id/menu_change_time"
        android:orderInCategory="1"
        android:title="改变时间"/>

    <item
        android:id="@+id/menu_change_color"
        android:orderInCategory="8"
        android:title="改变颜色"/>
    
    <item
        android:id="@+id/menu_change_bg"
        android:orderInCategory="9"
        android:title="改变背景"/>
</menu>
```


`activity_option_menu.xml`

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical" >

    <android.support.v7.widget.Toolbar
        android:id="@+id/tl_head"
        android:layout_width="match_parent"
        android:layout_height="55dp"
        android:background="@color/blue_light"
        app:navigationIcon="@drawable/ic_back" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="10dp" >

        <Button
            android:id="@+id/btn_option"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="点击右上角三点图标，也可按菜单键"
            android:textColor="@color/black"
            android:textSize="17sp" />

        <TextView
            android:id="@+id/tv_option"
            android:layout_width="match_parent"
            android:layout_height="50dp"
            android:gravity="left|center"
            android:textColor="@color/black"
            android:textSize="17sp" />
    </LinearLayout>

</LinearLayout>
```

MenuOptionActivity.kt

```kotlin
class MenuOptionActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_menu_option)
        setSupportActionBar(tl_head)
        tl_head.setNavigationOnClickListener { finish() }

        //注意：如果当前页面继承自AppCompatActivity，并且appcompat版本不低于22.1.0
        //那么调用openOptionsMenu方法可能不会弹出菜单。这应该是Android的一个bug
        btn_option.setOnClickListener { openOptionsMenu() }
        setRandomTime()
    }

    override fun onCreateOptionsMenu(menu: Menu): Boolean {
        menuInflater.inflate(R.menu.menu_option, menu)
        return true
    }

    override fun onOptionsItemSelected(item: MenuItem): Boolean {
        when (item.itemId) {
            R.id.menu_change_time -> setRandomTime()
            R.id.menu_change_color -> tv_option.setTextColor(randomColor)
            R.id.menu_change_bg -> tv_option.setBackgroundColor(randomColor)
        }
        return true
    }

    private fun setRandomTime() {
        val desc = "${DateUtil.nowDateTime} 这里是菜单显示文本"
        tv_option.text = desc
    }

    private val mColorArray = intArrayOf(Color.BLACK, Color.WHITE, Color.RED, Color.YELLOW, Color.GREEN, Color.BLUE, Color.CYAN, Color.MAGENTA, Color.GRAY, Color.DKGRAY)
    private val randomColor: Int
        get() {
            val random = (Math.random() * 10 % 10).toInt()
            return mColorArray[random]
        }

}
```

#### (3)、控件设计
略


#### (4)、关键代码

##### A: 页面跳转

商品列表和购物车之间来回跳转时使用clearTop模式，防止多次向栈中添加内容。

```kotlin
btn_shopping_channel.setOnClickListener {
     startActivity(intentFor<ShoppingChannelActivity>().clearTop())
}
 
iv_cart.setOnClickListener {
     startActivity(intentFor<ShoppingCartActivity>().clearTop())
}
```

##### B: 弹出Menu
省略

##### C: 本地图片缓存

在 ShoppingCartActivity.kt 中

```kotlin
companion object {
        private val TAG = "ShoppingCartActivity"

        //模拟网络数据，初始化数据库中的商品信息
        fun downloadGoods(ctx: Context, isFirst: String, helper: GoodsDBHelper) {
            val path = MainApplication.instance().getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + "/"
            Log.d(TAG, "path=$path")
            if (isFirst == "true") {
                val goodsList = GoodsInfo.defaultList
                for (i in goodsList.indices) {
                    val info = goodsList[i]
                    val rowid = helper.insert(info)
                    info.rowid = rowid
                    //往运行内存写入商品小图
                    val thumb = BitmapFactory.decodeResource(ctx.resources, info.thumb)
                    MainApplication.instance().mIconMap.put(rowid, thumb)
                    val thumb_path = "$path${rowid}_s.jpg"
                    FileUtil.saveImage(thumb_path, thumb)
                    info.thumb_path = thumb_path
                    //往SD卡保存商品大图
                    val pic = BitmapFactory.decodeResource(ctx.resources, info.pic)
                    val pic_path = "$path${rowid}.jpg"
                    FileUtil.saveImage(pic_path, pic)
                    pic.recycle()
                    info.pic_path = pic_path
                    helper.update(info)
                }
            } else {
                val goodsArray = helper.queryAll()
                for (item in goodsArray) {
                    Log.d(TAG, "rowid=${item.rowid}, thumb_path=${item.thumb_path}")
                    val thumb = BitmapFactory.decodeFile(item.thumb_path)
                    MainApplication.instance().mIconMap.put(item.rowid, thumb)
                }
            }
        }
    }
```

##### D:列表的实现

略

##### E:弹窗的实现

略


---
## 九、Kotlin自定义控件

### 1、自定义普通视图
#### (1)、构造对象

 **自定义视图务必要在类名后面增加“@JvmOverloads constructor”** ，表示该类支持被Java代码调用，因为xml布局文件中声明自定义控件之后， **系统是通过SDK中的Java代码找到自定义视图类的** 。所以，自定义视图必须加上给注解，否则报错。


示例：

自定义CustomPagerTab，继承自 PagerTabStrip。实现通过属性改变字体颜色和大小的需求。

```kotlin
//自定义视图务必要在类名后面增加“@JvmOverloads constructor”，因为布局文件中的自定义视图必须兼容Java
class CustomPagerTab @JvmOverloads constructor(context: Context, attrs: AttributeSet?=null) : PagerTabStrip(context, attrs) {
    private var txtColor = Color.BLACK
    private var textSize = 15
    init {
        //初始化时从attrs.xml读取CustomPagerTab的自定义属性
        if (attrs != null) {
            val attrArray = getContext().obtainStyledAttributes(attrs, R.styleable.CustomPagerTab)
            //从布局文件中获取新属性textColor的数值
            txtColor = attrArray.getColor(R.styleable.CustomPagerTab_textColor, txtColor)
            //从布局文件中获取新属性textSize的数值
            textSize = attrArray.getDimensionPixelSize(R.styleable.CustomPagerTab_textSize, textSize)
            attrArray.recycle()
        }
        //应用布局文件的textColor文本颜色
        setTextColor(txtColor)
        //应用布局文件的textSize文本大小
        setTextSize(TypedValue.COMPLEX_UNIT_SP, textSize.toFloat())
    }
}
```


#### (2)、测量尺寸

完整的自定义视图步骤有三：

* 定义构造函数，读取自定义属性并初始化
* 重写测量函数 onMesure
* 重写绘制函数：onDraw 或 dispatchDraw

Java版自定义NoScrollListView

```kotlin
public class NoScrollListView extends ListView {
    public NoScrollListView(Context context) {
         super(context);
    }

    public NoScrollListView(Context context, AttributeSet attrs) {
         super(context, attrs);
    }
    
    public NoScrollListView(Context context, AttributeSet attrs, int de fStyle) {
        super(context, attrs, defStyle);
    }
    
    @Override
    public void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        //设置ListView的高度为最大值
        int expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE>> 2, MeasureSpec.AT_MOST);
        super.onMeasure(widthMeasureSpec, expandSpec);
    }
}
```

Kotlin版自定义NoScrollListView

```kotlin
//自定义控件必须添加@JvmOverloads注解，因为有注解，所以constructor关键字也要显示出来
class NoScrollListView @JvmOverloads constructor(context: Context, attr s: AttributeSet? =null, defStyle: Int=0) : ListView(context, attrs, defStyle) {
    
    public override fun onMeasure(widthMeasureSpec: Int, heightMeasureSpec: Int) {
        //Kotlin中位运算中的右移使用shr标识
        val expandSpec = MeasureSpec.makeMeasureSpec(Integer.MAX_VALUE shr 2, MeasureSpec.AT_MOST)
        super.onMeasure(widthMeasureSpec, expandSpec)
    }
}
```

#### (3)、绘制部件

绘制函数有 onDraw 和 dispatchDraw 两种。

* 容器类型的View，拥有上述两个函数。如 LinearLayout等ViewGroup
* 控件类型的View，仅拥有onDraw. 如 TextView 


假设某个xml文件中包含一个LinearLayout，其中包含一个TextView，那么，其绘制函数执行流程为： **线性布局的OnDraw-->TextView的onDraw-->线性布局的dispatchDraw** 。基于这个执行流程，线性布局onDraw绘制出来的内容，可能会被TextView的onDraw中的内容覆盖。所以， 

—— **容器类型的View最好重写 dispatchDraw** 

自定义圆角TextView:

```kotlin
class RoundTextView @JvmOverloads constructor(context: Context, attrs: AttributeSet?=null, defStyle: Int=0) : TextView(context, attrs, defStyle) {

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        val paint = Paint()
        paint.color = Color.RED 
        paint.strokeWidth = 2f 
        paint.style = Style.STROKE 
        paint.isAntiAlias = true 
        val rectF = RectF(1f, 1f, (this.width - 1).toFloat(), (this.height - 1).toFloat())
        canvas.drawRoundRect(rectF, 10f, 10f, paint)
} }
```

自定义圆角LinearLayout:

```kotlin
class RoundLayout @JvmOverloads constructor(context: Context, attrs: AttributeSet?=null, defStyle: Int=0) : LinearLayout(context, attrs, defStyle) {

    override fun dispatchDraw(canvas: Canvas) {
        super.dispatchDraw(canvas)
        val paint = Paint()
        paint.color = Color.BLUE
        paint.strokeWidth = 2f
        paint.style = Style.STROKE
        paint.isAntiAlias = true
        val rectF = RectF(1f, 1f, (this.width - 1).toFloat(), (this.height - 1).toFloat())
        canvas.drawRoundRect(rectF, 10f, 10f, paint)
    }
}
```

### 2、自定义简单动画

#### (1)、Runnable
Kotlin中声明Runnable对象有四种方式：内部类、匿名内部类、简化类实例、匿名实例

##### A: 内部类

示例：点击之后每隔1秒计数一次

```kotlin
private val handler = Handler()
private var count = 0

inner private class Counter : Runnable {
    override fun run() {
         count++
         tv_result.text = "当前计数值为：$count" 
         handler.postDelayed(this, 1000)
    } 
}

//触发某个按钮的点击事件之后，调用如下内容：
handler.post(Counter())
```

##### B: 匿名内部类形式

```kotlin
//使用关键字object占位，表示这是一个匿名内部类。
private val counter = object : Runnable {
        override fun run() {
            count++
            tv_result.text = "当前计数值为：$count"
            handler.postDelayed(this, 1000)
        }
}

//触发某个按钮的点击事件之后，调用如下内容：
handler.post(counter)
```

内部类和匿名内部类的内部都拥有该类的实例，所以run方法内允许使用this代指当前对象。

##### C:简化类实例

```kotlin
//简化类继承和方法重写的内容，
private val counter = Runnable {
    count++
    tv_result.text = "当前计数值为：$count" 
}

//触发某个按钮的点击事件之后，调用如下内容：
handler.post(counter)
```

这种方式，虽然在写法上简练了，但是， **Runnable后面的大括号中调用 this 时，该this不再代表Runnable实例，而是当前页面的实例。** 

##### D:匿名实例

```kotlin
//写法1：
handler.post(Runnable {
        count++
        tv_result.text = "当前计数值为：$count" }
)

//写法2：如果任务只执行一次，可以使用这种写法 
handler.post {
    count++
    tv_result.text = "当前计数值为:$count" 
}

//写法3：延时执行任务
handler.postDelayed({
    count++
    tv_result.text = "当前计数值为:$count" 
    }, 1000
)
```
匿名实例方式仅能被调用一次。不能被其他地方调用。


#### (2)、ProgressBar

水平 ProgressBar 的 progressDrawable的需要使用 LayerDrawable。

该 LayerDrawable必须包含一个背景层和一个进度层，而且id必须固定为 @android:id/background 、@android:id/progress


示例：

```kotlin
<layer-list xmlns:android="http://schemas.android.com/apk/res/android" >
    <item android:id="@android:id/background">
        <shape>
            <solid android:color="#333333" />
        </shape>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <nine-patch android:src="@drawable/notify_green" />
        </clip>
    </item>
</layer-list>
```

```kotlin
class ProgressBarActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_progress_bar)
        //设置最大进度
        pb_progress.max = 100
        //设置默认进度
        pb_progress.progress = 0
        //设置进度条图形
        pb_progress.progressDrawable = resources.getDrawable(R.drawable.progress_green)
        btn_progress.setOnClickListener {
            //根据输入的进度数值，展示进度条的当前进度
            pb_progress.progress = et_progress.text.toString().toInt()
        }
    }
}
```


#### (3)、自定义文本进度条

图形变化的同时显示对应的文字提示：

```kotlin
class TextProgressBar @JvmOverloads constructor(context: Context, attrs: AttributeSet? = null,defStyle: Int = 0) : ProgressBar(context, attrs, defStyle) {
    var progressText = ""
    private var paint: Paint
    private var textColor = Color.WHITE
    private var textSize = 30f

    init {
        //初始化画笔
        paint = Paint()
        paint.color = textColor
        paint.textSize = textSize
    }

    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)
        val rect = Rect()
        //获得进度文本的矩形边界
        paint.getTextBounds(progressText, 0, progressText.length, rect)
        val x = width / 2 - rect.centerX()
        val y = height / 2 - rect.centerY()
        //把文本内容绘制在进度条的中间位置
        canvas.drawText(progressText, x.toFloat(), y.toFloat(), paint)
    }
}
```


##### 补充：通过TextView背景变更的形式，实现上述效果——Java版
    
```kotlin
 /**
     * 作者：CnPeng
     * 时间：2018/7/19 下午9:04
     * 功用：获取不带圆角的LayerDrawable——即 xml 中的layerList
     * 说明：
     *
     * @param bgColorInt 底层/背景颜色色值。格式 0xfff000。非该格式的可以使用Color.parseColor("#ffc000")、或 getResources().getColor(colorRes)进行格式化
     * @param fgColorInt 上层/前景颜色色值
     * @param layerInset 上层图片相对于下层图片的缩进。该数组必须有四个元素！
     */
    public static LayerDrawable getRectLayerDrawable(@ColorInt int bgColorInt, @ColorInt int fgColorInt, int[] layerInset) {

        if (null == layerInset) {
            layerInset = new int[]{0, 0, 0, 0};
        }

        // TODO: CnPeng 2018/7/19 下午9:18 后期补充数组元素不足四个或者超过四个的处理

        RectShape rectShape = new RectShape();
        ShapeDrawable shapeDrawableBG = new ShapeDrawable(rectShape);
        shapeDrawableBG.getPaint().setStyle(Paint.Style.FILL);
        shapeDrawableBG.getPaint().setColor(bgColorInt);

        ShapeDrawable shapeDrawableFG = new ShapeDrawable(rectShape);
        shapeDrawableFG.getPaint().setStyle(Paint.Style.FILL);
        shapeDrawableFG.getPaint().setColor(fgColorInt);

        LayerDrawable layerDrawable = new LayerDrawable(new Drawable[]{shapeDrawableBG, shapeDrawableFG});
        layerDrawable.setLayerInset(1, layerInset[0], layerInset[1], layerInset[2], layerInset[3]);

        return layerDrawable;
    }

```
#### (4)、实现进度条动画

```kotlin
class ProgressAnimationActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_progress_animation)
        btn_animation.setOnClickListener {
            btn_animation.isEnabled = false
            //延迟50毫秒开始进度条动画
            handler.postDelayed(animation, 50)
        }
    }

    private var mProgress = 0
    private val handler = Handler()
    //定义一个刷新进度条的任务
    private val animation = object : Runnable {
        override fun run() {
            if (mProgress <= 100) {
                tpb_progress.progress = mProgress
                tpb_progress.progressText = "当前处理进度为$mProgress%"
                //当前进度未满100%，则继续进度刷新动画
                handler.postDelayed(this, 50)
                mProgress++
            } else {
                //进度条动画结束，恢复初始进度数值
                mProgress = 0
                btn_animation.isEnabled = true
            }
        }
    }
}
```

### 3、自定义通知栏

[点击查看Notification官方文档](https://developer.android.com/guide/topics/ui/notifiers/notifications?hl=zh-cn)

[ **Notificaiotn在8.0及更高版本中必须声明channel，点击查看** ](https://developer.android.com/training/notify-user/channels)

#### (1)、Notification

构建通知时需要使用 Notification.Builder，其主要方法如下：

函数|含义
---|---
setAutoCancel|点击之后是否自动消失
setWhen|设置推送时间，格式为“时：分”，在通知栏右方显示
setShowWhen|是否显示推送时间
setUsesChronometer|是否显示计数器，为true时不显示推送时间，动态显示从通知达到距离当前的时间间隔，以“分：秒”的格式显示
setSmallIcon|状态栏中的小图标
setTicker|状态栏中的提示文本
setLargeIcon|通知栏大图标
setContentTitle|通知栏标题文本
setContentText|通知栏文本内容
setSubText|通知栏附加说明文本，位于内容文本下方。调用该方法后，setProgress将失效
setProgress|设置进度条与当前进度。进度条位于标题文本和内容文本中间。
setNumber|设置通知栏右下方的数字，可与setProgress联合使用，表示当前进度
setContentInfo|设置通知栏右下方的文本，若调用该方法，则setNumber将失效
setContentIntent|设置内容的延迟意图——PendingIntent，在点击该通知时触发该意图。通常调用PendingIntent的getActivity方法获得延时意图对象，getActivity表示点击之后的跳转页面。
setDeleteIntent|设置删除通知时的跳转意图——PendingIntent
build|构建通知对象。


##### A: 静止的简单消息

setSmallIcon必须调用，否则不显示通知

```kotlin
class NotifySimpleActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_simple)
        btn_send_simple.setOnClickListener {
            val title = et_title.text.toString()
            val message = et_message.text.toString()
            sendSimpleNotify(title, message)
            toast("简单消息已推送到通知栏。点击该消息回到首页")
        }
    }

    private fun sendSimpleNotify(title: String, message: String) {
        //声明一个点击通知栏时触发的动作意图
        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        val notifyBuilder: Notification.Builder
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //8.0及以后版本必须声明channel
            val notifyChannel = NotificationChannel(UUID.randomUUID().toString(), "简单通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(notifyChannel)
            val notifyChannelID: String = notifyChannel.id
            notifyBuilder = Notification.Builder(this, notifyChannelID)
        } else {
            notifyBuilder = Notification.Builder(this)
        }
        val clickIntent = intentFor<MainActivity>()
        val piClick = PendingIntent.getActivity(this,
                R.string.app_name, clickIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        //开始构建简单消息的各个参数
        val notify = notifyBuilder.setContentIntent(piClick)
                .setAutoCancel(true)
                .setSmallIcon(R.drawable.ic_app)
                //.setSubText("这里是副本")
                .setTicker("简单消息来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message).build()
        //获取系统的通知管理器
        notifyMgr.notify(R.string.app_name, notify)
    }
}
```

##### B: 带有计时器和进度条的通知消息
* setWhen和setUsesChronometer不能同时调用,
* setNumber和setContentInfo不能同时调用

```kotlin
class NotifySimpleActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_simple)
        btn_send_simple.setOnClickListener {
            val title = et_title.text.toString()
            val message = et_message.text.toString()
            sendSimpleNotify(title, message)
            toast("简单消息已推送到通知栏。点击该消息回到首页")
        }
    }

    private fun sendSimpleNotify(title: String, message: String) {
        //声明一个点击通知栏时触发的动作意图
        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        val notifyBuilder: Notification.Builder
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //8.0及以后版本必须声明channel
            val notifyChannel = NotificationChannel(UUID.randomUUID().toString(), "简单通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(notifyChannel)
            val notifyChannelID: String = notifyChannel.id
            notifyBuilder = Notification.Builder(this, notifyChannelID)
        } else {
            notifyBuilder = Notification.Builder(this)
        }
        val clickIntent = intentFor<MainActivity>()
        val piClick = PendingIntent.getActivity(this,
                R.string.app_name, clickIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        //开始构建简单消息的各个参数
        val notify = notifyBuilder.setContentIntent(piClick)
                .setAutoCancel(true)
                .setSmallIcon(R.drawable.ic_app)
                //.setSubText("这里是副本")
                .setTicker("简单消息来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message).build()
        //获取系统的通知管理器
        notifyMgr.notify(R.string.app_name, notify)
    }
}
```

#### (2)、大视图通知

大视图通知比普通通知具有更大的视图，使用 NotificationCompat.Builder 进行构建。

相对于 Notification.Builder ，NotificationCompat.Builder 新增了如下函数：

函数|含义
---|---
setDefaults|设置通知到达时的提醒方式。可同时设置多种方式，使用 or 链接
addAction|在通知底部增加动作按钮，可以同时添加多个，依次从左向右排列。接收三个参数，第一个为按钮图片资源id，第二个为按钮文本内容，第三个为动作意图
setStyle|通知的大视图风格。有三种，分别为：NotificationCompat.BigTextStyle——大文本、NotificationCompat.BigPictureStyle——大图片、NotificationCompat.InboxStyle——收件箱


通知到达时的提醒方式：

方式|含义
---|---
Notification.DEFAULT_ALL|默认所有
Notification.DEFAULT_SOUND|铃声
Notification.DEFAULT_VIBRATE|震动
Notification.DEFAULT_LIGHTS|闪光

通知的大视图风格——Style

Style|内容设置方法|含义
---|---|---
|NotificationCompat.BigTextStyle|bigText|设置文本
|NotificationCompat.BigPictureStyle|bigPicture|设置大图
|NotificationCompat.InboxStyle|addLine|添加一行消息文本，可多次调用，每调用一次就添加一条文本

三种大视图Style共有的方法为：

函数|含义
---|---
setBigContentTitle|设置大视图通知的标题文本
setSummaryText|设置大视图通知的摘要文本，位于底部按钮的上方

注意：大视图通知并不一定总是完全展开显示。多数情况会想普通通知一样显示窄窄的一条，只有该通知处于通知栏顶部并且用户手动下拉时，才会完全展示。


收件箱风格：

![收件箱风格](https://images.gitee.com/uploads/images/2019/0103/205928_6bd9bff4_930142.png "屏幕截图.png")

大图片风格：

![大图片风格](https://images.gitee.com/uploads/images/2019/0103/210014_741d24ce_930142.png "屏幕截图.png")

大文字风格：

![大文字风格](https://images.gitee.com/uploads/images/2019/0103/210047_498dcdf1_930142.png "屏幕截图.png")

```kotlin
class NotifyLargeActivity : AppCompatActivity() {
    private val styles = listOf("大文字风格", "大图片风格", "收件箱风格")
    private var type = 0

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_large)
        sp_style.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE
        tv_spinner.text = styles[0]
        tv_spinner.setOnClickListener {
            selector("请选择大视图风格", styles) { i ->
                tv_spinner.text = styles[i]
                type = i
            }
        }

        btn_send_large.setOnClickListener {
            getStyleAndSend(et_title.text.toString(), et_message.text.toString(), type)
        }
    }

    private fun getStyleAndSend(title: String, message: String, type: Int) {
        var style: NotificationCompat.Style? = null
        when (type) {
            0 -> { //声明大文本风格
                style = NotificationCompat.BigTextStyle()
                style.setBigContentTitle(title)
                style.setSummaryText(message)
                style.bigText("这是一条大文字风格的通知消息")
            }
            1 -> { //声明大图片风格
                style = NotificationCompat.BigPictureStyle()
                style.setBigContentTitle(title)
                style.setSummaryText(message)
                style.bigLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.icon_financer))
                style.bigPicture(BitmapFactory.decodeResource(resources, R.drawable.icon_sunshine))
            }
            2 -> { //声明收件箱风格
                style = NotificationCompat.InboxStyle()
                style.setBigContentTitle(title)
                style.setSummaryText(message)
                style.addLine("天青色等烟雨，而我在等你")
                style.addLine("炊烟袅袅升起，隔江千万里")
                style.addLine("在瓶底书汉隶仿前朝的飘逸")
            }
        }
        sendLargeNotify(title, message, style)
        toast("大视图消息已推送到通知栏。")
    }

    private fun sendLargeNotify(title: String, message: String, style: NotificationCompat.Style?) {
        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        val builder: NotificationCompat.Builder
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel(UUID.randomUUID().toString(), "大视图通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(channel)
            builder = NotificationCompat.Builder(this, channel.id)
        } else {
            //大视图通知需要通过NotificationCompat.Builder来构建
            builder = NotificationCompat.Builder(this)
        }

        //声明一个“取消”按钮的动作意图
        val cancelIntent = intentFor<NotifyLargeActivity>()
        val piCancel = PendingIntent.getActivity(this,
                R.string.app_name, cancelIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        //声明一个“前往”按钮的动作意图
        val confirmIntent = intentFor<NotifyLargeActivity>()
        val piConfirm = PendingIntent.getActivity(this,
                R.string.app_name, confirmIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        builder.setSmallIcon(R.drawable.ic_app)
                .setTicker("大视图消息来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message)
                .setDefaults(Notification.DEFAULT_ALL) //设置大视图通知的提醒方式
                .setStyle(style) //设置大视图通知的风格类型
                .addAction(R.drawable.icon_cancel, "取消", piCancel) //添加取消按钮
                .addAction(R.drawable.icon_confirm, "前往", piConfirm) //添加前往按钮
        val notify = builder.build()
        notifyMgr.notify(1, notify)
    }
}
```
#### (3)、三种特殊的通知类型
进度通知、浮动通知、锁屏通知

##### A: 进度通知
进度通知指能在通知栏动态刷新进度的消息通知。

随书示例代码中实际使用了一个一个setProgress的通知+runnbale实现，这种虽然实现了进度变更，但是会一直有通知到达的提醒。


##### B: 浮动通知
* 是指不离开当前页面并在屏幕顶部悬挂显示的通知。
* 适用于5.0及以上版本。
* 实现核心是Notification.Builder的setFullScreenIntent

CnPeng 实测并不怎么好使...具体测试情况为：

* AS自带模拟器——Android9系统中都不生效！！！一直以普通通知的形式显示
* OppoR15 8.1系统中开启各种通知权限之后可以实现悬浮。
* 小米6x 8.1（MiUI 10.2） 可以正常使用

 **注意：ChannelID 必须是一个字符串常量，否则，在通知管理界面中会出现一大堆通知名称**，前面使用UUID变量作为id时就会出现好多好多通知名称。 


##### C: 锁屏通知

* 锁屏界面依旧显示的通知消息。
* 适用于5.0及以上版本
* 核心是Notification.Builder的setVisibility

锁屏通知的显示方式

方式|含义
---|---
Notification.VISIBILITY_PRIVATE|显示基本信息，如通知的图标，但隐藏通知的全部内容
Notification.VISIBILITY_PUBLIC|显示通知的全部内容
Notification.VISIBILITY_SECRET|不显示任何内容，包括图标


##### D:以上三种特殊通知的完整示例代码
 
```kotlin
class NotifySpecialActivity : AppCompatActivity() {
    private val handler = Handler()
    private var count = 0
    private var refreshNotify: ProgressNotify? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_special)
        btn_notify_progress.setOnClickListener { v -> clickNotify(v) }
        btn_notify_float.setOnClickListener { v -> clickNotify(v) }
        btn_lock_private.setOnClickListener { v -> clickNotify(v) }
        btn_lock_public.setOnClickListener { v -> clickNotify(v) }
    }

    private fun clickNotify(v: View) {
        val title = et_title.text.toString()
        val message = et_message.text.toString()
        when (v.id) {
            R.id.btn_notify_progress -> startProgressNotify(title, message)
            R.id.btn_notify_float -> sendFloatNotify(title, message)
            R.id.btn_lock_private -> sendLockNotify(title, message, false)
            R.id.btn_lock_public -> sendLockNotify(title, message, true)
        }
    }

    //开始播放进度通知的刷新动画
    private fun startProgressNotify(title: String, message: String) {
        count = 0
        refreshNotify = ProgressNotify(title, message)
        handler.post(refreshNotify)
        toast("已推送到进度通知。")
    }

    //定义一个持续发送进度通知的任务内部类，当进度超过100%时任务停止
    private inner class ProgressNotify(private val title: String, private val message: String) : Runnable {
        override fun run() {
            sendProgressNotify(title, message, count)
            count++
            if (count <= 100) {
                handler.postDelayed(refreshNotify, 200)
            }
        }
    }

    //发送单次进度通知。若要不断刷新进度，需外部多次调用该方法
    private fun sendProgressNotify(title: String, message: String, progress: Int) {
        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

        val builder: Notification.Builder

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //8.0及以后版本必须声明channel
            val notifyChannel = NotificationChannel(UUID.randomUUID().toString(), "简单通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(notifyChannel)
            val notifyChannelID: String = notifyChannel.id
            builder = Notification.Builder(this, notifyChannelID)
        } else {
            builder = Notification.Builder(this)
        }

        val clickIntent = intentFor<MainActivity>()
        val piClick = PendingIntent.getActivity(this,
                R.string.app_name, clickIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        builder.setContentIntent(piClick)
                .setAutoCancel(true)
                .setSmallIcon(R.drawable.ic_app)
                .setTicker("进度通知来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message)
                .setProgress(100, progress, false) //设置进度条的当前进度
        val notify = builder.build()
        notifyMgr.notify(R.string.app_name, notify)
    }

    private fun sendFloatNotify(title: String, message: String) {
        toast("已推送到浮动通知。")

        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        val builder: Notification.Builder

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //8.0及以后版本必须声明channel
            val notifyChannel = NotificationChannel("99", "a浮窗通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(notifyChannel)
            val notifyChannelID: String = notifyChannel.id
            builder = Notification.Builder(this, notifyChannelID)
        } else {
            builder = Notification.Builder(this)
        }

        val clickIntent = intentFor<MainActivity>()
        val piClick = PendingIntent.getActivity(this,
                R.string.app_name, clickIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        builder.setContentIntent(piClick)
                .setAutoCancel(true)
                .setSmallIcon(R.drawable.ic_app)
                .setTicker("浮动通知来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message)
                .setFullScreenIntent(piClick, true) //设置浮动窗的点击事件
        val notify = builder.build()
        notifyMgr.notify(R.string.app_name, notify)
    }

    private fun sendLockNotify(title: String, message: String, visibile: Boolean) {

        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        val builder: Notification.Builder

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            //8.0及以后版本必须声明channel
            val notifyChannel = NotificationChannel("100", "锁屏通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(notifyChannel)
            val notifyChannelID: String = notifyChannel.id
            builder = Notification.Builder(this, notifyChannelID)
        } else {
            builder = Notification.Builder(this)
        }

        val clickIntent = intentFor<MainActivity>()
        val piClick = PendingIntent.getActivity(this,
                R.string.app_name, clickIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        builder.setContentIntent(piClick)
                .setAutoCancel(true)
                .setSmallIcon(R.drawable.ic_app)
                .setTicker("锁屏通知来啦")
                .setWhen(System.currentTimeMillis())
                .setLargeIcon(BitmapFactory.decodeResource(resources, R.drawable.ic_app))
                .setContentTitle(title)
                .setContentText(message)
        //设置锁屏通知的可见性
        if (Build.VERSION.SDK_INT < Build.VERSION_CODES.LOLLIPOP) {
            toast("锁屏通知需要5.0以上系统支持。")
            return
        } else {
            toast("已推送锁屏通知。")
            builder.setVisibility(if (visibile) Notification.VISIBILITY_PUBLIC else Notification.VISIBILITY_PRIVATE)
        }
        val notify = builder.build()
        //获取系统的通知管理器
        notifyMgr.notify(R.string.app_name, notify)
    }
}
```


#### (4)、远程视图：RemoteViews

* 借助 RemoteViews可以修改通知消息的布局文件。
* 核心方法是 Notification.Builder中的setContent(), 接收的参数就是RemoteViews,设置后将替换默认的布局

##### A: RemoteViews 的使用限制：
* RemoteViews主要用于通知部件和桌面部件的布局，
* 仅支持少数几种控件，如TextView、 ImageView 、 Button 、ImageButton、ProgressBar、Chronometer(计时器)、AnalogClock(模拟时钟)
* RemoteViews不可以直接获取和设置控件信息，只能通过该对象的set方法来设置控件信息。

##### B:RemoteViews的常用函数

函数|含义
---|---
构造函数|构建对象，第一个参数为包名，第二个参数为使用的布局id
setViewVisibility|指定控件是否可见
setViewPadding|指定控件的间距
setTextViewText|设置Tv/Bt的文本内容
setTextViewTextSize|指定文本大小
setTextColor|指定文本颜色
setTextViewCompoundDrawables|设置Tv/Bt周边的图片
setImageViewResource|imageView/imageButton 的图片资源
setImageViewBitmap|imageView/imageButton 的图片对象
setChronometer|计时器信息
setProgressBar|进度条信息
setOnClickPendingIntent|控件的点击事件

需要动态改变控件的属性时调用上述方法，如果不需要动态改变的直接从布局文件中编写即可。

##### C: 修改默认的通知视图
构建完 RemoteViews对象之后，修改默认通知视图的方式有：

* Notification.Builder中的setContent() ——适用于API24之前
* Notification.Builder中的setCustomContentView()——适用于API24之后
* Notification对象的contentView 属性——上面两个方法最终也是将值赋给该属性


##### D: 示例代码

在下面 折叠通知中 包含了全部代码

#### (5)、自定义折叠式通知

前面介绍的大视图通知，可以看做是一种折叠式通知，处于折叠状态时显示普通通知内容，展开之后显示大视图通知内容。

实现这种通知布局时也是需要借助 RemoteViews。

只是在修改默认大视图模式的视图时使用的是：

* Notification的bigContentView属性，API16时加入
* Notification.Builder中的setCustomBigContentView——适用于API24之后


示例代码如下：

notify_music.xml:

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:minHeight="64dp"
    android:background="@color/grey"
    android:orientation="horizontal" >

    <ImageView
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:scaleType="fitCenter"
        android:src="@drawable/tt" />

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_marginLeft="3dp"
        android:layout_marginRight="3dp"
        android:layout_weight="4"
        android:orientation="vertical" >

        <ProgressBar
            android:id="@+id/pb_play"
            style="?android:attr/progressBarStyleHorizontal"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:max="100"
            android:progress="10" />

        <TextView
            android:id="@+id/tv_play"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:textColor="@color/black"
            android:textSize="17sp" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:orientation="vertical" >

        <Chronometer
            android:id="@+id/chr_play"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="1"
            android:textColor="@color/black"
            android:gravity="center" />

        <Button
            android:id="@+id/btn_play"
            android:layout_width="match_parent"
            android:layout_height="0dp"
            android:layout_weight="2"
            android:background="#ffc"
            android:text="暂停"
            android:textColor="#ff0000"
            android:textSize="15sp" />
    </LinearLayout>

</LinearLayout>
```

notify_expand.xml:

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@color/grey"
    android:orientation="vertical" >

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:minHeight="50dp"
        android:orientation="horizontal" >

        <ImageView
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:scaleType="fitCenter"
            android:src="@drawable/tt" />

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_marginLeft="3dp"
            android:layout_marginRight="3dp"
            android:layout_weight="4"
            android:orientation="vertical" >

            <ProgressBar
                android:id="@+id/pb_play"
                style="?android:attr/progressBarStyleHorizontal"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                android:max="100"
                android:progress="10" />

            <TextView
                android:id="@+id/tv_play"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                android:textColor="@color/black"
                android:textSize="17sp" />
        </LinearLayout>

        <Chronometer
            android:id="@+id/chr_play"
            android:layout_width="0dp"
            android:layout_height="match_parent"
            android:layout_weight="1"
            android:textColor="@color/black"
            android:gravity="center" />
    </LinearLayout>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal" >

        <Button
            android:id="@+id/btn_back"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="返回"
            android:textColor="@color/white"
            android:textSize="15sp" />

        <Button
            android:id="@+id/btn_play"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="暂停"
            android:textColor="@color/white"
            android:textSize="15sp" />
    </LinearLayout>

</LinearLayout>
```

NotifyCustomActivity.kt:

```kotlin
class NotifyCustomActivity : AppCompatActivity() {
    lateinit var mLocaBraodCastManager: LocalBroadcastManager

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_custom)
        val PAUSE_EVENT = resources.getString(R.string.pause_event)
        
        initClickEvent(PAUSE_EVENT)
    }

    private fun initClickEvent(PAUSE_EVENT: String) {
        btn_send_custom.setOnClickListener {
            val contentView = getNotifyMusic(this, PAUSE_EVENT,
                    et_song.text.toString(), true, 50, SystemClock.elapsedRealtime())
            sendCustomNotify(this, et_song.text.toString(), contentView, null)
            toast("歌曲${et_song.text}已推送到通知栏")
        }

        btn_send_expand.setOnClickListener {
            val contentView = getNotifyMusic(this, PAUSE_EVENT,
                    et_song.text.toString(), true, 50, SystemClock.elapsedRealtime())

            val bigContentView = getNotifyExpand(this, PAUSE_EVENT,
                    et_song.text.toString(), true, 50, SystemClock.elapsedRealtime())
            sendCustomNotify(this, et_song.text.toString(), contentView, bigContentView)
            toast("歌曲${et_song.text}已推送到折叠式通知")
        }
    }

    private fun sendCustomNotify(ctx: Context, song: String, contentView: RemoteViews, bigContentView: RemoteViews?) {
        val notifyMgr = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        val builder: Notification.Builder
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel("101", "自定义通知视图的通知", NotificationManager.IMPORTANCE_DEFAULT)
            notifyMgr.createNotificationChannel(channel)
            builder = Notification.Builder(ctx, channel.id)
        } else {
            builder = Notification.Builder(ctx)
        }

        //声明一个点击通知消息时触发的动作意图
        val intent = ctx.intentFor<MainActivity>()
        val contentIntent = PendingIntent.getActivity(ctx,
                R.string.app_name, intent, PendingIntent.FLAG_UPDATE_CURRENT)

        builder.setContentIntent(contentIntent)
                .setContent(contentView) //采用自定义的通知布局
                .setTicker(song)
                .setSmallIcon(R.drawable.tt_s)

        val notify = builder.build()
        //如果builder中没有配置setContent，也可以在此配置，两者最终都指向一个变量
        //        notify.contentView = contentView
        if (bigContentView != null) {
            //展开后的自定义通知
            notify.bigContentView = bigContentView
        }
        notifyMgr.notify(R.string.app_name, notify)
    }

    //获取播放器的通知栏布局
    private fun getNotifyMusic(ctx: Context, actionStr: String, song: String, isPlay: Boolean, progress: Int, time: Long): RemoteViews {
        //从notify_music.xml布局文件构造远程视图对象
        val notify_music = RemoteViews(ctx.packageName, R.layout.notify_music)
        if (isPlay) {
            notify_music.setTextViewText(R.id.btn_play, "暂停")
            notify_music.setTextViewText(R.id.tv_play, song + "正在播放")
            notify_music.setChronometer(R.id.chr_play, time, "%s", true)
        } else {
            notify_music.setTextViewText(R.id.btn_play, "继续")
            notify_music.setTextViewText(R.id.tv_play, song + "暂停播放")
            notify_music.setChronometer(R.id.chr_play, time, "%s", false)
        }

        notify_music.setProgressBar(R.id.pb_play, 100, progress, false)

        //设置暂停/继续按钮的点击动作对应的广播事件
        val pIntent = Intent(actionStr)
        val piPause = PendingIntent.getBroadcast(
                ctx, R.string.app_name, pIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        notify_music.setOnClickPendingIntent(R.id.btn_play, piPause)
        return notify_music
    }

    //获取折叠视图展开后的通知栏布局
    private fun getNotifyExpand(ctx: Context, event: String, song: String, isPlay: Boolean, progress: Int, time: Long): RemoteViews {
        //从notify_expand.xml布局文件构造远程视图对象
        val notify_expand = RemoteViews(ctx.packageName, R.layout.notify_expand)
        if (isPlay) {
            notify_expand.setTextViewText(R.id.btn_play, "暂停")
            notify_expand.setTextViewText(R.id.tv_play, song + "正在播放")
            notify_expand.setChronometer(R.id.chr_play, time, "%s", true)
        } else {
            notify_expand.setTextViewText(R.id.btn_play, "继续")
            notify_expand.setTextViewText(R.id.tv_play, song + "暂停播放")
            notify_expand.setChronometer(R.id.chr_play, time, "%s", false)
        }
        notify_expand.setProgressBar(R.id.pb_play, 100, progress, false)

        //设置播放按钮的点击动作对应的广播事件
        val pIntent = Intent(event)
        val piPause = PendingIntent.getBroadcast(
                ctx, R.string.app_name, pIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        notify_expand.setOnClickPendingIntent(R.id.btn_play, piPause)

        //设置返回按钮的点击动作对应的跳转事件
        val bIntent = Intent(ctx, NotifyCustomActivity::class.java)
        val piBack = PendingIntent.getActivity(ctx,
                R.string.app_name, bIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        notify_expand.setOnClickPendingIntent(R.id.btn_back, piBack)
        return notify_expand
    }
}
```

效果图：

![](https://images.gitee.com/uploads/images/2019/0104/163114_896560d6_930142.png "屏幕截图.png")

 **注意，实际运行后并未得到上图效果：测试机包括 OppoR15——8.1， 小米6x——8.1，模拟器——9.0** 

### 4、Service服务

服务需要在清单文件中的<service>节点注册。

#### (1)、普通方式启动服务

基本写法：

```kotlin
val intent = Intent(ctx, NormalService::class.java)
startService(intent)
```

不需要关闭的Service

```kotlin
//使用了anko中的扩展函数
startService<NormalService>("request_content" to et_request.text.toString())
```

需要关闭的service

```kotlin
//使用了anko的扩展函数——关闭service时也需要开启时的intent
val intent = intentFor<NormalService> (Pair("request_content", et_request.text.toString()))
startService(intent)


//关闭service——传入开启时的intent
btn_stop.setOnClickListener {
    if (null！=intent) {
        stopService(intent)
    }
}

```

完整示例：

```kotlin
class ServiceNormalActivity : AppCompatActivity() {
    var intentNormal: Intent? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_service_normal)
        
        btn_start.setOnClickListener {
            //第一种写法，参数名和参数值使用关键字to隔开
            intentNormal = intentFor<NormalService>("request_content" to et_request.text.toString())
           
            //第二种写法，利用Pair把参数名和参数值进行配对
            //intentNormal = intentFor<NormalService>(Pair("request_content", et_request.text.toString()))
           
            startService(intentNormal)
           
            //虽然Anko库集成了startService的简化写法，但是一般不这么调用。
            //因为服务启动之后是需要停止的，按Anko的简化写法会无法停止服务
            //除非无需在代码中停止该服务，才可以采用Anko的简化写法
            //startService<NormalService>("request_content" to et_request.text.toString())
            toast("普通服务已启动")
        }
        
        btn_stop.setOnClickListener {
            if (intentNormal != null) {
                stopService(intentNormal)
                toast("普通服务已停止")
            }
        }
        
        Normal.tv_normal = findViewById<TextView>(R.id.tv_normal)
    }

    companion object Normal {
        private var tv_normal: TextView? = null
        private var mDesc = ""
        //静态方法showText给NormalService内部调用
        fun showText(desc: String) {
            mDesc = "${mDesc}${DateUtil.nowTime} $desc\n"
            //如果tv_normal非空才设置文本，否则不设置文本
            tv_normal?.text = mDesc
        }
    }
}
```

```kotlin
class NormalService : Service() {

    override fun onCreate() {
        ServiceNormalActivity.showText("创建服务")
        super.onCreate()
    }

    override fun onStartCommand(intent: Intent, flags: Int, startid: Int): Int {
        val bundle = intent.extras
        val request_content = bundle.getString("request_content")
        ServiceNormalActivity.showText("启动服务，收到请求内容：${request_content}")
        return Service.START_STICKY
    }

    override fun onDestroy() {
        ServiceNormalActivity.showText("停止服务")
        super.onDestroy()
    }

    override fun onBind(intent: Intent): IBinder? = null

}
```
#### (2)、绑定方式开启服务

* bindService() ——绑定的方式开启服务
* unbindService() ——绑定的形式关闭服务

```kotlin
class ServiceBindActivity : AppCompatActivity() {

    private var mBindService: BindService? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_service_bind)
        Bind.tv_bind = findViewById<TextView>(R.id.tv_bind)

        btn_start_bind.setOnClickListener {
            val intentBind = intentFor<BindService>("request_content" to et_request.text.toString())
            val bindFlag = bindService(intentBind, mFirstConn, Context.BIND_AUTO_CREATE)
            Log.d(TAG, "bindFlag=" + bindFlag)
            toast("服务已绑定启动")
        }

        btn_unbind.setOnClickListener {
            if (mBindService != null) {
                unbindService(mFirstConn)
                mBindService = null
                toast("服务已解除绑定")
            }
        }
    }

    private val mFirstConn = object : ServiceConnection {
        //获取服务对象时的操作
        override fun onServiceConnected(name: ComponentName, service: IBinder) {
            //如果服务运行于另外一个进程，则不能直接强制转换类型，
            //否则会报错“java.lang.ClassCastException: android.os.BinderProxy cannot be cast to...”
            mBindService = (service as BindService.LocalBinder).service
            Log.d(TAG, "onServiceConnected")
        }

        //无法获取到服务对象时的操作
        override fun onServiceDisconnected(name: ComponentName) {
            mBindService = null
            Log.d(TAG, "onServiceDisconnected")
        }
    }

    companion object Bind {
        private val TAG = "ServiceBindActivity"
        private var tv_bind: TextView? = null
        private var mDesc = ""

        fun showText(desc: String) {
            mDesc = "$mDesc${DateUtil.nowTime} $desc\n"
            tv_bind?.text = mDesc
        }
    }
}
```


```kotlin
class BindService : Service() {
    private val mBinder = LocalBinder()

    inner class LocalBinder : Binder() {
        val service: BindService
            get() = this@BindService
    }

    override fun onCreate() {
        ServiceBindActivity.showText("创建服务")
        super.onCreate()
    }

    override fun onBind(intent: Intent): IBinder? {
        val bundle = intent.extras
        val request_content = bundle.getString("request_content")
        ServiceBindActivity.showText("绑定服务，收到请求内容：${request_content}")
        return mBinder
    }

    override fun onUnbind(intent: Intent): Boolean {
        ServiceBindActivity.showText("解绑服务")
        return true
    }
}
```

#### (3)、两种开启方式的比较_CnPeng

start方式开启的服务特点

* 服务一旦开启，长期后台运行，服务和开启者(Activity)没有任何的关系，开启者退出了，服务还是继续在后台长期运行
* 开启者(Activity)不可以调用服务里面的方法
* 在系统设置界面里面可以观察到

 bind的方式开启服务

* 如果开启者(Activity)退出了，服务也会跟着挂掉
* 开启者(Activity)可以间接的利用中间人调用服务里面的方法
* 在系统设置界面看不到的.


绑定方式开启的服务的生命周期 :

* onCreate  onBind  onUnBind  onDestroy  ,都只是一次的操作
* 绑定方式开启的服务中虽然也有onStart  和 onStartCommond ,但因为不是start方式开启,所以不会调用

Start方式开启的服务的生命周期:

* OnCreate  onStart/onStartCommond  onDestroy  ,onstart/onStartCommond可以复用




#### (3)、推送服务到前台——显示到通知栏中

##### A: 服务的使用注意事项
在前面的示例代码中，为了观察服务的运行情况，所以强行调用了XXActivity中的静态方法,但这样很不安全，因为，页面随时可能会被销毁。所以——

* 实际应用时，服务不要依托于界面。
* 但是，android 系统允许服务以通知栏的形式进行展示——也就是所谓的显示到前台

##### Ｂ: 如何将服务显示到前台
服务内部的下列两个函数可以决定是否将服务显示到通知栏中：

函数|含义
---|---
startForeground|把当前服务切换到前台运行——显示到通知栏，第一个参数表示通知的编号，第二个参数为Notification对象。
stopForeground|停止前台运行。true时清除通知，false则不清除

##### C: 应用场景

服务在前台运行的常见场景就是音乐播放器。

即使用户离开了播放器页面，手机也能继续在后台播放音乐。同时还可以通过下拉通知栏的方式查看播放进度以及控制播放。 **注意这个示例有问题——因为频繁的使用通知更新，所以会一直有通知到达的提醒** 

示例如下：

`activity_notify_service.xml`

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp" >

    <EditText
        android:id="@+id/et_song"
        android:layout_width="match_parent"
        android:layout_height="40dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入歌曲名称"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <Button
        android:id="@+id/btn_send_service"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="开始播放音乐"
        android:textColor="#000000"
        android:textSize="17sp" />

</LinearLayout>
```

NotifyServiceActivity.kt

```kotlin
class NotifyServiceActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_notify_service)
        var bPlay = false

        btn_send_service.setOnClickListener {
            bPlay = !bPlay
            val intent = intentFor<MusicService>("is_play" to bPlay,
                    "song" to et_song.text.toString())
            if (bPlay) {
                startService(intent)
                toast("歌曲${et_song.text}已在通知栏开始播放")
                btn_send_service.text = "停止播放音乐"
            } else {
                stopService(intent)
                toast("歌曲${et_song.text}已从通知栏清除")
                btn_send_service.text = "开始播放音乐"
            }
        }
    }
}
```

MusicService.kt

```kotlin
class MusicService : Service() {

    private val mBinder = LocalBinder()
    private var pauseReceiver: PauseReceiver? = null
    private var mSong: String = ""
    private var PAUSE_EVENT = ""
    private var isPlay = true
    private var mBaseTime: Long = 0
    private var mPauseTime: Long = 0
    private var mProgress = 0
    private val handler = Handler()

    private val playTask = object : Runnable {
        override fun run() {
            if (isPlay) {
                if (mProgress < 100) {
                    mProgress += 2
                } else {
                    mProgress = 0
                }
                handler.postDelayed(this, 1000)
            }
            val notify = getNotify(this@MusicService, PAUSE_EVENT, mSong, isPlay, mProgress, mBaseTime)
            //持续刷新通知栏上的播放进度
            startForeground(2, notify)
        }
    }

    override fun onBind(intent: Intent): IBinder? = mBinder

    private fun getNotify(ctx: Context, event: String, song: String, isPlay: Boolean, progress: Int, time: Long): Notification {
        val pIntent = Intent(event)
        //getActivity会跳转到某个页面；getBroadcast触发的事件仅作用于当前类内部
        val nIntent = PendingIntent.getBroadcast(ctx,
                R.string.app_name, pIntent, PendingIntent.FLAG_UPDATE_CURRENT)
        val notify_music = RemoteViews(ctx.packageName, R.layout.notify_music)

        if (isPlay) {
            notify_music.setTextViewText(R.id.btn_play, "暂停")
            notify_music.setTextViewText(R.id.tv_play, "${song}正在播放")
            notify_music.setChronometer(R.id.chr_play, time, "%s", true)
        } else {
            notify_music.setTextViewText(R.id.btn_play, "继续")
            notify_music.setTextViewText(R.id.tv_play, "${song}暂停播放")
            notify_music.setChronometer(R.id.chr_play, time, "%s", false)
        }
        notify_music.setProgressBar(R.id.pb_play, 100, progress, false)
        notify_music.setOnClickPendingIntent(R.id.btn_play, nIntent)

        val builder: Notification.Builder

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            val channel = NotificationChannel("102", "前台服务——通知形式展现", NotificationManager.IMPORTANCE_DEFAULT)
            val notifyManager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
            notifyManager.createNotificationChannel(channel)
            builder = Notification.Builder(ctx, channel.id)
        } else {
            builder = Notification.Builder(ctx)
        }

        val intent = ctx.intentFor<MainActivity>()
        val cIntent = PendingIntent.getActivity(ctx,
                R.string.app_name, intent, PendingIntent.FLAG_UPDATE_CURRENT)

        return builder.setContentIntent(cIntent)
                .setContent(notify_music)
                .setTicker(song)
                .setSmallIcon(R.drawable.tt_s).build()
    }

    //CnPeng 2019/1/4 9:12 PM 重复startService时该函数复用，而不会重复调用。也就是说，暂停之后再播放只处罚playTask中的操作
    override fun onStartCommand(intent: Intent, flags: Int, startid: Int): Int {
        mBaseTime = SystemClock.elapsedRealtime()
        Log.e("服务——mBaseTime初始化", mBaseTime.toString())

        isPlay = intent.getBooleanExtra("is_play", true)
        mSong = intent.getStringExtra("song")
        handler.postDelayed(playTask, 200)
        return Service.START_STICKY
    }

    override fun onCreate() {
        PAUSE_EVENT = resources.getString(R.string.pause_event)
        pauseReceiver = PauseReceiver()
        registerReceiver(pauseReceiver, IntentFilter(PAUSE_EVENT))
        super.onCreate()
    }

    override fun onDestroy() {
        unregisterReceiver(pauseReceiver)
        super.onDestroy()
    }

    //定义一个处理播放/暂停事件的广播接收器内部类
    inner class PauseReceiver : BroadcastReceiver() {
        override fun onReceive(context: Context, intent: Intent?) {
            if (intent != null) {
                isPlay = !isPlay
                if (isPlay) {
                    handler.postDelayed(playTask, 200)
                    if (mPauseTime > 0) {
                        val gap = SystemClock.elapsedRealtime() - mPauseTime
                        Log.e("服务——gap", gap.toString())

                        mBaseTime += gap

                        Log.e("服务——mBaseTime", mBaseTime.toString())
                    }
                } else {
                    mPauseTime = SystemClock.elapsedRealtime()
                    Log.e("服务——mPauseTime", mPauseTime.toString())
                }
            }
        }
    }

    inner class LocalBinder : Binder() {
        val service: MusicService
            get() = this@MusicService
    }
}
```


### 5、实战：APP生鲜团购

#### (1)、需求分析
略

#### (2)、振动器--vibrate

使用震动器时首先需要在清单文件中声明权限：

```kotlin
<uses-permission android:name="android.permission.VIBRATE" />
```

触发震动的基本写法

```kotlin
val vibrator = getSystemService(Context.VIBRATOR_SERVICE) as Vibrator
vibrator.vibrate(3000)
```

为了方便调用可以给Context扩展函数：

```kotlin
//扩展该函数之后，外部只需要通过context对象调用getVibrator即可
fun Context.getVibrator() : Vibrator {
   return getSystemService(Context.VIBRATOR_SERVICE) as Vibrator
}

//调用示例：
context.getVibrator().vibrate(3000)
```

还可以为Context扩展属性：

```kotlin
//为Context扩展属性，同时为该属性扩展默认的get方法，外部调用该属性时本质是调用了get()
val Context.vibrator : Vibrator
            get() = getSystemService(Context.VIBRATOR_SERVICE) as Vibrator

//外部调用示例
context.vibrator.vibrate(3000)
```

其他扩展示例：

```kotlin
val Context.notifier: NotificationManager
    get() = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager

val Context.downloader: DownloadManager
    get() = getSystemService(Context.DOWNLOAD_SERVICE) as DownloadManager


val Context.locator: LocationManager
    get() = getSystemService(Context.LOCATION_SERVICE) as LocationManager

//链接管理器
val Context.connector: ConnectivityManager
    get() = getSystemService(Context.CONNECTIVITY_SERVICE) as ConnectivityManager

//电话管理器
val Context.telephone: TelephonyManager
    get() = getSystemService(Context.TELEPHONY_SERVICE) as TelephonyManager

//无线管理器
val Context.wifi: WifiManager
    get() = getSystemService(Context.WIFI_SERVICE) as WifiManager 

//闹钟
val Context.alarm: AlarmManager
    get() = getSystemService(Context.ALARM_SERVICE) as AlarmManager

//音频
val Context.audio: AudioManager
    get() = getSystemService(Context.AUDIO_SERVICE) as AudioManager
```


#### (3)、控件设计
略

#### (4)、关键代码
略

---
## 十、Kotlin实现网络通信

### 1、多线程技术

#### （1）、Thread与消息传递

自定义线程类示例：

```kotlin
private inner class PlayThread : Thread() {
    override fun run() {
     //...
    } 
}
```
   
简化版线程

```kotlin
Thread {
    //􏳨􏳩􏳪􏳫􏳬􏳭􏳮􏳯􏳰􏳱􏳲􏳳􏳴 
}.start()
```


随书示例代码：

```kotlin
class MessageActivity : AppCompatActivity() {
    private var bPlay = false
    private val BEGIN = 0 //开始播放新闻
    private val SCROLL = 1 //持续滚动新闻
    private val END = 2 //结束播放新闻
    private val news = arrayOf("北斗三号卫星发射成功，定位精度媲美GPS", "美国赌城拉斯维加斯发生重大枪击事件", "日本在越南承建的跨海大桥未建完已下沉", "南水北调功在当代，数亿人喝上长江水", "马克龙呼吁重建可与中国匹敌的强大欧洲")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_message)

        //指定文本视图内部文本的对齐方式为靠左且靠右对齐
        tv_message.gravity = Gravity.LEFT or Gravity.BOTTOM
        //指定文本视图的显示行数为8行
        tv_message.setLines(8)
        //指定文本视图的最大行数为8行
        tv_message.maxLines = 8
        //指定文本视图内部文本的移动方式为滚动
        tv_message.movementMethod = ScrollingMovementMethod()

        btn_start_message.setOnClickListener {
            if (!bPlay) {
                bPlay = true
                //线程第一种写法的调用方式，通过具体的线程类进行构造。
                //注意每个线程实例只能启动一次，不能重复启动。
                //若要多次执行该线程的任务，则需每次都构造新的线程实例。
                //PlayThread().start()

                //线程的第二种写法，采用匿名实例的形式。第二种写法无需显式构造
                Thread {
                    //发送“开始播放新闻”的消息类型
                    handler.sendEmptyMessage(BEGIN)
                    while (bPlay) {
                        //休眠两秒，模拟获取突发新闻的网络延迟
                        Thread.sleep(2000)
                        //调用Message的obtain方法，获得一个消息实例
                        val message = Message.obtain()
                        message.what = SCROLL
                        message.obj = news[(Math.random() * 30 % 5).toInt()]
                        //发送“持续滚动新闻”的消息类型
                        handler.sendMessage(message)
                    }

                    //CnPeng 2019/1/7 9:26 AM 此处为啥先置为false,发送关闭消息之后再置为true呢？点击之后本身就false啊？
                    bPlay = true
                    Thread.sleep(2000)
                    //发送“结束播放新闻”的消息类型
                    handler.sendEmptyMessage(END)
                    bPlay = false
                }.start()
            }
        }
        btn_stop_message.setOnClickListener { bPlay = false }
    }
    
    //线程的第一种写法，继承Thread类并重载run方法
//    private inner class PlayThread : Thread() {
//        override fun run() {
//            handler.sendEmptyMessage(BEGIN)
//            while (bPlay) {
//                Thread.sleep(2000)
//                val message = Message.obtain()
//                message.what = SCROLL
//                message.obj = news[(Math.random() * 30 % 5).toInt()]
//                handler.sendMessage(message)
//            }
//            bPlay = true
//            Thread.sleep(2000)
//            handler.sendEmptyMessage(END)
//            bPlay = false
//        }
//    }

    //自定义的处理器类，区分三种消息类型，给tv_message显示不同的文本内容
    private val handler = object : Handler() {
        override fun handleMessage(msg: Message) {
            val desc = tv_message.text.toString()
            tv_message.text = when (msg.what) {
                BEGIN -> "$desc\n${DateUtil.nowTime} 下面开始播放新闻"
                SCROLL -> "$desc\n${DateUtil.nowTime} ${msg.obj}"
                else -> "$desc\n${DateUtil.nowTime} 新闻播放结束，谢谢观看"
            }
        }
    }
}
```

示例运行效果：

![](https://images.gitee.com/uploads/images/2019/0107/093017_8159928b_930142.gif "1.gif")



#### （2）、ProgressDialog
##### A: 水平ProgressDialog

Java版：

```kotlin
ProgressDialog dialog = new ProgressDialog(this); 
dialog.setTitle("请稍候");
dialog.setMessage("正在努力加载。。。"); dialog.setMax(100);
dialog.setProgressStyle(ProgressDialog.STYLE_HORIZONTAL);
dialog.show();
```

Kotlin版——Anko

```kotlin
dialog = indeterminateProgressDialog("正在努力加载页面", "请稍候")
dialog!!.show()
```

在更新进度时，调用 `dialog!!.progress = 10`

##### B: 圆圈类型的进度

Java版本

```java
ProgressDialog dialog = new ProgressDialog(this);
dialog.setTitle("请稍候"); 
dialog.setMessage("正在努力加载");
dialog.setProgressStyle(ProgressDialog.STYLE_SPINNER);
dialog.show();
```

Kotlin版本——Anko

```kotlin
dialog = progressDialog("正在努力加载页面", "请稍候")
dialog!!.show()
```

#### （3）、异步任务doAsync 和 doAsyncResult

##### A: doAsync

* doAsync 用来标识牵涉界面交互的子线程
* uiThread 用来标识将子线程中的数据传递给主线程

##### B: doAsyncResult
有些时候，App需要启动多个线程，然后在代码中对这些线程对象进行调度，从而动态的控制每个线程的状态。此时需要使用 doAsyncResult。

* 有返回值，返回的就是异步线程对象。通过调用该线程对象的相应方法可以人为干预线程的运行功能。

##### C : 完整示例代码

注意：doAsyncResult 那一块示例好像有问题。点击《红楼梦》 之后dialog会一直卡在那里，直到延时任务跑完。

```kotlin
class AsyncTaskActivity : AppCompatActivity() {
    private lateinit var dialog: ProgressDialog
    private val books = listOf("三国演义", "西游记", "红楼梦")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_async_task)

        sp_style.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE
        tv_spinner.text = books[0]
        tv_spinner.setOnClickListener {
            selector("请选择要加载的小说", books) { i ->
                tv_spinner.text = books[i]
                when (i) {
                    0 -> {
                        dialogCircle(books[i])
                    }
                    1 -> {
                        dialogBar(books[i])
                    }
                    else -> {
                        progressBar(books[i])
                    }
                }
            }
        }
    }

    //展示在圆圈进度对话框
    private fun dialogCircle(book: String) {
        dialog = indeterminateProgressDialog("${book}页面加载中……", "稍等")
        doAsync {
            // 睡眠200毫秒模拟网络通信处理
            for (ratio in 0..20) Thread.sleep(200)
            //处理完成，回到主线程在界面上显示书籍加载结果
            uiThread { finishLoad(book) }
        }
    }

    //展示在长条进度对话框
    private fun dialogBar(book: String) {
        dialog = progressDialog("${book}页面加载中……", "稍等")
        doAsync {
            for (ratio in 0..20) {
                Thread.sleep(200)
                //处理过程中，实时通知主线程当前的处理进度
                uiThread { dialog.progress = ratio * 100 / 20 }
            }
            uiThread { finishLoad(book) }
        }
    }

    //展示在进度条ProgressBar
    private fun progressBar(book: String) {
        //构造异步处理需要执行的代码段longTask，返回字符串类型
        val longTask: (AnkoAsyncContext<Context>.() -> String) = {
            for (ratio in 0..20) Thread.sleep(200)
            "加载好了" //这是longTask处理完成的返回结果
        }
        //doAsyncResult返回一个异步线程对象
        val future: Future<String> = doAsyncResult(null, longTask)
        for (count in 0..10) {
            if (future.isDone) {
                //isDone是否完成，isCancelled是否取消，get获取处理结果
                tv_async.text = "您要阅读的《${book}》已经${future.get()}"
                pb_async.progress = 100
                break
            }
            pb_async.progress = count * 100 / 10
            Thread.sleep(1000)
        }
    }

    private fun finishLoad(book: String) {
        tv_async.text = "您要阅读的《$book》已经加载完毕"
        //如果进度对话框还在显示，则关闭进度对话框
        if (dialog.isShowing) dialog.dismiss()
    }
}
```

### 2、访问HTTP接口

#### (1)、移动数据JSON格式

Android自带Json格式的处理工具包，主要提供了 JsonObject 和 JsonArray

##### A: JsonObject

常用函数解析

函数|含义
---|---
构造函数|根据提供的json字符串创建一个JsonObject对象
getJsonObject|获取指定名称的JsonObject对象
getString| 获取指定名称的字符串
getInt|获取指定名称的整型数
getDouble|获取指定名称的Double数据
getBoolean|获取指定名称的Boolean
getJsonArray|获取指定名称的JsonArray
put|添加一个JSONObject对象
toString|把当前JsonObejct输出为一个JSON串

##### B: JsonArray

常用函数解析

函数|含义
---|---
length|获取jsonArray数组对象的长度
getJsonObject|获取JsonArray中指定位置的JsonObject对象
put|向JsonArray中添加一个JsonObject对象

##### C: Json串构造和解析示例

使用JsonObject / JsonArray 手动构造和解析 

```kotlin
class JsonParseActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_json_parse)
        
        btn_construct_json.setOnClickListener { tv_json.text = jsonStr }
        btn_parser_json.setOnClickListener { tv_json.text = parserJson(jsonStr) }
        btn_traverse_json.setOnClickListener { tv_json.text = traverseJson(jsonStr) }
    }

    //构造json串
    private val jsonStr: String
        get() {
            val obj = JSONObject()
            obj.put("name", "地址信息")
            val array = JSONArray()
            for (i in 0..2) {
                val item = JSONObject()
                item.put("item", "第${i+1}个元素")
                array.put(item)
            }
            obj.put("list", array)
            obj.put("count", array.length())
            obj.put("desc", "这是测试串")
            return obj.toString()
        }

    //解析json串
    private fun parserJson(jsonStr: String?): String {
        val obj = JSONObject(jsonStr)
        var result = "name=${obj.getString("name")}\n" +
                "desc=${obj.getString("desc")}\n" +
                "count=${obj.getInt("count")}\n"
        val listArray = obj.getJSONArray("list")
        //util表示的范围是左闭右开区间。以下语句相当于for (i in 0..listArray.length() - 1)
        for (i in 0 until listArray.length()) {
            val item = listArray.getJSONObject(i)
            result = "${result}\titem=${item.getString("item")}\n"
        }
        return result
    }

    //遍历json串
    private fun traverseJson(jsonStr: String?): String {
        var result = ""
        val obj = JSONObject(jsonStr)
        val it = obj.keys()
        while (it.hasNext()) { // 遍历JSONObject
            var key = it.next().toString()
            result = "${result}key=$key, value=${obj.getString(key)}\n"
        }
        return result
    }
}
```
#### (2)、JSON转数据类—GSON
谷歌提供了GSON用来实现JSON串的自动解析。

使用Gson时首先需要在 module的build.gradle文件中新增依赖

```kotlin
    implementation "com.google.code.gson:gson:2.8.2"
```

##### A： 常用函数

函数|含义
---|---
toJson|把数据对象转换为Json串
fromJson| 把Json串转换为数据对象。
 fromJson的调用格式|fromJson(json串, 数据类名::class.java)

##### B: 转换示例

```kotlin
class JsonConvertActivity : AppCompatActivity() {
    private val user = UserInfo(name="阿四", age=25, height=160L, weight=45.0f, married=false)
    //把数据类的对象直接转换成json格式
    private val json = Gson().toJson(user)

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_json_convert)
        btn_origin_json.setOnClickListener { tv_json.text = "json串内容如下：\n$json" }
        btn_convert_json.setOnClickListener {
            //利用Gson包直接将json串解析为对应格式的数据类对象
            val newUser = Gson().fromJson(json, UserInfo::class.java)
            tv_json.text = "从json串解析而来的用户信息如下：" +
                    "\n\t姓名=${newUser.name}" +
                    "\n\t年龄=${newUser.age}" +
                    "\n\t身高=${newUser.height}" +
                    "\n\t体重=${newUser.weight}" +
                    "\n\t婚否=${newUser.married}"
        }
    }
}
```
UserInfo.kt 数据类

```kotlin
data class UserInfo(var name: String="", var age: Int=0, var height: Long=0L, var weight: Float=0F, var married: Boolean=false)
```
#### (3)、HTTP接口调用

如果直接使用HttpURLConnection 调用Http接口，需要考虑如下因素：

* HTTP的请求方式是什么？Post、Put、Get、Delete
* HTTP的连接超时时间是多少？响应超时时间是多少？
* HTTP的头部语言和浏览器信息该怎么设置？
* HTTP传输的数据内容采用哪种编码？
* HTTP的响应数据如果是压缩过的，该如何解压？
* HTTP输入输出流需要注意哪些事项？
* HTTP如何分块传输较大的数据信息？

基于上述内容，所以出现了HTTP请求的三方库(框架)：**HttpClient、Android-Async-Http、Volley、OkHttp、Retrofit**

Kotlin把网络交互看做是跟文件读写一样的I/O 操作。后端服务器地址就像一个文件路径，这样， 请求服务器的数据就像读取文件内容一样。同时，文本分为文本文件和二进制文件，对应的，HTTP接口就有获取文本数据和获取二进制数据两种。这样， 网络请求就被简化为网络数据的保存和读取了。

##### A： 常用函数

函数|含义
---|---
URL(网络地址)|获取网络地址对象
readText|获取文本形式的应答数据
readBytes|获取二进制形式的应答数据。如图片、音频等大文件

网络访问是一个耗时操作，所以需要放到单独的线程中，这样就需要借助 doAsync 和 uiThread

##### B: 示例: 根据经纬度获取详细地址——核心代码

```kotlin
    private val mapsUrl = "http://maps.google.cn/maps/api/geocode/json?latlng={0},{1}&sensor=true&language=zh-CN"

    //在主线程中把定位信息连同地址信息都打印到界面上
    private fun findAddress(location: Location, address: String) {
        tv_location.text = "$mLocation\n定位对象信息如下： " +
                "\n\t时间：${DateUtil.nowDateTime}" +
                "\n\t经度：${location.longitude}，纬度：${location.latitude}" +
                "\n\t高度：${location.altitude}米，精度：${location.accuracy}米" +
                "\n\t地址：$address"
    }

    //位置监听器侦听到定位变化事件，就调用该函数请求详细地址
    private fun setLocationText(location: Location?) {
        if (location != null) {
            doAsync {
                //根据经纬度数据从谷歌地图获取详细地址信息
                val url = MessageFormat.format(mapsUrl, location.latitude, location.longitude)
                val text = URL(url).readText()
                val obj = JSONObject(text)
                val resultArray = obj.getJSONArray("results")
                var address = ""
                //解析json字符串，其中formatted_address字段为具体地址名称
                if (resultArray.length() > 0) {
                    val resultObj = resultArray.getJSONObject(0)
                    address = resultObj.getString("formatted_address")
                }
                //获得该地点的详细地址之后，回到主线程把地址显示在界面上
                uiThread { findAddress(location, address) }
            }
        } else {
            tv_location.text = "$mLocation\n暂未获取到定位对象"
        }
    }
```

##### C: 完整示例代码

**注意:** 下列示例代码中如果运行在6.0以上系统，需要先添加手动申请定位、网络权限的代码

```kotlin
class HttpRequestActivity : AppCompatActivity() {
    private var mLocation = ""
    private val handler = Handler()
    private var bLocationEnable = false
    private val mapsUrl = "http://maps.google.cn/maps/api/geocode/json?latlng={0},{1}&sensor=true&language=zh-CN"

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_http_request)
        initLocation()

        //CnPeng 2019/1/10 10:06 AM 这一段没看明白是啥意思，为啥又加这么一个handler？
        handler.postDelayed(mRefresh, 100)
    }

    private fun initLocation() {
        //CnPeng 2019/1/10 9:58 AM 注意，在6.0之后的版本中，需要先检测是否有定位权限，否则 locater 会空
        val bestProvider: String = locator.getBestProvider(criteria, true)
        if (bestProvider.isNotEmpty() && locator.isProviderEnabled(bestProvider)) {
            tv_location.text = "正在获取${bestProvider}定位对象"
            mLocation = "定位类型=$bestProvider"
            beginLocation(bestProvider)
            bLocationEnable = true
        } else {
            tv_location.text = "\n${bestProvider}定位不可用"
            bLocationEnable = false
        }
    }

    //在主线程中把定位信息连同地址信息都打印到界面上
    private fun findAddress(location: Location, address: String) {
        tv_location.text = "$mLocation\n定位对象信息如下： " +
                "\n\t时间：${DateUtil.nowDateTime}" +
                "\n\t经度：${location.longitude}，纬度：${location.latitude}" +
                "\n\t高度：${location.altitude}米，精度：${location.accuracy}米" +
                "\n\t地址：$address"
    }

    //位置监听器侦听到定位变化事件，就调用该函数请求详细地址
    private fun setLocationText(location: Location?) {
        if (location != null) {
            doAsync {
                //根据经纬度数据从谷歌地图获取详细地址信息
                val url = MessageFormat.format(mapsUrl, location.latitude, location.longitude)
                val text = URL(url).readText()
                val obj = JSONObject(text)
                val resultArray = obj.getJSONArray("results")
                var address = ""
                //解析json字符串，其中formatted_address字段为具体地址名称
                if (resultArray.length() > 0) {
                    val resultObj = resultArray.getJSONObject(0)
                    address = resultObj.getString("formatted_address")
                }
                //获得该地点的详细地址之后，回到主线程把地址显示在界面上
                uiThread { findAddress(location, address) }
            }
        } else {
            tv_location.text = "$mLocation\n暂未获取到定位对象"
        }
    }

    private fun beginLocation(method: String) {
        locator.requestLocationUpdates(method, 300, 0f, mLocationListener)
        val location = locator.getLastKnownLocation(method)
        setLocationText(location)
    }

    // 位置监听器
    private val mLocationListener = object : LocationListener {
        override fun onLocationChanged(location: Location) {
            setLocationText(location)
        }

        override fun onProviderDisabled(arg0: String) {}

        override fun onProviderEnabled(arg0: String) {}

        override fun onStatusChanged(arg0: String, arg1: Int, arg2: Bundle) {}
    }

    private val mRefresh = object : Runnable {
        override fun run() {
            if (!bLocationEnable) {
                initLocation()
                handler.postDelayed(this, 1000)
            }
        }
    }

    override fun onDestroy() {
        locator.removeUpdates(mLocationListener)
        super.onDestroy()
    }
}
```

System.kt 工具类

```kotlin
//获取定位管理器
val Context.locator: LocationManager
    get() = getSystemService(Context.LOCATION_SERVICE) as LocationManager

//获取定位规则
val Context.criteria: Criteria
    get() {
        var cri = Criteria()
        cri.accuracy = Criteria.ACCURACY_FINE
        cri.isAltitudeRequired = true
        cri.isBearingRequired = true
        cri.isCostAllowed = true
        cri.powerRequirement = Criteria.POWER_LOW
        return cri
    }
```

清单文件中添加权限

```kotlin
 <!-- 互联网 -->
    <uses-permission android:name="android.permission.INTERNET" />
<!-- 定位 -->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
```
#### (4)、HTTP图片获取

##### A: 实现思路:

* 通过URL类构建地址对象
* 然后在 doAsync中调用 readBytes 得到图片的字节数组
* 利用 BitmapFactory的decodeByteArray将图片字节数组转换为位图对象
* 利用File对象的writeBytes 可以根据图片的字节数组保存为本地图片

##### B: 示例——动态获取图片验证码

```kotlin
class HttpImageActivity : AppCompatActivity() {
    private val imageUrl = "http://222.77.181.14/ValidateCode.aspx?r="

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_http_image)
        iv_image_code.setOnClickListener { getImageCode() }
        getImageCode()
    }

    //获取网络上的图片验证码
    private fun getImageCode() {
        iv_image_code.isEnabled = false
        doAsync {
            val url = "$imageUrl${DateUtil.getFormatTime()}"
            Log.e("图片地址", url)
            val bytes = URL(url).readBytes()

            //把字节数组解码为位图数据
            val bitmap = BitmapFactory.decodeByteArray(bytes, 0, bytes.size)

            //也可通过下面三行代码把字节数组写入文件，即生成一个图片文件
            val path = getExternalFilesDir(Environment.DIRECTORY_DOWNLOADS).toString() + "/"
            val file_path = "$path${DateUtil.getFormatTime()}.png"
            File(file_path).writeBytes(bytes)

            //获得验证码图片数据，回到主线程把验证码显示在界面上
            uiThread { finishGet(bitmap) }
        }
    }

    //在主线程中显示获得到的验证码图片
    private fun finishGet(bitmap: Bitmap) {
        iv_image_code.setImageBitmap(bitmap)
        iv_image_code.isEnabled = true
    }
}
```

### 3、文件下载

#### (1)、下载管理器 DownloadManager

URL 对象的 readBytes 可以方便的获取小图片(如验证码图片)，但是有诸多限制，如:

* 无法断点续传
* 只能转码为图片，难以转为其他文件
* 不是真正意义上的下载，无法设置下载参数

Android从2.3(API 9) 开始提供专门的下载工具——DownLoadManager，用于统一管理下载操作。通过系统服务  Context.DOWNLOAD_SERVICE 可以获取下载器的对象。

使用 DownLoadManager 时可以分为三个步骤：构建下载请求，执行下载操作，查询下载进度。

##### A： 构建下载请求——Request

要想使用下载功能，首先需要构建一个下载请求。请求中指明从哪里下载、下载参数是什么、下载的文件保存到哪里等信息。这个下载请求就时DownLoadManager内部的Request。该类的常用函数如下：

* Request常用函数

函数|含义
---|---
构造函数|指定从哪个地址下载内容
setAllowedNetworkTypes|  指定允许下载的网络类型。多个类型使用位运算 or  链接。具体取值类型，后面会介绍
setDestinationInExternalFilesDir|下载文件在本地的保存路径。如果目录中存在同名文件，系统会将下载的文件重命名，添加后缀-1，-2 等
addRequestHeader|给HTTP添加请求头
setMimeType|设置下载文件的媒体类型，一般无须设置，默认是服务器返回的类型
setTitle|设置通知栏上的消息标题。如果不设置，默认显示下载的文件名
setDescription|设置通知栏上的消息描述，如果不设置，默认显示系统估算的下载时间。
setVisibleInDownloadsUi|设置是否显示在系统的下载页面中
setNotificationVisibility|设置通知栏的下载任务可见类型。取值参考后面有介绍。

* DownloadManager.Request.setAllowedNetworkTypes的取值类型：

类型|含义
---|---
NETWORK_WIFI |WIFI网络
NETWORK_MOBILE |移动网络
NETWORK_BLUETOOTH |蓝牙

* setNotificationVisibility取值类型：

取值|含义
---|---
`VISIBILITY_HIDEEN`|隐藏
`VISIBILITY_VISIBLE`|下载时可见，下载完成后消失
`VISIBILITY_VISIBLE_NOTIFY_COMPLETED`|下载进行时和完成后都可见
`VISIBILITY_VISIBLE_NOTIFY_ONLY_COMPLETION`|只有下载完成后可见

##### B: 进行下载操作

构建完下载请求之后才能进行DownloadManager的下载操作。主要函数包括：

函数|含义
---|---
enqueue| 将下载请求加入到任务队列中，排队等待下载。返回本次下载任务的编号
remove|取消指定编号的下载任务
restartDownload|重新开始指定编号的下载任务
openDownloadedFile|打开下载完成的文件
getMimeTypeForDownloadedFile|获取已下载文件的媒体类型
query|根据查询请求获取符合条件的结果游标集

##### C: 查询下载进度——Query

虽然下载进度可以在通知栏中查看，但APP本身也想监测进度时，可以通过DownloadManager 的 query() 函数实现。该函数接收一个 Query 对象，返回结果集的游标 Cursor ，该游标集对象中包含完整的下载任务信息。

* Query类的常用函数

函数|含义
---|---
setFilterById|根据编号过滤下载任务
setFilterByStatus| 根据状态进行过滤
setOnlyIncludeVisibleInDownloadsUi| 是否只包含在系统下载页面中可见的任务
orderBy| 结果集按照指定字段排序

* query() 函数返回Cursor的主要字段

DownlaodManager的下载状态|含义
---|---
STATUS_PENDING | 挂起/正在等待
STATUS_RUNNING|运行中
STATUS_PAUSED| 暂停
STATUS_SUCCESSFUL|成功
STATUS_FAILED|失败

DownlaodManager的下载字段|含义
---|---
`COLUMN_LOCAL_FILENAME`|下载文件的本地存储路径（已废弃）
`COLUMN_LOCAL_URI`|下载文件的本地存储路径(正常使用，未废弃)
`COLUMN_MEDIA_TYPE`|下载文件的媒体类型
`COLUMN_TOTAL_SIZE_BYTES`|下载文件的总大小
`COLUMN_BYTES_DOWNLOADED_SO_FAR`|已下载文件的大小
`COLUMN_STATUS`| 下载状态，取值为前一个表中的内容

Android7.0之后增强了文件访问权限，`DownloadManager.COLUMN_LOCAL_FILENAME` 被废弃。所以，在7.0以上的手机中访问该字段会触发 java.lang.SecurityException 异常。此时，如果需要获取下载文件的路径，需要使用 `DownloadManager.COLUMN_LOCAL_URI`

#### (2)、下载相关的三个广播

此外，系统的下载服务还提供了三种下载事件，开发者可以通过监听对应的广播消息从而进行相应的处理。这三种下载事件的处理过程说明如下：

##### A: 下载完成

下载完成时系统会发出：`DownloadManager.ACTION_DOWNLOAD_COMPLETE `广播，该广播对应的具体值为：`"android.intent.action.DOWNLOAD_COMPLETE"`。

所以，可以注册一个该广播的接收器，判断当前下载任务是否已经完成。

##### B: 下载进行时的通知栏点击事件
下载过程中，如果用户点击了通知栏中的下载任务，系统会发出 `DownloadManager. ACTION_NOTIFICATION_CLICKED` 广播，该广播的具体值为：`android.intent.action.DOWNLOAD_ NOTIFICATION_CLICKED`。

所以，可以注册一个该广播的接收器，执行相应的点击事件，如跳转到该任务的下载进度页面。

##### C: 下载完成后的通知栏点击事件

下载完成之后，点击通知栏中的下载任务会触发 Intent.ACTION_VIEW（即 浏览行为），对于文件浏览行为，系统会根据媒体类型自动寻找对应的APP打开文件。

因此，开发者如果要控制此时的点击行为，可以调用 Request 对象的 setMimeType 来设置媒体类型。这样Android系统就会根据指定的类型去打开该APP。

###  (3): 下载示例： 
* activity_download_apk.xml


```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="vertical"
    android:padding="5dp">

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="40dp" >

        <TextView
            android:id="@+id/tv_apk_url"
            android:layout_width="wrap_content"
            android:layout_height="match_parent"
            android:layout_alignParentLeft="true"
            android:gravity="center"
            android:text="请选择要下载的安装包 "
            android:textColor="@color/black"
            android:textSize="17sp" />

        <!--<Spinner-->
            <!--android:id="@+id/sp_apk_url"-->
            <!--android:layout_width="match_parent"-->
            <!--android:layout_height="match_parent"-->
            <!--android:layout_toRightOf="@+id/tv_apk_url"-->
            <!--android:gravity="left|center"-->
            <!--android:spinnerMode="dialog" />-->

        <TextView
            android:id="@+id/tv_spinner"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_toRightOf="@+id/tv_apk_url"
            android:gravity="center"
            android:drawableRight="@drawable/arrow_down"
            android:textColor="@color/black"
            android:textSize="17sp"
            tools:visibility="visible"
            android:visibility="gone" />
    </RelativeLayout>

    <TextView
        android:id="@+id/tv_apk_result"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@color/black"
        android:textSize="17sp" />
</LinearLayout>
```

* DownloadApkActivity.kt


**注意：**个别手机不显示通知栏的下载进度。实测 小米6X 可以显示。oppoR15 不显示，模拟器 Android9 不显示。

```kotlin
class DownloadApkActivity : AppCompatActivity() {
    private val apkNames = listOf(
            "支付宝", "微信", "手机QQ")
    private val apkUrls = listOf(
            "https://qd.myapp.com/myapp/qqteam/AndroidQQ/mobileqq_android.apk",
            "https://qd.myapp.com/myapp/qqteam/AndroidQQ/mobileqq_android.apk",
            "https://qd.myapp.com/myapp/qqteam/AndroidQQ/mobileqq_android.apk"
    )

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_download_apk)

        Download.tv_apk_result = findViewById<TextView>(R.id.tv_apk_result)

        //        sp_apk_url.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE

        tv_spinner.text = apkNames[0]
        tv_spinner.setOnClickListener {
            selector("请选择要下载的安装包", apkNames) { i ->
                tv_spinner.text = apkNames[i]
                toast("${apkNames[i]}正在下载，详情见通知栏")

                //声明下载任务的请求对象
                val down = Request(Uri.parse(apkUrls[i]))
                //指定通知栏上的标题文本
                down.setTitle("${apkNames[i]}下载信息")
                //指定通知栏上的描述文本
                down.setDescription("${apkNames[i]}安装包正在下载")
                //手机连上移动网络或者连上WIFI时均可进行下载操作
                down.setAllowedNetworkTypes(Request.NETWORK_MOBILE or Request.NETWORK_WIFI)
                //指定下载通知栏在下载中与完成后都可见
                down.setNotificationVisibility(Request.VISIBILITY_VISIBLE_NOTIFY_COMPLETED)

                //指定显示在系统的下载页面上
                down.setVisibleInDownloadsUi(true)
                //指定下载文件在本地的保存路径
                down.setDestinationInExternalFilesDir(this,
                        Environment.DIRECTORY_DOWNLOADS, "$i.apk")

                //把下载请求添加到下载队列中
                //这里利用扩展属性实现了自动获取下载管理器实例
                //有关扩展属性的介绍参见第9章的“9.5.2 开始热身：震动器Vibrator”
                downloadId = downloader.enqueue(down)
            }
        }
    }

    // 接收下载完成事件
    class DownloadCompleteReceiver : BroadcastReceiver() {
        override fun onReceive(context: Context, intent: Intent) {
            if (intent.action == DownloadManager.ACTION_DOWNLOAD_COMPLETE && Download.tv_apk_result != null) {
                //获取下载任务的编号
                val downId = intent.getLongExtra(DownloadManager.EXTRA_DOWNLOAD_ID, -1)
                Log.d(TAG, " download complete! id : $downId, downloadId=$downloadId")
                Download.tv_apk_result?.visibility = View.VISIBLE
                Download.tv_apk_result?.text = "${DateUtil.getFormatTime()} 编号${downId}的下载任务已完成"
            }
        }
    }

    // 接收下载通知栏的点击事件，在下载过程中有效，下载完成后失效
    class NotificationClickReceiver : BroadcastReceiver() {
        override fun onReceive(context: Context, intent: Intent) {
            Log.d(TAG, " NotificationClickReceiver onReceive")
            if (intent.action == DownloadManager.ACTION_NOTIFICATION_CLICKED && Download.tv_apk_result != null) {
                //获取下载任务的编号数组
                val downIds = intent.getLongArrayExtra(DownloadManager.EXTRA_NOTIFICATION_CLICK_DOWNLOAD_IDS)
                for (downId in downIds) {
                    Log.d(TAG, " notify click! id : $downId, downloadId=$downloadId")
                    //只处理当前下载任务的点击事件
                    if (downId == downloadId) {
                        Download.tv_apk_result?.text = "${DateUtil.getFormatTime()} 编号${downId}的下载进度条被点击了一下"
                    }
                }
            }
        }
    }

    companion object Download {
        private val TAG = "DownloadApkActivity"
        /* 因为DownloadCompleteReceiver和NotificationClickReceiver是嵌套类
           嵌套类只能操作类的静态属性，所以把tv_apk_result和downloadId放在伴生对象里面*/
        var tv_apk_result: TextView? = null
        private var downloadId: Long = 0
    }
}
```
* 清单文件中注册两个广播


```kotlin
  <!-- 注册下载完成事件的广播接收器 -->
        <receiver android:name=".DownloadApkActivity$DownloadCompleteReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_COMPLETE" />
            </intent-filter>
        </receiver>
        <!-- 注册下载通知栏点击事件的广播接收器 -->
        <receiver android:name=".DownloadApkActivity$NotificationClickReceiver">
            <intent-filter>
                <action android:name="android.intent.action.DOWNLOAD_NOTIFICATION_CLICKED" />
            </intent-filter>
        </receiver>
```

#### (4)、自定义文本进度圈

```kotlin
//自定义视图务必要在类名后面增加“@JvmOverloads constructor”，因为布局文件中的自定义视图必须兼容Java
class TextProgressCircle @JvmOverloads constructor(private val mContext: Context, attr: AttributeSet? = null) : View(mContext, attr) {

    private val paintBack: Paint = Paint()
    private val paintFore: Paint = Paint()
    private val paintText: Paint = Paint()

    private var lineWidth = 10
    private var lineColor = Color.GREEN
    private var mTextSize = 50.0f
    private lateinit var mRect: RectF
    private var mProgress = 0

    init {
        //初始化背景画笔的相关属性
        paintBack.isAntiAlias = true
        paintBack.color = Color.LTGRAY
        paintBack.strokeWidth = lineWidth.toFloat()
        paintBack.style = Style.STROKE

        //初始化前景画笔的相关属性
        paintFore.isAntiAlias = true
        paintFore.color = lineColor
        paintFore.strokeWidth = lineWidth.toFloat()
        paintFore.style = Style.STROKE

        //初始化文本画笔的相关属性
        paintText.isAntiAlias = true
        paintText.color = Color.BLUE
        paintText.textSize = mTextSize
    }

    //重写onDraw绘图函数，绘制圆圈背景、圆圈前景，以及中央的进度文本
    override fun onDraw(canvas: Canvas) {
        super.onDraw(canvas)

        val width = measuredWidth  //获得当前视图的丈量宽度
        val height = measuredHeight  //获得当前视图的丈量高度

        if (width <= 0 || height <= 0) {
            return
        }

        val diameter = Math.min(width, height)
        val recLeftPoint = ((width - diameter) / 2 + lineWidth).toFloat()
        val recTopPoint = ((height - diameter) / 2 + lineWidth).toFloat()
        val recRightPoint = ((width + diameter) / 2 - lineWidth).toFloat()
        val recBottomPoint = ((height + diameter) / 2 - lineWidth).toFloat()

        mRect = RectF(recLeftPoint, recTopPoint, recRightPoint, recBottomPoint)

        //绘制进度圆圈的背景，背景是完整的圆环(360度绘制)
        canvas.drawArc(mRect, 0f, 360f, false, paintBack)
        //绘制进度圆圈的前景，前景是实际进度占360度的百分比
        canvas.drawArc(mRect, 0f, (mProgress * 360 / 100).toFloat(), false, paintFore)
        
        val text = "${mProgress.toString()}%"
        val rect = Rect()
        //获得进度文本的矩形边界
        paintText.getTextBounds(text, 0, text.length, rect)
        val x = getWidth() / 2 - rect.centerX()
        val y = getHeight() / 2 - rect.centerY()
        //把文本内容绘制在进度圆圈的圆心位置
        canvas.drawText(text, x.toFloat(), y.toFloat(), paintText)
    }

    //设置进度数值以及进度文本的文字大小
    fun setProgress(progress: Int, textSize: Float) {
        mProgress = progress
        if (textSize > 0) {
            mTextSize = textSize
            paintText.textSize = mTextSize
        }
        invalidate()
    }

    //设置进度圆圈的线宽与颜色
    fun setProgressStyle(line_width: Int, line_color: Int) {
        if (line_width > 0) {
            lineWidth = line_width
            paintFore.strokeWidth = lineWidth.toFloat()
        }
        if (line_color > 0) {
            lineColor = line_color
            paintFore.color = lineColor
        }
        invalidate()
    }
}
```
#### (5)、页面上动态显示下载进度

```kotlin
class DownloadImageActivity : AppCompatActivity() {
    private var imagePath: String = ""
    private var downloadId: Long = 0
    private val imageNames = listOf(
            "洱海公园", "丹凤亭", "宛在堂", "满庭芳", "玉带桥", 
            "眺望洱海", "洱海女儿", "海心亭", "洱海岸边", "烟波浩渺")
    private val imageUrls = listOf(
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/nYJcslMIrGeDrujE5KZF2xBW8rjXMIVetZfrOAlSamM!/b/dPwxB5iaEQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/Adcl9XVS.RBED4D8shjceYHOhhR*6mcNyCcq24kJG2k!/b/dPwxB5iYEQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/bg*X6nT03YUReoJ97ked266WlWG3IzLjBdwHpKqkhYY!/b/dOg5CpjZEAAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/JOPAKl9BO1wragCEIVzXLlHwj83qVhb8uNuHdmVRwP4!/b/dPwxB5iSEQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/7hHOgBEOBshH*7YAUx7RP0JzPuxRBD727mblw9TObhc!/b/dG4WB5i2EgAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/m4Rjx20D9iFL0D5emuYqMMDji*HGQ2w2BWqv0zK*tRk!/b/dGp**5dYEAAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/swfCMVl7Oefv8xgboV3OqkrahEs33KO7XwwH6hh7bnY!/b/dECE*5e9EgAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b256.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/tpRlB0oozaD9PyBtCmf3pQ5QY0keJJxYGX93I7n5NwQ!/b/dAyVmZiVEQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b256.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/wMX2*LM6y.mBsFIYu8spAa7xXWUkPD.GHyazd.vMmYA!/b/dGYwoZjREQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4", 
            "http://b255.photo.store.qq.com/psb?/V11ZojBI0Zz6pV/2vl1n0KmKTPCv944MVJgLxKAhMiM*sqajIFQ43c*9DM!/b/dPaoCJhuEQAA&bo=IANYAgAAAAABB1k!&rf=viewer_4")

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_download_image)

        sp_image_url.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE
        tv_spinner.text = imageNames[0]
        tv_spinner.setOnClickListener {
            selector("请选择要下载的图片", imageNames) { i ->
                tv_spinner.text = imageNames[i]
                tv_spinner.isEnabled = false
                iv_image_url.setImageDrawable(null)
                tpc_progress.setProgress(0, 100f)
                tpc_progress.visibility = View.VISIBLE

                //声明下载任务的请求对象
                val down = Request(Uri.parse(imageUrls[i]))
                //手机连上移动网络或者连上WIFI时均可进行下载操作
                down.setAllowedNetworkTypes(Request.NETWORK_MOBILE or Request.NETWORK_WIFI)
                //隐藏下载通知栏
                down.setNotificationVisibility(Request.VISIBILITY_HIDDEN)
                //指定不在系统的下载页面显示
                down.setVisibleInDownloadsUi(false)
                //指定下载文件在本地的保存路径
                down.setDestinationInExternalFilesDir(this, Environment.DIRECTORY_DCIM, "$i.jpg")
                //把下载请求添加到下载队列中
                //这里利用扩展属性实现了自动获取下载管理器实例
                //有关扩展属性的介绍参见第9章的“9.5.2 开始热身：震动器Vibrator”
                downloadId = downloader.enqueue(down)
                //启动下载进度的刷新任务
                handler.postDelayed(mRefresh, 100)
            }
        }
    }

    private val handler = Handler()
    private val mRefresh = object : Runnable {
        override fun run() {
            var bFinish = false
            val down_query = Query()
            //根据编号来过滤下载任务
            down_query.setFilterById(downloadId)
            // 根据查询请求，获取符合条件的结果集游标
            val cursor = downloader.query(down_query)
            while (cursor.moveToNext()) {
                //获取下载文件的uri路径
                val uriIdx = cursor.getColumnIndex(DownloadManager.COLUMN_LOCAL_URI)
                //获取文件的媒体类型
                val mediaTypeIdx = cursor.getColumnIndex(DownloadManager.COLUMN_MEDIA_TYPE)
                //获取文件的总大小
                val totalSizeIdx = cursor.getColumnIndex(DownloadManager.COLUMN_TOTAL_SIZE_BYTES)
                //获取已下载的文件大小
                val nowSizeIdx = cursor.getColumnIndex(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR)
                //获取文件的下载状态
                val statusIdx = cursor.getColumnIndex(DownloadManager.COLUMN_STATUS)
                //计算当前的下载进度百分比
                val progress = (100 * cursor.getLong(nowSizeIdx) / cursor.getLong(totalSizeIdx)).toInt()
                if (cursor.getString(uriIdx) == null) {
                    break
                }
                //设置文本进度圈的当前进度
                tpc_progress.setProgress(progress, 100f)
                imagePath = cursor.getString(uriIdx)
                tv_image_result.text = "文件路径：${cursor.getString(uriIdx)}\n" +
                        "媒体类型：${cursor.getString(mediaTypeIdx)}\n" +
                        "文件总大小：${cursor.getLong(totalSizeIdx)}\n" +
                        "已下载大小：${cursor.getLong(nowSizeIdx)}\n" +
                        "下载进度：$progress%%\n" +
                        "下载状态：${statusMap[cursor.getInt(statusIdx)]}\n"
                //下载进度达到100%，表示下载完成
                if (progress >= 100) {
                    bFinish = true
                }
            }
            cursor.close()

            //CnPeng 2019/1/10 3:19 PM 由于查询回来的是一个游标集，所以while中的循环可能会执行多次。所以，将下面这段放在while外面
            //尚未完成下载，继续轮询下载进度
            if (!bFinish) {
                handler.postDelayed(this, 100)
            } else {
                tv_spinner.isEnabled = true
                //下载完毕，隐藏圆圈进度，改为显示下载好的图片
                tpc_progress.visibility = View.INVISIBLE
                iv_image_url.setImageURI(Uri.parse(imagePath))
            }
        }
    }

    companion object {
        //下载状态类型与中文名称的映射关系定义
        private val statusMap = mapOf(
                Pair(DownloadManager.STATUS_PENDING, "挂起"),
                Pair(DownloadManager.STATUS_RUNNING, "运行中"),
                Pair(DownloadManager.STATUS_PAUSED, "暂停"),
                Pair(DownloadManager.STATUS_SUCCESSFUL, "成功"),
                Pair(DownloadManager.STATUS_FAILED, "失败"))
    }
}
```

**注意**：在调用  down.setNotificationVisibility(Request.VISIBILITY_HIDDEN)  隐藏通知栏的下载进度时，需要申请权限：

```kotlin
 <!-- 下载时不提示通知栏 -->
<uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />
```
另外，下载时通知栏中的下载进度是默认显示的，也就是取值为 VISIBILITY_VISIBLE

### 4、ContentProvider
ContentProvider用于在不同的APP之间共享数据。其内部又细分为：ContentProvider、ContentResolver、ContentObserver

#### (1)、内容提供者 ContentProvider

##### A: 基本介绍
ContentProvider为APP存取内部数据提供了统一的外部接口。它让其他APP可以访问本APP内部的数据

实际编码中，ContentProvider类似于一个服务端的数据存储接口，开发者需要定义一个它的实现类。并重写下列重要函数：

函数|含义
---|---
onCreate|创建并获取数据连接
query|查询数据
insert|插入数据
update|更新数据
delete|删除数据
getType|获取数据类型

ContentProvider作为中间的接口，并不直接操作数据，而是通过SQLiteOpenHelper 和 SQLiteDatabase间接操作底层的SQLite。所以，在实现ContentProvider前，需要先自定义SQLite的数据库帮助类，然后再通过ContentProvider将SQLite中提供的方法对外暴露。

##### B: 示例代码
* 数据库帮助类——UserDBHelper.kt


```kotlin
class UserDBHelper(var context: Context, private var DB_VERSION: Int = CURRENT_VERSION) : ManagedSQLiteOpenHelper(
        context, DB_NAME, null, DB_VERSION) {
    companion object {
        private val TAG = "UserDBHelper"
        var DB_NAME = "user.db" //数据库名称
        var TABLE_NAME = "user_info" //表名称
        var CURRENT_VERSION = 1 //当前的最新版本，如有表结构变更，该版本号要加一
        private var instance: UserDBHelper? = null

        @Synchronized
        fun getInstance(ctx: Context, version: Int = 0): UserDBHelper {
            if (instance == null) {
                //如果调用时没传版本号，就使用默认的最新版本号
                instance = if (version > 0) {
                    UserDBHelper(ctx.applicationContext, version)
                } else {
                    UserDBHelper(ctx.applicationContext)
                }
            }
            return instance!!
        }
    }

    private var mDB: SQLiteDatabase? = null

    fun openReadLink(): SQLiteDatabase {
        if (mDB == null || mDB?.isOpen !== true) {
            mDB = instance?.getReadableDatabase()
        }
        return mDB!!
    }

    fun closeLink() {
        if (mDB != null && mDB?.isOpen === true) {
            mDB?.close()
            mDB = null
        }
    }

    override fun onCreate(db: SQLiteDatabase) {
        Log.d(TAG, "onCreate")
        //CnPeng 2019/1/10 4:01 PM 这里直接删除了现有的表，实际开发中不能这么干。需要在 onUpgrade中通过alter 修改表结构
        val drop_sql = "DROP TABLE IF EXISTS $TABLE_NAME;"
        Log.d(TAG, "drop_sql:" + drop_sql)
        db.execSQL(drop_sql)
        val create_sql = "CREATE TABLE IF NOT EXISTS $TABLE_NAME (" +
                "_id INTEGER PRIMARY KEY  AUTOINCREMENT NOT NULL," +
                "name VARCHAR NOT NULL," + "age INTEGER NOT NULL," +
                "height LONG NOT NULL," + "weight FLOAT NOT NULL," +
                "married INTEGER NOT NULL," + "update_time VARCHAR NOT NULL" +
                ",phone VARCHAR" + ",password VARCHAR" + ");"
        Log.d(TAG, "create_sql:" + create_sql)
        db.execSQL(create_sql)
    }

    override fun onUpgrade(db: SQLiteDatabase, oldVersion: Int, newVersion: Int) {
    }

    fun delete(selection: String?, selectionArgs: Array<String>?): Int {
        var count = 0

        //CnPeng 2019/1/10 4:04 PM 以为当前类继承自Anko库中的 ManagedSQLiteOpenHelper ，该类的特点就是相应代码需要放置在use{}代码块中
        use {
            count = delete(TABLE_NAME, selection, selectionArgs)
        }
        return count
    }

    fun insert(values: ContentValues?): Long {
        var rowId = 0L
        use {
            rowId = insert(TABLE_NAME, null, values)
        }
        return rowId
    }

    fun query(projection: Array<String>?, selection: String?,
              selectionArgs: Array<String>?, sortOrder: String?): Cursor? {
        var cursor: Cursor? = null
        use {
            cursor = query(TABLE_NAME,
                    projection, selection, selectionArgs, null, null, sortOrder)
        }
        return cursor
    }
}
```

* 用户信息定义类——UserInfoContent.kt


```kotlin
class UserInfoContent : BaseColumns {
    companion object {
        // 这里的名称必须与AndroidManifest.xml里的android:authorities保持一致
        val AUTHORITIES = "com.example.network.provider.UserInfoProvider"
        // 表名
        val TABLE_NAME = UserDBHelper.TABLE_NAME
        // 访问该内容提供器的URI——主机名+数据库名
        val CONTENT_URI = Uri.parse("content://$AUTHORITIES/user")
        //	// 该内容提供器返回的数据类型定义
        //	public static final String CONTENT_TYPE = "vnd.android.cursor.dir/vnd.myprovider.user";
        //	public static final String CONTENT_TYPE_ITEM = "vnd.android.cursor.item/vnd.myprovider.user";
        // 列名
        val USER_NAME = "name"
        val USER_AGE = "age"
        val USER_HEIGHT = "height"
        val USER_WEIGHT = "weight"
        val USER_MARRIED = "married"
        // 默认的排序方法
        val DEFAULT_SORT_ORDER = "_id desc"
    }
}
```

* 内容提供者——UserInfoProvider.kt

```kotlin
class UserInfoProvider : ContentProvider() {
    lateinit var userDB: UserDBHelper

    //删除数据
    override fun delete(uri: Uri, selection: String?, selectionArgs: Array<String>?): Int {
        var count = 0
        if (uriMatcher.match(uri) == USER_INFO) {
            count = userDB.delete(selection, selectionArgs)
        }
        return count
    }

    //插入数据
    override fun insert(uri: Uri, values: ContentValues?): Uri? {
        var newUri = uri
        if (uriMatcher.match(uri) == USER_INFO) {
            // 向指定的表插入数据，得到返回的Id
            val rowId = userDB.insert(values)
            if (rowId > 0) { // 判断插入是否执行成功
                // 如果添加成功，利用新添加的Id和生成新的地址
                newUri = ContentUris.withAppendedId(UserInfoContent.CONTENT_URI, rowId)
                // 通知监听器，数据已经改变
                context.contentResolver.notifyChange(newUri, null)
            }
        }
        return newUri
    }

    //创建ContentProvider时调用的回调函数
    override fun onCreate(): Boolean {
        userDB = UserDBHelper.getInstance(context, 1)
        return false
    }

    //查询数据库
    override fun query(uri: Uri, projection: Array<String>?, selection: String?,
                       selectionArgs: Array<String>?, sortOrder: String?): Cursor? {
        var cursor: Cursor? = null
        if (uriMatcher.match(uri) == USER_INFO) {
            val db = userDB.readableDatabase
            // 执行查询
            cursor = db.query(UserInfoContent.TABLE_NAME,
                    projection, selection, selectionArgs, null, null, sortOrder)
            // 设置监听
            cursor?.setNotificationUri(context.contentResolver, uri)
        }
        return cursor

    }

    //获取数据访问类型，暂未实现
    override fun getType(uri: Uri): String? {
        throw UnsupportedOperationException("Not yet implemented")
    }

    //更新数据，暂未实现
    override fun update(uri: Uri, values: ContentValues?, selection: String?, selectionArgs: Array<String>?): Int {
        throw UnsupportedOperationException("Not yet implemented")
    }

    companion object {
        val USER_INFO = 1
        val uriMatcher = UriMatcher(UriMatcher.NO_MATCH)
        //伴生对象的初始化操作
        init {
            //CnPeng 2019/1/10 4:17 PM 三个参数的含义依次为：主机名，数据库名称，匹配成功的响应码
            uriMatcher.addURI(UserInfoContent.AUTHORITIES, "/user", USER_INFO)
        }
    }
}
```
* 清单文件中进行注册

```kotlin
<!-- 注册用户信息的内容提供器 -->
        <provider
            android:name=".provider.UserInfoProvider"
            android:authorities="com.example.network.provider.UserInfoProvider"
            android:enabled="true"
            android:exported="true" />
```

#### (2)、内容解析者 ContentResolver
##### A: 基本介绍

如果A APP中提供了ContentProvider，那么B APP 就可以通过 ContentResolver来访问A中的数据。

在Activity界面中可以通过 getContentResolver() 函数获取ContentResolver对象，借助于kotlin编码时可以直接使用 contentResolver 对象。

ContentReslover中提供的函数和 ContentProvider是一致的。最常用的是query()函数, 该函数的参数取值如下：

参数|含义
---|---
uri| 其他APP的ContentProvider暴露的URI
projection| 字符串数组类型，指定要查询的字段名称列表
selection| 字符串类型，指定查询条件
selectionArgs|字符串数组，指定查询条件中的参数值列表
sortOrder|字符串类型，指定查询结果的排序条件

##### B: 示例

* activity_content_provider.xml

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp" >

    <EditText
        android:id="@+id/et_user_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入姓名"
        android:inputType="text"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <EditText
        android:id="@+id/et_user_age"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入年龄"
        android:inputType="number"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <EditText
        android:id="@+id/et_user_height"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入身高"
        android:inputType="number"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <EditText
        android:id="@+id/et_user_weight"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入体重"
        android:inputType="numberDecimal"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <Button
        android:id="@+id/btn_add_user"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="添加用户信息"
        android:textColor="@color/black"
        android:textSize="17sp" />

    <TextView
        android:id="@+id/tv_read_user"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>
```

* ContentProviderActivity.kt

```kotlin
class ContentProviderActivity : AppCompatActivity() {
    private var userCount = ""
    private var userResult = ""

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_content_provider)

        btn_add_user.setOnClickListener {

            ViewUtil.hideAllInputMethod(this)

            //CnPeng 2019/1/10 4:40 PM 此处没有判断输入非空！！
            val user = UserData(name = et_user_name.text.toString(),
                        age = et_user_age.text.toString().toInt(),
                        height = et_user_height.text.toString().toLong(),
                        weight = et_user_weight.text.toString().toFloat())
            addUser(contentResolver, user)
            showUserInfo()
            toast("成功写入用户信息")
        }

        tv_read_user.setOnClickListener {
            alert(userResult, userCount) {
                positiveButton("确定") {}
            }.show()
        }

        tv_read_user.setOnLongClickListener {
            contentResolver.delete(UserInfoContent.CONTENT_URI, "1==1", null)
            showUserInfo()
            toast("成功删除所有用户信息")
            true
        }
        showUserInfo()
    }

    private fun showUserInfo() {
        userResult = readAllUser(contentResolver)
        val list = userResult.split("\n")
        //val count = userResult.count({ it == '\n' })
        userCount = "当前共找到${list.size-1}位用户信息"
        tv_read_user.text = userCount
    }

    private fun addUser(resolver: ContentResolver, user: UserData) {
        val name = ContentValues()
        name.put("name", user.name)
        name.put("age", user.age)
        name.put("height", user.height)
        name.put("weight", user.weight)
        name.put("married", false)
        name.put("update_time", DateUtil.getFormatTime())
        //UserInfoContent.CONTENT_URI指向的字符串就是provider在AndroidManifest.xml里的android:authorities属性值
        resolver.insert(UserInfoContent.CONTENT_URI, name)
    }

    private fun readAllUser(resolver: ContentResolver): String {
        val userArray = ArrayList<UserData>()
        val cursor = resolver.query(UserInfoContent.CONTENT_URI, null, null, null, null)
        if (cursor != null) {
            while (cursor.moveToNext()) {
                val user = UserData()
                user.name = cursor.getString(cursor.getColumnIndex(UserInfoContent.USER_NAME))
                user.age = cursor.getInt(cursor.getColumnIndex(UserInfoContent.USER_AGE))
                user.height = cursor.getInt(cursor.getColumnIndex(UserInfoContent.USER_HEIGHT)).toLong()
                user.weight = cursor.getFloat(cursor.getColumnIndex(UserInfoContent.USER_WEIGHT))
                userArray.add(user)
            }
            cursor.close()
        }
        var result = ""
        for (user in userArray) {
            result = "$result${user.name}	年龄${user.age}	身高${user.height}	体重${user.weight}\n"
        }
        return result
    }
}
```

* ViewUtils.kt

```kotlin
object ViewUtil {

    fun getMaxLength(et: EditText): Int {
        var length = 0
        try {
            val inputFilters = et.filters
            for (filter in inputFilters) {
                val c = filter.javaClass
                if (c.name == "android.text.InputFilter\$LengthFilter") {
                    val f = c.declaredFields
                    for (field in f) {
                        if (field.name == "mMax") {
                            field.isAccessible = true
                            length = field.get(filter) as Int
                        }
                    }
                }
            }
        } catch (e: Exception) {
            e.printStackTrace()
        }

        return length
    }

    fun hideAllInputMethod(act: Activity) {
        val imm = act.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        //软键盘如果已经打开则关闭之
        if (imm.isActive) {
            imm.toggleSoftInput(0, InputMethodManager.HIDE_NOT_ALWAYS)
        }
    }

    fun hideOneInputMethod(act: Activity, v: View) {
        val imm = act.getSystemService(Context.INPUT_METHOD_SERVICE) as InputMethodManager
        imm.hideSoftInputFromWindow(v.windowToken, 0)
    }
}
```

##### C: 操作系统联系人的示例
实际使用中，APP一般不会向外暴露数据，更多的时候是通过ContenResolver读取和修改系统的联系人、短信、通话记录等。

系统联系人对外暴露的ContentUri为 `content://com.android.contacts/`

* CommunicationUtil.kt

```kotlin
object CommunicationUtil {
    private val TAG = "CommunicationUtil"
    private val mContactUri = ContactsContract.CommonDataKinds.Phone.CONTENT_URI
    private val mContactColumn = arrayOf(ContactsContract.CommonDataKinds.Phone.NUMBER, ContactsContract.CommonDataKinds.Phone.DISPLAY_NAME)

    fun readPhoneContacts(resolver: ContentResolver): Int {
        val contactArray = mutableListOf<Contact>()
        val cursor = resolver.query(mContactUri, mContactColumn, null, null, null)
        while (cursor.moveToNext()) {
            val contact = Contact()
            contact.phone = cursor.getString(0).replace("+86", "").replace(" ", "")
            contact.name = cursor.getString(1)
            Log.d(TAG, "${contact.name} ${contact.phone}")
            contactArray.add(contact)
        }
        cursor.close()
        return contactArray.size
    }

    fun readSimContacts(resolver: ContentResolver): Int {
        val simUri = Uri.parse("content://icc/adn")
        val contactArray = mutableListOf<Contact>()
        val cursor = resolver.query(simUri, mContactColumn, null, null, null)
        while (cursor.moveToNext()) {
            val contact = Contact()
            contact.phone = cursor.getString(0).replace("+86", "").replace(" ", "")
            contact.name = cursor.getString(1)
            Log.d(TAG, "${contact.name} ${contact.phone}")
            contactArray.add(contact)
        }
        cursor.close()
        return contactArray.size
    }

    fun addContacts(resolver: ContentResolver, contact: Contact) {
        val raw_uri = Uri.parse("content://com.android.contacts/raw_contacts")
        val values = ContentValues()
        // 添加一条联系人的主记录，并返回唯一的联系人编号
        val contactId = ContentUris.parseId(resolver.insert(raw_uri, values))
        val uri = Uri.parse("content://com.android.contacts/data")
        // 添加联系人姓名（要根据前面获取的id号）
        val name = ContentValues()
        name.put("raw_contact_id", contactId)
        name.put("mimetype", "vnd.android.cursor.item/name")
        name.put("data2", contact.name)
        resolver.insert(uri, name)
        // 添加联系人的手机号码
        val phone = ContentValues()
        phone.put("raw_contact_id", contactId)
        phone.put("mimetype", "vnd.android.cursor.item/phone_v2")
        phone.put("data2", "2")
        phone.put("data1", contact.phone)
        resolver.insert(uri, phone)
        // 添加联系人的电子邮箱
        val email = ContentValues()
        email.put("raw_contact_id", contactId)
        email.put("mimetype", "vnd.android.cursor.item/email_v2")
        email.put("data2", "2")
        email.put("data1", contact.email)
        resolver.insert(uri, email)
    }

    fun addFullContacts(resolver: ContentResolver, contact: Contact) {
        val raw_uri = Uri.parse("content://com.android.contacts/raw_contacts")
        val uri = Uri.parse("content://com.android.contacts/data")
        // 依次封装联系人主记录、联系人姓名、手机号码、电子邮箱的操作行为
        val op_main = ContentProviderOperation
                .newInsert(raw_uri).withValue("account_name", null).build()
        val op_name = ContentProviderOperation
                .newInsert(uri).withValueBackReference("raw_contact_id", 0)
                .withValue("mimetype", "vnd.android.cursor.item/name")
                .withValue("data2", contact.name).build()
        val op_phone = ContentProviderOperation
                .newInsert(uri).withValueBackReference("raw_contact_id", 0)
                .withValue("mimetype", "vnd.android.cursor.item/phone_v2")
                .withValue("data2", "2").withValue("data1", contact.phone)
                .build()
        val op_email = ContentProviderOperation
                .newInsert(uri).withValueBackReference("raw_contact_id", 0)
                .withValue("mimetype", "vnd.android.cursor.item/email_v2")
                .withValue("data2", "2").withValue("data1", contact.email)
                .build()
        // 把以上四个操作行为组成行为队列，并一次性处理解决该行为队列
        val operations = mutableListOf(op_main, op_name, op_phone, op_email)
        resolver.applyBatch("com.android.contacts", operations as ArrayList<ContentProviderOperation>)
    }

    private var mSmsUri = Uri.parse("content://sms/inbox")
    private var mSmsColumn = arrayOf("address", "person", "body", "date", "type")

    fun readSms(resolver: ContentResolver, phone: String?, gaps: Int): Int {
        val smsArray = mutableListOf<SmsContent>()
        var selection = ""
        if (phone != null && phone.isNotEmpty()) {
            selection = "address='$phone'"
        }
        if (gaps > 0) {
            selection = "$selection${if (selection.isNotEmpty()) " and " else ""}" +
                    "date>${System.currentTimeMillis() - gaps * 1000}"
        }
        val cursor = resolver.query(mSmsUri, mSmsColumn, selection, null, "date desc")
        while (cursor.moveToNext()) {
            val sms = SmsContent(address = cursor.getString(0),
                    person = cursor.getString(1),
                    body = cursor.getString(2),
                    date = DateUtil.formatDate(cursor.getLong(3)),
                    type = cursor.getInt(4))  //type=1表示收到的短信，type=2表示发送的短信
            Log.d(TAG, "${sms.address} ${sms.person} ${sms.date} ${sms.type} ${sms.body}")
            smsArray.add(sms)
        }
        cursor.close()
        return smsArray.size
    }

    private val mRecordUri = CallLog.Calls.CONTENT_URI
    private val mRecordColumn = arrayOf(CallLog.Calls.CACHED_NAME, CallLog.Calls.NUMBER, CallLog.Calls.TYPE, CallLog.Calls.DATE, CallLog.Calls.DURATION, CallLog.Calls.NEW)

    fun readCallRecord(resolver: ContentResolver, gaps: Int): Int {
        val recordArray = mutableListOf<CallRecord>()
        val selection = "date>${System.currentTimeMillis() - gaps * 1000}"
        val cursor = resolver.query(mRecordUri, mRecordColumn, selection, null, "date desc")
        while (cursor.moveToNext()) {
            val record = CallRecord(name = cursor.getString(0),
                    phone = cursor.getString(1),
                    type = cursor.getInt(2),  //type=1表示接听，2表示拨出，3表示未接
                    date = DateUtil.formatDate(cursor.getLong(3)),
                    duration = cursor.getLong(4),
                    _new = cursor.getInt(5))
            Log.d(TAG, "${record.name} ${record.phone} ${record.type} ${record.date} ${record.duration}")
            recordArray.add(record)
        }
        cursor.close()
        return recordArray.size
    }

    private val mContactIdColumn = arrayOf(ContactsContract.Contacts._ID, ContactsContract.Contacts.DISPLAY_NAME)
    fun readAllContacts(resolver: ContentResolver): String {
        val contactArray = mutableListOf<Contact>()
        val cursor = resolver.query(
                ContactsContract.Contacts.CONTENT_URI, mContactIdColumn, null, null, null)
        while (cursor.moveToNext()) {
            //CnPeng 2019/1/10 5:04 PM 此处构造对象的时候要判断name是否为空，oppoR15手机中允许用户名为空！！！
            val contact = Contact(contactId = cursor.getString(0),
                    name = if (cursor.getString(1).isNullOrEmpty()) {
                        "未命名"
                    } else {
                        cursor.getString(1)
                    })
            contactArray.add(contact)
        }
        cursor.close()

        for (i in contactArray.indices) {
            val contact = contactArray[i]
            contact.phone = getColumn(resolver, contact.contactId,
                    ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
                    ContactsContract.CommonDataKinds.Phone.CONTACT_ID,
                    ContactsContract.CommonDataKinds.Phone.NUMBER)
            contact.email = getColumn(resolver, contact.contactId,
                    ContactsContract.CommonDataKinds.Email.CONTENT_URI,
                    ContactsContract.CommonDataKinds.Email.CONTACT_ID,
                    ContactsContract.CommonDataKinds.Email.DATA)
            contactArray[i] = contact
            Log.d(TAG, "${contact.contactId} ${contact.name} ${contact.phone} ${contact.email}")
        }
        var result = ""
        for (item in contactArray) {
            result = "$result${item.name} ${item.phone}	${item.email}\n"
        }
        return result
    }

    private fun getColumn(resolver: ContentResolver, contactId: String,
                          uri: Uri, selection: String, column: String): String {
        val cursor = resolver.query(uri, null, "$selection=$contactId", null, null)
        var index = 0
        if (cursor.count > 0) {
            index = cursor.getColumnIndex(column)
        }
        var value = ""
        while (cursor.moveToNext()) {
            value = cursor.getString(index)
        }
        cursor.close()
        return value
    }
}
```
* `activity_content_resolver.xml`

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp" >

    <EditText
        android:id="@+id/et_contact_name"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入联系人姓名"
        android:inputType="text"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <EditText
        android:id="@+id/et_contact_phone"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入联系人手机号"
        android:inputType="number"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <EditText
        android:id="@+id/et_contact_email"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:layout_margin="5dp"
        android:background="@drawable/editext_selector"
        android:hint="请输入联系人邮箱"
        android:inputType="textEmailAddress"
        android:textColor="@color/black"
        android:textSize="17sp" />
    
    <Button
        android:id="@+id/btn_add_contact"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="添加联系人"
        android:textColor="@color/black"
        android:textSize="17sp" />

    <TextView
        android:id="@+id/tv_read_contact"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="5dp"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>
```

* ContentResolverActivity.kt

```kotlin
class ContentResolverActivity : AppCompatActivity() {
    private var contactCount = ""
    private var contactResult = ""
    private var dialog: ProgressDialog? = null
    private val ADD = 0
    private val QUERY = 1

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_content_resolver)

        btn_add_contact.setOnClickListener {
            ViewUtil.hideAllInputMethod(this)
            dialog = indeterminateProgressDialog("正在写入联系人信息", "请稍候")
            dialog?.show()
            Thread { addContactInfo() }.start()
        }

        tv_read_contact.setOnClickListener {
            alert(contactResult, contactCount) {
                positiveButton("确定") {}
            }.show()
        }

        showContactInfo()
    }

    private fun showContactInfo() {
        dialog = indeterminateProgressDialog("正在读取联系人信息", "请稍候")
        dialog?.show()
        //查询联系人操作可能较耗时，故需放在线程中处理
        Thread { queryContactInfo() }.start()
    }

    private fun addContactInfo() {
        val contact = Contact()
        contact.name = et_contact_name.text.toString()
        contact.phone = et_contact_phone.text.toString()
        contact.email = et_contact_email.text.toString()
        //方式一，使用ContentResolver多次写入，每次一个字段
        CommunicationUtil.addContacts(contentResolver, contact)
        //方式二，使用ContentProviderOperation一次写入，每次多个字段
        //CommunicationUtil.addFullContacts(getContentResolver(), contact);
        handler.sendEmptyMessage(ADD)
    }

    private fun queryContactInfo() {
        contactResult = CommunicationUtil.readAllContacts(contentResolver)
        val list = contactResult.split("\n")
        //val count = userResult.count({ it == '\n' })
        contactCount = "当前共找到${list.size-1}位联系人"
        handler.sendEmptyMessage(QUERY)
    }

    private val handler = object : Handler() {
        override fun handleMessage(msg: Message) {
            dialog?.dismiss()
            when (msg.what) {
                ADD -> {
                    toast("成功写入联系人信息")
                    showContactInfo()
                }
                else -> tv_read_contact.text = contactCount
            }
        }
    }
}
```
在上面的 addContacts() 函数中分别使用了四个insert语句去添加用户信息，由于四个insert不再同一个事务中，任意一个插入失败，其他的也不能回滚，从而产生垃圾数据。

为了避免这种情况，所以Android又提供了 **ContentProviderOperation** 来进行批量的数据操作。它**可以在一个insert操作中封装多条信息的修改操作，然后一次性提交给ContentProvider**, 这样就实现了在一个事务中操作多条数据的需求。如果某个信息插入失败，那么它就会遵从事务原则，回滚相关数据。详细代码在上面的 `addFullContacts` 中

#### (3)、内容观察者 ContentObserver

##### A: 基本介绍
ContentResolver采用主动查询的方式去获取其他APP的数据，如果不触发就不会获取。如果我们想即时检测其他APP数据的变化，就需要使用 ContentObserver。

比如：在注册APP或者付款时，通常需要输入验证码。当手机收到系统发送的验证码时，如果我们在自己的APP中注册了ContentObserver ，那么就可以实时监测到验证码内容，然后主动填写到验证码输入框中。

ContentObserver的对象被 ContentResolver 调用，ContentResolver 中与内容观察者相关的方法如下：

函数|含义
---|---
registerContentObserver|注册内容观察者
unregisterContentObserver|注销内容观察者
notifyChange|通知内容观察者数据发生变化

##### B: 实现流量校准的功能
主动给10086 发送查询流量的短信，借助 ContentOberver监测到短信到达之后解析到界面中。

* activity_content_observer.xml

```kotlin
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="5dp" >

    <Button
        android:id="@+id/btn_check_send"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="进行流量校准"
        android:textColor="@color/black"
        android:textSize="17sp" />

    <TextView
        android:id="@+id/tv_check_info"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:textColor="@color/black"
        android:textSize="17sp" />

</LinearLayout>
```

* ContentObserverActivity.kt 

```kotlin
class ContentObserverActivity : AppCompatActivity() {
    private var mObserver: SmsGetObserver? = null

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_content_observer)

        Observer.tv_check_info = findViewById<TextView>(R.id.tv_check_info)

        tv_check_info.setOnClickListener {
            alert(mCheckResult, "收到流量校准短信") {
                positiveButton("确定") {}
            }.show()
        }

        btn_check_send.setOnClickListener {
            var dialog = indeterminateProgressDialog("正在进行流量校准", "请稍候")
            dialog.show()
            //查询数据流量，移动号码的查询方式为发送短信内容“18”给“10086”
            //电信和联通号码的短信查询方式请咨询当地运营商客服热线
            sendSmsAuto("10086", "18")
            Handler().postDelayed({
                if (dialog.isShowing == true) {
                    dialog.dismiss()
                }}, 5000)
        }

        mSmsUri = Uri.parse("content://sms")
        mSmsColumn = arrayOf("address", "body", "date")
        mObserver = SmsGetObserver(this, Handler())
        //注册短信接收的内容观察器
        contentResolver.registerContentObserver(mSmsUri!!, true, mObserver!!)
    }

    override fun onDestroy() {
        //注销短信接收的内容观察器
        contentResolver.unregisterContentObserver(mObserver!!)
        super.onDestroy()
    }

    //短信发送广播，如需处理可注册该事件的BroadcastReceiver
    private val SENT_SMS_ACTION = "com.example.network.SENT_SMS_ACTION"
    //短信接收广播，如需处理可注册该事件的BroadcastReceiver
    private val DELIVERED_SMS_ACTION = "com.example.network.DELIVERED_SMS_ACTION"

    fun sendSmsAuto(targetPhoneNumber: String, message: String) {
        //声明短信发送的广播意图
        val sentIntent = Intent(SENT_SMS_ACTION)
        sentIntent.putExtra("phone", targetPhoneNumber)
        sentIntent.putExtra("message", message)
        val sentPI = PendingIntent.getBroadcast(this, 0, sentIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        //声明短信接收的广播意图
        val deliverIntent = Intent(DELIVERED_SMS_ACTION)
        deliverIntent.putExtra("phone", targetPhoneNumber)
        deliverIntent.putExtra("message", message)
        val deliverPI = PendingIntent.getBroadcast(this, 1, deliverIntent, PendingIntent.FLAG_UPDATE_CURRENT)

        //要确保打开发送短信的完全权限，不是那种还需提示的不完整权限
        val smsManager = SmsManager.getDefault()
        smsManager.sendTextMessage(targetPhoneNumber, null, message, sentPI, deliverPI)
    }

    //定义一个短信观察器的嵌套类
    private class SmsGetObserver(private val mContext: Context, handler: Handler) : ContentObserver(handler) {

        override fun onChange(selfChange: Boolean) {
            var sender = ""
            var content = ""

            //查询收件箱中来自10086的最近短信
            val selection = "address='10086' and date>${System.currentTimeMillis()-1000*60*60}"
            val cursor = mContext.contentResolver.query(
                    mSmsUri!!, mSmsColumn, selection, null, " date desc")
            while (cursor.moveToNext()) {
                sender = cursor.getString(0)
                content = cursor.getString(1)
                break
            }
            cursor.close()

            mCheckResult = "发送号码：$sender\n短信内容：$content"
            //将解析后的短信内容显示到界面上
            Observer.tv_check_info!!.text = "流量校准结果如下：\n\t" +
                    "总流量为：${findFlow(content, "总流量为", "MB")}\n\t" +
                    "已使用：${findFlow(content, "已使用", "MB")}\n\t" +
                    "剩余：${findFlow(content, "剩余", "MB")}"
            super.onChange(selfChange)
        }
    }

    companion object Observer {
        private var tv_check_info: TextView? = null
        private var mCheckResult: String = ""
        private var mSmsUri: Uri? = null
        private var mSmsColumn: Array<String>? = null
        //在伴生对象中定义解析短信内容的方法
        private fun findFlow(sms: String, begin: String, end: String): String {
            val begin_pos = sms.indexOf(begin)
            if (begin_pos < 0) {
                return "未获取"
            }
            val sub_sms = sms.substring(begin_pos)
            val end_pos = sub_sms.indexOf(end)
            return if (end_pos < 0) {
                "未获取"
            } else sub_sms.substring(begin.length, end_pos + end.length)
        }
    }
}
```

### 5、电商APP自动升级
#### (1)、需求描述
* 检测是否有新版本
* 检测本地SD卡中是否已经下载了新版本
* 检测下载进度并显示在Dialog中，而非通知栏（部分手机通知栏不显示）


#### (2)、可变字符串——SpannableString
Java中如果某个字符串需要多个span只能多次调用`setSpan`。但是，

Anko库中封装了 `buildSpanned` 函数，在该函数内部调用格式为 `append("需要改变样式的文本", span样式对象)` 的代码就可以实现包含多个span的字符串。

##### A : 示例：一个字符串中定义多个span

* 基本写法：
 
```kotlin
val str: Spanned = buildSpanned {
      //粗体
      append("为", StyleSpan(Typeface.BOLD)) 
      //文字增大到1.5倍 
      append("人民",     RelativeSizeSpan(1.5f)) 
      //文字前景色设置为红色
      append("服务", ForegroundColorSpan(Color.RED))      
      //文字背景色设置为绿色 
      append("是谁", BackgroundColorSpan(Color.GREEN))
      //文字增加下划线
      append("提出来的", UnderlineSpan()) 
}
```

* 简化写法

```kotlin
val str: Spanned = buildSpanned { 
      append("为", Bold)
      append("人民", RelativeSizeSpan(1.5f)) 
      append("服务", foregroundColor(Color.RED)) 
      append("是谁", backgroundColor(Color.GREEN))    
      append("提出来的", Underline) 
}
```
##### B: 常用Span样式
完整的Span样式定义在 android.text.style 包中，共有三十多个，常用的有如下：

Span类型|Anko中简化写法|含义
---|---|---
RelativeSizeSpan| 无| 设置文字的相对大小
StyleSpan(TypeFace.BOLD) | Bold | 字体加粗 
StyleSpan(TypeFace.Italic) |Italic|斜体
ForegroundColorSpan|foregroundColor|文字颜色
BackgroundColorSpan|backgroundColor|文字背景色
UnderlineSpan|Underline|给文字加下划线
StrikethroughSpan|Strikethrough|文字加删除线
IamgeSpan|无|文本替换为图片


* 完整示例代码

```kotlin
class SpannableActivity : AppCompatActivity() {
    private val spannables = listOf("增大字号", "加粗字体", "前景红色", "背景绿色", "下划线", "表情图片", "Anko自定义")
    private val text = "为人民服务"
    private val key = "人民"
    private var beginPos = text.indexOf(key)
    private var endPos = beginPos + key.length

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_spannable)
        tv_spannable.text = text

        sp_spannable.visibility = View.GONE
        tv_spinner.visibility = View.VISIBLE
        tv_spinner.text = spannables[0]
        tv_spinner.setOnClickListener {
            selector("请选择可变字符串样式", spannables) { i ->
                tv_spinner.text = spannables[i]
                val spanText = SpannableString(text)
                //对这段文本运用指定的风格样式
                spanText.setSpan(when (i) {
                    0 -> RelativeSizeSpan(1.5f) //文字大小增大到1.5倍大
                    1 -> StyleSpan(Typeface.BOLD) //文字字体使用粗体
                    2 -> ForegroundColorSpan(Color.RED) //文字颜色使用红色
                    3 -> BackgroundColorSpan(Color.GREEN) //背景色使用绿色
                    4 -> UnderlineSpan() //文字下方增加下划线
                    else -> ImageSpan(this@SpannableActivity, R.drawable.people) //把文字替换为图片
                    }, beginPos, endPos, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE)
                tv_spannable.text = spanText
                if (i >= 6) {
                    //使用Anko库的buildSpanned函数连续构建可变字符串
                    tv_spannable.text = buildSpanned {
                        append("为", Bold)
                        append("人民", RelativeSizeSpan(1.5f))
                        append("服务", foregroundColor(Color.RED))
                        append("是谁", backgroundColor(Color.GREEN))
                        append("提出来的", Underline)
                    }
                }
            }
        }
    }
}
```
#### (3)、控件设计

若要查找手机上的APK安装包，可到媒体资源库中查找媒体类型为：`application/vnd.android.package- archive` 的文件。该类型就表示Android的安装包，也就是apk文件。


#### (4)、关键代码

#####  A: 从服务端请求版本信息

```kotlin
 btn_need_request.setOnClickListener {
            val pi = packageManager.getPackageInfo(packageName, 0)
            //开启分线程执行后端接口调用
            doAsync {
                //从服务端获取版本升级信息
                val url = "$checkUrl?package_name=${pi.packageName}&version_name=${pi.versionName}"
                val result = URL(url).readText()
                //回到主线程在界面上弹窗提示待升级的版本
                uiThread { checkUpdate(result) }
            }
        }
```

##### B:  扩展SpannableString工具类

```kotlin
//字符串中的关键语句用指定样式高亮显示
fun String.highlight(key: String, style: CharacterStyle): SpannableString {
    val spanText = SpannableString(this)
    val beginPos = this.indexOf(key)
    val endPos = beginPos + key.length
    spanText.setSpan(style, beginPos, endPos, Spanned.SPAN_EXCLUSIVE_EXCLUSIVE)
    return spanText
}
```

##### C: alert中显示SpannableString
Anko中扩展的alert 函数中 title 和 message 只能接受string，而SpannableString和String时两个类型，所以，可以再扩展一个接受 charsequence 的alert

```kotlin
//Anko自带的alert只支持String类型的文本，不支持富文本的CharSequence类型，
//故此处重写alert方法，使之支持可变字符串SpannableString
fun Context.alert(
        message: CharSequence,
        title: String? = null,
        init: (AlertDialogBuilder.() -> Unit)? = null
) = AlertDialogBuilder(this).apply {
    if (title != null) title(title)
    message(message)
    if (init != null) init()
}
```

##### D: 如何获取手机中的APK及其信息
通过PackageManager的`getPackageArchiveInfo() `函数可以获取相关信息，包括包名、版本号等

```kotlin
//检测本地是否已经有同版本的apk
    private fun getLocalPath(vc: VersionCheck): String {
        var local_path = ""
        //遍历本地所有的apk文件
        val cursor = contentResolver.query(MediaStore.Files.getContentUri("external"),
                null, "mime_type=\"application/vnd.android.package-archive\"", null, null)
        while (cursor.moveToNext()) {
            //TITLE获取文件名，DATA获取文件完整路径，SIZE获取文件大小
            val path = cursor.getString(cursor.getColumnIndex(MediaStore.Files.FileColumns.DATA))
            //从apk文件中解析得到该安装包的程序信息
            val pi = packageManager.getPackageArchiveInfo(path, PackageManager.GET_ACTIVITIES)
            if (pi != null) {
                //找到包名与版本号都符合条件的apk文件
                if (vc.package_name == pi.packageName && vc.version_name == pi.versionName) {
                    local_path = path
                }
            }
        }
        cursor.close()
        return local_path
    }
```
上面代码中VersionCheck类的定义如下：

```kotlin

data class VersionCheck(var app_name: String="", var package_name: String="",
                        var version_code: Int=0, var version_name: String="",
                        var need_update: Boolean=false, var download_url: String="",
                        var local_path: String="") {
}
```

##### E: APK下载的操作过程
如果本地已有安装包，则直接进行操作；如果不存在，则从网络下载安装包。

```kotlin
  //开始执行升级处理。如果本地已有安装包，则直接进行操作；如果不存在，则从网络下载安装包。
    private fun startInstallApp(vc: VersionCheck) {
        appVc = vc
        //本地路径非空，表示存储卡找到最新版本的安装包，此时无需下载即可进行安装操作
        if (vc.local_path.isNotEmpty()) {
            handler.postDelayed(mInstall, 100)
        } else {
            //构建安装包下载地址的请求任务
            val down = Request(Uri.parse(vc.download_url))
            down.setAllowedNetworkTypes(Request.NETWORK_MOBILE or Request.NETWORK_WIFI)
            //隐藏通知栏上的下载消息
            down.setNotificationVisibility(Request.VISIBILITY_HIDDEN)
            down.setVisibleInDownloadsUi(false)
            //指定下载文件的保存路径
            down.setDestinationInExternalFilesDir(this,
                    Environment.DIRECTORY_DOWNLOADS, "${vc.package_name}.apk")
            //将请求任务添加到下载队列中
            downloadId = downloader.enqueue(down)
            handler.postDelayed(mRefresh, 100)
            //弹出进度对话框，用于展示下载进度
            val message = "正在下载${appVc.app_name}的安装包"
            dialog = progressDialog(message, "请稍候")
            dialog.show()
        }
    }
```

##### F :完整示例代码

```kotlin
class AutoUpdateActivity : AppCompatActivity() {
    private val checkUrl = "http://192.168.0.212:8080/HttpTest/checkUpdate";
    //private val checkUrl = "http://192.168.1.5:8080/HttpTest/checkUpdate"
    private val handler = Handler()
    private var appVc: VersionCheck = VersionCheck()
    private var downloadId: Long = 0
    private lateinit var dialog: ProgressDialog

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_auto_update)
        tl_head.title = "商城首页"
        setSupportActionBar(tl_head)
        tl_head.setNavigationOnClickListener { finish() }

        rv_grid.layoutManager = GridLayoutManager(this, 5)
        rv_grid.adapter = RecyclerGridAdapter(this, RecyclerInfo.defaultGrid)
        rv_grid.itemAnimator = DefaultItemAnimator()
        rv_grid.addItemDecoration(SpacesItemDecoration(1))

        btn_no_request.setOnClickListener {
            //测试使用爱奇艺的安装包，包名是com.qiyi.video
            val result = "{\"app_name\":\"爱奇艺\",\"package_name\":\"com.qiyi.video\",\"version_code\":80930,\"version_name\":\"8.8.5\",\"need_update\":true,\"download_url\":\"http://www.lenovomm.com/appdown/21404091-2\"}"
            checkUpdate(result)
        }

        btn_need_request.setOnClickListener {
            val pi = packageManager.getPackageInfo(packageName, 0)
            //开启分线程执行后端接口调用
            doAsync {
                //从服务端获取版本升级信息
                val url = "$checkUrl?package_name=${pi.packageName}&version_name=${pi.versionName}"
                val result = URL(url).readText()
                //回到主线程在界面上弹窗提示待升级的版本
                uiThread { checkUpdate(result) }
            }
        }
    }

    private fun checkUpdate(result: String) {
        //利用gson库将json字符串自动转换为对应格式的数据类实例
        val vc = Gson().fromJson(result, VersionCheck::class.java)
        //检查本地是否存在相同版本的安装包
        vc.local_path = getLocalPath(vc)
        val message = "系统检测到${vc.app_name}的最新版本号是${vc.version_name}，" +
                if (vc.local_path.isNotEmpty()) "本次升级无需流量。"
                else "快去在线升级吧。"
        //高亮显示文本信息中的最新版本号
        val spanText = message.highlight(vc.version_name, ForegroundColorSpan(Color.RED))
        //弹出提示升级的对话框
        alert(spanText, "升级提醒") {
            positiveButton("确定升级") { startInstallApp(vc) }
            negativeButton("以后再说") { }
        }.show()
    }

    //检测本地是否已经有同版本的apk
    private fun getLocalPath(vc: VersionCheck): String {
        var local_path = ""
        //遍历本地所有的apk文件
        val cursor = contentResolver.query(MediaStore.Files.getContentUri("external"),
                null, "mime_type=\"application/vnd.android.package-archive\"", null, null)
        while (cursor.moveToNext()) {
            //TITLE获取文件名，DATA获取文件完整路径，SIZE获取文件大小
            val path = cursor.getString(cursor.getColumnIndex(MediaStore.Files.FileColumns.DATA))
            //从apk文件中解析得到该安装包的程序信息
            val pi = packageManager.getPackageArchiveInfo(path, PackageManager.GET_ACTIVITIES)
            if (pi != null) {
                //找到包名与版本号都符合条件的apk文件
                if (vc.package_name == pi.packageName && vc.version_name == pi.versionName) {
                    local_path = path
                }
            }
        }
        cursor.close()
        return local_path
    }

    //开始执行升级处理。如果本地已有安装包，则直接进行操作；如果不存在，则从网络下载安装包。
    private fun startInstallApp(vc: VersionCheck) {
        appVc = vc
        //本地路径非空，表示存储卡找到最新版本的安装包，此时无需下载即可进行安装操作
        if (vc.local_path.isNotEmpty()) {
            handler.postDelayed(mInstall, 100)
        } else {
            //构建安装包下载地址的请求任务
            val down = Request(Uri.parse(vc.download_url))
            down.setAllowedNetworkTypes(Request.NETWORK_MOBILE or Request.NETWORK_WIFI)
            //隐藏通知栏上的下载消息
            down.setNotificationVisibility(Request.VISIBILITY_HIDDEN)
            down.setVisibleInDownloadsUi(false)
            //指定下载文件的保存路径
            down.setDestinationInExternalFilesDir(this,
                    Environment.DIRECTORY_DOWNLOADS, "${vc.package_name}.apk")
            //将请求任务添加到下载队列中
            downloadId = downloader.enqueue(down)
            handler.postDelayed(mRefresh, 100)
            //弹出进度对话框，用于展示下载进度
            val message = "正在下载${appVc.app_name}的安装包"
            dialog = progressDialog(message, "请稍候")
            dialog.show()
        }
    }

    private val mRefresh = object : Runnable {
        override fun run() {
            var bFinish = false
            //构建下载任务的查询对象
            val down_query = Query()
            down_query.setFilterById(downloadId)
            //获得下载管理器的查询游标，轮询下载任务的下载进度
            val cursor = downloader.query(down_query)
            while (cursor.moveToNext()) {
                val uriIdx = cursor.getColumnIndex(DownloadManager.COLUMN_LOCAL_URI)
                val totalSizeIdx = cursor.getColumnIndex(DownloadManager.COLUMN_TOTAL_SIZE_BYTES)
                val nowSizeIdx = cursor.getColumnIndex(DownloadManager.COLUMN_BYTES_DOWNLOADED_SO_FAR)
                //计算当前的下载进度
                val progress = (100 * cursor.getLong(nowSizeIdx) / cursor.getLong(totalSizeIdx)).toInt()
                if (cursor.getString(uriIdx) == null) {
                    break
                }
                //实时刷新进度对话框上的下载进度
                dialog.progress = progress
                if (progress >= 100) {
                    bFinish = true
                }
            }
            cursor.close()
            if (!bFinish) {
                handler.postDelayed(this, 100)
            } else {
                //下载完毕，进行后续的安装操作
                dialog.dismiss()
                toast("${appVc.app_name}的安装包下载完成")
                handler.postDelayed(mInstall, 100)
            }
        }
    }

    private val mInstall = Runnable {
        //此处省略具体的APK安装过程。。。
        val message = "正在安装${appVc.app_name}的最新版本"
        dialog = progressDialog(message, "请稍候")
        dialog.show()
        handler.postDelayed({
            if (dialog.isShowing) {
                dialog.dismiss()
                //弹出升级完毕的提醒对话框
                val message = "${appVc.app_name}的${appVc.version_name}版本升级完成。"
                val spanText = message.highlight(appVc.version_name, ForegroundColorSpan(Color.RED))
                alert(spanText, "升级完毕") {
                    positiveButton("我知道了") { }
                }.show()
            }
        }, 5000) //延迟5秒，模拟安装升级包花费了5秒时间
    }
}
```