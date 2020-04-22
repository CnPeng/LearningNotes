
## 一、起步
* Xcode主界面：

![](https://images.gitee.com/uploads/images/2019/0111/090049_8387477f_930142.png "屏幕截图.png")

Get started with a playground 是快速开发和运行 Swift 的。

书中1-14章内容都是基于 playground 实现的。



* 显示控制台：

![显示控制台](https://images.gitee.com/uploads/images/2019/0111/090555_b3edcd17_930142.png "屏幕截图.png")


* 实时自动编译

![](https://images.gitee.com/uploads/images/2019/0111/105202_779002e2_930142.png "屏幕截图.png")

* 注释：

单行注释使用 //

---


## 二、类型/常量和变量

### 1、常量和变量

* 常量用let修饰。变量用var修饰
* 声明方式同Kotlin——修饰符在前，名称在中间，末尾为数据类型
* 也有类型推断功能。
* 布尔类型 使用 Bool 表示

```
var numberOfStoplights = "Four"
let numberOfStoplights: Int = 4
```

### 2、字符串模板/字符串插值
Swift中的字符串插值（String interpolation）就是指利用字符串模板拼接的字符串。

字符串模板的格式为 `\(“被引用的内容”)`

如：

```swift
import Cocoa

let numberOfStoplights: Int = 4
var population: Int
population = 5422
let townName: String = "Knowhere"

let townDescription ="\(townName) has a population of \(population) and \(numberOfStoplights) stoplights." print(townDescription)
```

---

## 三、条件语句

### 1、if/else
* if 后面的判断条件不需要括号

比较运算符|含义
---|---
<  | 计算左边的值是否小于右边的值
<= |计算左边的值是否小于等于右边的值
\>  | 计算左边的值是否大于右边的值
\>= | 计算左边的值是否大于等于右边的值 == 计算左边的值是否等于右边的值
!= |计算左边的值是否不等于右边的值
=== |计算两个实例是否指向同一个引用 !== 计算两个实例是否不指向同一个引用


### 2、三目运算
同Java——`a?b:c`


逻辑运算符：&&、||、！  

---

## 四、数

### 1、整数

#### (1)、基本介绍

```
import Cocoa

//输出 9223372036854775807, 此处Int为64位
print(Int.max)
//输出 -9223372036854775808
print(Int.min)
```

苹果从iPhone 5S、iPad Air和带Retina屏的iPad mini开始引入64位设备，而更早的设备还是32位架构。所以， **在构建程序时，编译器会根据设备动态决定Int是32位还是64位。** 

#### (2)、显示整数类型
Int32 , 就表示32位的整数类型。

```
import Cocoa

//2147483647
print(Int32.max)
//-2147483648
print(Int32.min)
```

相应的还有 Int8、Int16、Int64，但是大部分情况下还是直接使用 Int。

#### (3)、有符号整数和无符号整数

前面介绍的都是有符号整数，既能表示正数也能表示负数。

 **Swift也有无符号整数，用来表示大于等于0的整数** 。每个有符号整型都对应的有无符号整型。如  **UInt8、UInt16、UInt32、UInt64** 

* 无符号整数的最小值都是0
* 无符号整数的最大值为对应有符号整数最大值和最小值的绝对值之和。如：UInt的最大值为Int最大值和最小值的绝对值之和。也就是说， **对于Int 和 UInt都有2的64次方个取值，但是Int会留出一半给负数，而UInt则全为正数。** 
* Swift中常用有符号的Int

```
import Cocoa

//127
print(Int8.max)
//-128
print(Int8.min)

//255
print(UInt8.max)
//0
print(UInt8.min)
```

### 2、创建整数实例

```
import Cocoa

let num1=16
let num2:Int=32
var num3:Int32=666
var num4:UInt=999
//无符号num5不能取负数，其值必须在[0,255] 之间
//var num5:Unit8=-1
//超出了范围，因为有符号Int8的取值范围为[-128,127]
//var num6:Int8=200
```

### 3、整数操作符

#### (1)、基本操作符

+、- 、* 、/ 、% 、+= 、-= 的操作同Java

```
//输出-3
print(-11/3)
//输出-2
print(-11%3)
```

#### (2)、溢出操作符 &+

Swift提溢出操作符(overflow operator)，它们在值太大(或太小)时的行为不同于普通操作符，会绕过去而不是触发异常。

所谓绕过去就是再从最大/最小值开始取值。

溢出操作符包括：&+、&-、&*

```
let a:Int8=120
//&+ 为溢出操作符，因为120+10=130，超过了Int8的最大值127三位，所以，会把超出的部分从最小值开始取——即，-128，-127，-126，最终输出为-126
var b:Int8 = a&+10

let e:Int8 = -120
//输出126
var f:Int8 = -120&-10
```

### 4、转换整数类型

* 不同类型的数据进行算数操作时，Swift不会自动转类型，必须手动强转，否则报错。
* 强转类型时，尽量由低位向高位转换，因为高位向低位转换时可能会出现存储空间不足的情况。

```
let a:Int16 = 300
let b:Int8 = 10
//将Int8的b 强转为 Int16 , 低位向高位转不会b出错
var c = a+(Int16)(b)

//将Int16的a强转为Int8,因为a大于Int8的最大值，所以报错：Not enough bits to represent the passed value
//var d = (Int8)(a)+b

//报错！不同类型的数据不能进行运算
//var e=a+b
```


### 5、浮点数

#### (1)、浮点数的基本特点
浮点数 —— floating-point number

* 浮点数在计算机中是以尾数(mantissa)和指数(exponent)的形式存储的，类似于科学记数法。比如说，123.45可以用1.2345 × $10^2$的形式或12.345 × $10^1$的形式存储(尽管计算机使用二进制而不是十进制)。

*  **浮点数通常不精确** :有很多数无法以浮点数的形式存储。计算机会存储一个近似值，非常接近你要的数。

#### (2)、浮点数的分类
* Swift有两种基本的浮点数类型:32位——Float 和 64位——Double。

* Float和Double 的长度差异并不像整数那样影响其最小值和最大值，而是影响其精度度。Double的精度度比Float高，这意味着它能存储更精确的近似值。

* 在Swift中， **浮点数的默认推断类型是Double，也可以显式地 声明Float和Double** 
* 注意：由于浮点数的不精确性，所以 `（1.1+0.1） != 1.2`

```
let d1=1.5
let d2:Double=1.5
let f1:Float=1.5

//输出 true
print(d1==d2)
//输出 true
print(d1==(Double)(f1))
//这样报错，不同类型之间不能比较
//print(d1==f1)

//输出false, 因为浮点数的不精确性质，d1+0.1 得到的实际是近似于 1.200 000 000 000 000 1 的值，而1.2在内存中存储时实际是近似于 1.199 999 999 999 999 9 的值，所以输出false
print((d1+0.1)==1.2)

```


---

## 五、Switch语句

### 1、switch基本格式和特点

与Java 中的Switch相比，Swift中的Switch与如下特点
* switch后面的条件不需要括号
* case语句中不需要break，只要匹配上case就会执行对应的语句，执行完语句即终止。（Java中没有break时表示穿透）
* case后面可以跟多个值，多个值之间使用逗号间隔。（类似于Java中的穿透，但并不完全相同）


```
import Cocoa

var statusCode=400
var errorStr:String

switch statusCode {
case 400,401,402:
    errorStr="当前错误码为\(statusCode)"
default:
    errorStr="其他错误码"
}
```

### 2、fallthrough——穿透

在case 后面追加 `fallthrough` 之后，可以在执行完当前 case 对应的语句之后，接着执行下一条case的语句。

这等价于Java中的穿透。

```
import Cocoa
var statusCode=400
var errotStr:String

switch statusCode {
case 400:
    errotStr="当前错误码为\(statusCode)"
    print(errotStr)
    fallthrough
default:
    print("使用fallthrough穿透时，执行完case400,会接着执行这里")
}
```

上面的代码执行完之后输出的内容为：
```
当前错误码为400
使用fallthrough穿透时，执行完case400,会接着执行这里
```

### 3、case + 区间

前面已经知道，case后面可以跟多个值。如果这些值是连续的，那么就可以使用区间表示这些值。

* Swift中表示区间的模式同Kotlin

```swift
let age=25
var hintStr:String

switch age {
case 18...25:
    hintStr="年龄为\(age),在[18,25]之间"
case 26..<35:
    hintStr="年龄为\(age),在[26,35)之间"
default:
    break
}
```

### 4、case 中的值绑定

所谓值绑定，就是在case后面声明一个常量或者变量，将原先固定的case取值赋值给新声明的常量/变量。

* 值绑定声明的常量/变量只在当前分支有效

在case中不使用where表达式的前提下：（where表达式会在下一节介绍）
* CnPeng 通常在最后一个case 中使用值绑定，因为它会匹配所有之前case中没有匹配的情况
* CnPeng 使用值绑定之后，不再需要default分支。因为二者基本等同。

```swift
let statusCode=500
var errorStr:String

switch statusCode {
case 100:
    errorStr="当前错误码为100"
case 101,102:
    errorStr="当前错误码为101或102中的一个"
case 400...405:
    errorStr="当前错误码在 400...405 区间之内"
case let otherCode:
    errorStr="当前错误码为\(otherCode)"
default:
    errorStr="此处应该不会执行了，应为上一句中的otherCode就全都匹配了"
}
print(errorStr)
```

上述代码的输出结果为:

```
当前错误码为500
```


### 5、case 中的 值绑定+where判断

* case 后面的 值绑定 也可以跟where判断表达式配合使用.
* where必须配合值绑定使用，不能单独使用，否则报错。


```
let statusCode=500
var errorStr:String

switch statusCode {
case 100:
    errorStr="当前错误码为100"
case let otherCode where(otherCode>=450&&otherCode<=505)||otherCode>600:
    errorStr="此处使用了where, statusCode大于在[450,505]之间，或者大于600"

//不能直接使用where，必须配合值绑定使用，否则报错
//case  where(statusCode>=450&&statusCode<=505)||statusCode>600:
//    errorStr="此处使用了where, statusCode大于在[450,505]之间，或者大于600"

default:
    errorStr="其他错误码"
}
```

上述代码的输出结果：
```
此处使用了where, statusCode大于在[450,505]之间，或者大于600
```

* 错误示例：

下面示例是一个错误示例，因为  **值绑定 定义的常量/变量 只在当前分支有效，即便使用了 fallthrough 也不会在后面的分支中生效！！** 

```
let statusCode=500
var errorStr:String

switch statusCode {
case 100:
    errorStr="当前错误码为100"
case  var otherCode where(otherCode>=450&&otherCode<=505)||otherCode>600:
    errorStr="此处使用了where, statusCode大于在[450,505]之间，或者大于600"
    fallthrough
default:
    //报错！use of unresolved identifier 'otherCode'; did you mean 'CGCharCode'
    errorStr = "\(otherCode)其他错误码"
}
```


### 6、元组

#### (1)、元组 (tuple)

##### A: 元组的基本定义
元组是开发者认为有逻辑关系的两个或多个值的有限组合。不同的值被组合为单个复合，组合的结果是一个元组的有序列表。

将多个值放到同一个() 中，并用逗号间隔就组成了元组。

* 示例代码：

创建一个元组来组合statusCode和errorString

```
let statusCode=404
var errorStr:String

switch statusCode {
case 100:
    errorStr="错误码为100"
default:
    errorStr="错误码不是100"
}

let error=(statusCode,errorStr)
```
![元组的基本示例](https://images.gitee.com/uploads/images/2019/0115/173027_caa1d2d3_930142.png "屏幕截图.png")

##### B: 访问元组元素

在上图中，我们定义了一个元组常量error, 右侧区域中显示了元组中的具体元素。元素前面浅灰色的 .0 、.1 表示元素的索引，也就说，我们可以通过 error.0 、error.1 获取元素值。

```
let statusCode=404
var errorStr:String

switch statusCode {
case 100:
    errorStr="错误码为100"
default:
    errorStr="错误码不是100"
}

let error=(statusCode,errorStr)
print("error中元素值分别为:\(error.0),\(error.1)")

```

上述代码的输出结果为：

```
error中元素值分别为:404,错误码不是100
```

##### C: 具名元组
* 所谓具名元组，就是元组中的元素有名字。格式为：`let 元组名=（元素名：元素,元素名：元素）`,let也可以改为var
* 具名元组中，我们既可以通过索引访问元素，也可以通过名字访问元素

```
let statusCode=404
var errorStr:String

switch statusCode {
case 100:
    errorStr="错误码为100"
default:
    errorStr="错误码不是100"
}

let error2=(code:statusCode,desc:errorStr)
print("error2中元素值分别为:\(error2.0),\(error2.1)")
print("error2中元素值分别为:\(error2.code),\(error2.desc)")
```


### 7、模式匹配

 **在switch语句的分支中使用区间其实就是模式匹配(pattern matching)的一个例子** 。这种形式的模式匹配被称为区间匹配(interval matching)，因为 **每个分支都尝试匹配一个区间和给定值** 。  **元组在模式匹配中也很有用。** 

举个例子，想象有一个应用会发出多个Web请求。每次服务器的响应返回时，我们会保存HTTP状态码。然后，你想看是否有请求失败并且状态码是404(就是“请求的资源不存在”的错误)；如果有的话，查看是哪些请求。在switch语句中使用元组就能匹配非常特殊的模式。

```swift
let firstErrorCode=404
let secondErrorCode=200

let errorCodes=(firstErrorCode,secondErrorCode)

switch errorCodes {
case (404,404):
    print("no items found")
case (404,_):
    print(" first item not found")
case (_,404):
    print(" second item not found")
default:
    print("all items found")
}
```

上述代码的输出结果：

```java
 first item not found
```

在上述代码中，firstErrorCode和secondErrorCode表示两个不同Web请求的HTTP状态码。errorCodes是组合这些状态码的元组。

*  **下划线 `_` 表示能匹配任意字符的通配符。** 
* 上述代码中，第一个分支表示两个web请求都404，第二个分支表示只关注第一个web响应码，第三个分支表示只关注第二个响应码。由于firstErrorCode 为 404，所以会匹配上第二个分支。


### 8、switch 与 if/case

 **当 switch 分支中仅有一个 case 分支 和 default 分支，并且 default 分支中没有实际语句只用 break 结束判断时** ，就可以使用 if/case 替代 switch.

* switch/case示例

```java
let age=22

switch age{
case 18...25:
    print("当前年龄为\(age)，在[18,25]的区间内")
default:
    break
}
```

* if/case 示例

不要忘了条件和变量之间的 = 

```
let age=22

if case 18...25 = age {
    print("当前年龄为\(age),在[18,25]区间之内")
}
```

* if/case 后面跟多个条件

多个判断条件之间使用逗号间隔

```
let age=22

if case 18...25=age,age>21 {
    print("年龄为\(age),在[18,25]区间内，并且大于21")
}
```
---

## 六、循环

* 如果重复次数已知或者容易推断，可以用for循环。
* while循环则适合在满足某些条件时重复执行任务

### 1、for-in 循环

基本等同于Kotlin中的for-in 

#### (1)、基本示例

```
var tempNum = 5
for i in 1...5 {
    tempNum += i
    print(tempNum)
}
```

![for-in循环和查看运行中的详细](https://images.gitee.com/uploads/images/2019/0115/202414_5bcff961_930142.png "屏幕截图.png")


#### (2)、循环中使用通配符

当我们在循环体中不需要使用 for 和 in 之间的变量时，可以使用通配符 `_` 代替该变量

```
var tempNum=1
for _ in 1...5 {
    tempNum += 1
    print(tempNum)
}
```

#### (3)、for-in 的条件中嵌套where

在 for-in 格式中， **in 后面的条件可以追加 where 语句** 

注意：where 语句只是 in 后面条件的补充，不能单独存在。

```
for i in 1...10 where i%3 == 0{
    print(i)
}
```

输出结果：

```
3
6
9
```

上述代码等价于：

```
for i in 1...100 {
    if i % 3 == 0 {
    print(i) }
}
```

很显然，for-in-where 比下面的 for-in-if 简洁。

### 2、类型推断

在前面的示例代码中，对于 for-in 之间的变量i，其实就是使用了类型推断，也就是说，不用显示声明其类型，因为 in 后面的条件限定了 i 的取值范围—— Int 类型。

不适用类型推断时的完整代码为：

```
for i : Int in 1...10 where i%3 == 0{
    print(i)
}
```

 **Swift推荐使用类型推断** 


### 3、while 循环


```
var i=1

while i<6 {
    i += 1
    print(i)
}
```


### 4、repeat-while 循环

repeat-while 等价于其他语言中的 do-while

* 与 while 相比，repeat-while 至少会执行一次

```java
var i=1
repeat{
    i += 1
    print(i)
}while i<6
```

### 5、控制转移语句

* fallthrough、break 等都可以控制代码的执行过程，所以，被他们控制的代码语句就称为控制转移语句。

* 相关关键字有：continue、break、return、fallthrough
    * continue 跳过本次循环
    * break 中断循环，但是会继续执行循环后面的语句
    * return 中断循环，并且不再执行循环后面的语句
*  **千万注意：变量自增的过程 必须 在控制转移语句所在判断条件 之前！！！** 下面示例代码中有详细演示：当 a+=1 在 continue 所在判断条件之后时，continue 的实际效果等同于 break !! Kotlin中也是这样子。


```
var a=10
var b=1
var c=1

repeat{
    //a += 1
    a = a+1
    
    if a==15 {
        continue
    }
    //如果将自增语句放在了continue语句后面，当满足continue条件后，执行的是跳出循环的操作！！而不是跳过本次。效果等同于break。Kotlin中也是这个样子
    //a += 1
    print(a)
}while a < 20


while b<15 {
    b += 1
    if b==5 {
        continue
    }
    print("while循环：\(b)")
}

for _ in 1...10{
    c += 1
    if c==5 {
        continue
    }
    print(c)
}
```

---

## 七、字符串

Swift字符串本身并不是集合，但是其底层内容确实以集合形式存在

### 1、使用字符串

#### (1)、字符串基本介绍

* 字符串使用 `“”` 包裹
* Swift中的字符串可以直接通过 + 、+= 进行拼接。

```
var str="Hello,Swift"
str += "!"
print(str + "!")
```

#### (2)、字符串的组成

组成Swift字符串的字符都是Character类型。

Swift的Character类型表示Unicode字符，组合起来形成String实例。

**注意:** 在 Swift 4.2.1 中已经将字符串的 characters 属性废弃，如果我们需要遍历获取组成字符串的 character , 可以直接遍历字符串，不再需要访问 chatacters 属性。具体示例如下： 

```
var str = "Hello,Swift"
str += "!"
str = (str+"!")

//在Swift 4.2.1 中 characters 已经被废弃，'characters' is deprecated: Please use String or Substring directly
for c in str.characters {
    print(c)
}

//在Swift 4.2.1 中 characters 已经被废弃，可以直接对str进行遍历
for c in str{
    print(c)
}
```

### 2、Unicode

Unicode是字符编码的国际标准，目前是不用考虑平台即可无缝处理和表达字符。

Unicode在 计算机上表示人类语言(还有其他形式的通用符号，比如emoji)。

标准中的每个字符都被赋予了唯一的数。

Swift的String和Character类型构建于Unicode之上。


#### (1)、Unicode标量

从内部实现说，Swift字符串是由Unicode标量(Unicode scalar)组成的。

Unicode标量是21位的数，表示Unicode标准中一个特定字符。比如，U+0061 表示小写拉丁字母 a 。U+1F60E表示戴着墨镜的笑脸emoji。

文本 U+1F60E 是书写Unicode字符的标准方式。1F60E 部分是十六进制数。 

创建一个常量，来看看在Swift和playground中如何使用特定的Unicode标量：

```
let oneCoolDude = "\u{1F60E}"
```

![Unicode字符](https://images.gitee.com/uploads/images/2019/0116/091810_bed7c19c_930142.png "屏幕截图.png")

上图示例中：`\u{}` 表示十六进制标量，十六进制数放在大括号内部。 

#### (2)、Unicode 组合标量

在 Swift 中，每个字符都是一个或多个 Unicode 标量构成。

一个 Unicode 标量对应某种给定语言中的一个基本字符。我们之所以说字符是由“一个或多个” Unicode 标量构成的，是因为还存在 组合标量(combining scalar)。

比如，U+0301表示可组合的重音符号(’)的Unicode标量。这个标量将重音符号放置在它前面标量所对应的字符上面，也就是与前面的字符组合。用这个标量和小写拉丁字母 a 可以创建字符 á 

```
let oneCoolDude = "\u{0061}\u{0301}"
//输出 á 
print(oneCoolDude)
```

![组合标量](https://images.gitee.com/uploads/images/2019/0116/092602_611f0dd9_930142.png "屏幕截图.png")


Swift 中的每个字符都是 **扩展字形簇** (extended grapheme cluster)。扩展字形簇是人类可读的单个字符，由一个或多个 Unicode 标量组合而成。

刚才我们把字符 á 拆成了两部分 Unicode 标量: 字符和重音。把字符实现为扩展字形簇让Swift具备了处理复杂的文本字符的灵活性。

Swift还提供了一种机制，能让我们看到字符串中所有的 Unicode 标量。

比如，你可以利用  **unicodeScalars**  属性看到字符串实例中的所有字符的Unicode标量，该属性持有所有Swift用来构建该字符串的标量。

```
var str="Hello,Swift"
for scaler in str.unicodeScalars {
    print("scaler的值为：\(scaler),对应的UInt32为Unicode标量值为\(scaler.value)")
}
```

![查看字符串中字符的Unicode标量](https://images.gitee.com/uploads/images/2019/0116/094217_ee17cf97_930142.png "屏幕截图.png")

在上面的示例中，scaler 输出的是组成字符串的每个字符，scaler.value 输出的则是字符对应的 UInt32 的 Unicode 标量。第一个72对应的是十六进制的 U+0048, 即大写字母 H 。

>注意：怎么将无符号32位数转换为十六进制呢？还不会啊。。。。



#### (3)、标准等价

组合标量有其存在意义，不过Unicode也为某些常见字符提供了已经组合过的形式。比如，á 有一个专属的标量，实际上不用分为字母和重音符号两部分。这个标量是U+00E1，这种组合过的标量称为： **预组合标量
** 

```
let str1 = "\u{0061}\u{0301}"
let str2 = "\u{00E1}"
print(str1,str2)
print(str1==str2)
```

![标准等价](https://images.gitee.com/uploads/images/2019/0116/095145_282d5b2a_930142.png "屏幕截图.png")

上述代码中，虽然 str1 用由个标量组成，str2 由一个标量组成，但 swift 还是认为他们时相等的，这就是  **标准等价(canonical equivalence)** 

 **标准等价是指两个 Unicode 标量序列 在语言学层面 是否相等。** 

对于两个字符或者两个字符串，如果它们具备相同的语言学含义和外观，那么无论是否用相同的Unicode标量创建，都认为两者 相等。str1 和 str2 是相等的字符串，因为两者都表示带上重音符号的小写拉丁字母a，而它们由不同的Unicode标量创建的事实并不影响这一点。


### 3、字符串操作

#### (1)、计算元素数量

标准等价对字符串的元素数量计算会有影响——无论是用组合标量还是预组合标量，只要两个字符串符合标准等价，那么他们的元素数量也就一致。

* .count 属性可以直接获取字符串元素个数/字符串长度

```
let str1 = "\u{0061}\u{0301}"
let str2 = "\u{00E1}"

var flag:Bool=str1.count==str2.count
//输出true
print(flag)

//Swift 4.2.1 中已经废弃了characters 属性，可以直接调用 str.count
//str1.characters.count==str2.characters.count
```

#### (2)、索引和区间

Swift 编译器不允许用下标直接访问字符串中的特定字符。这个限制与Swift字符串和字符的存储方式有关。不能用整数作为索引访问字符串，因为 **Swift无法在不遍历前面每个字符的情况下 知道指定的索引对应于哪个Unicode标量。** 

Swift用名为`String.CharacterView.Index`的类型记录索引。CharacterView类型负责以有序集合的形式提供字符串内部字符的访问接口。

##### A: 获取指定索引位置字符

获取指定索引位置字符的步骤：
- 调用 .startIndex 属性构建起始索引对象
- 通过 .index(startIndex,offset:x) 构建目标索引对象——endIndex 
- 调用 [endIndex] 获取 endIndex 位置的字符。 

示例如下：

* 获取指定位置的字符

```swift
let str="Hello,Swift"

let startIndex=str.startIndex
//从 startIndex 向右 “再” 数4位。
let endIndex=str.index(startIndex, offsetBy: 4)
var char=str[endIndex]
print(char)

//从endIndex 向左 “再” 数3位。负数向左数，正数向右数
let endIndex2=str.index(endIndex, offsetBy: -3)
var char2=str[endIndex2]
print(char2)
```
##### B: 利用区间截取字符串

截取字符串的步骤：
- 调用 .startIndex 属性构建起始索引对象
- 通过 .index(startIndex,offset:x) 构建结束索引对象——endIndex 
- 构建 startIndex...endIndex 区间range
- 调用 [range] 获取 range 区间内的字符串。 

示例如下：

* 截取字符串

```swift
let str="Hello,Swift"

let startIndex=str.startIndex
let endIndex=str.index(startIndex, offsetBy: 4)
let range=startIndex...endIndex
var subStr=str[range]
```

##### C: 判断字符串是否为空

```
let emptyStr=""
var flag=emptyStr.isEmpty
//输出true
print(flag)
```
---

## 八、可空类型

可空类型(optional)是Swift的独特特性，用来指定某个实例可能没有值。看到可空类型时，你会知道该实例一定:要么有值并且已经可用，要么没有值。 **如果一个实例没有值，就称其为 nil。** 

任何类型都可以用可空类型来说明一个实例可能是nil。


### 1、可空类型

> CnPeng 与Kotlin中的可空类型极为类似： **除非在声明变量时主动在类型后面追加了 ? , 否则，默认非空。** 

#### (1)、基本使用

* 直接声明未赋值时打印出来的是 nil 

```
var errorStr:String?
print(errorStr)
```

![](https://images.gitee.com/uploads/images/2019/0116/120412_183ddbfa_930142.png "屏幕截图.png")


#### (2)、强制展开(forced unwrapping)

所谓强制展开，就是强制访问可空实例的值，如果值不存在，会报错！

强制展开时，需要在可空变量后面追加叹号——`!`

> CnPeng 等价于Kotlin中的 断言非空——`!!` ，只是kotlin中有两个叹号。

```
var errorStr:String?
errorStr="强制展开——断言非空"

if nil != errorStr {
    let tempStr=errorStr!
    print(tempStr)
}
print(errorStr)
```

输出结果

```
强制展开——断言非空
Optional("强制展开——断言非空")
```

### 2、可空实例绑定

前一节强制展开的示例代码中，在条件语句内部创建常量，然后去使用这个常量，这样有些笨重。因为，Swift有更好的方法把可空实例的值绑定给常量——这种方法称为 **可空实例绑定** 。

#### (1)、基本格式

可空实例绑定(optional binding)是一种固定模式，对于判断可空实例是否有值很有用。如果有值，就将其赋给一个临时常量或变量，并且使这个常量或变量在条件语句的第一个分支代码中可用。

下面是基本的语法:

```
if let temporaryConstant = anOptional { 
    // anOptional 有值并将值赋给了 temporaryConstant。此时可以使用后者
} else {
    // anOptional 没有值，为nil
}
```

所以，前一节 强制展开 中的代码就可以使用可空实例绑定简化为：

```
var errorStr:String?
errorStr="可空实例绑定"

if let noNilStr=errorStr{
    print("errorStr非空，将值赋给 noNilStr——值为 \(noNilStr)")
}
```

#### (2)、嵌套的可空实例绑定

```
var errorStr:String?
errorStr="404"

if let noNilStr=errorStr{
    if let errorCode=Int(noNilStr){
        print(errorCode)
    }
}
```

上述代码中，当 errorStr 不为 nil 时，创建局部常量 noNilStr , 并将值赋给这个常量。然后将 noNilStr 转换为 Int 类型，因为并不是所有的字符串都能转为 Int —— 如“哈哈”，当不能转换时会返回 nil 。所以，又做了一次可空实例绑定，当转换结果不为空时，创建常量 errorCode 并将转换得到的 Int 值赋给它，然后打印。

* Int(noNilStr) 表示将 noNilStr 字符串转换为 Int

上面的示例代码可以简化为如下模式：

```
var errorStr:String?
errorStr="404"

if let noNilStr=errorStr,let errorCode=Int(noNilStr){
    print("noNilStr 为 \(noNilStr), errorCode 为 \(errorCode)")
}
```
这一段代码则比前面两个if 的示例更简洁，看着也更舒服。但需要注意的是，中间是使用 逗号 间隔的。


#### (3)、可空实例绑定中的附加条件

```
var errorStr:String?
errorStr="404"

if let noNilStr=errorStr,let errorCode=Int(noNilStr),errorCode==404{
    print("noNilStr 为 \(noNilStr), errorCode 为 \(errorCode)")
}
```

上述代码中，只有同时满足 errorStr 不为 nil , errorCode 不为 nil , 并且 errorCode 为 404 才会执行后面的打印语句。而 `errorCode==404` 就是附加的判断条件。


### 3、隐式展开可空类型

#### (1)、基本概念和应用

隐式展开可空类型(implicitly unwrapped optional)，与普通可空类型类似，只是有一个重要的区别:它们不需要展开（不需要断言非空）。

因为 **在声明变量/常量时会在类型后面追加 `！`** , 表示这是一个隐式展开可空类型的数据。但这并不安全，如果这个变量/常量没有赋值依旧为 nil , 那么在使用时该报错的还是会报错。

也就是说，隐式展开可空类型数据依旧可能为 nil , 只是概率比 可空类型数据 会低。

```
var errorStr:String!
//如果注掉这一行，打印结果依旧为 nil , 不注掉的话打印：Optional("404")
errorStr="404"
print(errorStr)
```

 **注意：这种 隐式展开可空类型能不用就不用，不如 可空类型实在！！** 

#### (2)、String、String!、String?的区别

* String  非空字符串，值必然不为 nil
* String! 隐式可空，有可能为 nil , 但概率比较小
* String？显示可空，极有可能为 nil

由于 String! 和 String? 的区别并不是很明显，所以，尽量不用 String!

```java
var errorStr:String
//error: nil cannot be assigned to type 'String'
//errorStr=nil

var errorStr1:String!
errorStr1=nil
print(type(of:errorStr1))

//报错！Unexpectedly found nil while unwrapping an Optional value
//var errorStr2:String=errorStr

var errorStr3=errorStr1
print(errorStr3 is String)
print(errorStr3 is String!)
print(errorStr3 is String?)
print(type(of:errorStr3))

var errorStr4="444"
print(errorStr4 is String)
print(errorStr4 is String!)
print(errorStr4 is String?)
print(type(of:errorStr4))
```

![](https://images.gitee.com/uploads/images/2019/0116/162744_f972af67_930142.png "屏幕截图.png")

从上图我们可以看到：
* 把 nil 赋值给 String 类型会报错。
* 如果某个变量置为 nil , 那么它的类型就是 Optional<String>——可空类型，而不区分是 String! 还是 String？。
* errorStr3 的值为 nil , 调用 `errorStr3 is String!` 和 `errorStr3 is String?` 时都会返回true
* errorStr4 的值非空，调用 `errorStr4 is String`,`errorStr4 is String!`、`errorStr4 is String?` 都会返回 true.

补充：

* is 用来判断变量是否为某个类型的实例（同Kotlin）
* type(of:xxx) 用来获取xxx的类型

### 4、可空链式调用

与可空实例绑定类似，可空链式调用(optional chaining)提供了一种 **对可空实例进行查询以判断其是否包含值的机制。**
 
两者的一个重要区别是，可空链式调用允许程序员把多个查询串联为一个可空实例的值。
- 如果链式调用中的每个可空实例都包含值，那么每个调用都会成功，整个查询链会返回期望类型的可空实例
- 如果 **查询链中的任意可空实例是nil，那么整个链式调用会返回nil** 。

> CnPeng 其实就是在调用变量时后面追加一个`？`，表示判断该变量是否为空。为空直接返回 nil , 不为空则执行相关语句。Kotlin中也有同样的机制。

```
var errorCodeString: String?
errorCodeString = "404"
var errorDescription: String?

if let theError = errorCodeString, let errorCodeInteger = Int(theError), errorCodeInteger == 404 {
    print("\(theError): \(errorCodeInteger)")
    errorDescription = "\(errorCodeInteger + 200): resource was not found."
    print(errorDescription)
}

//下面这句代码才是真正的 可空链式调用。变量后面追加 ？，表示如果为空返回nil, 不为空则执行uppercased（）
var upCaseErrorDescription = errorDescription?.uppercased()
print(upCaseErrorDescription)
``` 

![](https://images.gitee.com/uploads/images/2019/0116/201243_c551bafd_930142.png "屏幕截图.png")

### 5、原地修改可空实例

可空实例可以被“原地”修改，从而免去创建新变量或常量的麻烦。

直白一点，就是 **在调用变量时进行空安全调用，如果该变量不为空接着去修改这个变量；如果为空，不做修改** 。

```
var str:String?
//注掉这一行，则打印 nil , 不注掉则打印：Optional("注意：变量不为空时,原地修改可空变量")
//str="注意："
str?.append("变量不为空时,原地修改可空变量")
print(str)
```

> 这种`?.`的形式，与Kotlin中的 `?.` 相同


### 6、nil合并运算符——??

* 如果变量为不为空，执行 ?? 前面的内容；为空，执行后面的内容
* ?? 左边为可空变量，右边为非空的同类型数据
* 基本等同于Kotlin中的 `?:`

```swift
var str:String?
//注掉这一段打印 -1 ，不注掉则打印3
//str = "字符串"
var length=str?.count ?? -1
print(length)
```

---

## 九、数组

数组是值的有序集合。数组的每个位置都用索引标记，任何值都可以在数组中出现多次。数组通常用于值的顺序很重要或者很有用的场合

### 1、创建数组

```
//声明数组方式1
var bucketList:Array<String>

//声明数组方式2
var bucketList2:[String]

//声明数组方式3
var bucketList3=["山东","北京","河北","天津"]

//声明空数组
var arr1=[Int]()
```

### 2、访问和修改数组

函数/属性|含义
---|---
array.append()|向数组array中追加元素
array.remove(at:index)| 删除数组array中index位置的元素
array.count|获取array数组的元素个数
array[start...end]| 获取array数组在指定区间的元素
array[x]=y | 将array数组中索引为x的元素值修改为y
array[x]+=y | 为array数组中索引为x的元素追加内容y
array += array1 | 将array1中的元素追加到array中
array.insert(yyy , at:x) | 将内容yyy追加到array数组的x索引处

```
var province = ["山东"]
province.append("北京")
province.append("河北")
province.append("天津")
province.append("江苏")
province.append("浙江")

province.remove(at: 5)

var count = province.count

province[0...3]

province[4] = "广西"

province[4] += "壮族自治区"

var province2 = ["黑龙江","吉林","辽宁"]
province += province2

//如果目标索引不存在，会报索引越界
province.insert("山西", at: 8)
```

![](https://images.gitee.com/uploads/images/2019/0116/212329_baba11a6_930142.png "屏幕截图.png")


### 3、数组相等

* 使用 `==` 检查数组是否相等
* 由于数组中的元素有序，所以，数组相等的条件是：元素相同，并且元素顺序相同

```
var province = ["北京","河北","山东"]
var province2 = ["河北","山东","北京"]
var province3 = ["北京","河北","山东"]

province == province2
province == province3
``` 

![](https://images.gitee.com/uploads/images/2019/0116/212757_f3effd07_930142.png "屏幕截图.png")


### 4、不可变数组(immutable array)

元素不能被修改，只要使用 `let` 修饰数组即可。

```
let province=["山东","河北","北京"]
```

### 5、查看 Swift API 文档

点击 Xcode 界面中的 Help--Developer Documentation 即可打开 Swift API 文档界面——第二张图中的界面。然后在第二张图顶部的搜索框中输入搜索内容即可。

![](https://images.gitee.com/uploads/images/2019/0116/213227_1b1ddbaf_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0116/213349_13c97e92_930142.png "屏幕截图.png")


---

## 十、字典——dictionary

Swift 中的字典对应 Java 中的 Map, 但不完全相同。因为字典有 Index !!!
* 存储数据时是以键值对的形式存储的。
* 键必须唯一，字典的值可以是基本数据类型，也可以是数组(API中有示例)等。

### 1、创建字典

创建字典时有如下四种形式：

```
//显示声明键和值的类型，等号右侧使用 [:] 形式
var dict1:Dictionary<String,Double>=[:]
var dict2:[String:Double]=[:]

//类型推断的形式确定键和值的类型，等号右侧使用（）形式
var dict3=Dictionary<String,Double>()
var dict4=[String:Double]()
```

上述四种声明方式都是得到一个空的字典实例——`[:]`


### 2、填充字典

创建有内容的字典对象。多个元素之间使用逗号间隔。

```
//字典中的多个元素之间使用 逗号 间隔
var userDict=["张三":23,"李四":24,"王五":25]
```


### 3、访问和修改字典

函数/属性|含义
---|---
dict.count|获取字典的元素总数
dict[key]|获取键为key时对应的值;如果key不存在，返回nil
dict[key]=newValue|修改key对应的值，或新增键值对
dict.updateValue(value,forKey:key)|单纯改值。如果key存在,返回旧值；key不存在，返回nil


```
//字典中的多个元素之间使用 逗号 间隔
var userDict=["李四":24,"王五":25]
userDict.count

//键不存在时返回nil
userDict["赵大"]

//改值或新增键值对
userDict["钱二"]=22
userDict["钱二"]=23
userDict.count

//单纯改值，键不存在则返回nil
userDict.updateValue(22, forKey: "孙三")
//单纯该值，键存在，则返回旧的值
userDict.updateValue(22, forKey: "李四")
```

![](https://images.gitee.com/uploads/images/2019/0117/103024_4c224e7d_930142.png "屏幕截图.png")


### 4、增加和删除值

> CnPeng 到底该怎么理解这里的 Index 索引类呢？字典和Java中的Map在存储结构上又有啥区别呢？

函数|含义
---|---
dict[key]=value|新增键值对
dict.removeValue(forKey:key)|删除key对应的值。key存在则返回被删除的value,key不存在返回nil
dict.remove(at:index)|删除指定index位置的元素。返回被删除的元素对象——Element
dict[key]=nil|将key的值置为nil,也是一种删除方式，但没有返回值

```
var userDict=["张三":23,"李四":24,"赵大":26,"钱2二":22,"孙三":23]

//新增键值对
userDict["王五"]=25
userDict.count

//根据键删除值，返回被删除的值; 键不存在则返回nil
var delValue1=userDict.removeValue(forKey: "张三")
var delValue2=userDict.removeValue(forKey: "Tom")
userDict.count

//将键对应的值置为nil也是删除的一种方式, 但没有返回值
userDict["李四"]=nil
userDict.count

userDict
var startInx=userDict.startIndex
var targetInx=userDict.index(startInx, offsetBy: 3)
//使用索引的方式删除元素时，直接返回被删除的键值对——Element
userDict.remove(at:targetInx )
userDict.count
```

![](https://images.gitee.com/uploads/images/2019/0117/105847_d0b14ed5_930142.png "屏幕截图.png")

### 5、循环——遍历字典元素

```
var userDict:Dictionary<String,Int>=["张三":23,"李四":24,"王五":25]

//遍历方式1：for-in 之间必须用小括号包裹内容
for (key,value) in userDict{
    print("\(key)的年龄为\(value)")
}

//f遍历方式2：因为构建字典时，实际是将键值对构建成了Element对象，所以，elem就是这个对象
for elem in userDict {
    print(elem)
    print("\(elem.key)的年龄为\(elem.value)")
}
```

### 6、不可变字典

同声明数组或其他变量一样，使用 `let` 修饰即可

> 这一点比 Kotlin 方便。kotlin中分别使用 mapOf()、mutableMapOf()构建不可变和可变map

### 7、把字典转为数组

```
var userDict:Dictionary<String,Int>=["张三":23,"李四":24,"王五":25]

//每一个字典都有keys、values 属性
var nameArray=Array(userDict.keys)
var ageArray=Array(userDict.values)
```

![](https://images.gitee.com/uploads/images/2019/0117/112237_a6e27300_930142.png "屏幕截图.png")

---

## 十一、集合--set


### 1、什么是集合

集合是一组不同实例的无序组合。

与数组相比，数组中元素有序，并且元素可以重复。而集合中的元素无序，并且元素不能重复

* Swift容器比较（摘自原书）

容器|有序|唯一|存储
---|---|---|---
数组|是|否|元素
字典|否|键唯一|键值对
集合|否|唯一|元素

### 2、创建集合

#### (1)、 声明集合并添加元素 

```swift
//声明方式1
var set1 = Set<String>()
//声明方式2
var set2:Set = ["张三","李四","王五"]
//声明方式3
var set3=Set(["张三","李四"])
type(of: set3)

//插入元素
set1.insert("赵六")
print(set1)
```

![](https://images.gitee.com/uploads/images/2019/0117/122013_27de817b_930142.png "屏幕截图.png")

#### (2)、遍历集合

```swift
for user in set2{
    print(user)
}
```


### 3、运用集合

* 判断集合中是否包含某个元素 

```swift
var userSet:Set=["张三","李四","王五"]
//contains() 判断是否包含某个元素。返回true/false
userSet.contains("张三")
```

#### (1)、并集--union

```
var userSet1:Set=["张三","李四","王五"]
var userSet2:Set=["张三","赵六"]

//并集，两个集合元素相加，重复的只保留一个。返回 {"张三", "赵六", "李四", "王五"}
userSet2.union(userSet1)
```

#### (2)、交集--intersection

```
var userSet1:Set=["张三","李四","王五"]
var userSet2:Set=["张三","赵六"]

//交集。返回 {"张三"}
userSet2.intersection(userSet1)
```

#### (3)、不相交--isDisjoint

```
var userSet1:Set=["张三","李四","王五"]
var userSet2:Set=["张三","赵六"]

//是否不相交——是否没有共同元素--返回false
userSet2.isDisjoint(with: userSet1)
```

---

## 十二、函数


### 1、一个基本函数

* 函数的声明和调用

```
func testFunc(){
    print("这是一个简单的函数")
}

testFunc()
```

### 2、函数参数

* 形参时声明函数时的参数，实参是调用函数时传递的数据
* 声明带参数的函数时，参数在前，后面跟冒号，冒号后面是参数的类型（这种声明方式与 Kotlin 相同）
* 调用函数时需要指明参数名称——类似于Kotlin中的具名调用

```
func printName(name:String){
    print("输入的姓名为\(name)")
}

printName(name: "张三")
```

#### (1)、参数名字

一个参数可以有两个名字，一个给函数体内部调用使用，一个给调用方做具名使用。给调用方使用的参数就被称为 `外部参数`

*  **声明普通参数时，在该参数前面再加一个参数名，并使用空格将两者隔开，空格前面的就是外部参数，后面的则是内部参数** 
* 外部参数仅调用方具名使用，函数内部无法调用外部参数。


```
//具有外部参数的函数。外部参数仅供调用方使用，内部参数仅供函数内部使用
func emailTo(name userName:String){
    
    print("你将要给\(userName)写邮件")
    
    //函数体内使用外部参数时会报错：error: use of unresolved identifier 'name';
    //print("你将要给\(name)写邮件")
}

emailTo(name: "张三")
```

#### (2)、可变长度参数

* 可变长度参数(variadic)接零个或多个输入值作为实参。
* 函数只能有一个可变长度参数，并且一般应该是最后一个参数。
* 可变长度参数本质是一个数组。
* 要声明可变长度数时，在参数类型后面追加三个点即可——如 `names: String...`

```swift
func emailTo(names userNames : String...){
    
    print("你将要给\(userNames)写邮件")
    for name in userNames{
         print("你正在给\(name)写邮件")
    }
}

//调用函数时，不需要构建数组对象。与Kotlin中的可变长度参数相同
emailTo(names: "张三","李四","王五")
```

![](https://images.gitee.com/uploads/images/2019/0117/180118_d3bda2df_930142.png "屏幕截图.png")

#### (3)、默认值参数

> 与 Kotlin 中的默认参数相同

* 声明函数参数时，可以为某个参数声明默认值，这种就称为`默认值参数`
* 默认值参数需要作为最后一个参数。
* 调用具有默认值参数的函数时，可以不传递该参数，此时，使用默认值

```
func printUserInfo(name uName:String , age:Int,sex:String="男"){
    print("\(uName)的年龄为\(age),性别为\(sex)")
}

printUserInfo(name: "张三", age: 23)
printUserInfo(name: "李四", age: 24 ,sex: "女")
```
![](https://images.gitee.com/uploads/images/2019/0117/180951_fb2bdf20_930142.png "屏幕截图.png")

#### (4)、in-out参数
* in-out 参数(in-out parameter)能让函数影响函数体以外的变量
* in-out 参数不能有默认值
* in-out 参数不能是 可变长度参数
* 调用方传递的 in-out 参数必须是 var 类型
* 调用方传递 in-out 参数时，必须在其前添加 & 前缀

```
//必须为var 可变类型。
var uAge=23

// 下划线_ 表示调用方传递第一个实参时不需要具名; inout 参数需要跟在冒号后面，类型签名
func printUserInfo2(_ uName:String, age: inout Int){
    age+=2
    print("\(uName)的年龄为\(age)")
}

//实参前面必须前缀 &，并且实参必须为var
printUserInfo2("张三", age: &uAge)
print(uAge)
```

![](https://images.gitee.com/uploads/images/2019/0117/183537_2f8d35f4_930142.png "屏幕截图.png")

* 上面函数中，第一个参数在声明外部参数时，使用了下划线_ , 表示调用方在传递该参数时不需要具名

### 3、函数返回值

* 函数后面追加 `->类型`
* 没有返回值的函数本质上是省略了 `->Void`, Void 也是一种类型

```
func getMax(num1:Int , num2:Int)->Int{
    return num1>num2 ? num1:num2
}

let maxNum=getMax(num1: 1, num2: 2)
print(maxNum)
```


### 4、嵌套函数和作用域

* 函数A 内包含 函数B ，函数B 则被称为 `嵌套函数`
* 嵌套函数B 仅在 函数A 内有效——作用域的限定

```java
func getTriangleArea(base:Double, height:Double)-> Double{
    let numerator=base*height
    
    //嵌套函数。仅在父级函数内有效；可以调用父级函数中的属性/参数
    func getArea()->Double{
        return numerator / 2
    }
    
    return getArea()
}

let area=getTriangleArea(base: 6, height: 10)
print(area)
```

实际使用时，上面这个例子完全不需要嵌套函数。但，这里仅是为了举例。


### 5、多个返回值

Swift 中的函数可以有多个返回值，此时，多个返回值使用 元组 包裹。

```
//返回包含三个元素的 具名元组——元组中的每个元素都有名称
func groupNum(numbers:Int...)->(below10:[Int],below20:[Int],other:[Int]){
    //构建三个空数组
    var arr1=[Int]()
    var arr2=[Int]()
    var arr3=[Int]()
    
    for num in numbers{
        if num<10{
            arr1.append(num)
        }else if num<20{
            arr2.append(num)
        }else{
            arr3.append(num)
        }
    }
    
    return (arr1,arr2,arr3)
}

let numTupple=groupNum(numbers: 1,2,5,12,13,22,35,55)
//根据名称调用元组中的元素
print(numTupple.below10)
print(numTupple.below20)
//根据索引调用元组中的元素
print(numTupple.2)
```

![](https://images.gitee.com/uploads/images/2019/0117/192127_f4a90eaf_930142.png "屏幕截图.png")


### 6、可空返回值类型

* 返回值类型后面追加 `？`

```
func printPenName(_ yourNames:(schoolName:String ,penName:String?))->String?{
    return yourNames.penName
}

let penName1=printPenName((schoolName: "张三", penName: nil))
if let name1=penName1{
    print(name1)
}

let penName2=printPenName((schoolName: "李四", penName: "小四"))
if let name2=penName2{
    print(name2)
}
```

上述示例中，接收的参数为具名元组。


### 7、提前退出函数

guard语句。

格式为：

```
guard condition else {
    <#statements#>
}
```

* 跟if/else 语句一样，guard语句会根据某个表达式返回的布尔值结果来执行代码;
* 但不同之处是，如果某些条件没有满足，才会执行 else 中的语句，从这里可以直接退出程序；满足条件则继续执行语句外面的内容

```
func helloTo(names:(schoolName:String,nickName:String?)){
    
    guard let nick=names.nickName else {
        print("Hello,\(names.schoolName)")
        return
    }
    
    print("Hello,亲爱的\(nick)")
}

helloTo(names: (schoolName: "张三", nickName: nil))
helloTo(names: (schoolName: "李四", nickName: "小四"))
```

上述示例代码中，传入 学名和昵称 组成的元组。如果没有昵称，则直接使用学名打招呼，然后退出函数。如果有昵称，则使用昵称打招呼


### 8、函数类型

* 所有函数都有函数类型 function type
* 函数类型由 参数类型和返回值类型组合而成，格式：`(参数1类型,参数2类型) -> (返回值类型)`
* 如果某个函数没有参数，也没有返回值，那么它的函数类型为：` ()->() `


```
func printPenName(_ yourNames:(schoolName:String ,penName:String?))->String?{
    return yourNames.penName
}

let penName1=printPenName((schoolName: "张三", penName: nil))
if let name1=penName1{
    print(name1)
}

let penName2=printPenName((schoolName: "李四", penName: "小四"))
if let name2=penName2{
    print(name2)
}
```
上述示例代码的函数类型为： **((String,String?)) -> (String?)** 


* 定义 函数类型 的常量

```
func groupNum2(nums:Int...)->([Int],[Int]){
    //偶数数组
    var evenArr=[Int]()
    //奇数数组
    var oddArr=[Int]()
    for num in nums{
        if num%2==0 {
            evenArr.append(num)
        }else{
            oddArr.append(num)
        }
    }
    return(evenArr,oddArr)
}

//声明一个类型为 (Int...)->([Int],[Int]) 的常量，它的值为 groupNum2 函数
let numArr: (Int...)->([Int],[Int]) = groupNum2

//调用常量 numArr 等价于调用 groupNum2 函数
let result=numArr(1,2,3,4,5,6)

print(result)
```

上面的示例代码中，定义了函数类型的常量 numArr , 其值为 groupNum2 。


**注意:exclamation:注意:exclamation:注意:exclamation:：**

**在定义函数类型的常量/变量时,**
* **如果函数有参数，那么，常量/变量的值后面不需要加 `()`**
* **如果函数没有参数，那么，常量/变量值后面必须加 `()`**


---

## 十三、闭包 - closure

closure  ['kləʊʒə] 关闭、终止、结束

* 闭包(closure)是在应用中完成特定任务的互相分离的功能组。

* 函数是闭包的特殊情况，可以把函数理解为有名字的闭包——具名闭包。

* 闭包有类似于函数的结构，还能省去命名和函数声明。所以，可以用 函数参数或返回值 的形式传递闭包。

### 1、闭包语法

```
//不同地区上报的志愿者数量的数组
let volunteerCounts = [1,3,40,32,2,53,77,13]

//升序排列
func sortAscending(_ i: Int, _ j: Int) -> Bool {
    return i < j
}

let volunteersSorted = volunteerCounts.sorted(by: sortAscending)

//降序排列
func sortDescending(_ i: Int, _ j: Int) -> Bool {
    return i > j
}

let volunteersSorted2 = volunteerCounts.sorted(by: sortDescending)
```

在解析上面示例前先看一下数组中 sorted()函数的API ：

```
/// - Parameter areInIncreasingOrder: A predicate that returns `true` if its first argument should be ordered before its second argument; otherwise, `false`.
/// - Returns: A sorted array of the sequence's elements.
public func sorted(by areInIncreasingOrder: (Element, Element) throws -> Bool) rethrows -> [Element]
```

这段API描述说：

* 参数  **areInIncreasingOrder**  是一个函数类型的参数，具体类型为：`(Element, Element) throws -> Bool`。如果这个函数类型 **参数的值为true，那么前一个元素就需要左边，第二个参数排右边。否则，第二个排左边，第一个排右边。** 
* sorted() 函数最终会返回一个排序后的数组。
* sorted() 指定了外部参数——by, 对应的内部参数就是——areInIncreasingOrder

明白了上述API之后，我们再看一开始的示例代码。
* 我们定义的 sortAscending 、 sortDescending 两个函数，分别触发了 sorted() 函数的升序和降序操作
* 当把sortAscending(）作为参数传递给 sorted（）时中，如果 i<j ，那么，i 排左边，j 排右边，也就是说，小值放左边。所以，最终就得到了一个升序排列的数组。
* 当把sortDescending(）作为参数传递给 sorted（）时中，如果 i>j ，那么，i 排左边，j 排右边，也就是说，大值放左边。所以，最终就得到了一个降序排列的数组。
* 由于函数也是一种特殊的闭包——具名闭包，所以，上面的这个示例本质是：把闭包作为参数进行传递。





### 2、闭包表达式

#### (1)、闭包表达式的标准格式

由于闭包可以省略函数命名和函数声明，所以，闭包的简化格式为：

```
{(parameters) -> return type in 
    // 代码内容
}
```
在上述格式中：
* 闭包表达式使用大括号进行包裹
* in 用来间隔返回值类型 和 代码内容

所以，根据上面的这个闭包表达式格式，可以简化第一小节中的排序代码：

```
let volunteerCounts = [1,3,40,32,2,53,77,13]

let volunteersSorted = volunteerCounts.sorted(by: {
    (i:Int,j:Int)-> Bool in
        i<j
})

let volunteersSorted2 = volunteerCounts.sorted(by: {
    (a:Int,b:Int)->Bool in
        a>b
})
```

* 示例中这种直接将闭包作为参数的形式称为 内联闭包

#### (2)、根据类型推断简化闭包

闭包表达式也可使用Swift的类型推断。

也就是说，在前一小节的示例中：i、j 的类型可以根据数组元素的类型进行推断；而闭包返回值的类型则可以根据闭包中的代码进行推断，既然返回值类型可以省略，那么返回标记 `->` 也就可以直接省掉，所以。。。

```
let volunteerCounts = [1,3,40,32,2,53,77,13]

let volunteersSorted = volunteerCounts.sorted(by: {
    i,j in i<j
})

let volunteersSorted2 = volunteerCounts.sorted(by: {
    (a,b) in a>b
})
```


#### (3)、快捷参数

在前面的示例中，编译器知道 `sorted(by:)` 接收一个闭包，也知道闭包中的参数个数和参数类型。在这种情况下，我们可以不必再为闭包的参数声明变量名——也就是说，可以直接省略 i、j 、a、b 的定义和间隔符 `in`，然后在闭包的代码语句中直接使用`快捷参数`进行相关运算。

 **快捷参数，就是以 `$index` 格式表示的函数参数。$ 是其中的固定格式，index 表示参数的索引（函数的第几个参数，从0计数）。** 如：`$0`, 表示函数的第一个参数；`$1` , 表示函数的第二个参数，依次类推。

内联闭包表达式中可以使用快捷参数。（CnPeng 还有哪些使用场景？）

* 使用快捷参数简化排序代码

```
let volunteerCounts = [1,3,40,32,2,53,77,13]

let volunteersSorted = volunteerCounts.sorted(by: {$0<$1})

let volunteersSorted2 = volunteerCounts.sorted(by: {$0>$1})
```

#### (4)、尾部闭包

 **如果某个函数的最后一个参数是闭包表达式，那么，在调用该函数时可以将这个闭包放置在 `（ ）` 外部, 并且可以省略参数名**。这就叫做尾部闭包语法（trailing closure syntax）。

> CnPeng 在Kotlin中如果某个函数的最后一个参数为lambda表达式，那么，调用该函数时可以将lambda移到 () 外部。


* 使用尾部闭包语法简化代码 

```java
let volunteerCounts = [1,3,40,32,2,53,77,13]
let volunteersSorted = volunteerCounts.sorted{$0<$1}
let volunteersSorted2 = volunteerCounts.sorted{$0>$1}
```


### 3、函数作为返回值

函数可以返回其它函数作为返回值。——CnPeng 因为在前面的章节中我们已经知道，函数在Swift中也是一种数据类型。

假设 函数A 的返回值为 函数B，不论函数A是否有参数，在定义函数A的函数体时并没有区别。但是在调用的时候会有区别。

#### (1)、函数A没有参数


```
//--------将函数作为返回值-----------------
func getLightCount() -> (Int,Int)->Int {
    func calcuLightNum(newAdd : Int , existed : Int)->Int{
        return newAdd+existed
    }
    
 //注意，因为 calcuLightNum 有参数，所以末尾没有括号！！！
 return calcuLightNum
}

//定义函数类型的常量——用来接收最新的路灯总数量
let curLightCount=getLightCount()
//let curLightCount2:(Int,Int)->Int=getLightCount()
var newAddLights=6
//已经存在的路灯总数量
var existedLightCount=10

print("当前总的路灯数量为\(curLightCount(newAddLights,existedLightCount))")

existedLightCount=curLightCount(newAddLights,existedLightCount)
print("当前总的路灯数量为\(existedLightCount)")
```
上述示例中：
* 使用了嵌套函数，并且把内部嵌套的函数作为返回值
* 我们定义了 (Int,Int)->Int 类型的常量curLightCount，由于 getLightCount() 没有参数，所以在为curLightCount赋值时，getLightCount后面必须追加（）
* 当我们使用 curLightCount 时才传递了新、旧路灯的数量

#### (2)、函数A有参数


```
func getLightCount(str:String) ->(Int)->Int {
    print("有参数的函数，也可以把另一个函数作为返回值")
    func calcuLightNum(count:Int)->Int{
        return count
    }
    return calcuLightNum
}

//函数有参数，所以赋值时getLightCount后面省略括号。注意 curLightCount 的类型
let curLightCount:(String)->(Int)->Int=getLightCount
var newAddLights=6
//已经存在的路灯总数量
var existedLightCount=10
var str="哈哈哈"
//调用函数类型的常量时，注意这里参数的传递形式！！
existedLightCount=curLightCount(str)(newAddLights)
print("当前总的路灯数量为\(existedLightCount)")
```

#### (3)、CnPeng ：链式调用

* 调用返回值为函数的函数时，本质上使用的是链式调用。

下面的示例中，`getLightCount(str: "哈哈哈哈")(666)` 和 `getLightCount2()(777)`都包含了两个（）。前一个（）表示 getLightCount/getLightCount2 自己需要的参数，后一个括号表示返回值中所需要的参数。

```
func getLightCount(str:String) ->(Int)->Int {
    print("有参数的函数，也可以把另一个函数作为返回值")
    func calcuLightNum(count:Int)->Int{
        return count
    }
    return calcuLightNum
}

var existedLightCount=getLightCount(str: "哈哈哈哈")(666)
print("当前总的路灯数量为\(existedLightCount)")

func getLightCount2() ->(Int)->Int {
    print("没有参数的函数，也可以把另一个函数作为返回值")
    func calcuLightNum(count:Int)->Int{
        return count
    }
    return calcuLightNum
}

var existedLightCount2=getLightCount2()(777)
print("当前总的路灯数量为\(existedLightCount2)")
```


### 4、函数作为参数

函数可以作为其它函数的参数。

其实在 [闭包语法] 一节中，就是把 sortAscending() 作为 sorted()函数的参数。

```
func addNewLights(budget:Int,condition:(Int)->Bool)->((Int,Int)->Int)?{
    if condition(budget) {
        func getLightCount(newAdd:Int,exited:Int)->Int {
            return newAdd+exited
        }
        //这两种返回方式都可以
        //return getLightCount
        return getLightCount(newAdd:exited:)
    }else{
        return nil
    }
}

func isBudgetEnough(budegt:Int)->Bool{
    print(budegt>=10000 ? "预算够了，可以新增路灯":"预算不足，不新增路灯")
    return budegt>=10000
}

var newAddLights=6
var totalLightCount=10
//这里展示了tempLightCount 完整的类型
//let tempLightCount:(Int,(Int)->Bool)->((Int,Int)->Int)?=addNewLights

//注意：传递函数作为参数时，只传函数名即可
if let tempLightCount = addNewLights(budget:99990,condition:isBudgetEnough){
    totalLightCount=tempLightCount(6,totalLightCount)
}

print(totalLightCount)
```

### 5、闭包能捕获变量

```
func getNumCount(count:Int) -> (Int)->Int {
    var totalNumCount=count

    func getSum(step:Int) -> Int{
        totalNumCount += step
        return totalNumCount
    }
    
    return getSum
}

var curCount=10

//sumBy 的类型为 (Int) -> Int ，它持有的是：getNumCount(count: curCount)返回值 的引用
let sumBy=getNumCount(count: curCount)
//输出11
sumBy(1)
//输出14
sumBy(3)
//输出20
curCount=sumBy(6)

//sumBy2 的类型为 (Int) -> (Int) -> Int ，它持有的是：getNumCount 的引用
let sumBy2=getNumCount
//输出11
sumBy2(10)(1)
//输出15
sumBy2(12)(3)
```

>CnPeng：该小节解说是自己的理解，原书说的太笼统了。。。 

* 由于函数也是闭包的一种，所以，函数类型常量/变量也是属于闭包。
* 上述示例代码中，sumBy 的类型为 (Int) -> Int ，它持有的是：getNumCount(count: curCount)返回值 的引用。
    * A: 在构造sumBy时传入了count, 所以系统在划分内存存储sumBy时也将 totalNumCount 存起来了。
    * B: 在调用 sumBy(1) 时，totalNumCount并没有被重新初始化，可以直接参与 getSum() 中的运算 —— 这就是所谓的  **闭包捕获变量！！！！** wtf!!!
    * C: sumBy(1) 触发 getSum() 后修改了内存中 totalNumCount 的值，但不管怎么修改，这个值一直在内存中存留——除非 sumBy 常量被销毁了。
* 而 sumBy2 的类型是 (Int) -> (Int) -> Int ，它持有的是：getNumCount 的引用。
    * A: 在构造 sumBy2 时，只是在内存中存储了sumBy2，而没有存储 totalNumCount
    * B: 在调用 sumBy2(10)(1) 时才划分内存对 totalNumCount 进行存储。当调用sumBy2(12)(3)时，又重新创建了一个 totalNumCount 的引用。两次调用时的 totalNumCount 并没有指向同一个引用，所以——这种情况下就不能被称为”闭包捕获变量“

### 6、闭包是引用类型

闭包是引用类型(reference type)。

所以，在构建函数类型的常量/变量时，这个常量/变量实际是持有了闭包的引用。

```
func getNumCount(count:Int) -> (Int)->Int {
    var totalNumCount=curCount

    func getSum(step:Int) -> Int{
        totalNumCount += step
        return totalNumCount
    }

    return getSum
}

var curCount=10

//sumBy 的类型为 (Int) -> Int ，它持有的是：getNumCount(count: curCount) 的引用
let sumBy=getNumCount(count: curCount)
//输出11
sumBy(1)
//输出14
sumBy(3)
//输出20
curCount=sumBy(6)

//虽然新定义了一个常量sumBy2 ,但它持有的只是 sumBy 的引用，并没有重新划分内存空间
let sumBy2=sumBy
//输出36
sumBy2(16)
```

上述示例中，构造了一个sumBy2, 但它持有的只是 sumBy 的引用，并没有重新划分内存空间。所以，输出结果会在原来的基础上累加，最终输出36

> 引用类型和值类型的内容在第18章会介绍。

### 7、函数式编程

函数式编程(functional programming)

#### (1)、函数式编程的定义和特点

略


#### (2)、高阶函数（higher-order function）

高阶函数至少接收一个函数作为输入。

前面使用的数组中的 sorted(by:_) 就是一个高阶函数。

此处再介绍其他几个高阶函数：

##### A: map ( _ : )

map(_:)可以用来改变数组的元素值（注意：不是增删，只是改值）, 然后将修改后的元素组合成一个新的数组作为返回值。

```
let oldNumArr=[1,2,3,4]

let newNumArr=oldNumArr.map{
    //这里 $0 为快捷参数
    return $0 * 2
}

//输出 [2, 4, 6, 8]
print(newNumArr)
```

##### B: filter( _ : )

filter(_:)对数组内容进行过滤，将符合过滤条件的元素组成一个新数组返回。

```
let oldNumArr=[1,2,3,4]
let newNumArr=oldNumArr.filter {
    return $0 % 2 == 0
}
//返回[2, 4]
print(newNumArr)

let newNumArr2=oldNumArr.filter {
    return $0 > 5
}
//返回[]
print(newNumArr2)
```

##### C: reduce( _ : )

reduce [rɪ'djuːs] 归纳，整理

接收两个参数，第一个参数为起始值，第二个参数为闭包。闭包中接收两个参数，并指定了运算规则。返回值为Result类型

* 起始值也会参与运算。
* 调用方为空时，直接返回指定的起始值

```
let oldNumArr=[1,2,3,4]
let result=oldNumArr.reduce(10){
    return $0 + $1
}
//20
print(result)

let oldNumArr2=[Int]()
let result2=oldNumArr2.reduce(10){
    return $0 + $1
}
//10
print(result2)
```


---

## 十四、枚举

### 1、基本枚举

* 定义枚举的方式是 `enum` 关键字后面跟  **枚举 类**  的名字
* 枚举的内容使用 `{}` 包裹
* 枚举中至少包含一个 `case` , 表示枚举的成员

#### (1)、基本示例

枚举类型的变量在声明时，必须初始化

```java
enum TextAlignment{
    case left
    case right
    case center
}

//枚举类型的变量在声明时，必须初始化
var alignment=TextAlignment.left
print(alignment)
```
#### (2)、枚举类型的推断

如果变量已经确定类型为枚举类型，再次赋值时，可以省略枚举类型，直接使用 .xxx 的形式调用

```java
enum TextAlignment{
    case left
    case right
    case center
}

//枚举类型的变量在声明时，必须初始化
var alignment=TextAlignment.left
print(alignment)

//如果变量已经确定类型为枚举类型，此时，再次赋值时，可以省略枚举类型，直接使用 .xxx 的形式调用
alignment = .center
print(alignment)
```
#### (3)、枚举成员的比较

```
enum TextAlignment{
    case left
    case right
    case center
}

//枚举类型的变量在声明时，必须初始化
var alignment=TextAlignment.left
print(alignment)

//如果变量已经确定类型为枚举类型，此时，再次赋值时，可以省略枚举类型，直接使用 .xxx 的形式调用
alignment = .center
print(alignment)

if alignment == .center {
    print("居中对齐——\(alignment)")
}
```

#### (4)、使用switch比较枚举值

* 在switch语句中，如果 case 已经完全涵盖了 枚举类的成员，那么就可以省略default语句。
* 但是，通常来说，为了保险，还是会保留default



```java
enum TextAlignment{
    case left
    case right
    case center
}

//枚举类型的变量在声明时，必须初始化
var alignment=TextAlignment.left
print(alignment)

//如果变量已经确定类型为枚举类型，此时，再次赋值时，可以省略枚举类型，直接使用 .xxx 的形式调用
alignment = .center
print(alignment)

if alignment == .center {
    print("居中对齐——\(alignment)")
}

switch alignment {
case .left:
    print("左对齐")
case .right:
    print("右对齐")
case .center:
    print("居中对齐")
default:
    print("为其他情况备用的")
}
```

### 2、原始值枚举

原始值(raw value)类型的枚举.

>CnPeng 所谓原始值类型，就是指定枚举中成员的取值类型。并且还可以使用 `.rawValue` 属性获取 rawValue值 


#### (1)、基本示例

```java
enum TextAlignment : Int{
    case left
    case right
    case center
    case justify
}

//输出left
print(TextAlignment.left)

//输出0
print(TextAlignment.left.rawValue)
```

上述示例代码中：
* 声明了一个 TextAlignment 枚举，其成员的原始类型为 ：Int 。
* 示例中，指定了成员类型为Int，但并没有为成员进行初始赋值，此时，使用默认值，第一个成员默认值为0，第二个为1，第三个为2...
* 使用 `枚举名.成员名.rawValue` 可以获取成员的原始值

#### (2)、指定原始值（为枚举成员赋初值）

```java
enum TextAlignment : Int{
    case left=10
    case right=20
    case center=30
    case justify=40
}

//输出left
print(TextAlignment.left)
//输出10
print(TextAlignment.left.rawValue)
````
* 上述代码中，在创建枚举成员时为它们赋了初值。

#### (3)、将原始值转换为枚举成员

使用原始枚举值的作用在于：在传输或存储枚举时，通过rawValue将枚举成员转换成原始值进行存储/传输。

那么，又该如何将原始值转换为枚举成员呢？

```java
enum TextAlignment : Int{
    case left=10
    case right=20
    case center=30
    case justify=40
}

let myAlignment=30

//使用TextAlignment(rawValue:)构造枚举成员时，返回值是TextAlignment?，所以，此处使用了实例绑定
if let alignment=TextAlignment(rawValue:myAlignment){
    print("原始值转枚举成员——成功：\(alignment),原始值为\(alignment.rawValue)")
}else{
    print("转换失败")
}
```

上述示例代码中
* 使用 `TextAlignment(rawValue:myAlignment)` 构造枚举成员，返回值类型为 `TextAlignment?`


#### (4)、原始值类型为字符串的枚举

* 原始值类型为字符串时，如果没有指定初始值，那么rawValue 输出的就是成员的名字

```java
enum TextAlignment : String{
    case left="left——指定的rawValue"
    case right="right——指定的rawValue"
    case center
    case justify
}

print(TextAlignment.left)
print(TextAlignment.left.rawValue)

print(TextAlignment.center)
//原始值类型为字符串时，如果没有指定初始值，那么rawValue 输出的就是成员的名字
print(TextAlignment.center.rawValue)
```

### 3、方法/函数

Swift 中枚举也可以有函数

#### (1)、基本示例以及函数中的self

示例代码中定义了一个电灯泡的枚举，有两个成员一个方法。

假设灯泡开启之后温度即刻升为工作温度，关闭之后温度立刻降为0度。那么，当开启之后，灯泡表面温度为：环境温度 ambient + 工作温度（150）；关闭之后灯泡表面温度仅为外部环境温度。

 **注意：**
 **Swift中，所有的方法/函数都会存在一个隐式参数 `self`——表示当前函数所在类的实例。（ 同Java中的this )** 

```
enum LightBulb{
    case on
    case off
    
    func surfaceTemperature(ambientTemperature ambient:Double)->Double{
        switch self {
        case .on:
            return ambient+150
        case .off:
            return ambient
        }
    }
}

let lightBulb=LightBulb.on
let curLightTemp=lightBulb.surfaceTemperature(ambientTemperature: 77)
print("当前电灯泡的开关状态为：\(lightBulb),温度为：\(curLightTemp)")
```
#### (2)、修改self的值

* Swift 中，枚举是`值类型(value type)`, 值类型中的函数不能修改self。(第15章会有介绍值类型）
* 如果需要让值类型中包含的函数修改 self, 需要在声明该函数时，在func 关键字前面增加 `mutating`

下面的示例代码中增加了 灯泡的开关函数--toggle. 触发该函数之后，改变self的值——也就是 LightBulb 的值。
 
```java
enum LightBulb{
    case on
    case off
    
    func surfaceTemperature(ambientTemperature ambient:Double)->Double{
        switch self {
        case .on:
            return ambient+150
        case .off:
            return ambient
        }
    }
    
   //值类型中的函数想修改self时必须增加 mutating关键字
   mutating func toggle(){
        switch self{
        case .on:
            self = .off
        case .off:
            self = .on
        }
    }
}

var lightBulb=LightBulb.on
let curLightTemp=lightBulb.surfaceTemperature(ambientTemperature: 77)
print("当前电灯泡的开关状态为：\(lightBulb),温度为：\(curLightTemp)")

lightBulb.toggle()
let curLightTemp2=lightBulb.surfaceTemperature(ambientTemperature: 77)
print("当前电灯泡的开关状态为：\(lightBulb),温度为：\(curLightTemp2)")
```

### 4、关联值

前面的示例中，都是在枚举中定义了一个单纯的枚举静态成员。

Swift 中，枚举的成员还可以有关联值。
* 通过关联值可以把数据附着在枚举实例上
* 不同的成员可以有不同的关联值

> CnPeng 带关联值的枚举成员看上去像是一个接受参数的函数。但书中把他们描述为 **元组**

#### (1)、带关联值的枚举

我们在获取某个形状的面积时，不同的形状，需要的参数不一样：正方形只要知道边长即可，长方形则需要宽和高。所以，我们下面定义了一个关联值的枚举：正方形关联了边长，长方形关联了宽和高。

```
enum ShapeDimensions{
    case square(side:Double)
    case rectangelr(width:Double,height:Double)
}

var squareShape=ShapeDimensions.square(side: 10)
var rectShape=ShapeDimensions.rectangelr(width: 20, height: 10)
```
#### (2)、switch中使用带有关联值的枚举

下面 area() 函数中, 在switch中使用了模式匹配的形式，将带有关联值的枚举绑定到了新的常量上——绑定实例。如果不在绑定实例，会报错。

```
enum ShapeDimensions{
    case square(side:Double)
    case rectangelr(width:Double,height:Double)
    case point

    func area()->Double{
        switch self {
        //这里的case 语句使用了模式匹配，把self的关联值绑定到的新的常量上。必须要有let，否则，报错。
        case let .square(side:cusSide):
            return cusSide * cusSide
        case let .rectangelr(width: w, height:h):
            return w*h
        case .point:
            return 0
        }
    }
}

var squareShape=ShapeDimensions.square(side: 10)
var rectShape=ShapeDimensions.rectangelr(width: 20, height: 10)
var pointShaoe=ShapeDimensions.point

print(squareShape.area())
print(rectShape.area())
print(pointShaoe.area())
```

### 5、递归枚举

在枚举A中，假设有成员B，该成员B有关联值，假设其关联值的类型为枚举A，那么，这种枚举就被称为`递归枚举。`

* 对于这种递归枚举，可以在声明枚举时，在 enum 关键字前面添加 `indirect` 关键字；或者在枚举成员 case 关键字前面添加 `indirect`()。

示例如下：

```
indirect enum A{
    case B(c:A)
}

enum A1{
   indirect case B1(c:A1)
}
``` 

在枚举中，为了存放枚举的成员，我们必须知道每一个成员所占用的内存空间大小，然后才能划分内存区域。

在上述示例中，我们要想知道 A 的内存空间，就必须先知道 B 占用的内存空间。由于 B 中有关联值 c ，所以，B 的内存空间最终由 c 确定。但 c 又是 A 类型的，这样就产生了一个递归循环关系。

如果我们没有添加 indirect 关键字，那么编译器就会任务 A 需要无限空间，然后就会报错。如果我们添加了 indirect 关键字，那么，编译器就会分配一个指针的大小给 A——在64为架构下，一个指针有8位。

综上， **`indirect` 关键字的作用是：告知编译器把枚举数据放到一个指针大小的内存空间中。** 


---

## 十五、结构体和类

### 1、新工程

#### (1)、创建新工程

如果XCode没有在运行，打开之后选择：`Create a new Xcode project`
![](https://images.gitee.com/uploads/images/2019/0121/141916_c1d1066c_930142.png "屏幕截图.png")

如果XCode 正在运行，则点击 `File → New → Project`

然后就会呈现下图，并选中其中的 `macOS → Command Line Tool`：

![](https://images.gitee.com/uploads/images/2019/0121/120834_8a476edc_930142.png "屏幕截图.png")

然后一路next并填写相应信息即可。

创建完成之后会主动打开该工程并显示下图：

![](https://images.gitee.com/uploads/images/2019/0121/141551_b65c6509_930142.png "屏幕截图.png")

上图中显示了签名信息、依赖项 等内容。（CnPeng 等同于AndroidStudio中的 ProjectStructure）


#### (2)、Xcode窗口布局

![](https://images.gitee.com/uploads/images/2019/0121/142745_12be1947_930142.png "屏幕截图.png")

工具栏右上角的 ![](https://images.gitee.com/uploads/images/2019/0121/142850_a0da8f39_930142.png "屏幕截图.png") 分别用来控制 导航栏、调试区、工具区 的显示和隐藏

#### (3)、mian.swift 文件

main.swift表示程序的入口。

main.swift 通常包含“顶层”代码，也就是不包括在任何函数中或不在其它类型(比如结构体或类)中定义的代码。这个文件中的代码是顺序执行的:从上到下。

main.swift中的代码通常用来做初始化工作。（CnPeng 这个类似于Android中的Application类）

我们在单独的文件中定义类，然后在main.swift中创建类的实例。比如，创建一个 Town.swift 文件保存结构体 Town 的定义，然后在main.swift中创建Town的实例。

新建的工程中，main.swift 文件包含如下内容：

```
//  main.swift
//  MonsterTown
//
//  Created by CnPeng on 2019/1/21.
//  Copyright © 2019 CnPeng. All rights reserved.
//

import Foundation

print("Hello, World!")
```

其中的 `import Foundation` 表示在 `main.swift` 中引用了 `Foundation框架(framework)`。这个框架包含用来和 Object-C 交互的类。

#### (4)、构建和运行程序

* 方式1：Product -> run

![](https://images.gitee.com/uploads/images/2019/0121/143932_2dceb600_930142.png "屏幕截图.png")

* 方式2：Cmd+R

可以直接利用上图中的快捷方式： cmmand+R

* 方式3：黑三角

![](https://images.gitee.com/uploads/images/2019/0121/144117_33f4bedf_930142.png "屏幕截图.png")


### 2、结构体

结构体(struct)是把相关数据块组合在一起放在内存中的一种类型。

当需要把数据组合为一种通用类型时就可以用结构体。比如，在 MonsterTown 中创建结构体 Town 来为一个有怪兽侵袭问题的镇子建模。

#### (1)、新建Town结构体

`File -> New -> file` 或者可以直接使用快捷键 Cmd+N  

![](https://images.gitee.com/uploads/images/2019/0121/144632_377984ef_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0121/144802_4f14d6c0_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0121/145216_6be4127a_930142.png "屏幕截图.png")

#### (2)、声明结构体--struct

在新建的 Town.swift 文件中声明结构体Town

* 结构体使用关键字 struct 修饰

```java
import Foundation
struct Town{
    
}
```

上述示例中，在 `{}` 内部定义结构体的行为。比如为结构体添加一些变量——这些变量被称为属性。这些属性可以是常量或者变量。

```java
import Foundation
struct Town{
    //声明人数为 3500
    var population=3500
    //声明红绿灯个数为 4
    var numOfStopLoghts=4
}
```

#### (3)、在 main.swift 中创建 Town 实例

```java
import Foundation

var town=Town()
print("城镇人口为：\(town.population), 城镇红绿灯数量为:\(town.numOfStopLoghts)")
```

### 3、实例方法

结构体中可以定义函数。

#### (1)、定义函数

```java
import Foundation
struct Town{
    var population=3500
    var numOfStopLoghts=4
    
    func printDescription(){
        print("城镇人口为：\(population), 城镇红绿灯数量为:\(numOfStopLoghts)")
    }
}
```
#### (2)、调用函数

在 main.swift 中调用 结构体中的函数

```java
import Foundation

var town = Town()
town.printDescription()
```

### 4、mutating方法/函数

因为结构体和枚举一样都属于值类型，所以，结构体内部的方法要修改结构体的属性时，该函数必须被 `mutating` 修饰

```java
import Foundation
struct Town{
    var population=3500
    var numOfStopLoghts=4
    
    func printDescription(){
        print("城镇人口为：\(population), 城镇红绿灯数量为:\(numOfStopLoghts)")
    }
    
    //值类型的类内部的函数修改成员时，函数需要前缀 mutating
    mutating func changePopulation(by amount:Int){
        population += amount
        printDescription()
    }
}
```

在 main.swift 中调用 changePopulation 函数

```
import Foundation

var town = Town()
town.printDescription()
town.changePopulation(by: 1000)
```

### 5、类

#### (1)、类的声明

* 类使用 `class` 修饰
* 类中可以有属性和函数

新建Monster.swift 文件，然后添加代码：

```java
import Foundation

class Monster{
    var town:Town?
    var name="Monster"
    
    //terrorize ['tɛrəraɪz] 恐吓，侵扰
    func terrorizeTown() {
        if nil != town {
            print("\(name) is terrorizing a town")
        }else{
            print("\(name) hasn't found a town to terrorize yet...")
        }
    }
}
```

#### (2)、继承

* 类的主要特性是继承(inheritance)，而 **结构体不能被继承** 
* 声明 A 类继承自 B 类时， A 、B 之间使用 冒号` ：` 相连
* 子类会继承父类的属性和方法，重写方法时，会调用 super 语句。（枚举、结构体不能被继承，所以，没有super）

##### A: 定义Monster 的子类 Zombie

* 重写父类函数时，需要加 override 关键字

定义Monster 的子类 Zombie

```
import Foundation

class Zombie: Monster {
    //僵尸走路一瘸一拐的。limp [lɪmp] 跛行
    var walkWithLimp=true
    
    override func terrorizeTown() {
        //僵尸入侵镇子，抓走了10个人。town 是继承自父类的属性
        town?.changePopulation(by: -10)
        super.terrorizeTown()
    }
}
```

在 main.swift 中使用 Zombie：

```
import Foundation

var town = Town()

var zombie=Zombie()
zombie.town=town
//僵尸要去破坏镇子了。。。town 不为空，所以，镇子人数会减少10个
zombie.terrorizeTown()
zombie.town?.printDescription()
```
此时，运行结果为：

```
城镇人口为：3490, 城镇红绿灯数量为:4
Monster is terrorizing a town
城镇人口为：3500, 城镇红绿灯数量为:4
```

由于 Zombie 中的 `terrorizeTown()` 先调用了一次 `changePopulation()`，所以，第一行输出是此时打印的。第三个输出，则是 `zombie.town?.printDescription()` 的输出

在上述示例中，我们在调用 changePopulation 函数时，必然要判断一下 town 是否为nil——此处使用了可空链式调用 `?.`的格式。目的是确保 town 非空的时候才去调用 changePopulation 函数。

前面已经学习过实例绑定，所以，你可能会想着也可以将 Zombie 中的 terrorizeTown() 改为实例绑定模式：

```
func terrorizeTown() {
    if let terrorTown=town{
        terrorTown.changePopulation(by: -10)  
    }
    super.terrorizeTown()
}
```

但是，我们运行之后，却得到了如下结果：

```
城镇人口为：3490, 城镇红绿灯数量为:4
Monster is terrorizing a town
城镇人口为：3500, 城镇红绿灯数量为:4
```

第三行输出的人口依旧是3500！！！Zombie 中的 terrorizeTown() 已经被调用了，并且确实被僵尸抓走了10个人，但这里为什么人口数量依旧是3500呢？


因为：**Town 的类型为 struct——结构体，结构体属于值类型，值类型在传递数据时会被复制一份。也就是说，terrorTown 常量 和 town 实例 在内存中的地址不一致，他们是两个实例。所以，terrorTown 的值被修改之后, town 并不受影响。** 

所以，要慎用实例绑定。


##### B: 禁止重写

如果某个父类不希望它的属性或方法被子类重写，则将这些属性和方法使用 `final` 修饰即可。

我们先定义一个 FinalMonster 类，将其中的函数定义为final

```
class FinalMonster {
    var town:Town?
    var name="Monster"
    
    //terrorize ['tɛrəraɪz] 恐吓，侵扰
    final  func terrorizeTown() {
        if  nil != town {
            print("\(name) is terrorizing a town")
        }else{
            print("\(name) hasn't found a town to terrorize yet...")
        }
    }
}
```

然后定义一个 MonsterBoss 类，并尝试重写 terrorizeTown(）:

```
import Foundation

class MonsterBoss: FinalMonster {

   // //报错 Instance method overrides a 'final' instance method
   // override func terrorizeTown(){
        
   // }
}
```

此时，报错了！！！ 报错 Instance method overrides a 'final' instance method


### 6、类型方法/函数

（该小节对应原书中的 深入学习：类型方法）

可以直接通过 `类名.函数名()` 调用的函数被称为类型方法/函数。（CnPeng 同Java中的静态函数/Kotlin的伴生对象中的函数） 

#### (1)、结构体中的类型方法/函数

* 使用关键字 static 修饰函数

```
struct Square {
    static func numberOfSides() -> Int {
        return 4 
    }
}
```

#### (2)、类中的类型方法/函数

* 如果该类型方法/函数，可以被子类重写，则使用 class 修饰。子类重写时，在 override 后面添加 class 关键字
* 如果不希望子类重写，则使用 static 修饰


##### A: 子类可以重写的类型方法/函数

* 声明类型函数 makeSpookyNoise() 

```
class Zombie: Monster {
    class func makeSpookyNoise() -> String {
        return "Brains..."
    }
    ... 
}
```

* 子类重写父类的类型函数

```
class GiantZombie: Zombie {
    override class func makeSpookyNoise() -> String {
        return "ROAR!"
    }
}
```

##### B: 子类不可以重写的类型函数

* 不希望被子类重写的类型函数，使用 static 修饰

```
class Zombie: Monster {
    static func makeSpookyNoise() -> String {
        return "Brains..."
    }
    ... 
}
```

### 7、mutating方法

（该小节对应原书中的 深入学习：mutating方法）

#### (1)、为什么需要 mutating

该小节使用示例介绍了为什么需要使用 mutating ，概括如下：

* Swift 中所有函数都有一个隐式参数 self
* 被 mutating 标记的函数实际是指明了self 是inout 类型的（12章中有介绍 inout 关键字，表示该参数可以在函数体内被修改）
* 没有被 mutating 标记的函数中，使用的是 self 的副本，对副本的修改并不会影响self本身。


#### (2)、其他知识点

将光标放到某个变量/常量/函数上，然后 **按住 Option 键，并单击** 该 变量/常量/函数 就可以查看类型信息和声明位置信息。

![输入图片说明](https://images.gitee.com/uploads/images/2019/0121/174422_f992254a_930142.png "屏幕截图.png")

---

## 十六、属性

类、结构体、枚举都可以有属性

属性分为两种:存储(stored)属性、计算(computed)属性

* 存储属性可以有默认值
* 计算属性则是根据已有信息返回一个计算结果
 

### 1、基本的存储属性

所谓存储属性，就是可以用来存储数据的属性。

前面使用过的属性都是存储属性。

如上一章 Town结构体 中的 `var population=3500`。 population 属性用来存储人口数量，所以，这就是存储属性。

### 2、嵌套类型/嵌套类

嵌套类型(nested type)是定义在另一个类型内部的类型。

（CnPeng： 同 java/Kotlin 中的内部类, Kotlin中的嵌套类和内部类是两个东西）

枚举通常作为嵌套类型进行使用。

在Town结构体中嵌套枚举，枚举成员描述镇子的规模。

```
import Foundation
struct Town{
    var population=3500
    var numOfStopLoghts=4
    
    //嵌套类型
    enum Size {
        case small
        case medium
        case large
    }
    
    func printDescription(){
        print("城镇人口为：\(population), 城镇红绿灯数量为:\(numOfStopLoghts)")
    }
    
    //值类型的类内部的函数修改成员时，函数需要前缀 mutating
    mutating func changePopulation(by amount:Int){
        population += amount
        printDescription()
    }
}
```

### 3、惰性存储属性

实际使用中，出于内存消耗或者其他因素影响，我们可能希望实例中的某些属性只在第一次被访问时才去初始化。这种情况就被称为 惰性加载(lazy loading)

* 惰性属性在构建类的实例时不会被初始化——也就是说，惰性属性初始化时，必然已经获取到了类的实例
* 惰性属性只在第一次被访问的时候才去初始化——只初始化一次
* 惰性属性必须是可变的——var
* 惰性属性使用 lazy 关键字修饰
* 为惰性属性赋值时，末尾必须追加`（）`，该小括号与 lazy 配合使用才表示是惰性属性，并且在第一次调用时初始化。如果省略（），仅是一个赋值操作，并且会报错 Cannot convert value of type '() -> _' to specified type 'Town.Size'


#### (1)、惰性属性的基本使用

* 在 Town 结构体中声明惰性属性--townSize

```java
import Foundation
struct Town{
    var population=3500
    var numOfStopLoghts=4
    
    //惰性属性： lazy + () 
    lazy var townSize:Size={
        switch self.population{
        case 0...10000:
           return Size.small
        case 10001...100000:
           return Size.medium
        default:
           return Size.large
        }
    }()
    
    //嵌套类型
    enum Size {
        case small
        case medium
        case large
    }
    
    func printDescription(){
        print("城镇人口为：\(population), 城镇红绿灯数量为:\(numOfStopLoghts)")
    }
    
    //值类型的类内部的函数修改成员时，函数需要前缀 mutating
    mutating func changePopulation(by amount:Int){
        population += amount
        printDescription()
    }
}
```

* main.swift中使用惰性属性

```
var myTown=Town()
myTown.printDescription()
print(myTown.townSize)
```


#### (2)、惰性属性的值只被初始化一次

下列代码中我们调用 changePopulation 将人口数增加 10000，此时我们期望的 mTownSize 应该输出 medium , 但是，由于惰性属性的值只能被初始化一次，所以，即便人口数变味了 13500，mTownSize 依旧没有改变。

```
var myTown=Town()
myTown.printDescription()
print(myTown.townSize)

myTown.changePopulation(by: 10000)
print(myTown.townSize)
```

输出结果：

```
城镇人口为：3500, 城镇红绿灯数量为:4
small
城镇人口为：13500, 城镇红绿灯数量为:4
small
```

所以，我们通常把只初始化一次，并且不需要再次修改的属性定义为惰性属性。


### 4、计算属性

类、结构体、枚举都可以使用计算属性。

计算属性不存储值，而是通过 set / get 函数来设置和读取值。
* 计算属性使用 var 修饰
* 只有 get 函数的计算属性被称为只读计算属性。
* 计算属性的类型需要显示声明

#### (1)、只读的计算属性

与惰性属性相比，只读计算属性在书写上有如下区别：
* 去除了 lazy， 没有了 = ，没有了末尾的（）
* 类型需要显示声明，并且类型后面追加了{}
* 在大括号后面编写 get{}

```java
import Foundation
struct Town{
    var population=3500
    var numOfStopLoghts=4
    
    //只读计算属性
    var townSize:Size {
        get{
            switch self.population{
            case 0...10000:
               return Size.small
            case 10001...100000:
               return Size.medium
            default:
               return Size.large
            }
        }
    }
    
    //嵌套类型
    enum Size {
        case small
        case medium
        case large
    }
    
    func printDescription(){
        print("城镇人口为：\(population), 城镇红绿灯数量为:\(numOfStopLoghts)")
    }
    
    //值类型的类内部的函数修改成员时，函数需要前缀 mutating
    mutating func changePopulation(by amount:Int){
        population += amount
        printDescription()
    }
}
```
* main.swift 中的调用

```java
var myTown=Town()
myTown.printDescription()
print(myTown.townSize)

myTown.changePopulation(by: 10000)
print(myTown.townSize)
```
使用 计算属性之后，我们对人口作出修改后，就能得到正确的 townSize 了

#### (2)、可读可写的计算属性

同时具有 set/get 的计算属性被称为可读可写的计算属性

使用set() 函数时，如果加（），则需要在其中指定新值的名称；如果不加小括号，则默认使用 newValue 作为新值的名称。

 
* 在怪兽类——Monster中定义可读可写的计算属性

```
class Monster{
    var town:Town?
    var name="Monster"
    
    //可读可写的计算属性——定义潜在的受害人群体
    var victimPool:Int{
        get{
            return town?.population ?? 0
        }
        set(newVictimPool){
            town?.population=newVictimPool
        }
    }
    
    //terrorize ['tɛrəraɪz] 恐吓，侵扰
    func terrorizeTown() {
        if  nil != town {
            print("\(name) is terrorizing a town")
        }else{
            print("\(name) hasn't found a town to terrorize yet...")
        }
    }
}
```
* main.swift 中调用可读可写的计算属性

```
var newZombie=Zombie()
var newTown=Town()
newZombie.town=newTown

//此处底层调用了 victimPool 的 get
print(newZombie.victimPool)

//此处底层调用了 victimPool 的 set
newZombie.victimPool=500
print(newZombie.victimPool)
```


### 5、属性观察者

属性观察者(property observation)，会观察并响应属性的变化。

 **属性观察者只适用于存储属性（包括继承的存储属性）**，计算属性不适用(计算属性的set/get可以达到同样效果)

willSet 用来观察即将发生的变化；disSet 用来观察已经发生的变化

两者在使用时，如果后面跟`()`,则需要在其中指定变量名; 如果不跟`()`, 则系统默认分别使用 newValuew、oldValue 代指变量名

* 使用 willSet 和 didSet 来监测 Town 结构体中人口数量的变化：

```
struct Town{
    var population=3500{
        willSet{
            //会有警告信息：Attempting to store to property 'population' within its own willSet, which is about to be overwritten by the new value
            population=newValue
            print("willSet中监测population的新值为\(newValue)")
        }
        
        ////也可以这么写。不加小括号时，默认使用oldValue表示旧值
        //didSet{
        //     print("didSet监测population的旧值为：\(oldValue), 新值为\(population)")
        //}
        
        didSet(oldPopulation){
            print("didSet监测population的旧值为：\(oldPopulation), 新值为\(population)")
        }
    }
    
    //值类型的类内部的函数修改成员时，函数需要前缀 mutating
    mutating func changePopulation(by amount:Int){
        population += amount
        // printDescription()
    }

    //省略其他代码块
    ... 
}
```
* main.swift中的调用函数为：

```
var zombie1=Zombie()
var town1=Town()
zombie1.town=town1
zombie1.town?.changePopulation(by: -500)
```

* 运行结果为:

```
willSet中监测population的新值为3000
didSet监测population的旧值为：3500, 新值为3000
```


### 6、类型属性——type property

所谓类型属性就是指通过 `类名.属性名` 直接调用的属性。

CnPeng 同Java中的静态属性。

* 值类型（结构体和枚举）既可以有存储类型的静态属性，也可以有计算类型的静态属性。
* 值类型的静态属性使用 static 修饰，而且存储类型的静态属性必须有默认值
* 类也是既可以有存储类型属性，也可以有计算类型属性
* 子类不能重写父类中使用static修饰的类型属性，但可以重写使用 class 修饰的类型属性

#### (1)、为Town结构体新增存储类型的静态属性

```
struct Town{
    //定义存储类型的 类型属性 region。存储类型的类型属性必须有初始值
    static let region="South"
}    
```
#### (2)、为Monster类定义计算类型的静态属性

如果计算类型的属性的 get函数只有返回语句，没有其他，则可以省略 get{} 直接写 return 语句

计算类型属性必须显示声明类型！！！

* 修改 Monster 类

```java
class Monster{
    
    //声明计算类型的静态属性。spooky ['spu:ki] 幽灵般的，令人害怕的
    class var makeSpookyNoise:String{
        //如果计算类型的属性的 get函数只有返回语句，没有其他，则可以省略 get{} 直接写 return 语句
        // get{
            // brains vt 猛击…的头部, n 脑袋，脑髓
            return "Monster...Brains"
        // }
    }
    
    //其他内容省略
    ...
}
```

* 修改Zombie类

重写关键字 override

被class 修饰的可以重写，static 修饰的不可以被重写

```
class Zombie: Monster {
    
    //重写父类的计算类型的静态属性
    override class var makeSpookyNoise:String{
        return "Zombie...Brains"
    }

    //其他内容省略
    ...

    static let isTerrifying=true
}
```

* main.swift 中调用

```
var zombieNoise=Zombie.makeSpookyNoise
print(zombieNoise)

var monsterNoise=Monster.makeSpookyNoise
print(monsterNoise)

if Zombie.isTerrifying{
    print("Run Away")
}
```

* 输出结果

```
Zombie...Brains
Monster...Brains
Run Away
```

### 7、访问控制——access control

访问控制是围绕着两个重要且相关的概念组织的:模块(module)和源码文件(source file)

模块是分发代码的单位。模块 A 可以被 模块B 通过 import 引入到模块B中。

源码文件表示一个文件，存在于特定的模块中。


#### (1)、Swift访问控制的级别

访问层级|描述|对...可见|能在...中继承
---|---|---|---
open|对于模块内的文件以及引入了该模块的文件可见，并且可以继承|当前模块以及引入方模块|当前模块以及引入方模块|
public|对于模块内的文件以及引入了该模块的文件可见|当前模块以及引入方模块|当前模块
internal(默认)|对于模块内的文件可见|当前模块|当前模块
fileprivate|只对所在源文件可见|所在文件|所在文件
private|只在当前作用域有效|所在作用域|所在作用域

* 通常，类中属性和方法的可见属性不能高于类的可见属性。


为Zombie类新增私有 Bool 属性 isFallPart ,该属性标识 僵尸的身体是否完整，不完整时将无法侵扰小镇。

```
class Zombie: Monster {
    
    //私有访问
    private var isFallPart=false
    
    //重写父类的计算类型的静态属性
    override class var makeSpookyNoise:String{
        return "Zombie...Brains"
    }
    
    //僵尸走路一瘸一拐的。limp [lɪmp] 跛行
    var walkWithLimp=true
    
    override func terrorizeTown() {
        //僵尸肢体完整则能够入侵镇子，然后抓走了10个人。town 是继承自父类的属性
        if !isFallPart{
            town?.changePopulation(by: -10)
        }
        super.terrorizeTown()
    }
}
```

#### (2)、控制读取方法和写入方法的可见性

通常情况下，属性的读写方法具有相同的可见属性，但我们也可以将他们的可见属性改为不同的级别。

格式：`可见属性1 可见属性2(set) var 属性名 = 属性值` 

上述格式中：
* 可见属性1 表示 get 方法的可见属性
* 可见属性2(set) 表示 set 方法的可见属性。

<br>

* 修改Zombie类中 isFallPart 属性的 set/get 可见性

```
class Zombie: Monster {
    
    ////完整写法：声明 get 函数的访问级别为 interval，set 函数的访问级别为 private
    //internal  private(set) var isFallPart=false
    
    //由于Swift中默认可见属性为internalm，所以，可以省略get函数的访问修饰符
    private(set) var isFallPart=false
   
    //默认 set/get的属性均为 internal
    var isFallPart2 = false

    //其他内容省略
    ...
}
```

* main.swift中调用

```java
var zombie2=Zombie()
//由于已经将set私有，所以此处会报错
//zombie2.isFallPart=true
print("Zombie 中 isFallPart的get方法可见性为 external,其值为：\(zombie2.isFallPart)")

//调用isFallPart2 的 set修改其值
zombie2.isFallPart2=true
print("Zombie 中 isFallPart2 的set/get可见性均为 external,其值为：\(zombie2.isFallPart2)")
```

---

## 十七、初始化

CnPeng: 个人理解，所谓初始化，基本等同于Java中的构造。

初始化方法(initializer)能帮助我们在创建实例的同时为存储属性赋值。

### 1、初始化方法语法——init代码块

* 初始化方法使用关键字 init 修饰，不需要 func关键字。
* 初始化方法没有返回值
* 初始化方法的作用是为存储属性赋值
* 结构体、枚举、类 的初始化方法格式一致。

初始化代码的格式如下：

```
struct CustomType {
   init(someValue: SomeType) {
    // 初始化代码。
   }
}
```
上述格式中，init 后面的括号中可以有参数也可以没有参数。


### 2、结构体初始化

结构体既可以有默认初始化方法，也可以有自定义初始化方法。

#### (1)、结构体的默认初始化方法

##### A: 空初始化方法

全称 empty initializer , 只没有参数的初始化方法

如：

```
var myTown = Town()
```

上述代码中，在创建Town对象时，（）中没有接受参数，所以称为空初始化方法。

##### B: 成员初始化方法

全称 memberwise initializer。需要为类的每一个存储属性赋值。

在上一章中，我们定义了Town 类，其中的存储属性有：population 和 numOfStopLights, 所以，如果使用成员初始化方法，就需要给这两个属性指定值。如：

* main.swift 调用成员初始化方法
```
var town=Town(population:5000,numOfStopLights:15)
town.printDescription()
```

#### (2)、结构体的自定义初始化方法

##### A: 自定义初始化方法

* 有了自定义初始化方法之后，默认初始化方法将不再生效。
* 如果init参数名称与成员属性名称一致，在init函数中为成员属性赋值时，需要前缀 self
* 允许在init初始化方法中为常量赋值——常量值的不可变仅是只在初始化完成之后不可变

示例如下

* 为Town结构体自定义初始化方法

```
struct Town{
    
    let region:String
    var numOfStopLights:Int
    var population:Int{
        //也可以这么写。不加小括号时，默认使用oldValue表示旧值
        didSet{
             print("didSet监测population的旧值为：\(oldValue), 新值为\(population)")
        }
    }
    
    init(region:String,population:Int,lights:Int) {
        //因为参数名和成员属性名称一致，所以前缀self——同Java中的this
         self.population=population
        //虽然region声明为常量，但编译器允许在初始化的时候为常量赋值，从而保证初始化完成后该常量有值
        self.region=region
        numOfStopLights=lights
    }

    //其他内容省略
    ...
}
```

* main.swift 中调用自定义初始化方法

```
var town=Town(region:"南方",population:5000,lights:15)
town.printDescription()
```

##### B: 委托初始化

 **同一个类中，初始化方法A中调用了初始化方法B**，那么这个过程就被称为委托初始化(initializer delegation)，通常用来提供多种创建实例的途径

>CnPeng Android体系中，自定义View的构造函数中经常使用这种委托初始化方式。

对于值类型(也就是枚举和结构体)来说，委托初始化相对比较直观。因为值类型不支持继承，所以委托初始化只涉及调用所在类型的其他初始化方法。对于类来说，委托初始化则相对复杂。

示例代码：

* 委托初始化

```
import Foundation
struct Town{
    
    let region:String
    var numOfStopLights:Int
    var population:Int{
        //也可以这么写。不加小括号时，默认使用oldValue表示旧值
        didSet{
             print("didSet监测population的旧值为：\(oldValue), 新值为\(population)")
        }
    }
    
    init(region:String,population:Int,lights:Int) {
        //因为参数名和成员属性名称一致，所以前缀self——同Java中的this
         self.population=population
        //虽然region声明为常量，但编译器允许在初始化的时候为常量赋值，从而保证初始化完成后该常量有值
        self.region=region
        numOfStopLights=lights
    }
    
    init(region:String,population:Int){
        self.init(region:region, population:population,lights:8)
    }

    //其他内容省略
    ...
}
```

* main.swift中调用

```
var town=Town(region:"南方",population:5000)
town.printDescription()
```

### 3、类初始化

类初始化语法与值类型初始化方法类似。

但是类的初始化语法中 新增了 指定(designated)初始化方法 和 便捷(convenience)初始化方法的概念。类的初始化方法必是其中之一

* 指定初始化方法负责确保初始化完成前所有属性都有值
* 便捷初始化方法 是 指定初始化方法的补充，通过调用所在类的指定初始化方法来实现。 


#### (1)、类的默认初始化方法

类的仅有一种默认初始化方法——无参的空初始化方法

（上一节中介绍过，结构体的默认初始化由两种：无参的空初始化、有参的成员初始化）

#### (2)、初始化和类继承



##### A: 初始化方法自动继承

如果父类中通过 init 自定义了初始化方法，并且子类继承了这个初始化方法，那么子类也将不能使用默认的空初始化方法。这种特性被称为：初始化方法自动继承 (automatic initializer inheritance)。

通常情况下，子类不会继承父类的初始化方法，但，如果  **子类的所有成员属性都有默认值**, 并且满足下列场景之一就会继承：
* 子类没有定义 指定初始化方法，会继承父类的 指定初始化方法
* 子类实现了父类的所有 指定初始化方法（无论显式还是隐式实现），就会继承父类的所有 便捷初始化方法

初始化方法自动继承的示例：


* 修改Monster类，增加指定初始化方法. 子类 Zombie 不做修改

```
class Monster{

    var town:Town?
    var name="Monster"
    
    //修改monster类，增加指定初始化方法
    init(town:Town?,monsterName:String) {
        self.town=town
        name=monsterName
    }

    //其他内容省略    
    ...
}    
```

* main.swift中调用

```
var town2=Town(region:"南方",population:5000)
town2.printDescription()

//初始化方法自动继承——zombie的初始化方法继承自父类 Monster
var zombie3=Zombie(town:town2,monsterName:"zombie_僵尸")
zombie3.terrorizeTown()
```

##### B: 类的指定初始化方法

指定初始化方法是类的主要初始化方法。其特点为：

* 指定初始化方法的部分作用是：确保类属性在初始化完成前都有值。
* 如果类有父类，那么子类的指定初始化方法必须调用父类的指定初始化方法。

 **指定初始化方法在 init 前不需要任何修饰符，而便捷初始化方法则需要 convenience 修饰，这是二者的重要区别。** 


示例代码：

* 为Zombie增加指定初始化方法

```java
class Zombie: Monster {
    
    //僵尸走路一瘸一拐的。limp [lɪmp] 跛行
    var walkWithLimp:Bool
    
    //由于Swift中默认可见属性为internalm，所以，可以省略get函数的访问修饰符
    private(set) var isFallPart:Bool
    
    //指定初始化方法
    init(isLimp:Bool,isFallPart:Bool,town: Town?, monsterName: String) {
        walkWithLimp=isLimp
        self.isFallPart=isFallPart
        
        //末尾需要调用父类的指定初始化方法
        super.init(town:town, monsterName: monsterName)
    }
    
    //其他内容省略
    ...
}
```

* main.swift中调用

```
var town2=Town(region:"南方",population:5000)
town2.printDescription()

//初始化方法自动继承——zombie的初始化方法继承自父类 Monster
var zombie3=Zombie(isLimp:true,isFallPart:false,town:town2,monsterName:"zombie_僵尸")
zombie3.terrorizeTown()
```


##### C: 类的便捷初始化方法

* 便捷初始化方法不需要确保类的所有属性都有值，而是做完自己的工作后把信息传递给其它的 便捷/指定初始化方法。
* 所有的便捷初始化方法都要调用所在类的其它初始化方法。而且必须会调用指定初始化方法。
* 便捷初始化方法使用 `convenience` 修饰。（ [kən'viːnɪəns] 便捷，便利，厕所）

一个类的便捷初始化方法和指定初始化方法会形成一条路径，类的存储属性通过这条路径得到初始值。

* 为Zombie类定义便捷初始化方法

```
class Zombie: Monster {
    
    //僵尸走路一瘸一拐的。limp [lɪmp] 跛行
    var walkWithLimp:Bool
    
    //由于Swift中默认可见属性为internalm，所以，可以省略get函数的访问修饰符
    private(set) var isFallPart:Bool
    
    //指定初始化方法
    init(isLimp:Bool,isFallPart:Bool,town: Town?, monsterName: String) {
        walkWithLimp=isLimp
        self.isFallPart=isFallPart
        super.init(town:town, monsterName: monsterName)
    }
    
    //便捷初始化方法
    convenience init(isLimp:Bool,isFallPart:Bool){
        //不能省略self, 而且必须是第一条语句
        self.init(isLimp:isLimp,isFallPart:isFallPart,town:nil,monsterName:"僵尸")
        
        if isLimp {
            print("嘿，这个僵尸走路一瘸一拐的")
        }
    }

    //其他省略
    ...
}
```

* main.swift中调用

```
var town2=Town(region:"南方",population:5000)
var zombie4=Zombie(isLimp: true, isFallPart: false)
```
* 输出结果：

```
嘿，这个僵尸走路一瘸一拐的
```

#### (3)、类的必须初始化方法

父类可以要求子类提供特殊的初始化方法。

* 使用关键字 required 修饰父类的 init 方法
* 子类在实现父类必须的init方法时，也是使用 required 修饰，并且不需要 override  

在子类中实现 required 修饰的初始化函数时，必须先初始化当前类的 存储属性，然后才能调用而且必须调用父类中 required 修饰的初始化函数。否则，编译器会报错。这个特点刚好符合 指定初始化函数 的特点，所以，子类实现的 required 初始化函数也是 指定初始化函数。

示例：

* Monster类中定义子类必须实现的初始化函数

```
class Monster{
    
    var town:Town?
    var name="Monster"
    
   //指定子类必须实现的初始化函数
   required init(town:Town?,monsterName:String) {
        self.town=town
        name=monsterName
   }
    
   //其他内容省略
   ...
}
```

* Zombie类中实现父类要求实现的初始化函数

```
class Zombie: Monster {
    
    //僵尸走路一瘸一拐的。limp [lɪmp] 跛行
    var walkWithLimp:Bool
    
    //由于Swift中默认可见属性为internalm，所以，可以省略get函数的访问修饰符
    private(set) var isFallPart:Bool
    
    
    //实现父类必须的init函数，这也是当前类的一个 指定初始化函数
    required init(town: Town?, monsterName: String) {
        //必须初始化存储属性，而且必须在super 之前，否则，报错
        walkWithLimp=true
        isFallPart=false
        
        print("调用父类要求子类必须实现的init函数初始化 Zombie ")
        
        //最后才调用父类中用 required 修饰的init函数
        super.init(town: town, monsterName: monsterName)
    }
    
    //其他内容省略 
    ...
}
```

* main.swift 中调用

```
var town2=Town(region:"南方",population:5000)
var zombie5=Zombie(town: town2, monsterName: "僵尸")
```


#### (4)、反初始化

反初始化(deinitialization)是指 类的实例没用之后将其清理出内存的过程。

* 只有引用类型可以反初始化，值类型不行。
* 将实例从内存中清除之前，必然会调用 返初始化方法
* 一个类只能有一个反初始化方法
* 反初始化方法使用 deinit 修饰

> CnPeng：感觉这个反初始化方法有些类似于 Android 中 Activtiy/Framgent 的 onDestory 函数


示例：

* 在 Zombie类中定义反初始化方法

```
class Zombie: Monster {
    
    deinit {
        print("销毁类的实例时。deinit修饰的反初始化函数被调用了。这个被销毁的Zombie name为\(name)")
    }

    //其他内容省略
    ...
}
```

* main.swift中调用

```
var town2=Town(region:"南方",population:5000)
//注意：后面要通过给zombie5赋值nil将其销毁，所以，声明时必须声明为可空
var zombie5:Zombie?=Zombie(town: town2, monsterName: "僵尸")
zombie5=nil
```

### 4、可失败的初始化方法

可失败的初始化方法(failable initalizer)，也就是说初始化方法可能会返回空——nil

导致初始化方法失败的因素有许多, 比如：传递的参数非法；依赖的外部资源不存在等

* 可失败的初始化方法使用 `init?` 修饰 


示例代码：

* Town中定义可失败的初始化方法

```java
struct Town{
    
    let region:String
    var numOfStopLights:Int
    var population:Int{
        //也可以这么写。不加小括号时，默认使用oldValue表示旧值
        didSet{
             print("didSet监测population的旧值为：\(oldValue), 新值为\(population)")
        }
    }
    
    //可失败的初始化方法
    init?(region:String,population:Int,lights:Int) {
        guard population>0 else{
            print("初始化失败，镇子人口数量必须大于0")
            return nil
        }
        
        self.population=population
        self.region=region
        numOfStopLights=lights
    }
    
    //由于三个参数的指定初始化方法是可失败的，所以，此处也必须使用 init?
    init?(region:String,population:Int){
        self.init(region:region, population:population,lights:8)
    }
   
    //其他内容省略
    ...
}
```

* main.swift 中调用

```
var town3=Town(region: "South", population: 0)
```

* 输出结果：

```
初始化失败，镇子人口数量必须大于0
```

### 5、初始化方法参数

对应原书 "深入学习：初始化方法参数" 一节


该节内容概括为一句话就是： **初始化方法中的参数，也可以同时定义外部参数名、内部参数名。** 


---

## 十八、值类型与引用类型

### 1、值语义

* 结构体、枚举都是值类型。
* 把值类型的数据赋值给某个实例或者作为函数参数传递时，实际对方接受的是副本, 而不是值类型数据本身。而副本的改变不会影响原数据。
* Swift的基本类型(Array, Dictionary,String,Int等)都是用结构体实现的，所以，也都是值类型。

> CnPeng: 值类型数据传递或赋值给某个实例时，是把值给了实例，内存中需要划分了新的存储空间来存储这个副本。

### 2、引用语义

> CnPeng: 引用类型数据A 赋值给实例B时，内存中并没有重新为B分配存储空间，而仅是把A的引用给了B

> CnPeng: 值类型和引用类型，对应Java体系中的基本数据类型和引用数据类型。这两种类型的根本区别在于内存中的存储方式。

### 3、值类型常量和引用类型常量

通常情况下：

* 引用类型的常量，可以改变属性值，但不能改变引用值
* 值类型的常量初始化之后
    * .如果其属性也是值类型，那么啥都不能变
    * .如果其属性为引用类型，那么该属性值的属性可以被改变（代码参考下一节：配合使用值类型和引用类型）

```java
//定义结构体——Pantheon 希腊神庙
struct Pantheon{
    //定义变量，表示首神
    var chifGod:GreekGod
}

//定义类——神
class GreekGod{
    var name:String
    
    init(godName:String){
        name=godName
    }
}

let chifGod=GreekGod(godName: "宙斯")
let pantheon=Pantheon(chifGod:chifGod)

//结论：引用类型的常量，可以改变属性值，但不能改变引用值
chifGod.name="维纳斯"
//Cannot assign to property: 'pantheon' is a 'let' constant
//chifGod=GreekGod(godName: "丘比特")

//结论：值类型的常量初始化之后，啥都不能变！
//Cannot assign to property: 'pantheon' is a 'let' constant
//pantheon.chifGod=chifGod
```

### 4、配合使用值类型和引用类型

实际使用中，可能会出现值类型数据中包含引用类型属性的情况。

此时：即便值类型数据被声明为常量，该属性值的属性依旧可以被改变。

```java
//定义结构体——Pantheon 希腊神庙
struct Pantheon{
    //定义变量，表示首神
    var chifGod:GreekGod
}

//定义类——神
class GreekGod{
    var name:String
    
    init(godName:String){
        name=godName
    }
}

let chifGod=GreekGod(godName: "宙斯")
let pantheon=Pantheon(chifGod:chifGod)

chifGod.name="维纳斯"

//输出 "维纳斯" 。—— 值类型的属性为引用类型时，该属性值的属性可以被修改
pantheon.chifGod.name
```

### 5、复制

Swift在语言层面仅提供 浅复制（shallow copy）；没有提供 深复制（deep copy），如果需要深复制，需要自己编写。

浅复制是指复制实例的引用，而不是创建一个新的实例或实例副本。

深复制是指完全创建一个新的实例或实例副本。

```
//定义结构体——Pantheon 希腊神庙
struct Pantheon{
    //定义变量，表示首神
    var chifGod:GreekGod
}

//定义类——神
class GreekGod{
    var name:String
    
    init(godName:String){
        name=godName
    }
}

let chifGod=GreekGod(godName: "宙斯")
let pantheon=Pantheon(chifGod:chifGod)

let pantheon2=pantheon
pantheon2.chifGod.name="玉皇大帝"

//patheon 和 patheon2 中的 chifGod 是同一个实例，所以改变name时，会同时生效——这就是浅复制
pantheon2.chifGod.name
pantheon.chifGod.name
```

### 6、相等与同一

相等(equality)是指两个实例的字面值一致，比如具有同样文本的两个String实例。`值相等`

同一(identity) 则是指两个变量或常量是否引用内存中的同一个实例。`引用相等`

* 值相等使用 `==` 比较
* 引用相等使用 `===` 比较
* 值类型只能进行值相等比较
* 引用类型只能进行引用相等比较。如果需要比较引用类型的值相等，需要借助协议 Equatable 
    * . 引用相等则值必然相等
    * . 值相等不一定引用相等


### 7、该使用结构体还是类？

* 如果类型需要传值，那就用结构体。这么做会确保赋值或传递到函数参数中时类型被复制。
* 如果类型不支持子类继承，那就用结构体。结构体不支持继承，所以不能有子类。
* 如果类型要表达的行为相对比较直观，而且包含一些简单值，那么考虑优先用结构体实现。有需要的话，之后可以随时把结构体改成类。
* 其它所有情况都用类。

Swift标准库中的String、Array和Dictionary类型都是用结构体实现的。

总之一句话，建议优先选用结构体——因为，改变实例副本中的值时原实例中的值不会被改变。

### 8、深入学习：写时复制

由于值类型传递时会被复制，那么是否会产生一大堆没用的副本呢? 实际上，这取决于数据及其用法。在实践中，Swift标准库中的值类型实现了被称为 **写时复制** 的机制。

写时复制(copy on write，COW)是指对值类型的底层存储的隐式共享。

这种优化能让某个 **值类型的多个实例共享同一个底层存储** ，也就是说每个实例自身并不持有数据的副本; 反之，每个实例会维护自身对存储的引用。如果某个实例 **需要修改或写入存储，那么这个实例就会产生一个自己的副本** 。这意味着值类型能避免创建多个的数据副本。


---

> 按照原书结构描述，第19-25 章为 Swift 高阶编程

## 十九、协议

> CnPeng ： **注意：文中所说的 “类型”， 是 结构体、枚举、类的统称，并非单指 类** 

在第16章中，我们学习了使用访问控制来隐藏信息。隐藏信息是封装(encapsulation)的一种形式，可以在设计软件时达到其中一部分的变化不影响其它部分的目的。

Swift还提供另一种形式的封装:协议。

协议可以让你不用知道类型本身的信息，就能指定并利用类型的接口(interface)。 接口是类型提供的一组属性和方法。

协议这个概念比我们目前为止学过的许多主题都抽象。要理解协议及其工作原理，我们将创建一个函数，把数据格式化输出到一个表格中，然后用协议让这个函数能灵活处理不同的数据源(data source)。

Mac和iOS应用通常把数据的展现和提供数据的源分离。这种分离是一个很有用的模式，让苹果提供处理展现的类，而把决定数据如何存储的工作留给开发者。


### 1、格式化表格数据

#### (1)、打印基本的表格结构

定义一个二维数组，然后以其中每个一维数组为一行，将一维数组中的元素打印出来

```
//定义一个二维数组，然后以每个一维数组为一行，将一维数组中的元素打印出来
func printTable(_ data:[[String]]){
    
    for row in data {
        var out="|"
        
        for item in row {
            out += "\(item)|"
        }
        print(out)
    }
}

//定义二维数组
let data = [
    ["张三","30","5"],
    ["李四","40","10"],
    ["王五","50","15"]
]

printTable(data)
```

输出结果：

```
|张三|30|5|
|李四|40|10|
|王五|50|15|
```

#### (2)、为表格添加头信息

```java
//定义一个二维数组，然后以每个一维数组为一行，将一维数组中的元素打印出来
func printTable(_ data:[[String]], columnLables:[String]){
    
    //打印头信息
    var firstRow="|"
    for labels in columnLables {
        firstRow += "\(labels)|"
    }
    print(firstRow)
    
    //打印表格主体信息
    for row in data {
        var out="|"
        
        for item in row {
            out += "\(item)|"
        }
        print(out)
    }
}

//定义二维数组
let data = [
    ["张三","30","5"],
    ["李四","40","10"],
    ["王五","50","15"]
]

//定义表格的头信息——第一行的内容
let rolumnLables=["姓名","年龄","工作年限"]

printTable(data,columnLables: rolumnLables)

```

输出结果：

```
|姓名|年龄|工作年限|
|张三|30|5|
|李四|40|10|
|王五|50|15|
```

#### (3)、对齐各列

```
//定义一个二维数组，然后以每个一维数组为一行，将一维数组中的元素打印出来
func printTable(_ data:[[String]], columnLables:[String]){
    //3、记录列宽——假设第一行标题为最宽，以字符长度为宽度
    var columnWidths=[Int]()
    
    //2、打印头信息
    var firstRow="|"
    for labels in columnLables {
        firstRow += "\(labels)|"
        columnWidths.append(labels.count)
    }
    print(firstRow)
    
    //1、打印表格主体信息
    for row in data {
        var out="|"
        
        //数组的 enumerated() 函数返回 索引和条目的元素
        for (j,item) in row.enumerated() {
            var itemLength=item.count
            var columnWidth=columnWidths[j]>itemLength ? columnWidths[j] : itemLength
            columnWidths[j]=columnWidth
            
            //repeatElement 作用是获取重复元素的数组，joined 的作用是将数组或集合中的元素组合成一个字符串
            let paddingBlank=repeatElement(" ", count: columnWidth-itemLength).joined()
            
            out += "\(paddingBlank)\(item)|"
        }
        print(out)
    }
}

//定义二维数组
let data = [
    ["张三","30","5"],
    ["李四","40","10"],
    ["王二","50","15"]
]

//定义表格的头信息——第一行的内容
let rolumnLables=["姓名","年龄","工作年限"]

printTable(data,columnLables: rolumnLables)
```

输出结果：

```
|姓名|年龄|工作年限|
|张三|30|   5|
|李四|40|  10|
|王二|50|  15|
```

#### (4)、添加数据模型对象

在上一小节的代码中，我们实现了打印对齐的表格。但是，我们必须单独的提供一个二维数组，再提供一个头信息数组。这样显然不够灵活。

所以，我们考虑使用数据模型的形式在组织这两个数据：

```
struct Person{
    var name:String
    var age:Int
    var yearOfExperience:Int
}

struct Department{
    var departName:String
    var empolyArr=[Person]()
    
    init(name:String) {
        departName=name
    }
    
   mutating func addEmpoly(person:Person){
        self.empolyArr.append(person)
    }
}

var deparentMent=Department(name:"研发组")
deparentMent.addEmpoly(person:Person(name:"张三",age:30,yearOfExperience:5))
deparentMent.addEmpoly(person:Person(name:"李四",age:40,yearOfExperience:10))
deparentMent.addEmpoly(person:Person(name:"王五",age:50,yearOfExperience:15))
```

上述代码中我们构建了两个结构体，一个Person,一个Deparentment。Deparentment中包含一个Person数组, Person 又包含三个属性，这样，我们就实现了将上一小节中二维数组转为数据模型的逻辑。

此时，如果我们想用 printTable() 将 Department 中的成员信息都打印出来，可以修改 printTable()函数的入参类型, 然后再修改函数内部的实现。

但是，我们的终结目标是封装一个通用的函数，不关心入参到底是什么类型，只要入参满足一定的格式就可以将信息打印成表格。此时，我们就需要使用下一节中介绍的 协议 了。

### 2、协议

协议(protocol)能让你定义类型需要满足的接口。（CnPeng: 在理解上协议和接口类似，但 协义 != 接口）

满足某个协议的类型被称为 符合/匹配(conform) 这个协议。


#### (1)、定义协议

在 printTable() 函数中，我们要想打印一个表格，必须知道 表头信息、行数、列数、条目内容，所以，我们可以定义下列协议：

```
//tabular ['tæbjʊlə] 扁平的，表格的
protocol TabularDataSource{
    //总行数。get 表示需要读取数据
    var countOfRow:Int{get}
    //总列数。get set 表示需要读数据
    var countOfColumn:Int{get}
    //获取第X列的表头信息
    func getColumnLabel(column:Int)->String
    //获取x行y列的条目数据
    func getItemContent(row:Int,column:Int)->String
}
```

当某个类型数据 符合/匹配 上述协议时，必须拥有上述两个属性和上述两个函数。

协议中通用定义 类型数据 最基本的属性和方法（CnPeng 同接口或抽象类很类似）.

#### (2)、符合/匹配协议

我们定义好协议之后，接下来要做的事情是，让 Department 符合/匹配该协议，并实现相应的属性和函数。

匹配协议的写法和继承一样，也是当前类型后面追加 `: 协议名`

```
//符合/匹配协议的结构体
struct Department:TabularDataSource{
    
    var departName:String
    var employArr=[Person]()
    
    init(name:String) {
        departName=name
    }
    
    mutating func addEmploy(person:Person){
        self.employArr.append(person)
    }
    
    //以下内容为 符合/匹配协议之后需要实现的内容
    var countOfRow: Int{
        //此处是计算属性
        get{
            return employArr.count
        }
        set{
            countOfRow = newValue
        }
    }
    var countOfColumn: Int{
        return 3
    }
    
    func getColumnLabel(column: Int) -> String {
        switch column {
        case 0: return "姓名"
        case 1: return "年龄"
        case 2 : return "工作年限"
        default : fatalError("不存在的列——列索引越界")
        }
    }
    
    //根据row取person对象，根据column 取person的不同属性
    func getItemContent(row: Int, column: Int) -> String {
        let person=employArr[row]
        switch column {
        case 0: return person.name
        case 1: return String(person.age)
        case 2: return String(person.yearOfExperience)
        default: fatalError("单元格不存在")
        }
    }
}
```

#### (3)、修改 printTable()的参数类型

协议可以作为类型使用。变量、参数、返回值都可以使用协议类型。

* 修改 printTable() 接收 TabularDataSource 类型数据

```java
func printTable(_ dataSource:TabularDataSource){
    //3、记录列宽——假设第一行标题为最宽，以字符长度为宽度
    var columnWidths=[Int]()
    
    //2、打印头信息
    var firstRow="|"
    for i in 0..<dataSource.countOfColumn {
        let label=dataSource.getColumnLabel(column: i)
        firstRow += "\(label)|"
        columnWidths.append(label.count)
    }
    print(firstRow)
    
    //1、打印表格主体信息
    for j in 0..<dataSource.countOfRow {
        var out="|"
        
        for k in 0..<dataSource.countOfColumn{
            let cellContent=dataSource.getItemContent(row: j, column: k)
            
            var itemLength=cellContent.count
            var columnWidth=columnWidths[k]>itemLength ? columnWidths[k] : itemLength
            columnWidths[k]=columnWidth
            
            //repeatElement 作用是获取重复元素的数组，joined 的作用是将数组或集合中的元素组合成一个字符串
            let paddingBlank=repeatElement(" ", count: columnWidth-itemLength).joined()
            
            out += "\(paddingBlank)\(cellContent)|"
        }
        print(out)
    }
}
```

* 完整代码：

```java
//tabular ['tæbjʊlə] 扁平的，表格的
protocol TabularDataSource{
    //总行数
    var countOfRow:Int{get}
    //总列数
    var countOfColumn:Int{get}
    //获取第X列的表头信息
    func getColumnLabel(column:Int)->String
    //获取x行y列的条目数据
    func getItemContent(row:Int,column:Int)->String
}

struct Person{
    var name:String
    var age:Int
    var yearOfExperience:Int
}

//符合/匹配协议的结构体
struct Department:TabularDataSource{
    
    var departName:String
    var employArr=[Person]()
    
    init(name:String) {
        departName=name
    }
    
    mutating func addEmploy(person:Person){
        self.employArr.append(person)
    }
    
    //以下内容为 符合/匹配协议之后需要实现的内容
    var countOfRow: Int{
        //此处是计算属性
        get{
            return employArr.count
        }
        set{
            countOfRow = newValue
        }
    }
    var countOfColumn: Int{
        return 3
    }
    
    func getColumnLabel(column: Int) -> String {
        switch column {
        case 0: return "姓名"
        case 1: return "年龄"
        case 2 : return "工作年限"
        default : fatalError("不存在的列——列索引越界")
        }
    }
    
    //根据row取person对象，根据column 取person的不同属性
    func getItemContent(row: Int, column: Int) -> String {
        let person=employArr[row]
        switch column {
        case 0: return person.name
        case 1: return String(person.age)
        case 2: return String(person.yearOfExperience)
        default: fatalError("单元格不存在")
        }
    }
}

var deparentMent=Department(name:"研发组")
deparentMent.addEmploy(person:Person(name:"张三",age:30,yearOfExperience:5))
deparentMent.addEmploy(person:Person(name:"李四",age:40,yearOfExperience:10))
deparentMent.addEmploy(person:Person(name:"王五",age:50,yearOfExperience:15))

printTable(deparentMent)

func printTable(_ dataSource:TabularDataSource){
    //3、记录列宽——假设第一行标题为最宽，以字符长度为宽度
    var columnWidths=[Int]()
    
    //2、打印头信息
    var firstRow="|"
    for i in 0..<dataSource.countOfColumn {
        let label=dataSource.getColumnLabel(column: i)
        firstRow += "\(label)|"
        columnWidths.append(label.count)
    }
    print(firstRow)
    
    //1、打印表格主体信息
    for j in 0..<dataSource.countOfRow {
        var out="|"
        
        for k in 0..<dataSource.countOfColumn{
            let cellContent=dataSource.getItemContent(row: j, column: k)
            
            var itemLength=cellContent.count
            var columnWidth=columnWidths[k]>itemLength ? columnWidths[k] : itemLength
            columnWidths[k]=columnWidth
            
            //repeatElement 作用是获取重复元素的数组，joined 的作用是将数组或集合中的元素组合成一个字符串
            let paddingBlank=repeatElement(" ", count: columnWidth-itemLength).joined()
            
            out += "\(paddingBlank)\(cellContent)|"
        }
        print(out)
    }
}
```

* 运行结果

```
|姓名|年龄|工作年限|
|张三|30|   5|
|李四|40|  10|
|王五|50|  15|
```

### 3、符合协议

> 此处的 “类型” 是对 结构体、枚举、类的统称

* 所有类型都可以符合/匹配协议。结构体、枚举、类都可以。
* 结构体和枚举声明符合/匹配协议时，格式一致：`枚举/结构体名称 : 协议名 `
* 一个类型可以符合/匹配多个协议


#### (1)、符合/匹配多个协议

CustomStringConvertible 是 Swift 预置的一个协议。

当某个类型希望控制自己的实例被如何转换为字符串时就可以实现这个协议。比如 print() 函数会在判断如何显示要打印的值时检查参数是否符合 CustomStringConvertible 协议。

CustomStringConvertible 协议只有一个要求: 类型必须有一个返回String的可读属性 description。

* 符合/匹配多个协议时，协议之间使用逗号间隔

示例代码：

* 临时修改 Department 同时符合 CustomStringConvertible 协议

```java
struct Department:TabularDataSource,CustomStringConvertible{
    
    ...

    var description: String {
        return departName
    }
    
    ...   
}
```

* 构建 Department 并直接打印

```
print(deparentMent)
```

* 输出结果：

```
研发组
```

#### (2)、让类符合/匹配协议

类在 符合/匹配 协议时有两种情况。

##### A: 类没有父类

与结构体的格式一致。

```
class ClassName: ProtocolOne, ProtocolTwo {
    // ...
}
```

##### B: 有父类

则冒号后面必须先声明父类，然后声明 符合/匹配 的协议

```java
class ClassName: SuperClass, ProtocolOne, ProtocolTwo {
    // ...
}
```

### 4、协议继承

Swift 支持协议继承(protocol inheritance）。 

> inheritance  [ɪn'hɛrɪtəns] 继承，遗传 

#### (1)、协议继承

**当某个类型 符合/匹配的协议是一个子协议时，该类型需要实现 子协议以及父协议 的全部属性和方法。**

在下面的示例代码中，TabularDataSource 协议继承自 CustomStringConvertiable 协议，而 Department 结构体又 符合/匹配 TabularDataSource，由于 TabularDataSource 是一个子协议，所以，Department 中 需要实现三个属性两个函数。

完整示例：

```
//tabular ['tæbjʊlə] 扁平的，表格的
protocol TabularDataSource:CustomStringConvertible{
    //总行数
    var countOfRow:Int{get}
    //总列数
    var countOfColumn:Int{get}
    //获取第X列的表头信息
    func getColumnLabel(column:Int)->String
    //获取x行y列的条目数据
    func getItemContent(row:Int,column:Int)->String
}

struct Person{
    var name:String
    var age:Int
    var yearOfExperience:Int
}

//符合/匹配协议的结构体
struct Department:TabularDataSource{
    
    var departName:String
    var employArr=[Person]()
    
    init(name:String) {
        departName=name
    }
    
    mutating func addEmploy(person:Person){
        self.employArr.append(person)
    }
    
    //以下内容为 符合/匹配协议之后需要实现的内容
    
    var description: String {
        return departName
    }
    
    var countOfRow: Int{
        //此处是计算属性
        get{
            return employArr.count
        }
        set{
            countOfRow = newValue
        }
    }
    var countOfColumn: Int{
        return 3
    }
    
    func getColumnLabel(column: Int) -> String {
        switch column {
        case 0: return "姓名"
        case 1: return "年龄"
        case 2 : return "工作年限"
        default : fatalError("不存在的列——列索引越界")
        }
    }
    
    //根据row取person对象，根据column 取person的不同属性
    func getItemContent(row: Int, column: Int) -> String {
        let person=employArr[row]
        switch column {
        case 0: return person.name
        case 1: return String(person.age)
        case 2: return String(person.yearOfExperience)
        default: fatalError("单元格不存在")
        }
    }
}

var deparentMent=Department(name:"研发组")
deparentMent.addEmploy(person:Person(name:"张三",age:30,yearOfExperience:5))
deparentMent.addEmploy(person:Person(name:"李四",age:40,yearOfExperience:10))
deparentMent.addEmploy(person:Person(name:"王五",age:50,yearOfExperience:15))

printTable(deparentMent)

func printTable(_ dataSource:TabularDataSource){
    //4、由于TabularDataSource 协议继承了CustomStringConvertiable，所以拥有 description属性
    print(dataSource.description)
    
    //3、记录列宽——假设第一行标题为最宽，以字符长度为宽度
    var columnWidths=[Int]()
    
    //2、打印头信息
    var firstRow="|"
    for i in 0..<dataSource.countOfColumn {
        let label=dataSource.getColumnLabel(column: i)
        firstRow += "\(label)|"
        columnWidths.append(label.count)
    }
    print(firstRow)
    
    //1、打印表格主体信息
    for j in 0..<dataSource.countOfRow {
        var out="|"
        
        for k in 0..<dataSource.countOfColumn{
            let cellContent=dataSource.getItemContent(row: j, column: k)
            
            var itemLength=cellContent.count
            var columnWidth=columnWidths[k]>itemLength ? columnWidths[k] : itemLength
            columnWidths[k]=columnWidth
            
            //repeatElement 作用是获取重复元素的数组，joined 的作用是将数组或集合中的元素组合成一个字符串
            let paddingBlank=repeatElement(" ", count: columnWidth-itemLength).joined()
            
            out += "\(paddingBlank)\(cellContent)|"
        }
        print(out)
    }
}
```
运行结果:

```
研发组
|姓名|年龄|工作年限|
|张三|30|   5|
|李四|40|  10|
|王五|50|  15|
```

#### (2)、协议的多继承

协议支持多继承，格式如下：

```
protocol MyProtocol: MyOtherProtocol, CustomStringConvertible {
   ...
}
```

### 5、协议组合

协议组合的格式为 `协议A & 协议B & 协议C` 

中缀符 `&` 的作用是：告知编译器，类型需要同时符合这些协议

> CnPeng 在早期版本中，协议组合格式为 protocol<协议A, 协议B, 协议C>, 但现在该格式已经被废弃，如果继续使用，会报错！！


#### (1)、使用协议组合

下列代码中没有再让 TabularDataSource协议 继承 CustomStringConvertiable 协议。而让 Department 同时 符合/匹配这两个协议

```
import Cocoa

//tabular ['tæbjʊlə] 扁平的，表格的
protocol TabularDataSource{
    //总行数
    var countOfRow:Int{get}
    //总列数
    var countOfColumn:Int{get}
    //获取第X列的表头信息
    func getColumnLabel(column:Int)->String
    //获取x行y列的条目数据
    func getItemContent(row:Int,column:Int)->String
}

struct Person{
    var name:String
    var age:Int
    var yearOfExperience:Int
}

//符合/匹配协议的结构体
struct Department:TabularDataSource,CustomStringConvertible{
    
    var departName:String
    var employArr=[Person]()
    
    init(name:String) {
        departName=name
    }
    
    mutating func addEmploy(person:Person){
        self.employArr.append(person)
    }
    
    //以下内容为 符合/匹配协议之后需要实现的内容
    
    var description: String {
        return departName
    }
    
    var countOfRow: Int{
        //此处是计算属性
        get{
            return employArr.count
        }
        set{
            countOfRow = newValue
        }
    }
    var countOfColumn: Int{
        return 3
    }
    
    func getColumnLabel(column: Int) -> String {
        switch column {
        case 0: return "姓名"
        case 1: return "年龄"
        case 2 : return "工作年限"
        default : fatalError("不存在的列——列索引越界")
        }
    }
    
    //根据row取person对象，根据column 取person的不同属性
    func getItemContent(row: Int, column: Int) -> String {
        let person=employArr[row]
        switch column {
        case 0: return person.name
        case 1: return String(person.age)
        case 2: return String(person.yearOfExperience)
        default: fatalError("单元格不存在")
        }
    }
}

var deparentMent=Department(name:"研发组")
deparentMent.addEmploy(person:Person(name:"张三",age:30,yearOfExperience:5))
deparentMent.addEmploy(person:Person(name:"李四",age:40,yearOfExperience:10))
deparentMent.addEmploy(person:Person(name:"王五",age:50,yearOfExperience:15))

printTable(deparentMent)

func printTable(_ dataSource: TabularDataSource & CustomStringConvertible ){
    //4、由于TabularDataSource 协议继承了CustomStringConvertiable，所以拥有 description属性
    print(dataSource.description)
    
    //3、记录列宽——假设第一行标题为最宽，以字符长度为宽度
    var columnWidths=[Int]()
    
    //2、打印头信息
    var firstRow="|"
    for i in 0..<dataSource.countOfColumn {
        let label=dataSource.getColumnLabel(column: i)
        firstRow += "\(label)|"
        columnWidths.append(label.count)
    }
    print(firstRow)
    
    //1、打印表格主体信息
    for j in 0..<dataSource.countOfRow {
        var out="|"
        
        for k in 0..<dataSource.countOfColumn{
            let cellContent=dataSource.getItemContent(row: j, column: k)
            
            var itemLength=cellContent.count
            var columnWidth=columnWidths[k]>itemLength ? columnWidths[k] : itemLength
            columnWidths[k]=columnWidth
            
            //repeatElement 作用是获取重复元素的数组，joined 的作用是将数组或集合中的元素组合成一个字符串
            let paddingBlank=repeatElement(" ", count: columnWidth-itemLength).joined()
            
            out += "\(paddingBlank)\(cellContent)|"
        }
        print(out)
    }
}
```

### 6、mutating方法

假设我们有如下一个协议，协议中的函数使用 mutating 修饰

```
protocol Toggleable {    
    mutating func toggle()
}
```

当值类型符合/匹配该协议时，如果实现的 toggle() 函数内部需要修改 self ，则必须具有 mutating 前缀

当类 符合/匹配 该协议时，如果实现的 toggle() 函数内部需要修改 self , 不需要 mutating 前缀，加上也会报错！

因为：引用类型数据的函数默认就可以修改self；而值类型数据必须使用 mutating 修饰


完整示例：

```
protocol ToggleProtocol{
    mutating func toggle()
}

enum lightBulb:ToggleProtocol{
    case on
    case off
    
  mutating  func toggle() {
        switch self {
        case .on:
            self = .off
        case .off:
            self = .on
        }
    }
}

class Light:ToggleProtocol{
    var isBulbOn:Bool=false
    
    func toggle() {
        self.isBulbOn=true
    }
}
```
---

## 二十、错误处理

### 1、错误分类

可恢复的错误(recoverable error) 和 不可恢复的错误(nonrecoverable error)。



### 2、对输入字符串做词法分析

本章内容将实现一个函数——输入 "12+5+6" 的字符串，然后返回Int值23的函数。在此过程中介绍 Swift 的错误处理机制

实现上述函数的关键在于将字符串中的数字和符号读取出来，这种就称作语法分析——实现词法分析（lexing）是把输入转化为一个符号(token) 序列。

符号就是某种有意义的东西，比如数或者加号(编译器认识的两种符号)。语法分析有时候也被称为“符号化”，因为我们是把某种对编译器来说无意义的输入(比如字符串)变成有意义的符号序列。


#### (1)、声明Token类型

Token 用来存储词法分析过程中得到的数字和运算符。可以使用枚举来表示

```
enum Token{
    case number(Int)
    case plus
}
```

#### (2)、创建分析器类型——Lexer

分析器要做的事情是，接收一个字符串，然后读取单个字符并获取字符的位置。

##### A: 先定义一个分析器


```
class Lexer{
    var inputStr:String
    var position:String.Index
    
    init(input:String) {
        inputStr=input
        position=inputStr.startIndex
    }
}
```

##### B: 定义获取字符和位置的方法

词法分析获取到单个字符/符号后，为了最终的运算，我们需要先定义一个数组来存储这些符号。这个数组接收的数据类型为前面我们已经定义好的 —— Token

接下来，从第一个字符开始分析，如果是数字(digit，即一个 0~9的数)，那就分析输入的字符串，收集后面所有的数字形成一个数，再往符号数组找中添加一个 .number 。如果字符是 + ，就向Token数组中添加一个 .plus。如果字符是空格，忽略它。 以上三种情况发生后，都要在输入字符串中前进一个字符，然后重复上面的分析过程。

在扫描输入字符串的过程中，如果遇到上述三种情况以外的字符，一律认为遇到了错误: 无效输入。 当到达输入字符串的末尾时，词法分析阶段就结束了。

下面，我们就先定义获取字符和位置的方法，要点如下：

* 每一个字符串都会包含 startIndex / endIndex
* 在获取某个索引位置的字符时要确保不会索引越界
* 获取指定索引位置字符的格式为：`str[index]`。index 取值类型为：String.index
* `str.index(after:pos)`表示获取 pos 索引位置的下一个索引对象。

```
class Lexer{
    
    //获取当个字符
    func getCharacter() -> Character?{
        //确保索引不越界
        guard position<inputStr.endIndex else {
            return nil
        }
        return inputStr[position]
    }
    
    //获取下一个索引位置
    func getNextPosition(){
        //str.index(after:pos) 的含义是：获取pos后面的一个索引位置
        position = inputStr.index(after: position)
    }
}
```

#### (3)、断言——assert、precondition

在上一节中我们定义了获取字符和获取下一个索引的方法。

在获取字符的函数中，由于需要有返回值，所以我们使用 guard/else 进行了判断，如果出错，则返回 nil。

但是在获取下一个索引的方法中，我们还没有添加错误处理。

由于索引越界会导致崩溃，这是一种不可修复的错误，所以，此处我们可以使用 assert 或 precondition 进行断言。

* 断言的格式分别为：`assert(条件,错误信息)`  `precondition(条件,错误信息)`
* assert 只在调试环境下有效，生产环境无效；precondition 在调试和生产环境均有效

```
//获取下一个索引位置
func getNextPosition(){
    //断言，索引越界时触发崩溃并抛出错误信息。precondition 调试/生产环境均有效。assert 仅在调试环境有效。
    precondition(position<inputStr.endIndex, "索引越界")
        
    //str.index(after:pos) 的含义是：获取pos后面的一个索引位置
    position = inputStr.index(after: position)
}
```


#### (4)、声明可能会抛出错误的方法——throws

接下来，我们需要在 Lexer 中定义一个分析方法——lex()，该方法最终返回一个符号数组。由于这个方法中会调用  getNextPosition，而 getNextPosition 中使用了断言，可能会产生崩溃并抛出错误信息，所以，我们需要在 lex() 后面追加 `throws` , 表示这个方法可能会出现异常。

要点：

* `方法A() throws` 表示 方法A可能会出错


```
class Lexer{
    ...
    
    func lex() throws -> [Token]{
        var tokenArr=[Token]()
        
        while let char = getCharacter() {
            switch char {
            case "0"..."9":
                //如果是0-9之间的字符，则需要继续获取后面的几位数——因为这可能是一个多位数
            
            case "+":
                //由于已经知道 tokenArr接收 Token 枚举，所以，可以省略类型 使用 . 代替
                //tokenArr.append(Token.plus)
                tokenArr.append(.plus)
                getNextPosition()
            case " ":
                getNextPosition()
            default:
                //出错了，非法字符了
            }
        }
        
        return tokenArr
    }
}
```

#### (5)、自定义一个符合 Swift.Error 的错误

在 lex(）方法中，我们 default 分支表示接收到了其他无效字符，然后在此抛出错误。 

Swift中，
* 自定义的错误必须符合/匹配 Swift.Error 协议，Swift.Error 中无任何属性和方法
* 自定义错误时，通常使用枚举，不同的case表示不同的错误

在Lexer类内部使用嵌套枚举定义错误

```
enum LexError:Swift.Error {
    case invaliedCharacter(Character)
}
```

default分支中抛出自定义错误：

```
 func lex() throws -> [Token]{
        var tokenArr=[Token]()
        
        while let char = getCharacter() {
            switch char {
            case "0"..."9":
                //如果是0-9之间的字符，则需要继续获取后面的几位数——因为这可能是一个多位数
            case "+":
                //由于已经知道 tokenArr接收 Token 枚举，所以，可以省略类型 使用 . 代替
                //tokenArr.append(Token.plus)
                tokenArr.append(.plus)
                getNextPosition()
            case " ":
                getNextPosition()
            default:
                //出错了，非法字符了
                throw LexError.invaliedCharacter(char)
            }
        }
        
        return tokenArr
    }
```

#### (6)、获取字符串中的数值

注意：Character 需要先转为String，然后才能转为Int；

```java
class Lexer{
    ...
    
    //将字符类型的数字转为Int型数字
    func convertCharToNum()->Int{
        var value=0
        
        while let char=getCharacter() {
            switch char {
            case "0"..."9":
                // Character 需要先转为String，然后才能转为Int；
                // 而且由于case限定了char的取值，所以，必然不会转换失败,故末尾加了！
                let digitalValue=Int(String(char))!
                //value*10 是为了进位；
                value = value*10 + digitalValue
                
                getNextPosition()
                fallthrough
            default:
                return value
            }
        }
        
        return value
    }
    
    
    func lex() throws -> [Token]{
        var tokenArr=[Token]()
        
        while let char = getCharacter() {
            switch char {
            case "0"..."9":
                //如果是0-9之间的字符，则需要继续获取后面的几位数——因为这可能是一个多位数
                let num=convertCharToNum()
                tokenArr.append(.number(num))
                break
               
                ...
            }
        }
        
        return tokenArr
    }
    
    enum LexError :Swift.Error{
        case invaliedCharacter(Character)
    }
}
```

#### (7)、运行词法分析器

前面几节已经把词法分析器需要的函数定义完毕，接下来就需要调用测试了。

在定义一个函数，用来调用词法分析器中的分析函数：

```
func evaluate(str:String){
    
    print("输入内容为：\(str)")
    let lex=Lexer(input:str)
    //报错！Call can throw, but it is not marked with 'try' and the error is not handled
    let tokenArr = lex.lex()
    
    print("解析得到的数组为：\(tokenArr)")
}

evaluate(str: "1 + 1 0 +5")
evaluate(str: "1+ab")
```

但是，当我们定义完上述函数后，`let tokenArr = lex.lex()` 处报错了一个错误，提示我们，此处可能会报错，但我们没有对错误进行处理。

那么怎么处理错误呢？下一节 【捕获错误】中会做介绍。


### 3、捕获错误




#### (1)、使用 do/catch 处理 evaluate() 函数中的异常

* 如某个函数通过`throw`声明了可能会抛出异常，那么调用该函数时需要前缀 `try`
* 然后使用 do/catch 对错误进行处理。do 作用域中至少有一个 try
* Swift中使用常量 error 标记错误实例

```
func evaluate(str:String){
    
    print("输入内容为：\(str)")
    let lex=Lexer(input:str)
    do{
        let tokenArr = try lex.lex()
        print("解析得到的数组为：\(tokenArr)")
    }catch{
        print("抛出异常\(error)")
    }
}
evaluate(str: "1 + 1 0 +5")
evaluate(str: "1+ab")
```

运行结果：

```
输入内容为：1 + 1 0 +5
解析得到的数组为：[__lldb_expr_4.Token.number(1), __lldb_expr_4.Token.plus, __lldb_expr_4.Token.number(1), __lldb_expr_4.Token.number(0), __lldb_expr_4.Token.plus, __lldb_expr_4.Token.number(5)]
输入内容为：1+ab
抛出异常invaliedCharacter("a")
```

#### (2)、catch中捕获具体错误

上一节的代码中直接使用catch 捕获了所有类型的错误。但，实际使用中我们可能需要捕获具体类型的错误，示例如下：

Lexer.LexError.invaliedCharacter(let char) 是我们自定义的一个错误。 **注意括号中的参数，不仅声明了名称为char,而且指定了类型为let——常量**
 
```
func evaluate(str:String){
    
    print("输入内容为：\(str)")
    let lex=Lexer(input:str)
    do{
        let tokenArr = try lex.lex()
        print("解析得到的数组为：\(tokenArr)")
    }catch Lexer.LexError.invaliedCharacter(let char){
        print("输入内容中包含非数字\(char)")
    }catch{
        print("抛出异常\(error)")
    }
}
```

### 4、解析符号数组

在上一节中，我们已经通过 Lexer 类的 lex() 获取到了符号数组，那么，接下来的事情就是编写一个解析器，这个解析器中的函数接收 lex() 取出的数组为参数，然后返回数组元素的计算结果。

在这个解析器类中，我们首先需要定义一个函数获取数组中的每一个符号，然后定义一个将符号转为数值的函数，最后定义一个运算函数。

#### (1)、定义解析类以及获取单个符号的函数

```
class Parser{
    var tokenArr=[Token]()
    var position=0
    
    init(arr:[Token]) {
        tokenArr=arr
    }
    
    func getSingleToken() -> Token? {
        guard position<tokenArr.count else{
            print("索引越界")
            return nil
        }
        
        //先获取符号，然后position右移一位
        let token=tokenArr[position]
        position+=1
        return token
    }
}
```

#### (2)、自定义异常

我们预期的输入字符串应该是 "1+2+3" 这种格式，基于这种格式，Token 数组中的元素必须符合如下几点：
* 第一个元素必须是数值，
* 数值元素后面要么是加号，要么就没有元素了（到末尾了）。
* 加号后面也必须是数值；

如果不符合上述三点，就表示出异常了。此时，我们就需要抛出异常。

接下来，我们先定义两个异常：结束元素异常(如“1+2+”，如果起始元素异常，在 Lexer 的 lex() 中就已经捕获了)，中间元素异常（如“1+2++3”）

```
enum ParserErroe:Swift.Error{
    case unexpectedEndToken(Token)
    case invaliedMiddleToken(Token)
}
```

#### (3)、定义获取数字值的方法

该方法只用来返回数字值，非数值一律抛异常。至于什么时候调用该方法，由调用方自己判断。

```
 //该函数只用来返回数值，非数值一律抛异常。
    func toDigitalNum() throws ->Int {
        guard let token=getSingleToken() else {
            //当token为nil时，才会走这里；getSingleToken 中只有索引越界才会返回nil
            throw ParserError.unexpectedEnd
        }
        
        switch token {
        case .number(let value):
            return value
        default:
            throw ParserError.invaliedMiddleToken(token)
        }
    }
```

#### (4)、定义函数计算结果

toDigitalNum() 内部调用了 getSingleToken()，getSingleToken() 中负责处理 position的右移。

 **下面的代码中，由于 parser() 函数后面跟了 `throws`, 所以，函数内部被 `try` 修饰的语句不需要再写 `do/catch`。此时，如果 try 修饰的语句报错了，会继续把错误抛给 parser() 函数的调用方。** 

```java
class Parser{
   ...
    
    func parser() throws ->Int{
        
        //result初始值为数组第一个元素对应的数值
        var result=try toDigitalNum()
        
        //获取下一个Token。如果该token是运算符，则获取下一个数值，然后相加；
        //如果数值后面依旧是数值，就是一个异常情况
        while let nextToken=getSingleToken() {
            switch nextToken {
                case .plus:
                    let nextNum=try toDigitalNum()
                    result+=nextNum
                case .number:
                    throw ParserError.invaliedMiddleToken(nextToken)
            }
        }
        return result
    }
}
```

#### (5)、更新 evaluate() 调用parser()并处理异常

下面的代码中，
* 有两个 try , 一个是 lex()会抛出一个错误 , 一个是 parser()会抛出两个错误
* do/catch 中完整的捕获了三个错误
*  **末尾通用的catch 不能省略，否则报错** 

```
func evaluate(str:String){
    
    print("输入内容为：\(str)")
    do{
        let tokenArr=try Lexer(input: str).lex()
        let parserObj=Parser(arr: tokenArr)
        let result = try parserObj.parser()
        print("计算结果为：\(result)")
    }catch Parser.ParserError.invaliedMiddleToken(let token){
        print("异常：解析到\(token)时出错")
    }catch Parser.ParserError.unexpectedEnd{
        print("异常：末尾数据不对")
    }catch Lexer.LexError.invaliedCharacter(let char){
        print("异常：输入字符串中包含非法字符\(char)")
    }catch{
        print("异常：\(error)")
    }
}
```

调用：

```java
//10
evaluate(str: "1+2+3+4")
//解析到number(0)时出错
evaluate(str: "1 + 1 0 +5")
//末尾数据不对
evaluate(str: "1+")
//解析到plus时出错
evaluate(str: "1+2++")
//输入字符串中包含非法字符a
evaluate(str: "1+ab")
```

输出结果：

```
输入内容为：1+2+3+4
后面已经没有数据了。。。
计算结果为：10
输入内容为：1 + 1 0 +5
异常：解析到number(0)时出错
输入内容为：1+
后面已经没有数据了。。。
异常：末尾数据不对
输入内容为：1+2++
异常：解析到plus时出错
输入内容为：1+ab
异常：输入字符串中包含非法字符a
```

#### (6)、分词+解析+计算的完整代码

```
import Cocoa

//----------符号----------------
enum Token{
    case number(Int)
    case plus
}

//--------分词器----------------
class Lexer{
    var inputStr:String
    var position:String.Index
    
    init(input:String) {
        inputStr=input
        position=inputStr.startIndex
    }
    
    //获取当个字符
    func getCharacter() -> Character?{
        //确保索引不越界
        guard position<inputStr.endIndex else {
            return nil
        }
        return inputStr[position]
    }
    
    //获取下一个索引位置
    func getNextPosition(){
        //断言，索引越界时触发崩溃并抛出错误信息。precondition 调试/生产环境均有效。assert 仅在调试环境有效。
        precondition(position<inputStr.endIndex, "索引越界")
        
        //str.index(after:pos) 的含义是：获取pos后面的一个索引位置
        position = inputStr.index(after: position)
    }
    
    //将字符类型的数字转为Int型数字
    func convertCharToNum()->Int{
        var value=0
        
        while let char=getCharacter() {
            switch char {
            case "0"..."9":
                // Character 需要先转为String，然后才能转为Int；
                // 而且由于case限定了char的取值，所以，必然不会转换失败,故末尾加了！
                let digitalValue=Int(String(char))!
                //value*10 是为了进位；
                value = value*10 + digitalValue
                
                getNextPosition()
                fallthrough
            default:
                return value
            }
        }
        
        return value
    }
    
    
    func lex() throws -> [Token]{
        var tokenArr=[Token]()
        
        while let char = getCharacter() {
            switch char {
            case "0"..."9":
                //如果是0-9之间的字符，则需要继续获取后面的几位数——因为这可能是一个多位数
                let num=convertCharToNum()
                tokenArr.append(.number(num))
                break
            case "+":
                //由于已经知道 tokenArr接收 Token 枚举，所以，可以省略类型 使用 . 代替
                //tokenArr.append(Token.plus)
                tokenArr.append(.plus)
                getNextPosition()
            case " ":
                getNextPosition()
            default:
                //出错了，非法字符了
                throw LexError.invaliedCharacter(char)
            }
        }
        
        return tokenArr
    }
    
    enum LexError :Swift.Error{
        case invaliedCharacter(Character)
    }
}


//-------------解析器-----------------

class Parser{
    var tokenArr=[Token]()
    var position=0
    
    init(arr:[Token]) {
        tokenArr=arr
    }
    
    func getSingleToken() -> Token? {
        guard position<tokenArr.count else{
            print("后面已经没有数据了。。。")
            return nil
        }
        
        //先获取符号，然后position右移一位
        let token=tokenArr[position]
        position+=1
        return token
    }
    
    //该函数只用来返回数值，非数值一律抛异常。
    func toDigitalNum() throws ->Int {
        guard let token=getSingleToken() else {
            //当token为nil时，才会走这里；getSingleToken 中只有索引越界才会返回nil
            throw ParserError.unexpectedEnd
        }
        
        switch token {
        case .number(let value):
            return value
        default:
            throw ParserError.invaliedMiddleToken(token)
        }
    }
    
    func parser() throws ->Int{
        
        //result初始值为数组第一个元素对应的数值
        var result=try toDigitalNum()
        
        //获取下一个Token。如果该token是运算符，则获取下一个数值，然后相加；
        //如果数值后面依旧是数值，就是一个异常情况
        while let nextToken=getSingleToken() {
            switch nextToken {
                case .plus:
                    let nextNum=try toDigitalNum()
                    result+=nextNum
                case .number:
                    throw ParserError.invaliedMiddleToken(nextToken)
            }
        }
        return result
    }
    
    
    enum ParserError:Swift.Error{
        case unexpectedEnd
        case invaliedMiddleToken(Token)
    }
}


//--------调用方——获取结果-----------------
func evaluate(str:String){
    
    print("输入内容为：\(str)")
    do{
        let tokenArr=try Lexer(input: str).lex()
        let parserObj=Parser(arr: tokenArr)
        let result = try parserObj.parser()
        print("计算结果为：\(result)")
    }catch Parser.ParserError.invaliedMiddleToken(let token){
        print("异常：解析到\(token)时出错")
    }catch Parser.ParserError.unexpectedEnd{
        print("异常：末尾数据不对")
    }catch Lexer.LexError.invaliedCharacter(let char){
        print("异常：输入字符串中包含非法字符\(char)")
    }catch{
        print("异常：\(error)")
    }
}
//10
evaluate(str: "1+2+3+4")
//解析到number(0)时出错
evaluate(str: "1 + 1 0 +5")
//末尾数据不对
evaluate(str: "1+")
//解析到plus时出错
evaluate(str: "1+2++")
//输入字符串中包含非法字符a
evaluate(str: "1+ab")
```

### 5、使用鸵鸟策略处理错误

try 有三种格式：try、try!、try?

* try! 表示出错则直接崩溃, 声明的 do/catch 将不再生效
* try? 表示出错则忽略，返回nil, do/catch也将不生效。但这样会导致问题转移，后面的代码就可能出错

对于后面的这两个变种，可以直接尝试将上一节中 evaluate() 中的 try 分别改为 try!、 try?

用 try 就够了，后面两种慎用。

### 6、Swift的错误处理哲学

* 声明函数时，如果函数可能抛出错误，则需要用 throws 修饰
* 调用函数时，如果函数可能抛出错误，则需要用 try 修饰
* 调用函数时，使用 try 配合 do/catch 处理错误
* 如果某个函数声明了 throws , 当其内部有 try 语句时，可以不使用 do/catch , 当 try 语句报错时，错误会被传递给该函数的调用方
* 出错时，抛出的实际是符合/匹配 Swift.Error 协议的一个错误实例。该实例默认使用 `error` 常量表示。
* do/catch 时必须有一个不指明具体错误类型的 catch 分支。（也就是最末尾的 catch{ ...}）

---

## 二十一、扩展

扩展(extension)

通过扩展我们可以向已有的类型添加属性和函数等内容，这样就不用再定义一堆的工具类。

该章节的内容将会为汽车的行为建模——就是以汽车的行为编写示例

### 1、扩展Swift预置类型

velocity [vəˈlɒsəti] 速度

#### (1)、建立类型别名——typealias

速度是汽车的重要特征，而表示速度时最理想的类型是Double。

所以，当我们需要建立一个速度类型时，可以不用重新定义，而是直接为 Double 创建一个别名，这样，我们新定义的速度类型就指向了Double类型。

```
//为Double类型定义别名 —— Velocity. 注意 typealias 是全小写的
typealias Velocity = Double
```


#### (2)、扩展计算属性

* Swift 不允许为类型扩展存储属性， **只能扩展计算属性。** 

扩展计算属性的格式：

```
extension 类型名{
    var 属性名:属性类型{
        //此处定义set、get 函数
    }
}
```

我们为 Velocity 类型扩展两个属性：每小时公里数——kph, 每小时英里数——mph

```
typealias Velocity = Double

extension Velocity{
    var kph:Velocity{   return self * 1.60934 }
    var mph:Velocity{   return self }
}
```
上述扩展中，以 mph 为默认单位 ； Double 中也同时拥有了这两个属性


#### (3)、定义协议，协议中使用扩展的类型

vehicle [viːɪkəl] 车辆，运输工具

定义一个车辆协议，包含车辆的三个基本属性。

其中的 topSpeed 的属性类型会使用刚才扩展的类型——velocity。后续小节会使用该协议


```
//为Double类型定义别名 —— Velocity. 注意 typealias 是全小写的
typealias Velocity = Double

extension Velocity{
    var kph:Velocity{   return self * 1.60934 }
    var mph:Velocity{   return self }
}

protocol Vehicle{
    //最高速度
    var topSpeed:Velocity{get}
    //几个门
    var doorNums:Int{get}
    //是否可以接挂车（是否可以拉货）
    var flatBed:Bool{get}
}
```


### 2、扩展自定义类型

先新建一个自定的类型，然后利用扩展让该类型去符合 Vehicle 协议

```
struct Car {
    let make: String
    let model: String
    let year: Int
    let color: String
    let nickname: String
    var gasLevel: Double {
        willSet {
            precondition(newValue <= 1.0 && newValue >= 0.0,"new value must between 0 and 1")
        }
    }
}
```
上面定义了一个结构体，gasLevel 中使用了 属性观察者 willSet，并使用了断言 precondition

#### (1)、用扩展使类型符合/匹配协议

用扩展的方式，让 Car 符合 Vehicle 协议 

```
struct Car {
    let make: String
    let model: String
    let year: Int
    let color: String
    let nickname: String
    var gasLevel: Double {
        willSet {
            precondition(newValue <= 1.0 && newValue >= 0.0,"new value must between 0 and 1")
        }
    }
}

extension Car:Vehicle{
    var topSpeed:Velocity{ return 180 }
    var doorNums:Int{ return 4 }
    var hasFlatBed:Bool { return false }
}
```
#### (2)、用扩展添加初始化方法

在结构体 Car 中，我们没有自定义初始化方法，此时，Car 只有两个默认的初始化方法：空初始化方法、成员初始化方法。

如果我们既想保留 成员初始化方法，又想为 Car 新增一个自定义初始化方法， 此时，就可以利用扩展实现。 

```
extension Car{
    //扩展初始化方法
    init(make: String, model: String, year: Int) {
        //默认的成员初始化方法
        self.init(make: make,
                  model: model,
                  year: year,
                  color: "Black",
                  nickname: "N/A",
                  gasLevel: 1.0)
    }
}

//扩展的初始化方法
let car1=Car(make:"奥迪",model:"汽油动力",year:2018)
//默认的成员初始化方法
let car2=Car(make: "大众", model: "混合动力", year: 2019, color: "黑色", nickname: "没有", gasLevel: 0.4)
//由于Car结构体中的属性没有默认值，所以，此处不能调用默认的空初始化方法
//let car3=Car()
```
#### (3)、嵌套类型和扩展

通过扩展还可以给现有类型增加嵌套类型

```
extension Car{
    enum Kind {
        //注意这里的声明方式，直接用逗号间隔的
        case coupe,sedan
    }
    
    var kind:Kind{
        //两门是轿跑，四门是普通轿车
        return doorNums==2 ? .coupe : .sedan
    }
}
```

上述代码为 Car 扩展了一个嵌套类型 Kind, 并扩展了一个属性 kind

#### (4)、扩展中的函数

通过扩展可以给现有的类型新增函数

```
extension Car{
    mutating func consumeGas(by amount:Double){
        precondition(0<amount && amount<=1, "汽油消耗幅度只能在（0,1] 之间")
        gasLevel -= amount
    }
    
    mutating func fillGas(){
        gasLevel=1
    }
}
```

### 3、完整示例代码：

```
import Cocoa

//为Double类型定义别名 —— Velocity. 注意 typealias 是全小写的
typealias Velocity = Double

extension Velocity{
    var kph:Velocity{   return self * 1.60934 }
    var mph:Velocity{   return self }
}

protocol Vehicle{
    //最高速度
    var topSpeed:Velocity{get}
    //几个门
    var doorNums:Int{get}
    //是否可以接挂车（是否可以拉货）
    var hasFlatBed:Bool{get}
}

struct Car {
    let make: String
    let model: String
    let year: Int
    let color: String
    let nickname: String
    var gasLevel: Double {
        willSet {
            precondition(newValue <= 1.0 && newValue >= 0.0,"new value must between 0 and 1")
        }
    }
}

extension Car:Vehicle{
    var topSpeed:Velocity{ return 180 }
    var doorNums:Int{ return 4 }
    var hasFlatBed:Bool { return false }
}

extension Car{
    //扩展初始化方法
    init(make: String, model: String, year: Int) {
        //默认的成员初始化方法
        self.init(make: make,
                  model: model,
                  year: year,
                  color: "Black",
                  nickname: "N/A",
                  gasLevel: 1.0)
    }
}

extension Car{
    enum Kind {
        //注意这里的声明方式，直接用逗号间隔的
        case coupe,sedan
    }
    
    var kind:Kind{
        //两门是轿跑，四门是普通轿车
        return doorNums==2 ? .coupe : .sedan
    }
}

extension Car{
    mutating func consumeGas(by amount:Double){
        precondition(0<amount && amount<=1, "汽油消耗幅度只能在（0,1] 之间")
        let newGasLevel=gasLevel-amount
        gasLevel = amount<0 ? 0 : newGasLevel
    }
    
    mutating func fillGas(){
        gasLevel=1
    }
}


//扩展的初始化方法
var car1=Car(make:"奥迪",model:"汽油动力",year:2018)
//默认的成员初始化方法
var car2=Car(make: "大众", model: "混合动力", year: 2019, color: "黑色", nickname: "没有", gasLevel: 0.4)
//由于Car结构体中的属性没有默认值，所以，此处不能调用默认的空初始化方法
//let car3=Car()

car1.kind

car1.consumeGas(by: 0.3)
car1.gasLevel
car1.fillGas()
car1.gasLevel
```

---

---

## 二十二、泛型

generics

可空类型、数组、字典内部都是用了泛型

### 1、泛型数据结构

以泛型数据模拟内存中进栈和出栈的操作。

#### (1)、创建接收Int类型数据的栈

创建结构体 Stack , 然后模拟进栈出栈的操作。栈的规则是：后进先出

```java
struct Stack{
    var numArr=[Int]()
    
    mutating func push(num:Int){
        numArr.append(num)
    }
    
    mutating func pop()->Int?{
        guard !numArr.isEmpty else {
            //如果数组没有元素返回nil
            return nil
        }
       return numArr.removeLast()
    }
}

var stack1=Stack()
stack1.push(num: 1)
stack1.push(num: 2)

stack1.pop()
stack1.pop()
stack1.pop()
```

#### (2)、把自定义的 Stack 修改为泛型

Swift 中声明泛型的语法是：`类型名 < 占位类型名 > `

调用时必须指明具体类型

```
struct Stack<E>{
    var numArr=[E]()
    
    mutating func push(num:E){
        numArr.append(num)
    }
    
    mutating func pop()->E?{
        guard !numArr.isEmpty else {
            //如果数组没有元素返回nil
            return nil
        }
       return numArr.removeLast()
    }
}

var stack1=Stack<Int>()
stack1.push(num: 1)
stack1.push(num: 2)

stack1.pop()
stack1.pop()
stack1.pop()
```

### 2、泛型函数和方法

在第13章闭包中，介绍了一个数组的 map(） 函数，该函数接收一个闭包，然后在该闭包中对数组元素做修改，然后返回一个新的数组。

接下来，我们就借助泛型，自定义这个一个 map() 函数。

#### (1)、自定义 map() 函数

我们自定义的这个 map() 函数接收两个参数，一个是数源数组，一个是闭包。闭包中指定变换规则

```
//<A,B>声明当前函数需要两个泛型类，
func cusMap<A,B>(_ arr:[A],cusFunc:(A) -> B)->[B]{
    var resultArr=[B]()
    for item in arr{
        resultArr.append(cusFunc(item))
    }
    
    return resultArr
}

let strArr=["one","two","three"]
//函数的最后一个参数为闭包时，可以将该闭包放置在小括号外部
let lengthArr=cusMap(strArr){
    //闭包中未指明参数名称时，使用默认参数名 $0, $1 等
    $0.count
}
print(lengthArr)
```
上述函数中 
* `cusMap<A,B>` 声明当前函数需要使用两个泛型类
* cusMap() 函数接收两个参数，一个是元素类型为 A 的数组；一个是闭包函数，闭包函数接收 A 类型参数，然后返回 B 类型数据
* cusMap() 函数最终返回 B类型的数组
* cusFunc:(A) 是一个函数类型。其中的A表示cusFunc 的参数类型，所以，这里的 A 必须用括号包裹起来

#### (2)、为自定义的Stack增加自定义的 map 方法

```
import Cocoa

struct Stack<E>{
    var numArr=[E]()
    
    mutating func push(num:E){
        numArr.append(num)
    }
    
    mutating func pop()->E?{
        guard !numArr.isEmpty else {
            //如果数组没有元素返回nil
            return nil
        }
       return numArr.removeLast()
    }
    
    func map<C>(_ cusFunc:(E)->C)->Stack<C>{
        var arr=[C]()
        
        for item in numArr {
            arr.append(cusFunc(item))
        }
        return Stack<C>(numArr: arr)
    }
}

var stack1=Stack<Int>()
stack1.push(num: 1)
stack1.push(num: 2)

var stack2=stack1.map{
    String($0)
}
//Stack<String>(numArr: ["1", "2"])
print(stack2)

//2
stack1.pop()
//1
stack1.pop()
//nil
stack1.pop()

//2
stack2.pop()
//1
stack2.pop()
//nil
stack2.pop()
```

### 3、类型约束

假设某个函数接收两个泛型参数，由于我们无法确定这个泛型的最终类型，所以，在函数中我们不能直接做 值相等比较 或者 引用相等比较。

 **比如下面这段代码，就会报错！！！** 

```
func checkIfEqual<T>(_ first: T, _ second: T) -> Bool {
    //由于不确定 first second 到底是什么类型，所以，直接使用值相等比较会报错
    return first == second
}
```

 **没有限制条件的泛型是无意义的。** 

Swift 提供了 类型约束（type constraint）对函数参数的 泛型类型 进行限定。
有了这个限定之后，我们就可以知道两个参数是否可以进行相应的比较。

 **Swift的类型约束有两种：**
* **泛型的类型必须是某个类的子类**
* **泛型的类型必须符合某种协议（或者协议组合）** 


#### (1)、使用类型约束判断两个参数是否相等1

Equatable是Swift提供的协议，用来声明两个值的相等性可以检查。（25章会详细介绍）

```
func isEqual<T:Equatable>(_ first:T,_ second:T)->Bool{
    return first == second
}

//true
print(isEqual(1,1))
//false
print(isEqual("a","b"))
```
上述示例代码中：
* 函数接收泛型为T的两个参数，T 必须符合 Equaltable 协议

#### (2)、使用类型约束判断两个参数是否相等1

```
func checkIfDescMatch<A:CustomStringConvertible,B:CustomStringConvertible>(_ first:A,_ second:B)->Bool{
    return first.description==second.description
}

print(checkIfDescMatch(1, 2))
print(checkIfDescMatch("a", "a"))
print(checkIfDescMatch(1, 1.0))
```

上述代码中：

* checkIfDescMatch() 接收两个泛型参数，他们的类型分别为 A、B 。A 和 B 分别符合了CustomStringConvertable 协议
* 输出的是三个参数的 description 值是否相等


### 4、关联类型协议

类、方法 都可以定义为泛型的，但是  **协议不能定义为泛型的** 

协议支持类型的一个相关特性：关联类型（associated types)

我们来看一组 Swift 标准库中定义的协议，探索一下关联类型的协议。

我们即将用到的两个协议是`IteratorProtocol` 和 `Sequence`，它们共同实现自定义类型对利用 `for-in`循环遍历的支持。

#### (1)、IteratorProtocol协议

IteratorProtocol协议的内部实现如下：

```
protocol IteratorProtocol {
    associatedtype Element
    mutating func next() -> Element?
}
```
 `mutating func next() ` 表示如果某个类型符合了 IteratorProtocol 协议，只要重复调用 next(）就可以不断的获取值，当 next(）返回 nil 时，表示没有数据了。

 `associatedtype Element` 表示该协议的关联类型为 Element 。符合该协议的类型必须为 Element 提供一个具体的类型。所以， **符合这个协议的类型需要在内部为 Element 提供 typealias 的定义。** 

示例：自定义 StackIterator 

```
//遍历栈中的内容
struct StackIterator<T>:IteratorProtocol{
    typealias Element = T
    var stack:Stack<T>
    
    mutating func next() -> Element? {
        return stack.pop()
    }
}

//模拟栈的进出操作
struct Stack<E>{
    var numArr=[E]()

    mutating func push(num:E){
        numArr.append(num)
    }

    mutating func pop()->E?{
        guard !numArr.isEmpty else {
            //如果数组没有元素返回nil
            return nil
        }
       return numArr.removeLast()
    }

    func map<C>(_ cusFunc:(E)->C)->Stack<C>{
        var arr=[C]()

        for item in numArr {
            arr.append(cusFunc(item))
        }
        return Stack<C>(numArr: arr)
    }
}

var stack1=Stack<Int>()
stack1.push(num: 1)
stack1.push(num: 2)

//调用遍历
var stackIterator=StackIterator(stack: stack1)
while let result=stackIterator.next(){
    print("依次出栈的内容为 \(result)")
}
```

因为Swift可以推断协议的关联类型，所以只要指明 next() 返回的是T?，就可以省略显式的类型别名。也就是说 StackIterator 可以改为下面的样子：

```
struct StackIterator<T>:IteratorProtocol{
    //因为next()返回的是T，又由于Swift可以推断协议的关联类型，所以，该行可以省略
    //typealias Element = T
    var stack:Stack<T>
    
    mutating func next() -> T? {
        return stack.pop()
    }
}
```

#### (2)、Sequence 协议

Sequence 协议的关联类型为：Iterator

* 符合 Sequence 协议的类型中，必须有一个符合 IteratorProtocol 协议的 Iterator 
* 符合 Sequence 协议的类型中, 还必须实现 makeIterator() 方法，该方法返回一个 IteratorProtocol 值


示例代码：让 Stack 符合 Sequence 协议

```
struct Stack<E>:Sequence{
    
    //因为makeIterator()返回的是StackIterator<E>，
    //又由于Swift可以推断协议的关联类型，所以，该行可以省略
    //typealias Iterator = StackIterator<E>
    
    var numArr=[E]()

    mutating func push(num:E){
        numArr.append(num)
    }

    mutating func pop()->E?{
        guard !numArr.isEmpty else {
            //如果数组没有元素返回nil
            return nil
        }
       return numArr.removeLast()
    }

    func map<C>(_ cusFunc:(E)->C)->Stack<C>{
        var arr=[C]()

        for item in numArr {
            arr.append(cusFunc(item))
        }
        return Stack<C>(numArr: arr)
    }
    
    //这是符合 Sequence协议时必须实现的h方法
    func makeIterator() -> StackIterator<E> {
        return StackIterator(stack: self)
    }
}
```

Sequence协议是Swift为实现 for-in 循环在内部使用的协议。既然 Stack 符合了该协议，那么Stack 也可以进行 for-in 遍历

```
var stack1=Stack<Int>()
stack1.push(num: 1)
stack1.push(num: 2)

for value in stack1{
    print("Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 \(value)")
}
```
#### (3)、关联类型协议的注意事项

* 如果某个协议有关联类型，那么该协议就不能作为一个具体的类型使用。比如，不能声明一个类型为 Sequence 的变量


### 5、类型约束中的where子句

#### (1)、批量将数据放入栈中

为Stack结构体定义一个方法，该方法接收一个数组，函数内部遍历该数组并将元素放到栈中

```

struct Stack<E>:Sequence{
 ...
  
 mutating func pushAll(arr:[E]){
        for item in arr {
            push(num: item)
        }
    }
}
```

调用：

```
var stack1=Stack<Int>()
stack1.pushAll(arr: [4,5,6])
```
#### (2)、让 pushAll 变成通用函数

有了 pushAll 函数之后，我们向栈中添加数据的时候就方便多了。

但是，如果我们想把它做成一个通用的方法该怎么做呢？

由于 符合 Sequence 协议的类型本身就具有 for-in 循环的特性，所以，我们可以把 pushAll 接收的参数改为符合 Sequence 协议的类型。

然后我们就做出了如下修改：

```
mutating func pushAll<S:Sequence>(sequence:S){
    for item in sequence {
        push(num: item)
    }
}
```

#### (3)、添加where限定

我们把 pushAll 接收的参数修改为 符合Sequence协议的S之后，函数报错了。

因为，push 的时候，参数的类型必须是 E ，但我们在 pushAll 中调用 push 时传递的却是 S ，所以，我们必须添加额外的判断条件 —— 判断 S 的关联类型 Iterator 的别名 与 E 一致。

修改后的代码如下：

```
mutating func pushAll<S:Sequence>(sequence:S) where S.Iterator.Element==E{
   for item in sequence {
       push(num: item)
   }
}
```  

调用 pushAll()：

```
var stack1=Stack<Int>()
stack1.pushAll(sequence: [4,5,6])

var stack2=Stack<Int>()
stack2.pushAll(sequence: [1,2,3])
//pushAll接收符合Sequence协议的类型，由于Stack也符合该协议，所以可以直接做为参数。
stack2.pushAll(sequence: stack1)

for value in stack2{
    print("Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 \(value)")
}
```

输出结果：

```
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 4
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 5
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 6
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 3
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 2
Stack实现了Sequence后可以使用for-in遍历，遍历得到的值为 1
```

注意上面输出结果的顺序，这里很有意思, 并不是我们预期的 1，2，3，4，5，6。这里牵涉到了入栈和出栈的顺序问题。咱不做深究。


### 6、深入学习：理解可空类型——Optional

Optional背后的原理并没什么特别的，它只有两个成员的泛型枚举而已:

```
enum Optional<Wrapped> {
    case None
    case Some(Wrapped)
}
```

None 对应当前值为nil的可空实例。
而 Some 对应当前值不为nil ，而是 Wrapped 类型的可空实例。

大部可空类型的实例都会用到 `可空实例绑定`和 `可空链式调用`。但是，由于可空类型底层是一个枚举，所以，也可以把可空类型当成其他枚举来使用。

举个例子，如果 maybeAnInt 是 Int?，那就可以对两种成员值进行匹配:

```
switch maybeAnInt {
  case .None:
     print("maybeAnInt is nil")
  case let .Some(value):
     print("maybeAnInt has the value \(value)")
}
```
当然了，实际应用的时候，直接使用 `可空实例绑定` 和 `可空链式调用`会更方便。

### 7、深入学习：参数多态

假设函数接收的参数类型为A，如果传入 A 的子类 B 类型数据，也是可以的，这种情形就被称为 `多态`。确切的说应该叫 `运行时多态(runtime polymorphism)`或者`子类(subclass polymorphism）`

多态就是指多种形式，继承是多态的一种表现。

Swift为泛型添加限定也是一种多态表现，被称为 `编译时多态(compile-time polymorphism)` 或 `参数多态(parametric polymorphism)` 因为带约束的泛型函数也符合多态的定义:一个函数可以接收不同的类型。


运行时多态基于继承关系，所以，在改变一个类的时候，极有可能会连带着改变了另一个类中的内容（比如，修改了父类，那么子类中的内容随之变化)。并且，运行时多态在性能上的损耗略大。

编译时多态基于泛型和协议，它规避了运行时多态的两个缺点。编译时多态的性能损耗可以忽略不计。

---

## 二十三、协议扩展

面向对象编程(object-oriented programming)

传统的OOP中，用类为数据建模，然后用方法修改类实例的属性，并与其它的类实例进行通信。OOP中支持继承，但是如果继承层次比较多，就会让代码比较难以理解。

Swift支持OOP，但又与传统OOP不同。

Swift中更多的时候是使用协议和泛型来为数据建模。这样就避免了类的继承层级过深的问题。

协议扩展(protocol extension) 为Swift中的数据建模提供了更强大的支持。可以为协议扩展方法和计算属性。

### 1、为 锻炼计划 建模

在介绍协议扩展前，先定义一个协议和将要符合这个协议的类型。

#### (1)、定义 Exercise 协议 

Swift标准库中，为协议命名时大致遵循下列规则：
* 直接是一个名词
* 如果是用来表述能力的协议，则通常使用 `able`、`ible`、`ing` 结尾

```
protocol Exercise{
    var name:String { get }
    var minutes:Double { get }
    var caloriesBurned:Double { get }
}
```
上述代码中，定义了一个锻炼计划的协议。三个参数分别表示：运动名称，运动时长，消耗卡路里的量


#### (2)、构建用来锻炼的结构体

一个椭圆机:  elliptical [ɪ'lɪptɪk(ə)l] 椭圆机，椭圆形的。

一个跑步机： treadmill  ['tredmɪl] 跑步机

接下来，构建这两个结构体，并创建实例。

```
struct EllipticalWorkOut:Exercise{
    let name: String="椭圆机"
    let minutes: Double
    let caloriesBurned: Double
}

struct TreadmillWorkOut:Exercise{
    let name: String="跑步机"
    let minutes: Double
    let caloriesBurned: Double
    
    //圈数，这是跑步机自己独有的属性
    let laps:Double
}

//注意，在创建实例时，如果属性被声明为常量并且有默认值，则不用再重新设置值。如果声明为变量，即便有默认值也需要重新赋值
var elliptical=EllipticalWorkOut(  minutes: 20, caloriesBurned: 300)

var treadmill=TreadmillWorkOut(minutes: 30, caloriesBurned: 400, laps: 10.0)
```
上述示例代码中，结构体中的属性都被声明为常量。

这里有一些需要注意的地方：

 **注意：** :exclamation: 
* 如果属性被声明为常量，并且有默认值，在使用默认的成员初始化方法创建实例时，不需要再为该属性设置值
* 如果属性被声明为常量，但没有默认值，在使用默认的成员初始化方法创建实例时，需要再为该属性设置值
* 如果属性被声明为变量，不论是否有默认值，在使用默认的成员初始化方法创建实例时，必须设置值。



### 2、扩展自定义的Exercise协议

#### (1)、计算每分钟消耗的卡路里

```
//计算每分钟消耗的卡路里
func caloriesBurnedPerMinutes<E:Exercise>(for exercise:E)->Double{
    return exercise.caloriesBurned/exercise.minutes
}

print(caloriesBurnedPerMinutes(for: elliptical))
print(caloriesBurnedPerMinutes(for: treadmill))
```
上述方法中定义了一个泛型函数，泛型需要符合Exercise协议。

#### (2)、扩展协议属性

在上一节定义了计算每分钟小号卡路里的泛型函数，虽然实现了我们的需求，但对于每一种运动来说，每分钟消耗的卡路里量都是肯定存在的，所以，我们实际上完全可以把它扩展为Exercise协议的一个属性

* 扩展协议的属性时，也是只能扩展计算属性。
* 为协议扩展属性之后，所有符合协议的类型都可以直接调用这个属性。

```
extension Exercise{
    //只能扩展计算属性
    var caloriesBurnedPerMinutes:Double {
        return caloriesBurned/minutes
    }
}

print(elliptical.caloriesBurnedPerMinutes)
print(treadmill.caloriesBurnedPerMinutes)
```


### 3、带where子句的协议扩展

在前面的学习中我们已经知道 Sequence 协议的关联类型是Iterator,那么，接下来我们要做的事情就是：

为 Sequence 协议扩展一个函数，该函数仅对关联类型为 Exercise 的序列生效。

```java
//5、使用where为协议的扩展增加限定
extension Sequence where Iterator.Element==Exercise{
    
    func getTotalCaloriesBurned() -> Double {
        var  totalCaloriesBurned:Double=0.0
        
        for item in self {
            totalCaloriesBurned += item.caloriesBurned
        }
        return totalCaloriesBurned
    }
}

//此处需要显示声明 exercise 的类型为 [Exercise],否则报错
var exerciseArr:[Exercise]=[treadmill,elliptical]
print(exerciseArr.getTotalCaloriesBurned())
```

上述代码中：
* 数组是符合 Sequence 协议的。其关联类型 Iterator 的具体类型由数组元素的类型决定。
* 由于 `exerciseArr:[Exercise]` 的元素类型为Exercise，所以，该数组的关联类型就是 Exercise，所以，可以调用上面的扩展函数。
* 在构建 exerciseArr 时必须显示声明其类型，否则报错。


### 4、用协议扩展提供默认实现



#### (1)、用协议扩展提供默认实现

接下来，我们让 Exercise 协议 继承 CustomStringConvertiable 协议。此时，符合 Exercise 的类型必须实现 description 属性。

但是，我们又不想去 EllipticalWorkOut 和 TreadmillWorkOut 结构体中进行修改。此时，就可以使用扩展为符合 Exercise 协议的类型提供默认的 descriotion 实现。

```
protocol Exercise:CustomStringConvertible{
    var name:String { get }
    var minutes:Double { get }
    var caloriesBurned:Double { get }
}

...

extension Exercise{
    var description:String{
        return "用协议扩展提供默认实现：运动名称-\(name)"
    }
}

print(treadmill.description)
print(elliptical.description)
```

 **注意：** 

按照上述示例修改之后, Xcode 10.1 中一直会提醒 TreadmillWorkOut 、EllipticalWorkOut 没有实现 CustoomStringConvertable 中的description。但是不用管，因为我们已经通过扩展在 Exercise 中提供了默认实现。此时运行并不会出错。

#### (2)、覆盖协议扩展的默认实现

假设上一节中通过协议扩展提供的默认实现并不使我们想要的，那么，在符合 Exercise 协议的类型中，我们也可以自定义 description 的实现。

```
struct TreadmillWorkOut:Exercise{
    let name: String="跑步机"
    let minutes: Double
    let caloriesBurned: Double
    
    //圈数，这是跑步机自己独有的属性
    let laps:Double
    
    var description: String{
        return "覆盖Exercise的默认descriotion:这是一个跑步机呢"
    }
}

...

print(treadmill.description)
print(elliptical.description)
```
输出内容：

```
覆盖Exercise的默认descriotion:这是一个跑步机呢
用协议扩展提供默认实现：运动名称-椭圆机
```

### 5、关于命名

假设现有协议 A，和符合该协议的类型 B。

B 中现有一个属性 attrX, 如果我们又通过扩展的形式给A扩展了一个属性 attrX 。

那么，我们在调用 B 的 attrX 属性时，到底返回的是哪个值呢？

```
...

struct EllipticalWorkOut:Exercise{
    let name: String="椭圆机"
    let minutes: Double
    let caloriesBurned: Double
    let brand:String="这是一个奥迪牌的椭圆机"   
}

extension Exercise{
    var brand:String{
        return "啥牌子不重要，好使才是硬道理"
    }
}

var elliptical=EllipticalWorkOut(  minutes: 20, caloriesBurned: 300)
var treadmill=TreadmillWorkOut(minutes: 30, caloriesBurned: 400, laps: 10.0)

var exerciseArr:[Exercise]=[treadmill,elliptical]

for exercise in exerciseArr {
    print(exercise.brand)
}

print(elliptical.brand)
```

输出结果：

```
啥牌子不重要，好使才是硬道理
啥牌子不重要，好使才是硬道理
这是一个奥迪牌的椭圆机
```

上述示例代码中：

我们为 EllipticalWorkOut 定义了一个 brand 属性，同时又用扩展的方式为其父协议 Exercise 添加了同名属性 brand ，但两者取值不一样。

当我们在遍历数组和直接打印时虽然都是调用了 .brand ,但得到了不同的输出。这是因为：

* 我们把数组定义为了 Exercise 类型，所以，在遍历其元素并使用元素属性时，编译器认为 elliptical 的类型为 Exercise, 就会优先选取 Exercise 扩展中的值
* 我们直接打印时，elliptical 的类型为 EllipticalWorkOut , 编译器会选择 EllipticalWorkOut 中定义的值

综上：

*  **对于这种重名属性，编译器会根据调用场景决定到底使用哪个值。** 
* 扩展的时候，一定要注意同名问题。


### 6、本章节的完整示例代码

```
import Cocoa

//1 、 定义协议
protocol Exercise:CustomStringConvertible{
    var name:String { get }
    var minutes:Double { get }
    var caloriesBurned:Double { get }
}

//2、定义结构体和实例
struct EllipticalWorkOut:Exercise{
    let name: String="椭圆机"
    let minutes: Double
    let caloriesBurned: Double
    let brand:String="这是一个奥迪牌的椭圆机"
    
}

struct TreadmillWorkOut:Exercise{
    let name: String="跑步机"
    let minutes: Double
    let caloriesBurned: Double
    
    //圈数，这是跑步机自己独有的属性
    let laps:Double
    //7、覆盖默认实现
    var description: String{
        return "覆盖Exercise的默认descriotion:这是一个跑步机呢"
    }
}

//注意，在创建实例时，如果属性被声明为常量并且有默认值，则不用再重新设置值。如果声明为变量，即便有默认值也需要重新赋值
var elliptical=EllipticalWorkOut(  minutes: 20, caloriesBurned: 300)
var treadmill=TreadmillWorkOut(minutes: 30, caloriesBurned: 400, laps: 10.0)

//3、计算每分钟卡路里消耗量的函数
//计算每分钟消耗的卡路里
func caloriesBurnedPerMinutes<E:Exercise>(for exercise:E)->Double{
    return exercise.caloriesBurned/exercise.minutes
}
print(caloriesBurnedPerMinutes(for: elliptical))
print(caloriesBurnedPerMinutes(for: treadmill))


//4、扩展协议属性
extension Exercise{
    //只能扩展计算属性
    var caloriesBurnedPerMinutes:Double {
        return caloriesBurned/minutes
    }
}

print(elliptical.caloriesBurnedPerMinutes)
print(treadmill.caloriesBurnedPerMinutes)


//5、使用where为协议的扩展增加限定
extension Sequence where Iterator.Element==Exercise{
    
    func getTotalCaloriesBurned() -> Double {
        var  totalCaloriesBurned:Double=0.0
        
        for item in self {
            totalCaloriesBurned += item.caloriesBurned
        }
        return totalCaloriesBurned
    }
}

//此处需要显示声明 exercise 的类型为 [Exercise],否则报错
var exerciseArr:[Exercise]=[treadmill,elliptical]
print(exerciseArr.getTotalCaloriesBurned())

//6、用协议扩展提供默认实现
extension Exercise{
    var description:String{
        return "用协议扩展提供默认实现：运动名称-\(name)"
    }
}
print(treadmill.description)
print(elliptical.description)


//8、属性重名问题——协议扩展属性与符合协议的类型具有同名属性的问题
extension Exercise{
    var brand:String{
        return "啥牌子不重要，好使才是硬道理"
    }
}

for exercise in exerciseArr {
    print(exercise.brand)
}

print(elliptical.brand)
```

---

## 二十四、内存管理和ARC

所有的计算机程序都会使用内存。

大部分计算机程序会动态使用内存:程序在运行时动态分配和释放内存

Swift在管理内存时没有使用垃圾回收器，而是使用了引用计数(reference count)系统。



### 1、内存分配

值类型(枚举和结构体)的内存分配和管理很简单。新建值类型的实例时，系统会自动为实例划分内存空间。任何传递实例的动作，包括传递给函数以及存储到属性中，都会创建实例的副本。当实例不再存在时，Swift会主动回收内存。我们不需要手动的管理值类型的内存。

对于引用类型(特别是类)的内存管理，新建类的实例时，系统会为实例分配内存。当我们把类实例传递给函数或存储到属性中时，会创建一个对内存空间引用，而不是复制实例本身。对同一块内存有多个引用意味着，只要任意一个引用修改了类实例，所有的引用就都能看到变化。

Swift为每个类实例维护一个引用计数(reference count)。 这是对类实例所在内存空间的引用数量。只要引用计数大于0，实例就会存活。一旦引用计数变成0， 实例就会被回收，deinit方法运行。

2011􏰨，苹果为Objective-C 引入了 自动引用计数(automatic reference counting，ARC)。在 ARC下，编译器负责分析代码并在所有合适的位置引用和释放引用。Swift也是在ARC的基础上构建的。你不需要做任何事情来管理类实例的引用计数——编译器会帮你做。

### 2、循环强引用

**默认情况下，所有的引用都是强引用 (strong reference)，意味着它们会增加被引用实例的引用计数** 

#### (1)、定义Person类并引用

* Person.swift

```
import Foundation

class Person:CustomStringConvertible{
    var name:String
    var description: String{
        return "这个人的名字为\(name)"
    }
    
    init(name:String) {
        self.name=name
    }
    
    deinit {
        print("Person的引用被销毁了")
    }
}
```
* main.swift中引用

```
import Foundation

var person:Person?=Person(name:"张三")
print("创建了Person的实例——有了引用")

person=nil
print("Person赋值nil——引用被清空")
```

输出结果：

```
创建了Person的实例——有了引用
Person的引用被销毁了
Person赋值nil——引用被清空
Program ended with exit code: 0
```

#### (2)、定义Asset类并引用

* Asset.swift

```
import Foundation

class Asset: CustomStringConvertible {
    let name:String
    let value:Double
    var owner:Person?
    
    var description: String{
        if let assetOwner=owner{
            return "资产名称为：\(name), 价值：\(value)$ , 所有人：\(assetOwner.name)"
        }else{
            return "资产名称为：\(name), 价值：\(value)$ , 是公共财产"
        }
    }
    
    //因为owner可空，所以，初始化方法中可以不要求
    init(name :String,value:Double) {
        self.name=name
        self.value=value
    }
    
    deinit {
        //此处的self 指向的是 description
        print("\(self) 正在被 deallocated——释放")
    }
}
```

* main.swift中引用

```
var car:Asset?=Asset(name: "奥迪汽车", value: 500000)
var computer:Asset?=Asset(name: "电脑", value: 6666)
var desk:Asset?=Asset(name: "办公桌", value: 1000)

car=nil
computer=nil
desk=nil
```

* 输出结果

```
资产名称为：奥迪汽车, 价值：500000.0$ , 是公共财产 正在被 deallocated——释放
资产名称为：电脑, 价值：6666.0$ , 是公共财产 正在被 deallocated——释放
资产名称为：办公桌, 价值：1000.0$ , 是公共财产 正在被 deallocated——释放
```

#### (3)、让Person实现对Asset的强引用

 **假设 A 实例中强引用了 B 实例，B 实例中又强引用了 A实例，这种，就叫做循环强引用** 

* 修改Person

```
class Person:CustomStringConvertible{
    
    var assetArr=[Asset]()
    var name:String
    var description: String{
        return "这个人的名字为\(name)"
    }
    
    init(name:String) {
        self.name=name
    }
    
    deinit {
        print("Person的引用被销毁了")
    }
    
    func takeOwnerShip(_ asset:Asset){
        
        //此时，asset 实例持有了对 Person 实例的引用
        asset.owner=self
        //Person 实例又通过 数组 持有了 Asset实例的引用。
        assetArr.append(asset)
    }
}
```

* main.swift中调用

```
import Foundation

var person:Person?=Person(name:"张三")
print("创建了Person的实例——有了引用")

var car:Asset?=Asset(name: "奥迪汽车", value: 500000)
var computer:Asset?=Asset(name: "电脑", value: 6666)
print("创建了Asset的实例——创建引用")

person?.takeOwnerShip(car!)
person?.takeOwnerShip(computer!)

car=nil
computer=nil
person=nil

```

* 输出结果：

```
创建了Person的实例——有了引用
创建了Asset的实例——创建引用
```

上述示例代码中：

Person实例的 makeOwnerShip()方法中，让 Person 实例通过 数组持有了Asset 的实例，同时让传入的Asset持有了 Person实例。
 

根据上面的输出结果可知，我们虽然把三个实例都置为了 nil , 但是这三个实例都没有走 deinit() 函数，它们的引用依旧在内存中。这样，就形成了内存泄漏

为什么会这样呢？因为变量名 person 、car、computer 也都各自持有对应实例的一份引用，当把这三个变量的值置为 nil 时，只是清除了变量名对实例的引用。但是 person——car, person——computer 之间的引用依旧存在。

下图是直接从原书中截取的，虽然和上面示例代码中的变量名不一致，但是，他们的引用逻辑是一样的。

置为nil前，他们的引用关系为：

![](https://images.gitee.com/uploads/images/2019/0124/183745_55101b89_930142.png "屏幕截图.png")


置为nil后，他们的引用关系为：

![输入图片说明](https://images.gitee.com/uploads/images/2019/0124/183806_c58cafa7_930142.png "屏幕截图.png")



### 3、用week打破循环强引用

在PC中，内存泄漏的影响可能没有那么明显。但是，在手机中，内存泄漏达到一定程度后可能会导致程序的崩溃。所以，我们要想办法解决内存泄漏问题。

在 Swift 中，弱引用(weak reference) 可以用来解决强引用导致的内存泄漏问题。

* 弱引用使用 weak 前缀关键字修饰
* 弱引用必须使用 var 修饰——不然怎么赋nil值？
* 弱引用必须是可空的——不然怎么置为nil销毁？
* 弱引用不增加被引用实例的引用计数


#### (1)、修改Asset类

将 Asset 对 Person 的引用改为弱引用

```
class Asset: CustomStringConvertible {
    let name:String
    let value:Double
    //资产可能不属于某个人，所以，可以定义为弱引用。弱引用使用 weak 修饰
    weak var owner:Person?
    
    ...
}
```
main.swift 中的内容不做修改，依旧和上一节循环强引用中的处理一样。此时，运行，会输出如下内容

```
创建了Person的实例——有了引用
创建了Asset的实例——创建引用
Person的引用被销毁了
资产名称为：奥迪汽车, 价值：500000.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
资产名称为：电脑, 价值：6666.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
Person赋值nil——引用被清空
Program ended with exit code: 0
```
根据上面的输出结果我们可以看出，将 Asset 对 Person 的引用改为 weak 弱引用之后，将实例置为 nil 后，都走了 deinit() 函数——也就是说，引用计数为0，实例被彻底销毁了。 


### 4、闭包中的循环引用



#### (1)、添加一个会计类来记录Person的净资产

```
import Foundation

class Accountant {
    //为闭包类型声明一个别名
    typealias NetWorthChanged = (Double) -> Void
   
    //可空的闭包类型
    var netWorthChangedHandler: NetWorthChanged? = nil
    
    var netWorth: Double = 0.0 {
        didSet {
            netWorthChangedHandler?(netWorth)
        }
    }
    
    //计算净资产总值的方法
    func gained(_ asset: Asset) {
        netWorth += asset.value
    }
   
    deinit{
        print("Accountant的引用全部被销毁了")
    }
}
```

#### (2)、为Person配置一个会计

* 修改 Person 代码

```
import Foundation

class Person:CustomStringConvertible{
    
    var assetArr=[Asset]()
    var name:String
    var description: String{
        return "这个人的名字为\(name)"
    }
    var accountant=Accountant()
    
    init(name:String) {
        self.name=name
        
        accountant.netWorthChangedHandler={
            //因为 netWorthChangedHandler 对应的闭包只接受一个参数，所以，此处使用默认参数
            self.netWorthDidChange(to:$0)
            
            //因为 netWorthChangedHandler 的类型为没有返回值的闭包，所以，此处需要添加return
            return
        }
    }
    
    deinit {
        print("Person的引用被销毁了")
    }
    
    func takeOwnerShip(_ asset:Asset){
        
        //此时，asset 实例持有了对 Person 实例的引用
        asset.owner=self
        //Person 实例又通过 数组 持有了 Asset实例的引用。
        assetArr.append(asset)
        
        accountant.gained(asset)
    }

    func netWorthDidChange(to netWorth: Double) {
        print("Person,净资产总值发生变更：the net worth of \(self) is now \(netWorth)")
    }
}
```

上述代码中，

* 首先是通过 `var accountant=Accountant()` 声明了一个属性，这样，Person实例就持有了对Accountant 实例的引用。
* 然后在 takeOwnerShip() 函数中新增了 `accountant.gained(asset)` ,也就是说，当Person实例获取到新的资产之后，会计算一次新的资产总值
* 而在 Accountant 类中， gained(）函数中改变了其属性 networth 的值。由于 networth 属性调用了属性观察者，所以当 networth 的值发生变化时，会触发属性观察者中的 `netWorthChangedHandler?(netWorth)` 闭包。
* 上面这个闭包从哪里赋值的呢？就是在 Person 的 init 函数中新增的 `accountant.netWorthChangedHandler={}` 。这段代码的大括号中又调用了 Person 类中新增的 `netWorthDidChange（)`。这样，Accountant实例又间接的持有了Person的强引用——一个闭包循环强引用就形成了！！

#### (3)、闭包循环引用的输出

接下来，我们不修改 main.swift 中文件。依旧保持下面的样子：

```
import Foundation

var person:Person?=Person(name:"张三")
print("创建了Person的实例——有了引用")

var car:Asset?=Asset(name: "奥迪汽车", value: 500000)
var computer:Asset?=Asset(name: "电脑", value: 6666)
print("创建了Asset的实例——创建引用")

person?.takeOwnerShip(car!)
person?.takeOwnerShip(computer!)

car=nil
computer=nil

person=nil
print("Person赋值nil——引用被清空")
```
我们运行程序之后会得到如下输出：

```
创建了Person的实例——有了引用
创建了Asset的实例——创建引用
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 500000.0
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 506666.0
Person赋值nil——引用被清空
Program ended with exit code: 0
```
瞧，Person 、Asset 、Accountant 实例都没有走 deinit(）函数，它们的引用依旧存在——又产生内存泄漏了

#### (4)、使用捕获列表实现闭包中的弱引用

捕获列表（capture list），格式 `[weak xxx]` 这样，闭包中的 xxx 就变成了弱引用的。

* 修改Person的init函数

```
init(name:String) {
   self.name=name
        
   accountant.netWorthChangedHandler={
    
       [weak self] networth in
       self?.netWorthDidChange(to:networth)
            
       // 闭包中使用捕获列表实现弱引用时，不能使用默认参数。下面这种样子会报错
       //  [weak self]
       // self?.netWorthDidChange(to:$0)
            
       //因为 netWorthChangedHandler 的类型为没有返回值的闭包，所以，此处需要添加return
       return
    }
}
```

好了，现在再去运行一下程序：

```
创建了Person的实例——有了引用
创建了Asset的实例——创建引用
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 500000.0
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 506666.0
Person的引用被销毁了
资产名称为：奥迪汽车, 价值：500000.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
资产名称为：电脑, 价值：6666.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
Accountant的引用全部被销毁了
Person赋值nil——引用被清空
Program ended with exit code: 0
```
引用全都被销毁了，内存泄漏不存在了。。。


### 5、逃逸闭包和非逃逸闭包

Swift还支持不可能产生循环强引用的闭包。这类闭包被称为  **非逃逸闭包(non-escaping closure)** ，不需要显式调用self。

* 以 **函数参数形式声明的闭包默认是非逃逸** 的;其它场景中的闭包是逃逸的，比如上一节中 Accountant 中 netWorthChangedHandler这样的属性。

逃逸(escaping)是什么意思? 逃逸表示传递给一个函数的闭包可能会在该函数返回后被调用。也就是说，闭包在接收它作为参数的函数的作用域外部了，这个作用域已经对闭包没有限制作用了。如果闭包是非逃逸的，编译器就能知道它不可能在函数返回后被调用，所以不可能产生循环强引用。

#### (1)、非逃逸闭包的应用

在Accountant 类的 gained（) 中我们新增一个参数，这个参数为闭包——这是一个非逃逸闭包

```
class Accountant {
    typealias NetWorthChanged = (Double) -> Void
    var netWorthChangedHandler: NetWorthChanged? = nil
    
    //净资产值
    var netWorth: Double = 0.0 {
        didSet {
            netWorthChangedHandler?(netWorth)
        }
    }
    
    //计算净资产总之
    func gained(_ asset: Asset,completion:()->Void) {
        netWorth += asset.value
        completion()
    }
    
    deinit{
        print("Accountant的引用全部被销毁了")
    }
}
```
修改Person类中的 makeOwnerShip(）

```
  func takeOwnerShip(_ asset:Asset){
        // asset.owner=self
        // assetArr.append(asset)
        
        //非逃逸闭包。
        accountant.gained(asset){
            asset.owner=self
            //下面这个self可以省略——非逃逸闭包可以隐式调用self 的属性和方法
            self.assetArr.append(asset)
        }
    }
```

输出结果：

```
创建了Person的实例——有了引用
创建了Asset的实例——创建引用
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 500000.0
Person,净资产总值发生变更：the net worth of 这个人的名字为张三 is now 506666.0
Person的引用被销毁了
资产名称为：奥迪汽车, 价值：500000.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
资产名称为：电脑, 价值：6666.0$ , 是公共财产 正在被 deallocated——释放|引用销毁
Accountant的引用全部被销毁了
Person赋值nil——引用被清空
Program ended with exit code: 0
```
相关实例也是全都被销毁了

综上：

 **非逃逸闭包 和 捕获列表 的方式都可以解决闭包中的循环强引用问题。** 


#### (2)、强制启用逃逸闭包——@escaping

强制启用逃逸闭包的关键字——@escaping

```
class Person: CustomStringConvertible {
         ...
    func useNetWorthChangedHandler(handler: @escaping (Double) -> Void) { 
        //netWorthChangedHandler 是逃逸闭包，将非逃逸闭包赋值给逃逸闭包会报错，
        // 所以，使用@escaping 强制启用逃逸闭包
        accountant.netWorthChangedHandler = handler
    } 
}
```

main.swift中调用

```
//必须在 takeOwnerShip 之前调用，因为 Person 的 takeOwnerShip() 会触发 Accountant 的 gained(), gained() 触发前如果 Accountant 的 netWorthChangedHandler 没有值，那么，就不会触发这里的打印语句。
person?.useNetWorthChangedHandler{
    _ in
    print("将非逃逸闭包使用 @escaping 改为可逃逸")
}

person?.takeOwnerShip(car!)
person?.takeOwnerShip(computer!)
```

### 6、深入学习：我能获取实例的引用计数么？

Swift没有开放对任何实例的实际引用计数的访问——无法获取实例的引用计数

---

## 二十五、Equatable 和 Comparable

Swift为测试相等性和可比性提供两个协议:Equatable和Comparable

### 1、符合 Equatable

值相等协议。

#### (1)、符合 Equatable

符合 Equatable 协议之后，需要自己去 **实现静态函数 == ** 

```
import Cocoa

struct Point: Equatable {
    let x: Int
    let y: Int
    
    //符合 Equatable 协议之后，需要自己去实现静态函数 == 
    static func ==(lhs: Point, rhs: Point) -> Bool {
        return (lhs.x == rhs.x) && (lhs.y == rhs.y)
    }
}
let a = Point(x: 3, y: 4)
let b = Point(x: 3, y: 4)
let abEqual = (a == b)
print(abEqual)
```

#### (2)、中缀运算符

== 就是个中缀运算符。

#### (3)、方法”买一增一“
Swift标准库基于 == 的定义提供了一个 != 实现。一但一个类型通过实现自己的 == 符合了 Equatable，也就自动的拥有了一个能工作的 != 函数。

```
import Cocoa

struct Point: Equatable {
    let x: Int
    let y: Int
    
    //符合 Equatable 协议之后，需要自己去实现静态函数 ==
    static func ==(lhs: Point, rhs: Point) -> Bool {
        return (lhs.x == rhs.x) && (lhs.y == rhs.y)
    }
}
let a = Point(x: 3, y: 4)
let b = Point(x: 3, y: 4)
let abEqual = (a == b)
print(abEqual)
let aNotEqualb = a != b
print(aNotEqualb)
```

### 2、符合Comparable

比较协议。

* 符合该协议时必须实现 中缀比较运算 `<`
* Comparable 继承自 Equatable 协议，所以只要实现了 < ，就自动拥有了其他一些列中缀符

```
struct Point: Comparable {
    
    let x: Int
    let y: Int

    static func < (lhs: Point, rhs: Point) -> Bool {
        return lhs.x<rhs.x && lhs.y<rhs.y
    }
}

let a = Point(x: 3, y: 4)
let b = Point(x: 3, y: 4)

a<b
a>b
a==b
a>=b
a<=b
```

> 原书第三节是：继承Comparable ，讲的是 Comparable继承自Equatable，所以，当某个类型符合Comparable 时必须同时实现 == 和 < 。但在 Swift 4.2.1 环境下，只需要实现一个 < 就够了。也就是上面示例代码中的样子。 

### 3、深入学习：自定义运算符

Swift允许开发者创建自定􏲠运算符

接下来，我们创建自定义运算符用来表示两个 Person结婚了

#### (1)、创建类并声明实例

```
class Person {
    var name: String
    //spouse 表示配偶，可空
    weak var spouse: Person?
    init(name: String, spouse: Person?) {
        self.name = name
        self.spouse = spouse
    }
}
let matt = Person(name: "Matt", spouse: nil)
let drew = Person(name: "Drew", spouse: nil)
```


#### (2)、自定义中缀运算符

* 基本格式 `infix operator 运算符名称`
* 然后定义一个以 运算符名称 为名称的函数，并给出实现。

```
class Person {
    var name: String
    weak var spouse: Person?
    init(name: String, spouse: Person?) {
        self.name = name
        self.spouse = spouse
    }
}

let matt = Person(name: "Matt", spouse: nil)
let drew = Person(name: "Drew", spouse: nil)

infix operator +++
func +++(lhs: Person, rhs: Person) {
    lhs.spouse = rhs
    rhs.spouse = lhs
}
```


#### (3)、自定义运算符解析


+++没有指定定precedencegroup，也就是说使用DefaultPrecedence组。

```
precedencegroup DefaultPrecedence {
    higherThan: TernaryPrecedence
}
```
那么，上面代码中的 higherThan 是指什么?

higherThan 定义了运算符相对另一个  **precedencegroup**  的优先级。

在本例中，DefaultPrecedence 的优先级比另一个叫作 TernaryPrecedence 的 precedencegroup高。

TernaryPrecedence 是三目运算符的 precedencegroup。也就是说我们自定义的 +++ 优先级高于三目运算

precedencegroups 提供了一些定义自定义运算符的其他选项。除了 higherThan，还有一个重要的选项是 **associativity** 。

associativity 定义了同一优先级里的运算符如何结合。它接受 left 或 right。比如，运算符 + 和 - 属于同一个 precedencegroup(AdditionPrecedence)，所以优先先相同，两者都是 左 结合的。

> 出于代码的可读性等因素，尽量避免创建自定义运算符

---

> 后面章节的内容为  **事件驱动的应用** 。将开发第一个Mac应用 和 IOS应用，并了解与OC的交互

## 二十六、第一个Cocoa 应用

桌面Mac应用的原生API——Cocoa

### 1、开始创建 VocalTextEdit

File -> New -> Project (或者在欢迎窗口点击 Create a new Xcode project),然后出现下图：

![](https://images.gitee.com/uploads/images/2019/0125/084929_3e11d743_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0125/085316_dfe2145f_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0125/090355_a5e42869_930142.png "屏幕截图.png")

### 2、MVC

模型-视图-控制器(model-view-controller，MVC)是一种设计模式

* 模型： 负责保存和管理数据
* 视图： 最终绘制出来展示给用户的界面
* 控制： 模型和视图之间沟通的桥梁

### 3、设置视图控制器

在 ViewController 中添加两个方法和一个属性。

```
import Cocoa

class ViewController: NSViewController {

    @IBOutlet var textView:NSTextView!
    
    @IBAction func speakButtonClicked(_ sender: NSButton) {
        print("The speak button was clicked")
    }
    @IBAction func stopButtonClicked(_ sender: NSButton) {
        print("The stop button was clicked")
    }
}
```
上述代码中：

`@IBOutlet` 和 `@IBAction` 中的IB表示 `Interface Builder` 。很久以前，苹果公司为开发应用提供了两个工具:用来写源代码的 Xcode 和 用来布局用用户界面的Interface Builder。 Interface Builder 后来被合并进了 Xcode，但是程序员以及苹果还是把 Xcode 中跟“用户界面布局” 有关的部分叫作 Interface Builder。

`@IBOutlet` 和 `@IBAction` 是标记。这里的 `@IBOutlet` 和 `@IBAction` 不会以任何有意义的方式改变代码，而是让 Xcode 知道被标记的属性和方法跟 Interface Builder 有关。

`@IBOutlet` 告诉 Xcode， 属性 textView 会在 Interface Builder 中被设置;

而 `@IBAction` 告诉 Interface Builder，这个方法会在用户跟视图图交互时(比如点击按钮)被调用。


### 4、在 Interface Builder 中设置视图

在 Xcode 左侧的工程导航区中选择 `Main.storyboard`。故事板文件以可视化的方式用 Interface Builder 布局应用的窗口和视图。大型应用可以使用多个故事板。

故事板界面如下

![](https://images.gitee.com/uploads/images/2019/0125/110905_af329fea_930142.png "屏幕截图.png")


#### (1)、添加朗读和停止按钮


通过拖拽的方式添加一个button（以下内容基于Xcode 10.1 ）

* 点击下图中的 图标对象库 按钮

![](https://images.gitee.com/uploads/images/2019/0125/113255_1bbd365a_930142.png "屏幕截图.png")

* 接着弹出如下界面

![](https://images.gitee.com/uploads/images/2019/0125/113514_828510da_930142.png "屏幕截图.png")


* 搜索button

![](https://images.gitee.com/uploads/images/2019/0125/113801_cc0aae83_930142.png "屏幕截图.png")


* 选择一个按钮拖拽到ViewController中

注意：

A: 搜索界面中，那个黑色的只能放到标题栏位置，无法放到ViewController中。

B: 向下图中这个样子，控件 **显示了边线和小方块的状态下，按 delete 键可以直接删除** 。

![](https://images.gitee.com/uploads/images/2019/0125/113904_2ece965f_930142.png "屏幕截图.png")

* 再拖一个进来，并改名

将按钮拖进来之后，双击按钮可以修改按钮的文本内容

![](https://images.gitee.com/uploads/images/2019/0125/114407_a605b08b_930142.png "屏幕截图.png")

#### (2)、添加文本视图

点击图标对象库，然后搜索 textview , 然后拖一个文本框到界面中

![](https://images.gitee.com/uploads/images/2019/0125/115425_1325adb1_930142.png "屏幕截图.png")

调整好文本框位置之后，直接运行，会得到下图：

![](https://images.gitee.com/uploads/images/2019/0125/115642_1b66ac95_930142.png "屏幕截图.png")

一个简易的文本编辑器就可以使用了，我们可以在直接输入内容了。

但是，工具栏中默认提供的保存等操作并不能使用，如下图：

![](https://images.gitee.com/uploads/images/2019/0125/120439_6a4bec24_930142.png "屏幕截图.png")

还有就是，我们在拖拽 TextView 的位置时，甚至可以超出界面的范围：

![](https://images.gitee.com/uploads/images/2019/0125/120702_dae57590_930142.png "屏幕截图.png")

#### (3)、自动布局

##### A: 约束 Speak 按钮的位置

首先，创建约束强制 Speak 按钮处于文档窗口的右上角。

按住 Control 键，点击 Speak按钮，然后向右上方拖动，直到视图控制器的视图变成蓝色高亮状态。

![](https://images.gitee.com/uploads/images/2019/0125/121452_871d606d_930142.png "屏幕截图.png")

放开鼠标后，会弹出一个上下文菜单。我们要约束按钮的右边和上边，所以按住 Shift 键 并点击 `Trailing Space to Container` 和 `Top Space to Container`。

![](https://images.gitee.com/uploads/images/2019/0125/121651_c36c3450_930142.png "屏幕截图.png")

在从左至右阅读的语言中，"结束边缘"(trailing edge)是视图的右边界。


按下回车键，会看到一条蓝色的工字形线连接了 Speak按钮 的顶部和视图控制器图的顶部， 另一条则连接了右边。

![](https://images.gitee.com/uploads/images/2019/0125/141138_4b7f6101_930142.png "屏幕截图.png")

再次运行程序，尝试调整窗口大小。现在我们调整窗口大小的时候 Speak按钮 就会固定停留在正确的位置(右上角)了。

##### B: 约束 Stop 按钮的位置

不过 Stop按钮  和 文本视图 还是不对，需要给它们也加上约束。

为 Stop按钮 添加加相对于 Speak按钮 的约束，让它固定显示在 Speak按钮 的左边。

要在 Stop按钮 和 Speak按钮 之间创建约束，需要 按住Control键 并拖动 Stop按钮 到 Speak按钮上。 出现弹出菜单后，按住Shift键并选择 Horizontal Spacing 和 First Baseline ，然后按下回车键。

![](https://images.gitee.com/uploads/images/2019/0125/141430_8e96650f_930142.png "屏幕截图.png")

􏴨􏳷`Horizontal Spacing` 确保两个按钮之间的水平间距是固定的。`Baseline` 确保两个按钮在垂直方向上是按照基准按钮（Speak 按钮）中文字的基线对齐的。再次运行程序，此时无论窗口怎么调整，Stop 按钮 和 Speak 按钮固定的显示在左上角了。

![km3rq0.png](https://images.gitee.com/uploads/images/2019/0125/151526_58758d83_930142.png)

![km32iF.png](https://images.gitee.com/uploads/images/2019/0125/151527_fd0ab286_930142.png)

##### C： 调整文本视图

前面两个按钮都是在 ViewController 中配置的约束，但是，我们也可以直接从左侧的大纲中配置约束。

按住 Control 键，将 ScrollView - TextView 向它的父级 View 拖动（下图使用的原书图片，这个地方不好截图。 ）

![km3Xzd.md.png](https://images.gitee.com/uploads/images/2019/0125/151527_22dd57b6_930142.png)

然后按住 Shift键 在弹出的菜单中，选择三个约束:`Leading Space to Container`、`Trailing Space to Container`和`Bottom Space to Container`。这三个条件会约束TextView的左边、右边和下边分别于窗口的三个边对齐。

![kmJ7d0.png](https://images.gitee.com/uploads/images/2019/0125/151527_e15984ef_930142.png)

其实，我们添加了这些约束条件之后，在左侧文档大纲位置会多出一个 `Constraints` ,其中包含了我们当前为各个View添加的约束条件。而右侧 ViewController 中则使用蓝色工字线标记的约束的具体位置和状态。

![kmJXz4.png](https://images.gitee.com/uploads/images/2019/0125/151527_86ff380a_930142.png)

最后，我们在为文本框添加一个相对于 Speak 按钮的约束——让文本框固定在按钮下方。

按住 control 键，拖动 ScrollView-TextView 到 Speak 按钮，然后选择 `vertical spacing`。限定两者之间的垂直距离

![kmJzLR.png](https://images.gitee.com/uploads/images/2019/0125/151526_de361950_930142.png)

**注意：**

在调整 TextView 的大小时，不能让它超出界面，否则，运行之后，光标实际在界面外部。

### 5、连接

Interface Builder 既能创建视图和布局界面，也能连接视图和Swift代码。

#### (1)、为按钮设置 目标/动作 对

在运行应用并且加载 Main.storyboard 之后，Cocoa 运行时会创建一个 ViewController 的实例，配置好视􏱴图，设置好出口(outlet)和动作。对于 VocalTextEdit 来说，我们􏴟􏴸希望对􏲄管理当前􏰰文档􏰲的ViewController 实例调用 speakButtonClicked(_:) 方法。

按住 􏱏􏲷Control 键，从 􏰦Speak􏱏􏱐􏳡按钮 拖动到表示 ViewController 的图􏳀标上

![kmNdRU.png](https://images.gitee.com/uploads/images/2019/0131/201350_d8eb1080_930142.png)

放开􏴜􏳀后可以看到一个􏲸弹出菜􏳒单。这个菜􏳒单显示了 􏱠􏱡Speak按钮 被点击􏱏􏱐时所有可能发生的动作。 从弹􏰦􏲸出菜􏳒单中的 Received Actions 区􏱢中选择 `speakButtonClicked(_:)` 动作。这样， 我们就创建了一个`目标-动作对（target-action pair）`

![kmUpes.png](https://images.gitee.com/uploads/images/2019/0131/201350_160c1753_930142.png)

目􏳀􏱳动作对把目标􏳀(比如某个类型的实例)和对目􏳀标调用的动作(比如一个方法)关联􏴻起来，通常用来响应用户􏲑􏱀的动作(比如􏱠􏱡􏱏点击按钮􏱐)。当用户点击􏱀 􏱠􏱡Speak按钮 􏱏􏱐时，就会对 ViewController 调用`speakButtonClicked(_:)`方法。

重复这个过程，为Stop􏱏􏱐创建目􏳀􏱳动作对。按住 􏱏􏲷Control键，然后􏳡拖动 Stop按钮 􏱏􏱐到 ViewController图􏳀标上，在􏲸弹出􏳒单中选择 `stopButtonClicked(_:) `动作。

构建并运行应用，此时，当我们点击这两个按钮时，就会弹出两个方法中的需要输出的对应信息。

**注意：**

在设置 目标/动作对 时，既可以从 ViewController 中拖拽，也可以从文档大纲中拖拽。


#### (2)、连接文本视图出口

##### A: 连接出口

如果我们想要拿到输入框中的内容，就需要将 TextView 与 ViewController.swift 中定义的 `@IBOutlet` 关联起来。——这个操作就叫做 连接文本视图出口

􏱏􏲷按住 Control键 并􏳡 动􏱴图控制􏰩图标 􏳀到􏰰 文本视􏱴图上，然后选择弹出菜单中的 textView

![kmac8K.png](https://images.gitee.com/uploads/images/2019/0131/201351_717f6a05_930142.png)

接下来，我们修改 ViewController.swift 中的 speakButtonClicked() 方法，在其中将输入内容打印到控制台中。

```
@IBAction func speakButtonClicked(_ sender: NSButton) {
	print("The speak button was clicked, and will I should speak \(textView.string)")
}
```

上述代码中 `textView.string` 表示获取 输入的文本内容。

##### B：运行并查看输出结果 

在输入框中输入 ”你好，Swift“

![kmd5yF.png](https://images.gitee.com/uploads/images/2019/0131/201350_f825ffa8_930142.png)

输出结果为:

![kmdOW6.png](https://images.gitee.com/uploads/images/2019/0131/201351_66b9d28d_930142.png)


### 6、朗读文本

#### (1)、新增语音合成器——NSSpeechSynthesizer

Cocoa提􏰭供了一个 `NSSpeechSynthesizer` 类用来合成语音􏵎。

先在 ViewController.swift 􏲯中添加一个属性􏲊，类型是 `NSSpeechSynthesizer`

```
class ViewController: NSViewController {
    let speechSynthesizer = NSSpeechSynthesizer()
    ...
}
```

#### (2)、启动语音合成器

##### A: 实现朗读

`NSSpeechSynthesizer` 的默认空参􏵏初始化方法会创建一个使用􏵏认声􏵎音的语􏵎音合成器􏰩。语音合成器有一个 `startSpeaking(_:)` 方法，接收一个字符串参数，然后就可以将字符串内容朗读出来。`stopSpeaking()` 则用来终止朗读

修改 speakButtonClicked() 方法体：

```
//
//  ViewController.swift
//  Chapter26V2
//
//  Created by CnPeng on 2019/1/25.
//  Copyright © 2019 CnPeng. All rights reserved.
//

import Cocoa

class ViewController: NSViewController {
    let speechSynthesizer = NSSpeechSynthesizer()
    @IBOutlet var textView: NSTextView!
    
    @IBAction func speakButtonClicked(_ sender: NSButton) {
        var contents=textView.string
        //这一步操作是去除空格。因为swift中的isEmpty无法判断字符串是否为空格
        contents = contents.replacingOccurrences(of: " ", with: "")
        print("用户输入的内容为：\(contents)")
        
        //判断是否为空字符串——”“。
        if contents.isEmpty{
            speechSynthesizer.startSpeaking("你还没有输入文本呢")
        }else{
            speechSynthesizer.startSpeaking(contents)
        }
        
        //下面是原书的代码，但是，在Xcode10.1 + Swift 4 中会报错，
        //错误信息为：Initializer for conditional binding must have Optional type, not 'String'
        //这是因为 if let xxx = bbb{} 格式中，xxx 最终的值必须是可空的，
        //但textView.string 返回的直接是String——非空字符串，故此处不适用这种格式。
        //guard 语句也是同理
        //显示声明 contents 的类型为 String 后，不会报错，但是会报黄色提示。
        //        if let contents = textView.string{
        //                    speechSynthesizer.startSpeaking(contents)
        //                }else{
        //                    speechSynthesizer.startSpeaking("还没有输入内容")
        //                }
    }
    @IBAction func stopButtonClicked(_ sender: NSButton) {
         speechSynthesizer.stopSpeaking()
    }
}
```

##### B: 补充——Swift 字符串中空格的处理

String 字符串的 isEmpty 函数仅能判断是否为 ”“， 不能判断是否为空格。

Swfit中可以用来处理空格的函数：

```
// 替换
print("空格替换成-:", s.replacingOccurrences(of: " ", with: "-"))
// 过滤
print("空格过滤掉:", s.replacingOccurrences(of: " ", with: ""))
// 去首尾空格
print("去掉空格:", s.trimmingCharacters(in: .whitespaces))
```


### 7、保存和加载文档

前面虽然能获取输入内容并朗读内容，但是还不能保存。接下来，我们就看一下如何保存和加载文档。

先打开 `Document.swift` 看一下其中的内容：

````
class Document: NSDocument {

    override init() {
        super.init()
        // Add your subclass-specific initialization here.
    }

    override class var autosavesInPlace: Bool {
        return true
    }

    override func makeWindowControllers() {
        // Returns the Storyboard that contains your Document window.
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: nil)
        let windowController = storyboard.instantiateController(withIdentifier: NSStoryboard.SceneIdentifier("Document Window Controller")) as! NSWindowController
        self.addWindowController(windowController)
    }

    override func data(ofType typeName: String) throws -> Data {
        // Insert code here to write your document to data of the specified type, throwing an error in case of failure.
        // Alternatively, you could remove this method and override fileWrapper(ofType:), write(to:ofType:), or write(to:ofType:for:originalContentsURL:) instead.
        throw NSError(domain: NSOSStatusErrorDomain, code: unimpErr, userInfo: nil)
    }

    override func read(from data: Data, ofType typeName: String) throws {
        // Insert code here to read your document from the given data of the specified type, throwing an error in case of failure.
        // Alternatively, you could remove this method and override read(from:ofType:) instead.
        // If you do, you should also override isEntireFileLoaded to return false if the contents are lazily loaded.
        throw NSError(domain: NSOSStatusErrorDomain, code: unimpErr, userInfo: nil)
    }
}
````

在上面的内容中：

`autosavesInPlace()` 是 NSDocument 的类方法。返􏲶回 true ，就表示􏰰􏰲类支持用􏱀户随时输入􏱤、随时自动􏱙􏱚保存。

`makeWindowControllers()`会在创建新􏰰􏰲文档或打􏱁开就文档􏵯􏰰􏰲时被调用。它􏱼􏱽负责设置 NSWindowController ，这是一个􏲄管理文档窗口􏰲􏱂的类。该函数中的 `as!`是Swift的􏵰强制类型转换符 (type casting operator)。


#### (1)、类型转换

纯 Swift 中 类型转换使用的较少。通常使用协议和继承就可以。但与 Object-c 交互时会比较常用类型转换。

类型转换的关键字为: `as`, `as!`, `as?`


在 `makeWindowControllers()` 中第二行调用了 `instantiateController (withIdentifier:)`方法。注意，它的􏲶返回值是Any。

Any 是一个 Swift􏲚 协议，没有方法也没有􏲊属性:它可以表示任􏰔可能的类型。

`instantiateController(withIdentifier:)` 返􏲶回 Any 是因为􏳍故事板􏳎可能包含􏳗很多不同类型的控制􏰩器。为了利􏰛用返􏲶回的控制器􏰩，就需要把它转􏵈成实际类型:` NSWindowController`。在创建一个基于故事板􏳎的应用时，Xcode 会自动为创建的 NSWindoController 设置标􏳀识 Document Window Controller，并在这一行代码中自动􏵺插入同一个􏳀标识。

#### (2)、保存文档

`Document.swift` 文件中，末尾有两个函数：`read()`、`data()` 它们分别用来加载和保存文档。

接下里，我们在 `data()` 中实现保存的操作：

##### A: 获取 ViewController

先清除 data() 中原有的提示文本和 throws 语句，然后新增获取 viewController 的语句

```
override func data(ofType typeName: String) throws -> Data {
    let windowController = windowControllers[0]
    let viewController = windowController.contentViewController as! ViewController
    //...  
}
``` 

一􏲍来说，NSDocument 可能有多个窗􏱂口控制器􏰩。但在本例中，我们只创建了一个(在makeWindowControllers()中），所以通过 `windowControllers[0]` 就可以获取到这个窗口控制器。


一个 NSWindowController 可能有一个􏱴视图控制􏰩作为内容􏱓，也可能没有 —— Mac平􏳷台有􏱂窗口控制器历史􏶀􏶁比有􏱴视图控制器􏰩的历史久􏶀􏶁􏲾多了。按住 􏱏􏲷Option键 并点击 `􏱠􏱡contentViewController`，你会看到其类型是`NSViewController?`。窗􏱂口控制器􏰩可能没有内容􏱓视􏱴图控制器􏰩，此时这个属􏲊性就是 nil。如果 windowController 有内􏱓容视􏱴图控制器􏰩，编译器就会知道它要么是 NSViewController， 要么是NSViewController 的子类。

在这个应用中，只有一种 ViewController 类型; 不过对于大型应用，可能会有很多。用 as! 类型转􏵈􏵲换符可以告诉􏳄􏳅编译器􏰩:“我知道 Document窗􏱂口控制􏰩器的视图控制􏰩类型是 ViewController。”

有了􏱴图控制􏰩，就可以实现 data(ofType:) 的􏶂􏵮剩余部分了。

```
 enum Error: Swift.Error, LocalizedError {
        case UTF8Encoding
        
        var failureReason: String? {
            switch self {
                case .UTF8Encoding: return "File cannot be encoded in UTF-8."
            }
        }
    }
    
    override func data(ofType typeName: String) throws -> Data {
        let windowController = windowControllers[0]
        let viewController = windowController.contentViewController as! ViewController
        
        let contents = viewController.textView.string
        guard let data = contents.data(using: .utf8) else {
            throw Document.Error.UTF8Encoding
        }
        return data
    }
```

上述代码中：

我们先自定义了一个枚举类型的 Error, 它符合 Swift.Error, LocalizedError 协议。

LocalizedError 表示本地化错误，与国际化相对。

在 Error 中实现了 LocalizedError 中需要的 failureReason , 如果文件保存失败，就会将 failureReason 的内容以弹窗的形式展现。

`let contents = viewController.textView.string` 表示获取文本输入框中的内容。

`contents.data(using: .utf8)` 表示利用 data() 函数将字符串转成 Data。该方法要求制定字符串编码集。

好了，上面就是保存文档的全部内容了。

其实，保存时的步骤主要如下：

* 获取窗口控制器 WindowController —— windowControllers[0]
* 获取视图控制 ViewController —— windowController.contentViewController
* 获取输入的内容
* 使用 data() 函数将字符串内容转为 Data

> CnPeng 问题是，点击菜单栏中的保存之后，保存到哪里去了？根本找不到啊


#### (3)、加载文档

与保存文档时的步骤相反，我们在加载文档时：

* 先获取 Data 实例
* 然后从 Data 读取字符串内容
* 最后显示在视图控制器中。

注意：在加载文档内容时，必须先保证视图控制器已经初始化完毕，然后才去加载文档内容。

先定义一个全局变量用来存储读取到的文本内容

```
 var docContent = ""
```

将读取到的文本内容存储到预定义的变量中

```
 override func read(from data: Data, ofType typeName: String) throws {
        //从data中获取String，如果失败，抛出异常。
        guard let contents=String(data: data, encoding: .utf8) else {
            throw Error.UTF8Decoding
        }
        
        self.docContent = contents
    }
```

将文本内容显示在视图控制器中：

```java

 override func makeWindowControllers() {
        ...        
        
        //获取 viewController 并将文本内容显示在其中的 textView组件中
        let viewController=windowController.contentViewController as! ViewController
        viewController.textView.string = docContent
        
        self.addWindowController(windowController)
    }
```

完整示例：

```
class Document: NSDocument {

    var docContent = ""
    enum Error: Swift.Error, LocalizedError {
        case UTF8Encoding
        case UTF8Decoding
        
        var failureReason: String? {
            switch self {
            case .UTF8Encoding: return "File cannot be encoded in UTF-8."
            case .UTF8Decoding: return "File is not valid UTF-8."
            }
        }
    }

    override class var autosavesInPlace: Bool {
        return true
    }

    override func makeWindowControllers() {
        // Returns the Storyboard that contains your Document window.
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: nil)
        let windowController = storyboard.instantiateController(withIdentifier: NSStoryboard.SceneIdentifier("Document Window Controller")) as! NSWindowController
        
        //初始化viewController 并将文本内容显示在其中的 textView组件中
        let viewController=windowController.contentViewController as! ViewController
        viewController.textView.string = docContent
        
        self.addWindowController(windowController)
    }
    
    override func data(ofType typeName: String) throws -> Data {
        let windowController = windowControllers[0]
        let viewController = windowController.contentViewController as! ViewController
        
        //这里的 textView 指向我们在 ViewController.swift 中定义的 @IBOutlet var textView: NSTextView!
        let contents = viewController.textView.string
        guard let data = contents.data(using: .utf8) else {
            throw Document.Error.UTF8Encoding
        }
        return data
    }

    override func read(from data: Data, ofType typeName: String) throws {
        //从data中获取String，如果失败，抛出异常。
        guard let contents=String(data: data, encoding: .utf8) else {
            throw Error.UTF8Decoding
        }
        
        self.docContent = contents
    }
}
```


#### (4)、按照MVC模式整理代码

我们把 Document 用作 MVC 中的 M——模型类，在 MVC 模式中，它不应该关心内容显示在哪个视图中，也不应该做视图显示的处理逻辑。但是，我们在 data(）中的 `let contents = viewController.textView.string`和 makeWindowControllers（）中的 `viewController.textView.string = docContent
` 都是对视图显示的处理。

为了严格遵循 MVC，我们现在 ViewController.swift 中定义一个 变量，该变量的 set/get 函数处理视图的显示逻辑。

```
class ViewController: NSViewController {
    let speechSynthesizer = NSSpeechSynthesizer()
    @IBOutlet var textView: NSTextView!
    
    //定义变量用来处理视图显示
    var textContent:String{
        set{
            textView.string = textContent
        }
        
        get{
          return  textContent = textView.string
        }
    }
 	...
 }   
```

然后修改读取和保存的函数

```
 override func makeWindowControllers() {
        // Returns the Storyboard that contains your Document window.
        let storyboard = NSStoryboard(name: NSStoryboard.Name("Main"), bundle: nil)
        let windowController = storyboard.instantiateController(withIdentifier: NSStoryboard.SceneIdentifier("Document Window Controller")) as! NSWindowController
        
        let viewController=windowController.contentViewController as! ViewController
        //viewController.textView.string = docContent
        viewController.textContent = docContent
        
        self.addWindowController(windowController)
    }
    
    override func data(ofType typeName: String) throws -> Data {
        let windowController = windowControllers[0]
        let viewController = windowController.contentViewController as! ViewController
        
        //let contents = viewController.textView.string
        let contents = viewController.textContent
        guard let data = contents.data(using: .utf8) else {
            throw Document.Error.UTF8Encoding
        }
        return data
    }
```

## 二十七、第一个IOS应用

在本章中，我们会为 iPhone 创建一个名为 iTahDoodle 的iOS应用。iTahDoodle􏰿 允许用户􏱀创建待办􏷈事件􏷉列表

### 1、开始创建iTahDoodle

File → New → Project → SingleView 

![](https://images.gitee.com/uploads/images/2019/0131/201352_05cf3d06_930142.png)

![](https://images.gitee.com/uploads/images/2019/0131/201353_23cec153_930142.png)

然后选择保存目录。

### 2、布局用户界面

#### (1)、添加按钮和文本框

![](https://images.gitee.com/uploads/images/2019/0131/114532_831284cd_930142.png "屏幕截图.png")

在控件库（就是工具栏中那个铜钱图标）中搜索 button 然后在故事板中拖一个按钮在右上角，然后在属性面板中修改名称。

![](https://images.gitee.com/uploads/images/2019/0131/143139_8c856252_930142.png "屏幕截图.png")

在控件库中搜索 text field ，然后拖一个 Text控件 放在按钮左侧。

![](https://images.gitee.com/uploads/images/2019/0131/143438_4e024af5_930142.png "屏幕截图.png")

在左侧大纲栏中，按住 control 将，然后将 按钮 向它的父级 View 拖拽，然后按住 shift 键为其添加约束：

![](https://images.gitee.com/uploads/images/2019/0131/144037_7b3ee788_930142.png "屏幕截图.png")

为文本框添加相对于 按钮的约束：在 大纲栏 中按住 control ，将 文本框向 按钮 拖动，然后按住 shift 键添加约束。

![](https://images.gitee.com/uploads/images/2019/0131/144259_605de4c6_930142.png "屏幕截图.png")

此时，界面中出现了一个黄色叹号，点击之后展示的详细内容表示我们的约束还缺点东西：

![](https://images.gitee.com/uploads/images/2019/0131/144751_dd8a289d_930142.png "屏幕截图.png")

上图中的提示信息中的第二条表示我们的文本框还缺少左侧约束——为文本框添加相对于父级视图左侧的约束：

![](https://images.gitee.com/uploads/images/2019/0131/144929_d81dcc61_930142.png "屏幕截图.png")

此时再看，还是有黄色叹号，但是提示信息变了

![](https://images.gitee.com/uploads/images/2019/0131/145149_4bcfeffa_930142.png "屏幕截图.png")

ambiguous [æm'bɪgjʊəs] 表示模糊的，有分歧的。上面错误提示的意思是说，我们只约定了文本框在按钮的左侧，但并没有指明它们各自的尺寸有多大。

接下来，我们在尺寸面板中调整 按钮 在水平方向上布局的优先级：

![](https://images.gitee.com/uploads/images/2019/0131/150821_1f5abd0f_930142.png "屏幕截图.png")

上图中 `Content Hugging Priority` 表示控件的扩张优先级。默认都是 250 ，我们改成比 250 大的数值之后，就表示 按钮 的尺寸就这么大了，不需要随着手机尺寸的变更而变更。而文本框的优先级为250 ，则表示会随着手机尺寸的变更而动态变化。

此时，还是会有黄色叹号：

![](https://images.gitee.com/uploads/images/2019/0131/151223_3b0b0c8d_930142.png "屏幕截图.png")

解决这个黄色叹号只需要点击下图中的按钮即可。

![](https://images.gitee.com/uploads/images/2019/0131/151755_ec07e82c_930142.png "屏幕截图.png")


#### (2)、添加列表——TableView

IOS 中使用 UITableView类 来展示列表。接下来就使用故事板添加一个 TableView 

![](https://images.gitee.com/uploads/images/2019/0131/152308_80db8dd6_930142.png "屏幕截图.png")

添加一个 TableView 并调整其尺寸

![](https://images.gitee.com/uploads/images/2019/0131/201353_7d82eb80_930142.png)

添加相对于父级View的约束

![](https://images.gitee.com/uploads/images/2019/0131/201353_c895ca83_930142.png)

添加相对于文本框的约束

![](https://s2.ax1x.com/2019/01/31/k16bHs.png)

此时，我们运行程序，在模拟器中会看到下图的样子：

![](https://images.gitee.com/uploads/images/2019/0131/201355_f12965c9_930142.png)

#### (3)、连接用户界面

接下来为控件创建 出口 (IBOutlet) 和 动作（IBAction）.

##### A: 修改 ViewController.swift 文件

```
class ViewController: UIViewController {
    
    @IBOutlet var mTextField : UITextField!
    @IBOutlet var mTableView : UITableView!
    
    @IBAction func onInsertCilck( button : UIButton ){
        let itemDesc = mTextField.text ?? "空"
        print("按钮被点击了，此时文本内容为：\(itemDesc)")
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view, typically from a nib.
    }
}
```

##### B: 将 ViewController 中的变量与控件绑定

按住 control键，然后在大纲栏中 把 ViewController 向 文本框 拖拽，在弹出的菜单中选中 mTextField 

![](https://images.gitee.com/uploads/images/2019/0131/162455_ec102d34_930142.png "屏幕截图.png")

绑定 TableView

![](https://images.gitee.com/uploads/images/2019/0131/162800_17704aa7_930142.png "屏幕截图.png")

##### C: 绑定动作

按住 control 键，将 新建按钮 向 ViewController 拖拽，然后选择自定义的点击事件

![](https://images.gitee.com/uploads/images/2019/0131/162935_838d361b_930142.png "屏幕截图.png")

此时，构建应用，当我们点击新建按钮时，会在控制台中打印内容。

### 3、为待办事项列表建模

基于 MVC 结构，我们现在已经有了视图（故事板中创建的内容） 和 ViewController ，接下来需要创建 模型。

#### (1)、创建类

File → New → Cocoa Touch

![选择类型](https://images.gitee.com/uploads/images/2019/0131/201355_8c6925c4_930142.png)

![命名](https://images.gitee.com/uploads/images/2019/0131/201356_c5f1d9da_930142.png)

![存储位置](https://images.gitee.com/uploads/images/2019/0131/201358_8dce1201_930142.png)

创建完成之后，会自动打开我们创建的这个文件。

在 Swift 中，我们可以创建没有父􏲻类的类。但在 Objective-C 中，所有的类都需要有父􏲻类。

**NSObject** 被称为􏸰根类􏵰(root class)，它能提􏰭供一些基本的 Objective-C 运行时支持􏱘。我们让 TodoList 继承 􏱦􏵷NSObject 是因为要用它和 Cocoa Touch 类打交􏰍道，而 Cocoa Touch 类期􏴸望接􏲵Objective-C 对象

#### (2)、编辑类内容

定义一个数组和方法，数组用来存储条目内容，方法用来添加条目到数组

```
class TodoList: NSObject {
    fileprivate var items:[String] = []
    
    func add(_ item:String){
        items.append(item)
    }
}
```

􏱉这些条目数据需要展示在 UITableView 中，UITableView 有一个 `dataSource` 属􏲊性表示􏳣单元格的内容􏱓。`dataSource` 的取值需要符合 `UITableViewDataSource􏲚`协议。

该协议要求实现两个 tableView 函数: 一个返回Int,表示总的行数； 一个返回 UITableViewCell 表示单元格视图。

接下来，我们在 TodoList.swift 中用扩展的形式让 TodoList 实现该协议 , 并实现两个方法：

```
extension TodoList : UITableViewDataSource{
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        let item = items[indexPath.row]
        cell.textLabel!.text = item
        return cell
    }
}
```

现在我们来分析一下上面两个函数：

UITableView 支持􏱘一个表􏳣有多个表头，􏱉每个表头可以有0行或更多行。对 iTahDoodle 来说，只要用到一个表头，所以上面的方法􏲱􏲲了 section 􏰷数。我们􏲶在第一个 tableView 函数中直接返回 items.count 就􏳄􏳅表示每个代办事项􏷉独占一行。

在第二个函数中，

`tableView.dequeueReusableCell() ` 表示获取标识为 ”Cell“ 并且索引路径为 indexPath 的可复用单元格。IOS中使用复用池(reuse pool)来维护单元格。

`indexPath􏰷` 可以理解为需要展示的行视图的索引对象。它有 section 和 row 的属􏲊性。由于我们在列表中每行只展示一个item，所以，在获取条目内容时直接使用其 row 属性表示索引。


不是所有的 UITableViewCell 都有 textLabel，但是我们用的这个有。（CnPeng： 该怎么理解textLabel？而且，什么样的 Cell 没有这个 label 呢？）


### 4、设置UITableView

#### (1)、修改按钮的点击事件

在 `ViewController.swift` 中添加一个 TodoList的实例 作为􏲊属性，并修改按钮的点击事件：点击时，把内容添􏲯加到 TodoList 实例的 items 中。

```
class ViewController: UIViewController { 
  	...
  	
    let todoList = TodoList()
    
    @IBAction func onInsertCilck( button : UIButton ){
        let itemDesc = mTextField.text ?? "空"      
        todoList.add(itemDesc)
    }
	...
}
```

#### (2)、设置UITableView

为 UITableView 设置数据源时需要在 viewDidLoad() 函数中操作：

```
class ViewController: UIViewController {    
    ...
    override func viewDidLoad() {
        super.viewDidLoad()
        
        // nib 是构建应用时故事板被编译成的格式
        // Do any additional setup after loading the view, typically from a nib.
        
        mTableView.register(UITableViewCell.self, forCellReuseIdentifier: "Cell")
        mTableView.dataSource = todoList
    }
}
```

上述代码中，

register()函数 告知 TableView 创建单元格，并且单元格的标识为 ”Cell“。最后一行表示设置数据源。

#### (3)、通知列表更新数据

我们还需要做的事情就是，点击按钮新增条目之后，通知列表及时去更新视图。

```
@IBAction func onInsertCilck( button : UIButton ){
        let itemDesc = mTextField.text ?? "空"
        
        todoList.add(itemDesc)
        //通知列表更新数据
        mTableView.reloadData()
    }
```
现在运行一下就可以得到我们想要的效果了

#### (4)、完整代码

ViewController.swift

```
import UIKit

class ViewController: UIViewController {
    
    @IBOutlet var mTextField : UITextField!
    @IBOutlet var mTableView : UITableView!
    let todoList = TodoList()
    
    @IBAction func onInsertCilck( button : UIButton ){
        let itemDesc = mTextField.text ?? "空"
        
        todoList.add(itemDesc)
        mTableView.reloadData()
    }

    override func viewDidLoad() {
        super.viewDidLoad()
        
        // nib 是构建应用时故事板被编译成的格式
        // Do any additional setup after loading the view, typically from a nib.
        
        mTableView.register(UITableViewCell.self, forCellReuseIdentifier: "Cell")
        mTableView.dataSource = todoList
    }
}
```
TodoList.swift

```
class TodoList : NSObject {
    fileprivate var items:[String] = []
    
    func add(_ item:String){
        items.append(item)
    }
}


extension TodoList : UITableViewDataSource{
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        let item = items[indexPath.row]
        cell.textLabel!.text = item
        return cell
    }
}
```

### 5、保存和加载TodoList
#### (1)、保存TodoList

##### A: 获取文件存储路径

所有的iOS应用都在􏰂􏰃􏷟􏷠`应用沙盒`中运行。这意􏷡着一个应用看不见其􏰣应用创建的􏰰文件。􏷟沙盒的另一个副􏷢作用是，用来􏱙􏱚􏰰保存文件的目录可能会发生变化。因为要保存􏱙􏱚包含􏳗待办􏷈事件􏷉列表的文􏰰件，所以我们首先需要􏷣获知 iOS 在哪里保存文􏱙􏱚􏰰件——也就是下列代码中的 fileUrl

```
class TodoList : NSObject {
    fileprivate var items:[String] = []
    
    func add(_ item:String){
        items.append(item)
    }
    
    //获取文件存储路径
    private let fileUrl:URL={
        //获取当前应用沙盒中的文档存储路径的数组
        let documentUrls = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
        let documentUrl = documentUrls.first!
        //在取出的路径中添加后缀作为文件新的存储路径
       return documentUrl.appendingPathComponent("TodoList.items")
    }()
}
```

##### B: 保存

下面定义的 save() 方法中：先将 Array 转为 NSArray ，然后调用 NSArray 的 `write()` 函数将数组内容写出到指定目录中。`write()` 返回 布尔值表示保存成功或者失败。

```
class TodoList : NSObject {
    fileprivate var items:[String] = []
    
    func add(_ item:String){
        items.append(item)
        
        //执行保存操作
        save()
    }
    
    //获取文件存储路径
    private let mFileUrl:URL={
        //获取当前应用沙盒中的文档存储路径的数组
        let documentUrls = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
        let documentUrl = documentUrls.first!
        //在取出的路径中添加后缀作为文件新的存储路径
       return documentUrl.appendingPathComponent("TodoList.items")
    }()
    
    //定义保存的方法
    func save(){
        let itemsArray = items as NSArray
        if !itemsArray.write(to: mFileUrl, atomically: true){
            print("保存失败")
        }else{
            print("保存成功，保存路径为\(mFileUrl)")
        }
    }
}
```

#### (2)、加载TodoList

下面示例中,

定义了一个保存的函数 save(), 为 NSArray 构造传递一个地址表示加载对应URL地址中的内容，如果有内容转为字符串数组。

然后重写了 init() 函数，在该函数中执行加载本地文件的操作。也就是在初始化 TodoList 类的时候执行加载操作。

```
class TodoList : NSObject {
    ...
    //重写初始化函数，在初始化函数中加载本地数据
    override init() {
        super.init()
        load()
    }
    
    ...
    
    func load(){
        if let itemsArray = NSArray(contentsOf: mFileUrl) as? [String]{
            mItems=itemsArray
        }
    }
}
```

此时，我们重新运行程序，一个具有新建、保存、加载的代办小应用就完成了。（但是，如果输入文本较长的话，末尾会用省略号替代超出的内容。至于这种怎么优化，就需要更多的IOS知识了）

完整的 TodoList.swift 代码为：

```java

class TodoList : NSObject {
    fileprivate var mItems:[String] = []

    //获取文件存储路径
    private let mFileUrl:URL={
        //获取当前应用沙盒中的文档存储路径的数组
        let documentUrls = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask)
        let documentUrl = documentUrls.first!
        //在取出的路径中添加后缀作为文件新的存储路径
       return documentUrl.appendingPathComponent("TodoList.items")
    }()
    
    //重写初始化函数，在初始化函数中加载本地数据
    override init() {
        super.init()
        load()
    }
    
    //每新建一个代办就添加到数组中，并保存到本地
    func add(_ item:String){
        mItems.append(item)
        
        //执行保存操作
        save()
    }
    
    //定义保存的方法
    func save(){
        let itemsArray = mItems as NSArray
        if !itemsArray.write(to: mFileUrl, atomically: true){
            print("保存失败")
        }else{
            print("保存成功，保存路径为\(mFileUrl)")
        }
    }
    
    func load(){
        if let itemsArray = NSArray(contentsOf: mFileUrl) as? [String]{
            mItems=itemsArray
        }
    }
}


extension TodoList : UITableViewDataSource{
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return mItems.count
    }
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "Cell", for: indexPath)
        let item = mItems[indexPath.row]
        cell.textLabel!.text = item
        return cell
    }
}
```

### 6、练习题

* 新增一个代办之后清空输入框中的内容
* 输入框为空时不允许新建
* 删除代办

---

## 二十八、互操作

### 1、
#### (1)、

CnPeng: Object-c 和 Swift 之间的交互调用还没看，用到的时候再说吧




