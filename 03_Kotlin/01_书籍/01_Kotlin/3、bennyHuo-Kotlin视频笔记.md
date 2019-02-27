

## V04-Map 20180212

### 知识点：

（1）、**调用 .map 方法相当于直接遍历了这个数组中的元素，具体元素用 it 固定标识。**

```
fun main(args: Array<String>) {
    args.map { println(it) }
}
```
上述方法的含义是，接收一个数组类型的参数，然后遍历该数组，并将其中的参数逐个打印出来。

模拟参数输入的步骤如下:

* 运行该程序
* 按照下图选择“Edit Configutations”
![选择编辑参数](https://gitee.com/uploads/images/2018/0213/084428_a8ca21d1_930142.png "屏幕截图.png")
* 按照下图方式输入参数
![输入参数](https://gitee.com/uploads/images/2018/0213/084635_84970ebf_930142.png "屏幕截图.png")
* 重新运行改程序，在控制台就会输入结果，如下：
![运行结果[输入链接说明](http://)](https://gitee.com/uploads/images/2018/0213/084747_bb9b80bc_930142.png "屏幕截图.png")

## V05-flatMap 20180212

### 知识点：
（1）、**vararg 定义参数为可变参数（本质是一个数组）**

（2）、**.flatMap（） 遍历数组中的元素，并按照 split（） 指定的规则返回一个新的数组**

```
fun main(vararg args: String) {
    args.flatMap {
        it.split("_")
    }.map {
        println(it)
    }
}
```

输入的参数：
![输入参数](https://gitee.com/uploads/images/2018/0213/091110_87b49140_930142.png "屏幕截图.png")

输出的结果：
![输出参数](https://gitee.com/uploads/images/2018/0213/091133_3c84b9f9_930142.png "屏幕截图.png")


## v6-枚举、when表达式

###1、 知识点：
#### (1)、enum class
**enum class** 用来定义枚举类，在java中enum虽然没有显示体现 class ,但本质也是一个类。

```
/**
 * 普通 enum 类的初始化
 * 每个枚举常量都是一个对象，多个枚举常量之间用逗号间隔
 */
enum class Direction {
    NORTH,
    SOUTH, WEST, EAST
}

/**
 * 枚举类中的每一个枚举常量本质都是当前枚举类的一个实例，所以，枚举常量也可以携带参数。
 * 该参数会作为该枚举类构造实例时使用的参数。
 */
enum class Color(colorInt: Int) {
    RED(0xFF0000),
    GREEN(0x00FF00),
    BLUE(0x0000FF)
}

/**
 * 如果枚举类中有定义方法，则最后一个枚举常量后面需要跟分号
 * 如果枚举类中定义的方法需要使用枚举类的构造参数，则需要在构造参数前加 var 或者 val 标识
 */
enum class SayHello(val hello: String) {
    English("Hello"),
    Chinese("你好");

    fun sayHello() {
        println(hello)
    }
}
```

#### (2)、 **companion object** 
**companion object** 用来定义伴生对象，意思就是同类一起初始化，类似于java中的静态成员变量，也**可以直接通过当前类名直接调用伴生对象中的方法或变量。**
>
>    * 伴生对象并不等价于 java 中的静态成员

伴生对象的完整写法如下：
       
```
//伴生对象的本质也是定义一个类，所以可以指定类名
class Country {
    companion object Province {
        fun printProvinceName(args: String) {
            println("您输入的省份名称是：$args")
        }
    }
}
```
伴生对象的精简写法如下：
```
//这种精简写法省略了类名，会使用默认的类名：Companion
class Country {
    companion object {
        fun printProvinceName(args: String) {
            println("您输入的省份名称是：$args")
        }
    }
}

```
伴生对象的调用：
```
fun main(args: Array<String>) {
    Country.printProvinceName("山东")
}
```

伴生对象可以实现接口或继承类
```
interface Factory<T> { 
    fun create(): T
}

class MyClass {
    companion object : Factory<MyClass> {
        override fun create(): MyClass = MyClass() 
    }
}
```


#### (3)、扩展类方法
假设我们引用了一个三方库，我们无法修改它的源码，但是我们想往这个三方库中增加一个方法该怎么办呢？
`这个在 IOS 中有相关概念`
>
>fun 类名.新方法名（）{
>
>    //方法体
>
>}

示例代码如下：

```
/**
 * 定义 HaHa 类
 */
class HaHa {
}

/**
 * 在外部定义属于HaHa类的一个方法
 */
fun HaHa.printHaHa() {
    println("哈哈哈哈")
}


/**
 * 调用定义在HaHa类外部的方法时，也是需要使用HaHa类的实例进行调用
 */
fun main(array: Array<String>): Unit {
    HaHa().printHaHa()
}

```
###2、 完整需求和示例代码：
####（1） 完整需求
* 定义带有构造参数枚举类
* 在类外部定义属于该类的方法
* 使用when遍历
* 引用定义在该类外部的方法


#### （2）示例代码
```
enum class Language(val hello: String) {
    ENGLISH("Hello"),
    CHINESE("你好");

    companion object {
        fun parse(language: String): Language {
            return Language.valueOf(language.toUpperCase())
        }
    }

    fun sayHello() {
        println(hello)
    }
}

fun main(args: Array<String>) {
    if (args.isEmpty()) {
        return
    }

    val language = Language.parse(args[0])
    println(language)
    language.sayHello()

    //调用在类外部定义的方法
    language.sayBye()
}


/**
 * 在Language外部定义一个类方法，
 * kotlin 中 when 语句会有返回值
 * 下面 when 语句中的 this 代表当该方法所归属的类，即Language
 */
fun Language.sayBye() {
    val bye = when (this) {
        Language.ENGLISH -> "Bye"
        Language.CHINESE -> "明儿见"
    }

    println(bye)
}
```
#### （3）配置的参数：
![输入图片说明](https://gitee.com/uploads/images/2018/0213/160508_d4760fef_930142.png "屏幕截图.png")

#### （4）运行结果：

![输入图片说明](https://gitee.com/uploads/images/2018/0213/160526_d8a55fbd_930142.png "屏幕截图.png")



  

[https://github.com/enbandari/Kotlin-Tutorials](https://github.com/enbandari/Kotlin-Tutorials)
## Kt07（gradle项目）

### 学习目标：
统计某段文本中每个字符出现的次数


### 前提准备
* 添加 RxJava 依赖
* 在resources 目录下创建一个文本文件，命名为 input


### 知识点
* 在java中，参数是一个接口，并且只有一个方法，则可以直接用lambada表达式替代
* 查看字节码文件 Code -- show ByteCode xxx

### 代码

## Kt08（gradle项目）
### 学习目标
Kt08 使用 Retrofit 发送 GET 请求

### 知识点
* [查看 gitHub 某个项目中 star 的用户列表](https://api.github.com/repos/enbandari/Kotlin-Tutorials/stargazers)




