最后更新日期：2019-02-28

基于 [传智 Kotlin 基础--张泽华](http://study.163.com/course/courseLearn.htm?courseId=1004187027#/learn/video?lessonId=1048181209&courseId=1004187027) 整理



## V4、程序入口--main函数 
![](https://gitee.com/uploads/images/2018/0508/073038_4fc8e64c_930142.png "屏幕截图.png")


## V5、变量与输出

### 1、变量的声明：
  
```kotlin
//变量声明关键词 var , 语句末尾不需要分号
var name=“张三”  
```

### 2、数据类型

![](https://gitee.com/uploads/images/2018/0508/074815_f9c40ad3_930142.png "屏幕截图.png")


## V6、二进制基础
![](https://gitee.com/uploads/images/2018/0508/074959_085fa426_930142.png "屏幕截图.png")

![](https://gitee.com/uploads/images/2018/0508/075048_6f82928f_930142.png "屏幕截图.png")



## V7、类型推断和显示类型声明（变量和常量的声明）

### 1、类型推断
* 在声明一个变量时，我们可以不需要显示声明数据类型，kotlin 会根据你为变量赋的值动态的推导出其类型。
* 当一个变量被赋予了某个类型的数值之后，不能再赋给他其他类型的数值，否则，会报 类型错误。

```kotlin
fun main(args:Array<String>){
	 //声明变量a , 将int类型的10 赋值给a, a 将只能接受int型数据
    var a=10    
    a=15

	  //将字符串类型的数据赋值给int型的a ,报错！！
    //a="字符串"  

    var s="字符串"
}
```

### 2、显示类型声明

格式：`var 变量名：变量类型=值`

```kotlin
var a:Int=10 
```

### 3、变量声明的注意事项

* 如果声明变量时就赋初值，可以不写类型，此时，类型推导将生效
* 如果声明变量时没有赋初值，则必须写明类型，否则会报错。格式：`var a:Int`


### 4、常量的声明

关键字：val 
 
格式：`val 常量名：常量类型=常量值`

## V8、变量的取值范围

获取某种类型数据的最大最小值

```kotlin
fun main(args:Array<String>){
    val minByte:Byte=Byte.MIN_VALUE
    val maxByte:Byte=Byte.MAX_VALUE

	 //将二进制的0b0011 转换为10进制，并复制给a 
    val a:Int=0b0011    
 }
```

* Byte、Int、Long 可以通过上述方式获取最大最小值
* String 没有最大最小值，所以没有上述方法



## V9、Kotlin函数入门

* main() 程序入口
* println() 打印

函数声明基本格式：`fun 函数名（参数：参数类型）{函数体} `  



## V10、Boolean运算

```kotlin
 //sqrt()--开根号，获取5.0的开根值，即根号5的值
Math.sqrt(5.0)    

//pow()--幂函数，计算2的100次方    
Math.pow(2.0,100.0)    
```


## V11、命令行交互式工具

不实用，内容省略。


## V12、函数加强

函数命名格式说明：

```kotlin
fun 函数名(参数:参数类型):返回值类型{
    函数体
}
```

* 如果没有返回值，使用 :Unit 标识，也可以省略不写
* 返回值也是使用 return 返回。


## V13、函数声明练习题

内容省略


## V14、字符串模板

* `“ ” `, 被两个双引号包裹的内容是普通字符串，支持转义字符
* `“”“  ”“” `，被一对三引号包裹的内容是原样字符串，不支持转义字符，其中的内容被定义成什么样，输出的时候就是什么样。

* **字符串模板格式：${占位字符串}**


## V15、条件控制--if、else

基本格式：

```kotlin
if(条件){
    //DO STH 
}else{
    //DO STH
}
```

详细可参考：[https://www.jianshu.com/p/b8eb0fe28dad](https://www.jianshu.com/p/b8eb0fe28dad)


## V16、字符串比较

```kotlin
var str1="张三"    // = 是赋值
var str2="李四"

// == 是比较，等同于 Java中的 equals()  
boolean flag=str1==str2 
```

* 可以使用 == 比较字符串
* 也可以使用 **`equals( , )`** 方法比较字符串，该方法第二个参数表示是否忽略大小写，true--忽略大小写，false--不忽略大小写。
* `equals( , )` 中第二个参数为 true 时 效果等价于 Java 中的 `equalsIgnoreCase()`


## V17、空值处理

* 以 null 表示空值
* kotlin 中定义方法时，默认接收的是 非 null 参数。
* 如果定义某个方法可以接收 null参数，则在声明方法参数时在参数后面加上？。示例如下：

```kotlin
//String 后面的 ？ 就表示该方法可以接收 null 作为参数
fun test(str1:String?){    
    //DO STH 
}
```

## V18、When表达式

类似于 Java 中的 switch

基本使用格式：

```kotlin
when(变量){
    分支A -> 表达式
    else -> 表达式
}
```

带有返回值的When表达式：

```kotlin
var result=when(变量){
    分支A -> 表达式（要有返回值，最终将值赋给result）
    else -> 表达式（要有返回值，最终将值赋给result）
}
```

详细参考：[https://www.jianshu.com/p/b8eb0fe28dad](https://www.jianshu.com/p/b8eb0fe28dad)


## V19、Loop和Range


* 声明一个区间数组

```kotlin
//表示我们声明了一个闭区间数组，其中包含的数值为 1-100。 .. 表示闭区间
var nums1=1..100  

//前闭后开区间，取值 1-99. util 表示前闭后开区间
var nums2=1 util 100 
```

* for 基本循环格式

```kotlin
for(变量 in 数组或字符串){
    //DO STH 
}
```

* 带有步进的for循环

```kotlin
//所谓步进，就是递增幅度。默认步进为1
for (变量 in 数组 step 步进数量){    
    //DO STH 
}
```

* `数组.reversed()`，数组内容反转
* `数组.count()`，获取数组的容量，等价于 Java 中的 `数组.length`

详细参考：[https://www.jianshu.com/p/b8eb0fe28dad](https://www.jianshu.com/p/b8eb0fe28dad)

## V20、List 和 Map

### 1、List

* List的基本声明格式：

```kotlin
//声明List时主要是通过 listOf()实现
var list1=listOf(元素1，元素2，元素3)    
```

* 使用 for 循环同时遍历索引和索引对应的数值

```kotlin
 //重点是 withIndex() 方法，index 接收索引，value 接收对应的值
for（(index,value) in list.withIndex()）{   
    //DO  STH 
}
```

### 2、Map (字典)

* 基本声明格式：

```kotlin
var map=TreeMap<键类型,值类型>()
map[key]=value
```
* 示例代码：

```kotlin
//声明 map
var map=TreeMap<String,String>()    

//添加键值对元素
map["好"] = “good”
map["学习"] = “study”    

//取值并打印
println(map["好"])    
```


## V21、函数和函数表达式

### 1、函数的简化

```kotlin
//原函数：
fun sum(a:Int , b:Int):Int{
    return a+b
}

//简化后
fun sum(a:Int , b:Int):Int=a+b
```

### 2、使用 var 声明函数——函数表达式1(重点！)

kotlin 中除了使用基本的 fun 关键字声明函数外，还可以使用 var 声明。

（CnPeng :这种方式等价于 Go 和 Swift 中的函数类型）

示例如下：

```kotlin
//声明函数类型变量 i,接收两个Int类型参数 x、y，返回 x+y 的值
var i = {x:Int , y:Int -> x+y} 
//调用使用 var 声明的函数 i
i(3,5) 
```

### 3、使用 var 声明函数——函数表达式2（重点！）

```kotlin
//声明函数 j,它接收的参数是两个 Int， 返回一个 Int，对应的表达式是 {x,y->x+y}
var j:(Int,Int)->Int = {x,y -> x+y}   

//调用函数
j(4,4)    
```

  
详细参考：[https://www.jianshu.com/p/da4a93a356d6](https://www.jianshu.com/p/da4a93a356d6)



## V22、默认参数和具名参数

* 具有默认参数值的函数声明

```kotlin
val Pi = 3.1415926
fun getRoundArea(PI:Float=Pi , radius:Float):Float{    
	//为变量PI赋予了默认值 Pi,这样，调用该方法时可以不再传递PI。
	//但，如果我们想传入的值和默认值不一致时还是需要传入的
    return PI*radius*radius
}
```

* 调用带有默认参数值的函数

```kotlin
//因为我们相传入的PI和默认值不一致，所以，需要将3.14f传入
var a=getRoundArea(3.14f,5.0f) 
```

* 具名参数的使用

所谓具名参数，就是调用某个方法时指明传入的参数是给哪个变量的。

```kotlin
// 我们需要的 PI 值与默认值一致，此时不需要再传入PI值。
// 只需要通过 radius=5.0f 声明我们传入了半径值
var a = getRoundArea(radius=5.0f) 
```


## v23、字符串和数字的转换

```kotlin
//字符串转换为Int
var a="13"
a.toInt()    

//Int转换为字符串
var b=13
b.toString()    
```


## V24、人机交互--根据用户输入的数字动态计算Sum

意念交互、语音交互、眼动跟踪、体感交互、打字交互

```kotlin
fun main(array:ArrayList<String>){

    println(“请输入第一个数值”)
    //读取键盘输入的字符串内容，并赋值给a 
    var a=readLine()    
    println(“请输入第二个数值”)
    var b=readLine()

	//readLine(）得到的可能是null，所以此处通过 !! 断言输入不为null
    var num1=a!!.toInt()  
    var num2=b!!.toInt() 

    println("${num1}+${num2}=${num1+num2}")
}
```


## V25、异常处理

异常处理格式：--同Java

```kotlin
try{
    //可能会出错的代码块
}catch(e:Excepiton){
    //出错之后的处理逻辑
}
```


## V26、递归

### 1、利用递归实现 阶乘函数

```kotlin
fun fact(a:Int):Int{
    if(a==1){
        return 1
    }else{
    	 //函数内调用函数本身就成为了递归
        return a*fact(a-1) 
    }
}
```

### 2、BigInteger

在上面的方法中，我们用 Int 来接收阶乘的值，但是如果我们输入 100 ，会发现最终返回的值是 0， 是因为 100的阶乘超过了 Int的范围。所以，此时我们就需要使用到 **BigInteger**。

BigInteger 用来表示一个超大值。

```kotlin
import java.math.BigInteger

fun main(array: Array<String>) {
	 //声明BigInteger常量时传入一个字符串类型的数值
    val num = BigInteger("50")    
    println(fact(num))
}

fun fact(num: BigInteger): BigInteger {
    return if (num == BigInteger.ONE) {
        BigInteger.ONE
    } else {
        num * fact(num - BigInteger.ONE)
    }
}
```


## V27、尾递归优化

### 1、什么是尾递归？

[点击查看阮一峰老师关于尾递归的介绍](http://www.ruanyifeng.com/blog/2015/04/tail-call.html)

* 尾递归 ：是指某个函数的最后一步依旧是调用自身
* kotlin 中尾递归关键字 **tailrec**


### 2、为什么需要尾递归优化？ 

递归非常耗费内存，因为需要在内存中同时保存成千上百个调用记录，很容易发生"栈溢出"错误（stack overflow）。但 **对于尾递归来说，由于只存在一个调用记录，所以永远不会发生"栈溢出"错误**。


### 3、使用尾递归实现累加

```kotlin
fun main(args: Array<String>) {
    println(accumulation(5, 1))
}

/**
 * tailrec 是尾递归函数的关键字
 * 尾递归函数是指，在该函数的最后一步操作中依旧是调用函数本身
 * 为了实现尾递归，我们定义了该方法接收两个参数：num、total
  * num 是我们传入的需要计算累加值得的变量，total用来接收最终的返回值
 */
tailrec fun accumulation(num: Int, total: Int): Int {
    return if (num == 1) {
        total
    } else {
    	 //此时，该调用的含义是：先计算 total=num+total,然后计算 num=num-1
        accumulation(num - 1, num + total)  
    }
}
```


## V28、IDEA介绍
内容省略。
IDEA开发环境的搭建可以参考：https://www.jianshu.com/p/5a3e56f90b7f


## V29、面向对象入门——定义一个类并构建对象

```kotlin
// 定义一个类，包含两个成员变量 height 和 width——
// 因为加了var ,所以这两个参数也是Rect 类的属性
class Rect(var height:Int,var width:Int)

fun main(args: Array<String>) {
	 //构建Rect对象，不需要new
    var rect=Rect(5,10) 
        
        //引用Rect类中成员变量
    println("矩形的宽${rect.width}高${rect.height}") 
}
```


## V30、静态属性和动态行为/方法——为类定义成员方法

```kotlin
//定义一个类，包含两个成员变量 height 和 width.并定义一个成员方法
class Rect(var height: Int, var width: Int) {
    fun getArea(a: Int, b: Int): Int = a * b
}

fun main(args: Array<String>) {
	//构建Rect对象，不需要new
    var rect = Rect(5, 10) 
    //引用Rect类中成员变量
    println("矩形的宽${rect.width}高${rect.height}") 
    //引用Rect类中成员方法
    println("矩形的面积是${rect.getArea(rect.width, rect.height)}") 
}
```


## V31、面向对象的概念解释
没有实际内容，所以省略



## V32、面向对象--洗衣机洗衣服案例

### 1、新建 WashMachine 类文件

```kotlin
//定义成员变量——品牌，容量; 定义成员方法。
class WashMachine(var brand: String, var size: Int) {    
    fun openTheDoor() {
        println("打开洗衣机的门")
    }

    fun closeTheDoor() {
        println("关上洗衣机的门")
    }

    fun start() {
        println("启动洗衣机洗衣服")
    }
}
```

### 2、引用WashMachine类执行洗衣服的操作

```kotlin
fun main(args: Array<String>) {
    var washMachine = WashMachine("小天鹅", 15)    
      washMachine.openTheDoor()
    washMachine.closeTheDoor()
    washMachine.start()
}
```


## V33、面向对象——升级版洗衣机案例

### 1、升级版洗衣机逻辑--检测门的开启状态和转速模式

```kotlin
class WashMachine(var brand: String, var size: Int) {

    var isDoorOpen = false
    var curMode = 0

    fun openTheDoor() {
        println("打开洗衣机的门")
        isDoorOpen = true
    }

    fun closeTheDoor() {
        println("关上洗衣机的门")
        isDoorOpen = false
    }


    fun selectMode(mode: Int) {
        curMode = mode
        when (mode) {
            0 -> println("慢速模式")
            1 -> println("标准模式")
            2 -> println("快速模式")
            else -> println("初始状态")
        }
    }

    fun start() {
        if (isDoorOpen) {
            println("洗衣机的门还没有关闭")
        } else {
            when (curMode) {
                0 -> {
                    println("您选择的是慢速模式，发动机转速慢")
                    println("启动洗衣机洗衣服")
                }
                1 -> {
                    println("您选择的是标准模式，发动机常速运转")
                    println("启动洗衣机洗衣服")
                }
                2 -> {
                    println("您选择的是快速模式，发动机转速快")
                    println("启动洗衣机洗衣服")
                }
                else -> println("您还没有选择转速模式，发动机不会运转，不执行洗衣操作")
            }

        }
    }
}
```

### 2、引用洗衣机对象洗衣服

```kotlin
fun main(args: Array<String>) {
    var washMachine = WashMachine("小天鹅", 15)
    washMachine.openTheDoor()
    washMachine.closeTheDoor()
    washMachine.selectMode(1)    //加入了模式选择
    washMachine.start()
}
```

## V34、面向对象——封装

* 隐藏内部实现细节即为封装——Java系的程序员都知道！
* 私有关键字 ：private —— 同Java。私有即是封装的一种体现


## V35、面向对象——继承

* 一个对象直接使用另一个对象的属性或方法 —— 同Java
* **被继承的父类必须用 open 修饰，表示允许其他类继承该类**
* **父类中的方法如果允许子类重写，也需要用 open 修饰**
* **重写父类方法时需要用 overRide 修饰重写后的方法**
* 继承的格式：`class 子类：父类()`


父类：

```kotlin
//用 open 修饰，允许被继承
open class Father {    
    var character = "性格内向"
    
    //用open修饰，允许被重写
    open fun action() {    
        println("喜欢读书")
    }
}
```

子类：

```kotlin
//继承。 Son 继承自 Father
class Son : Father() {   

	 //重写父类方法 
    override fun action() {   
        //super.action()
        println("儿子的性格是$character")   
        println("儿子不喜欢看书，但是喜欢唱歌")
    }
}
```


## V36、抽象类及其实现

* 抽象的关键字 abstract —— 同Java
* 抽象类和方法不需要用 open 声明可以被继承/实现

**抽象类Human：**

```kotlin
 //定义抽象类，使用 abstract 修饰。包含成员变量name
abstract class Human (var name: String){     
 	 //定义抽象方法， 使用 abstract 修饰
    abstract fun eat()     
}
```

**抽象类的子类Man：**

```kotlin
//继承自Human抽象类
class Man(name: String) : Human(name) {  
	 //必须重写抽象方法 
    override fun eat() {    
        println("${name}是男人，是家中劳力，所以吃的多")
    }
}
```

**调用子类**

```kotlin
fun main(args: Array<String>) {
    var man=Man("张三")
    man.eat()
}
```


## V37、多态

* 同种功能，不同的表现形式 即为 多态

`视频中用男人和女人的尿尿姿势来解释多态，很形象哈`

**抽象类Human:**

```kotlin
 //定义抽象类，使用 abstract 修饰。包含成员变量name
abstract class Human(var name: String) {  
	 //定义抽象方法， 使用 abstract 修饰      
	 abstract fun eat() 
	 //定义抽象方法     
    abstract fun pee()  
}
```

**子类Man:**

```kotlin
class Man(name: String) : Human(name) {
    override fun pee() {
        println("${name}是男人，是站着尿尿的")
    }

    override fun eat() {
        println("${name}是男人，是家中劳力，所以吃的多")
    }
}
```

**子类Woman:**

```kotlin
class Woman(name: String) : Human(name) {
    override fun eat() {
        println("${name}是女人,饭量比较小")
    }

    override fun pee() {
        println("${name}是女人，是蹲着尿尿的")
    }
}
```

**外部调用:**

```kotlin
fun main(args: Array<String>) {
    var man=Man("张三")
    var woman=Woman("小花")

    var list= listOf<Human>(man,woman)    //定义集合
    for (human in list){    //遍历集合
        human.pee()
    }
}
```

## V38、接口及其实现

* 接口--数据有进有出的交互方式、
* 接口关键字：interface —— 同Java
* 接口是事物的能力（代表某种事物的特性），抽象类是事物的本质（代表的是一类事物的共性）
* 子类实现接口时，接口名后面不需要（）

**定义接口IMan:**

```kotlin
 //定义一个男人的接口
interface IMan {   
    fun xiaodidi()
}
```

**Man类实现IMan接口:**

```kotlin
//男人属于人，所以继承Human; 男人有小弟弟，所以实现 IMan接口
class Man(name: String) : Human(name) ,IMan{  
    override fun xiaodidi() {
        println("这是重写IMan接口中的方法——男人有小弟弟")
    }

    override fun pee() {
        println("${name}是男人，是站着尿尿的")
    }

    override fun eat() {
        println("${name}是男人，是家中劳力，所以吃的多")
    }
}
```

**太监不能实现IMan接口:**

```kotlin
 //太监属于人，但是太监没有小弟弟，所以不能实现 IMan接口
class TaiJian(name: String) : Human(name) { 
    override fun eat() {
        println("太监也能吃饭")
    }

    override fun pee() {
        println("太监也会尿尿")
    }
}
```


## V39、代理和委托——大头儿子和小头爸爸的洗碗案例

* 委托，把自己不干的事情交给别人做
* 代理，做别人委托的事情
* **kotlin中接口代理关键字：by**

### 1、场景说明

```
围裙妈妈只负责做饭，不负责洗碗
小头爸爸洗一次碗可以赚到10元
大头儿子洗一次碗可以赚到1元
小头爸爸承揽了洗碗的活，最终交给大头儿子做，中间赚了9元差价
```

### 2、代码实现——完全委托

#### (1)、定义洗碗的接口

```kotlin
//定义一个洗碗接口，包含一个洗碗方法
interface IWashBow {    
    fun washBow()
}
```

#### (2)、大头儿子实现接口

```kotlin
 //被实现的接口后面不需要加（）
class BigHeadSon:IWashBow {   
    override fun washBow() {
        println("我是大头儿子，每次洗碗赚1元钱")
    }
}
```

#### (3)、小头爸爸实现接口并委托事件给小头儿子

```kotlin
//委托关键字 by;被委托方(即代理方)如果不是单例类，则后面需要跟()
class SmallHeadFather : IWashBow by BigHeadSon(){    

}
```

#### (4)、程序调用及输出结果

```kotlin
fun main(args: Array<String>) {
    var father=SmallHeadFather()
    //小头爸爸已经将洗碗的操作委托为小头儿子了，所以，此处本质是调用的小头儿子的洗碗操作
    father.washBow()    
}
```

![](https://gitee.com/uploads/images/2018/0510/104919_b477339e_930142.png "屏幕截图.png")


## V40、单例模式

* **单例关键字：object**
`我们在定义一个类时，使用 object 替换 class 来修饰这个类，就表示，这是一个单例类`
* **单例类作为代理人时，不需要（ ）**


### 1、场景说明

```
小头爸爸为了增进父子感情，想和小头儿子一起洗碗
```


### 2、代码实现

#### (1)、小头爸爸重写接口方法 —— 未使用单例时的错误写法

```kotlin
class SmallHeadFather:IWashBow by BigHeadSon(){    

    override fun washBow() {
        println("我是小头爸爸，我把洗碗事件委托给了大头儿子")
        //委托方重写了事件之后，需要手动调用代理方的方法。但是，此处又通过（）构建了一个小头儿子对象，已经不再是我们初始委托的那个大头儿子了。所以，此处是有问题的。
        BigHeadSon().washBow()  
        println("我是小头爸爸，大头儿子洗完碗之后，我赚了9元")
    }
}
```

#### （2）、使用单例后的正确写法：

**大头儿子单例类**

```kotlin
//单例关键字 object ,声明为单例类之后会立即在内存中创建单例对象，并一直存在
object BigHeadSon:IWashBow {    
    override fun washBow() {
        println("我是大头儿子，每次洗碗赚1元钱")
    }
}
```

**小头爸爸委托事件给单例的大头儿子**

```kotlin
//被委托方(即代理方)是单例类，不需要通过（）构建对象
class SmallHeadFather:IWashBow by BigHeadSon {     

    override fun washBow() {
        println("我是小头爸爸，虽然我把洗碗事件委托给了小头儿子，但是我要和他一起洗碗")
        // 委托方重写了事件之后，需要手动调用代理方的方法。
        // 由于 BigHeadSon是单例的，所以，这还是我们之前委托的那个儿子
        BigHeadSon.washBow()  
        println("我是小头爸爸，我和小头儿子洗完碗之后，我赚了9元")
    }
}
```

#### （3）、外部调用
  
```kotlin
fun main(args: Array<String>) {
    var father=SmallHeadFather()
     // 小头爸爸已经将洗碗的操作委托为小头儿子了，但因为重写了洗完事件，所以，本质是调用的父亲的洗完事件，
     // 父亲的洗碗事件中有一部分是自己做的，另一部分是儿子做的
    father.washBow()    
}
```

#### （4）、输出结果

![](https://gitee.com/uploads/images/2018/0510/111256_7e7741e3_930142.png "屏幕截图.png")


## V41、枚举--enum

**枚举示例代码：**

```kotlin
//枚举关键字 enum 
enum class Week {   
    星期一, 星期二, 星期三, 星期四, 星期五, 星期六, 星期天
}

fun main(args: Array<String>) {
    println(Week.星期一)
    println("${Week.星期一}在Week中的索引是${Week.星期一.ordinal}")
}
```

**运行结果：**

![](https://gitee.com/uploads/images/2018/0510/112424_15b4d07d_930142.png "屏幕截图.png")



## V42、印章类/密封类 (Sealed Class)

### 1、印章类的特点

* 子类类型有限的类称为 印章类/密封类
* 印章类使用 sealed 作为修饰符
* 印章类本身没有构造方法

### 2、印章类与枚举的区别

* 都是有限的数据
* 枚举更注重具体的数据
* 印章类更注重数据的类型

### 3、印章类示例代码

#### （1）、场景说明

```
假设你家有一头公驴、一头母驴、一头公马。那么，
它们可能会生出一头小驴，
也可能会生出一头小骡子。
```

#### （2）、代码示例

在上述场景中，由于他们能生出的儿子类型时固定的，所以，我们可以使用印章类来标识。

**声明印章类：**

```kotlin
//使用 sealed 声明 Son 为印章类/密封类
sealed class Son { 
	 //声明小骡子 SmallMule 为 Son的子类。
    class SmallMule() : Son() 
    //声明小驴子 SmallDonkey 为 Son的子类  
    class SmallDonkey() : Son() 

    fun sayHello(son: Son) {
  		 //判断是不是XX的实例的关键字 is 
        if (son is SmallMule) {     
            println("小骡子对大家说大家好")
        } else if (son is SmallDonkey) {
            println("小驴子对大家说大家好")
        }
    }
}
```

**调用印章类:**

```kotlin
fun main(args: Array<String>) {
    var mule = Son.SmallMule()
    var donkey = Son.SmallDonkey()

    var list = listOf<Son>(mule, donkey)
    for (son in list) {
        son.sayHello(son)
    }
}
```

**运行结果:**

![](https://gitee.com/uploads/images/2018/0510/115330_fceb803f_930142.png "屏幕截图.png")

---

Kotlin基础到此完结，之后需要研究的内容

>* 游戏开发和游戏引擎（lwjgj）
>* Lambda 表达式
>* 高阶函数
>* DSL (特定领域语言)
>* 使用 Kotlin 进行 Android 开发
>* 使用 Kotlin 开发 SpringBoot 引用
