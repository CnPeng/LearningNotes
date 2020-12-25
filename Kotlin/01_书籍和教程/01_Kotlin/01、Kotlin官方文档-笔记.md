最后更新日期：2019-02-28

基于 [https://www.kotlincn.net/docs/reference/](https://www.kotlincn.net/docs/reference/) 摘录整理。


## 一、各版本特性

Error:Cannot run program "/Applications/IntelliJ IDEA CE.app/Contents/jre/jdk/Contents/Home/bin/java" (in directory "/Users/cnpeng/Library/Caches/IdeaIC2018.3/compile-server"): error=2, No such file or directory

### 1、Kotlin 1.1 新特性

#### (1)、协程

Go 中也有协程和通道的概念。

#### (2)、类型别名 
	
* Kotlin 中使用 typealias 修饰
* Swift 中也是使用 typealias 修饰
* Go中也有，直接使用 type 修饰

#### (3)、已绑定的可调用引用

使用 `::` 获取特定对象示例的成员方法或者成员属性的引用。

```kotlin
fun main() {
    // 直接调用 String 类型中的生成正则对象的方法
    val numRegex = "\\d+".toRegex()
    val nums = listOf("abc", "123", "a3c").filter(numRegex::matches)

    // 使用原样字符串包裹正则规则，此处和 Go 中的写法一致，但 Go 是使用 反引号 `` 表示原样字符串
    val numRegex1 = """\d+""".toRegex()
    val nums1 = listOf("abc", "123", "a3c").filter(numRegex1::matches)

    println(nums)
    println(nums1)
}
```

#### (4)、密封类和数据类

* 可以在同一个文件中的任意位置定义密封类的子类，而之前的版本中则要求子类必须作为密封类的嵌套类。
* 数据类可以继承其他类


#### (5)、lambda 表达式中的解构

简化传递给 lambda 的参数

```kotlin
fun main() {
    val map = mapOf<String, String>("one" to "1", "two" to "2")
    
    // 新的写法
    println(map.mapValues { (key, value) -> "$key ->$value" })

    // 旧的写法
    println(map.mapValues { entry ->
        val (key, value) = entry
        "$key -> $value"
    })
}
```

#### (6)、使用下划线 `_` 接收不使用的参数

这一点和 Go 中一样。

```kotlin
val map = mapOf<String, String>("one" to "1", "two" to "2")
// 下划线表示丢弃，不使用
map.forEach { _, value -> println("$value") }
```

#### (7)、数字字面值中的下划线

同 Java8 一样，允许使用下划线分割较大的数字值，方便阅读

```kotlin
val oneMillion = 1_000_000
val hexBytes = 0xFF_EC_DE_5E
val bytes = 0b11010010_01101001_10010100_10010010
```

#### (8)、省略属性的类型

如果某个属性的 getter 被定义为表达式，那么，可以省略该属性的类型。

```kotlin
data class Person(val name: String, val age: Int) {
    // 属性类型推断为 “Boolean”
    val isAdult get() = age >= 20
}
```

> 注意：此处中文版文档翻译有误。英文原文为：For properties with the getter defi􏰂ned as an expression body, the property type can now be omitted:


#### (9)、局部属性委托

局部变量可以使用属性委托了。通常用来定义一个延迟求职的局部变量。

```kotlin
val answer by lazy { 
	println("Calculating the answer...") 
	42
}
```


## 二、开始

### 1、基本语法

#### (1)、定义变量

* val : 定义 **只读局部变量** 使用关键字 val 定义。只能为其赋值一次。
* var : 可重新赋值的变量使用 var 关键字

#### (2)、类型检测和自动类型转转

```kotlin
fun getStringLength(obj: Any): Int? {
    if (obj !is String) return null
    
    // `obj` 在这一分支自动转换为 `String`
    return obj.length
}
```

#### (3)、for 循环遍历

三种遍历方式：

```kotlin
fun forFunc() {
    val namesList = listOf("张三", "李四", "王五")

    //    直接遍历元素
    for (name in namesList) {
        println("name = $name")
    }

    //    遍历索引——indices 得到的是一个索引闭区间
    for (index in namesList.indices) {
        println("nameList[$index]=${namesList[index]}")
    }

    //    同时遍历索引和元素
    for ((index, name) in namesList.withIndex()) {
        println("nameList[$index]=$name")
    }
}
```

#### (4)、区间

* `1..5` 表示 1-5 的闭区间
* `1 until 5` 表示 1-5 的左闭右开区间
* `5 downTo 1` 表示 5-1 的闭区间
* `a in range` 判断 a 是否在区间 range 内部
* `a !in range` 判断 a 是否不在区间 range 内部

```kotlin
fun main() {

    for (a in 1..5) {
        println("a = $a")
    }

    for (b in 1 until 5) {
        println("b ==> $b")
    }

    for (c in 5 downTo 1) {
        println("c ===> $c")
    }

    for (d in 1..6 step 2){
        println("步进为2遍历，d = $d ")
    }

    println("${4 in 1..4}")
    println("${5 !in 1..4}")
}
```

#### (5)、集合

```kotlin
fun main() {
    val nameList = listOf("张三", "李四", "王五")

    // 1、使用 in 判断集合中是否包含某个内容
    val flag = "张三" in nameList
    println("集合中包含元素 $flag")

    // 2、使用 lambda 过滤和映射集合
    val fruits = listOf("banana", "orange", "avocado", "apple")
    // 2-1、filter 过滤集合中符合规则的元素并生成一个新的集合
    fruits
            .filter { it.startsWith("a") }
            .sortedBy { it }
            .map { it.toUpperCase() }
            .forEach { println(it) }

    // 2-2、原始集合并没有被改变
    fruits.forEach {
        println("fruits的元素：$it")
    }
}
```


#### (6)、类型判断

* `a is 类型`  判断 a 是不是某个类型的数据


### 2、习惯用法

#### (1)、遍历 map

```kotlin
fun main() {
    val cusMap = mutableMapOf(1 to "One", 2 to "Two")
    //    添加元素方式1
    cusMap.put(3, "Three")
    //    添加元素方式2——推荐使用这种
    cusMap[4] = "Four"

    //    遍历 pair
    for (item in cusMap) {
        println("${item.key} = ${item.value}")
    }

    //    同时遍历 key 和 value
    for ((key, value) in cusMap) {
        println("$key ===== $value")
    }
}
```

#### (2)、延时属性

```kotlin
val p: String by lazy { 
	// 计算该字符串
}
```
#### (3)、if no null 缩写

* `xx?.属性` 如果 xx 为null，直接返回null；不为 null 则返回属性值 

```kotlin
val files = File("test").listFiles()

// 不为空的时候调用 files.size , 为空的时候直接返回 null
println(files?.size)
```
#### (4)、if not null and else 缩写

* `a ?: b` 如果 a 为 null 则返回 b , 否则返回 a。

```kotlin
val files = File("test").listFiles()

// ?: 表示 如果前面的为空为null，则使用后面的内容
println(files?.size ?: "没有文件")
```

#### (5)、表达式

if 、when 、 try/catch 都可以作为表达式返回一个值

#### (6)、with() —— 一个实例调用自己的多个方法

```kotlin
val myTurtle = Turtle()
with (myTurtle) { 
	 // 画一个 100 像素的正方形
    penDown()
    for (i in 1..4) {
        forward(100.0)
        turn(90.0)
    }
    penUp()
}

class Turtle {
    fun penDown()
    fun penUp()
    fun turn(degrees: Double)
    fun forward(pixels: Double)
}
```

#### (7)、可空布尔

```kotlin
val b: Boolean? = null

if (b == true) {
	// ......
} else {
	// `b` 是 false 或者 null 
}
```

### 3、编码规范

#### (1)、Code Style

* 使用推荐的编码样式：

Preferences | Editor | Code Style | Kotlin”，点击右上⻆的 “Set from...” 链接，并从菜单中选择 “Prede􏰀ned style / Kotlin style guide”

* 验证是否已经按照执行的样式格式化

进入 Preferences | Inspections, 并启用 “Kotlin | Style issues | File is not formatted according to project settings”探查项

#### (2)、语言特性的习惯用法

>TODO : CnPeng 这地方太迷了。。。。绕的一塌糊涂

##### A：使用作用域函数——apply/with/run/also/let

* 是否在块中的多个对象上调用方法，或者将上下文对象的实例作为参数传递? 如果是，则使用 also 或 let ，它们都以 it 而不是 this 的形式访问上下文对象。
	* 如果在代码块中根本没有用到接收者，那么使用 also 。

```kotlin
// 上下文对象是“it” 
class Baz {
    var currentBar: Bar?
    val observable: Observable
    val foo = createBar().also {
    	  // 访问 Baz 的属性
        currentBar = it
        // 将上下文对象作为参数传递
        observable.registerCallback(it)
    }
}

// 代码块中未使用接收者
val foo = createBar().also {
    LOG.info("Bar created")
}

// 上下文对象是“this” 
class Baz {
	val foo: Bar = createBar().apply {
		// 只访问 Bar 的属性 
		text = "Foo"
    	color = RED 
	} 
}
```

* 调用的结果是什么? 如果结果需是该上下文对象，那么使用 apply 或 also。如果需要从代码块中 返回一个值，那么使用 with、let 或者 run

```kotlin
// 返回值是上下文对象
class Baz {
    val foo: Bar = createBar().apply {
    	 // 只访问 Bar 的属性
        color = RED
        text = "Foo"
    }
}

// 返回值是代码块的结果
class Baz {
    // 只访问 Bar 的属性
    val foo: Bar = createNetworkConnection().let {
        loadBar()
    }
}
```

* 上下文对象是否可空，或者是否作为调用链的结果求值而来的? 如果是，那么使用 apply、let 或 者 run。否则，使用 with 或者 also

```kotlin
// 上下文对象可空
person.email?.let { sendEmail(it) }

// 上下文对象非空且可直接访问 
with(person) {
    println("First name: $firstName, last name: $lastName")
}
```

---

## 三、基础

### 1、基本类型
#### (1)、数字

* 不支持8进制
* 可以使用下划线标记较大的字面值
* 需要显示通过对应的函数进行类型转转


##### A: 位运算

完整的位运算列表(只用于 Int 与 Long)

中缀函数|含义
---|---
shl(bits) | 有符号左移 (Java 的 << ) 
shr(bits) | 有符号右移 (Java 的 >> ) 
ushr(bits) | 无符号右移 (Java 的 >>> ) 
and(bits) | 位与
or(bits) | 位或
xor(bits) | 位异或 
inv() | 位非

示例：`val x = (1 shl 2) and 0x000FF000`

#### (2)、字符——Char

* 不能作为数字直接参与运算，必须通过 `.toInt()` 等函数显示转换为数字类型
* 使用单引号包裹

#### (3)、布尔——Boolean

布尔运算符只有： `||`、`&&`、`!` 三种


#### (4)、数组——Array

* 内置 setter 、getter ，内置 size 属性
* 创建数组时，可以使用 arrayOf()、arrayOfNulls()、Array()
* 非字面值常量，只能使用上述三种方式构建数组。
* 对于字面值常量，除了前述三种方式，还可以使用 byteArrayOf()、shortArrayOf()、intArrayOf()等构建数组

```kotlin
fun main() {
    // 1、声明并初始化数组
    val array1 = arrayOf(1, 2, 3, 4)
    array1[0] = 11
    println("numArray1[0] = ${array1[0]}")

    // 2、声明指定元素类型和元素数量的空数组
    val array2 = arrayOfNulls<Int>(10)
    val value = array2[0]
    array2[0] = 1
    println("value = $value, nullArray[0]= ${array2[0]}")

    // 3、直接调用 Array 的构造函数：第一个参数为大小，第二个为获取元素的函数( lambda 表达式)
    val array3 = Array(10) { i -> "这是第 $i 个元素" }
    println("array3[3]= ${array3[3]}")
}
```

运行结果：

```
numArray1[0] = 11
value = null, nullArray[0]= 1
array3[3]= 这是第 3 个元素
```

#### (5)、无符号数值类型

##### A: 基本介绍

Kotlin 1.3 才开始使用无符号类型。(Swift 中也有)

* 类型包括：UByte、UShort、UInt、ULong
* 对于数组和区间也有对应的 UxxxArray 类型。
* 无符号数值在书写上需要后缀 u 或 U, 如果是 ULong 则需要后缀 UL 或 uL

```kotlin
//显示声明类型
val b: UByte = 1u
val s: UShort = 1U
val l: ULong = 1u

//类型推导
val a1 = 42u
val a2 = 0xFFFF_FFFF_FFFFu

val intU3 = 3uL
// true
println(intU3 is ULong)
```

##### B: 注意事项

无符号数值类型是实验性的内容，兼容性和稳定性不确定，后期支持情况不确定，慎用！！！



#### (6)、字符串

* 可以使用 `str[i]` 的索引模式访问字符串中的字符元素
* 可以使用 for 循环遍历字符串中的字符元素
* 可以使用 `+` 链接字符串，但推荐优先使用字符串模板——`${}` 
* 分为转义字符串和原始字符串。

原始字符串通常使用 `|` 作为每一行的边界前缀，使用 trimMargin() 可以去除原始字符串的边界前缀。

```kotlin
fun main() {

    //使用使用默认的边界前缀 | ，然后 trimMargin() 去除它
    val text = """
        |Tell me and I forget.
        |Teach me and I remember.
        |Involve me and I learn.
        |(Benjamin Franklin)
        """.trimMargin()
    println(text)
    
    println()

    //自定义边界前缀，然后去除它
    val text2 = """
        >Tell me and I forget.
        >Teach me and I remember.
        >Involve me and I learn.
        >(Benjamin Franklin)
        """.trimMargin(">")
    println(text2)
}
```

运行后的输出结果：

```
Tell me and I forget.
Teach me and I remember.
Involve me and I learn.
(Benjamin Franklin)

Tell me and I forget.
Teach me and I remember.
Involve me and I learn.
(Benjamin Franklin)
```


### 2、包
#### (1)、导入

* 导入包时如果出现同名的类型，可以定义别名, 然后使用别名引用其成员

```kotlin
import foo.Bar 
import bar.Bar as bBar  // bBar 代表“bar.Bar”
```

### 3、控制流
#### (1)、if 表达式
#### (2)、When 表达式
#### (3)、For 循环
#### (4)、While 循环
#### (5)、循环中的 break 和 continue 
#### (6)、

### 4、返回和跳转
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

## 四、类与对象

### 1、
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 2、包
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 3、控制流
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、返回和跳转
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

## 五、

### 1、
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 2、
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 3、
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

---

## 十二、协程

### 1、协程基础

在开始编码前，先新建 Gradle 项目，然后引用 kotlinx.coroutines 包。

![](https://images.gitee.com/uploads/images/2019/0304/103537_373787f0_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0304/103615_abe391fe_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0304/103706_68c306b1_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0304/103811_b3be5865_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0304/104047_7d4e0e8a_930142.png "屏幕截图.png")

[点击查看官方关于 引入 kotlinx.coroutines 包的参考](https://github.com/hltj/kotlinx.coroutines-cn/blob/master/README.md#using-in-your-projects)

#### (1)、第一个协程程序

#### (2)、桥接阻塞与非阻塞
#### (3)、等待作业
#### (4)、结构化并发
#### (5)、作用域构建器
#### (6)、协程是轻量的
#### (7)、全局协程与守护线程

### 2、取消与超时
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 3、通道(实验性)
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 4、组合挂起函数
#### (1)、
#### (2)、
#### (3)、
#### (4)、
#### (5)、
#### (6)、

### 5、携程上下文与调度


### 6、异常处理

### 7、监督

### 8、select 表达式(实验性的)

### 9、共享的可变状态与并发
