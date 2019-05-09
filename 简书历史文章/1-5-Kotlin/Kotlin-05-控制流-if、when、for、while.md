##一、if 表达式
在 Kotlin 中，if 既可以作为普通的判断语句使用，也可以作为表达式使用。
**当 if 作为表达式使用时，本身就会有返回值，其效果等同于 java 中的三元运算。**


我们来看使用 if 语句 获取两个值中较大值的代码，如果按照 java 中的模式，我们应该这么写：

```
fun getMaxVal1(a: Int, b: Int) {
    var max: Int
    if (a > b) {
        max = a
    } else {
        max = b
    }
}
```

但是，我们已经知道了，在 kotlin 中，if 作为表达式时有返回值，效果等同于 java 中的三元运算，所以，我们获取两个值中较大值的代码就可以这么写：

```
fun getMaxVal2(a: Int, b: Int) {
    var max = if (a > b) a else b    //这种方式等同于 java 中的 三元运算
}
```
这样是不是感觉很清新？

>注意：
>* 作为表达式使用时，其性质等同于 java 中的三元运算，**必须要有 else  字段**

另外，作为表达式使用时，if 和 else 分支后面不仅能跟普通的值，也可以跟代码块。如：
```
 val max = if (a > b) {    //作为表达式使用时，我们跟了一段代码块，
        print("Choose a") 
        a
    } else {
        print("Choose b") 
        b
    }
```
向上面的这段代码中，我们将 if  作为表达式使用，并且在 if 和 else 分支后面跟了一段代码块，**这样，我们不但能将 较大的值赋值给 max , 还能在同时做其他的操作** -- 这里是打印了一句话。 


##二、When 表达式

###（一）、主要特点：
kotlin 中 ，When 的主要特点如下：
- When 类似于 java 中的 switch ，但是功能比 switch 更为强大。

- **When 既可以当做表达式使用，也可以当做语句使用。**
  - 当做语句使用时，效果等同于 java 中的 switch 
  - 当做表达式使用时，会有返回值，符合条件的分支的值就是整个表达式的值。
   - **将 when 当做表达式使用时, 必须要有 else 分支，
   - when 的各个分支不仅可以是常量，也可以是表达式**。

* **如果多个分支有相同的处理方式，则可以把多个分支条件放在一起，用逗号分隔。**（`这种方式类似于 java 中 switch 语句的穿透`）

###（二）、用法示例
好了，我们已经知道了 when 的主要特点，那么接下来就看下 when 的具体用法：

####示例1、when 的基本使用格式
```
fun main(arg: Array<String>) {
    whenFunc1(6)
}

fun whenFunc1(a: Int) {    //基本使用格式示例
    when (a) {
        1 -> println("传入的值是1")
        2 -> println("传入的值是2")
        else -> {
            println("传入的值既不是1 也不是2")
        }
    }
}
```

####示例2、多分支共用一种处理（分支穿透）
```
fun whenFunc2(a: Int) {
    when (a) {
        1, 2 -> println("传入的是1 或者是2")   //分支穿透
        else -> {
            println("传入的既不是1 也不是2")
        }
    }
}
```

####示例3、以表达式(而不只是常量)作为分支条件
```
fun whenFunc3(a: Int) {
    when (a) {
        sum(3, 3) -> print("a 的值是 ${sum(3, 3)}")    //以表达式作为分支，实际是以表达式的值作为分支条件
        else -> print("我哪里知道分支值是多少")
    }
}
```
在上面的代码中，我们 使用 Integer.sum(a,b) 方法作为 when 的分支条件，实际就是以 sum 的值作为分支条件。

####示例4、将When 作为 if..else if 使用
#####A : 检测某个值是否在区间或者集合中
- 判断某个值是否在 XXX 中的关键字 是 **in** 
- 判断某个值是否在 XXX 中的关键字 是 **!in** 

```
var nums=1..100    //声明一个 包含 1--100之间数值的区间，默认是闭区间
```

```
fun whenFunc4(a: Int) {
    when (a) {
        in 1..5 -> println("$a 在 1..5 的区间之内")
        !in 10..15 -> println("$a 不在 10..15 的区间之内")
        else -> println("$a 在 10..15 的区间之内")
    }
}
```
上面的代码中，我们演示了判断一个值是否在区间之内，当然也可以判断值是否在某个集合中，关于集合的内容后面会有讲解。

>- 区间 就是指一个数据范围，在高中数学中有相应介绍。
>  -  区间分为开区间 、闭区间 、半开区间 。
>   - 开区间的表示方式为 ( a , b ) , 表示该范围内的数据 自 a 开始 到 b 结束，但不包含  a 和 b
>   - 闭区间的表示方式为 [ a , b ] , 表示该范围内的数据 自 a 开始到 b 结束，包含 a 和 b
>   - 半开区间有两种方式：( a , b ] 和 [ a ,b ) 。前者表示不包含 a 但是 包含 b , 后者表示 包含  a 但不包含 b  

#####B : 检测某个值是否是某种类型
- 检测某个值是某种类型的关键字是 *is*
- 检测某个值不是某种类型的关键字是 “!is” 
```
fun whenFunc5(a: Int?) {
    when (a) {
        is Int -> println("$a 是 Int 类型的数据")
        else -> println("$a 不是 Int 类型的数据")
    }
}
```


#####C ：when 后面也可以不跟参数
如果不提供参数，所有的分支条件都是简单的布尔表达式，而当一个分支的条件为真时则执行该分支:
```
fun whenFunc6(a: Int) {
    when {  //此处未跟参数，所以分支条件必须是 简单的 boolean 表达式
        a < 6 -> println("$a 小于6")
        a == 6 -> println("传入的值是6")
        else -> println("$a 大于6")
    }
}
```

##三、 For 循环
###（一）、for 循环的特点
Kotlin 中的 for 循环可以对任何提供迭代器(iterator)的对象进行遍历，等同于 java 中的 foreach 循环。

>for 可以循环遍历任何提供了迭代器的对象。也就是说：
>* 该对象中必须有一个 iterator( ) 函数，可以是它自己声明的也可以是继承自父类的。
>* iterator( ) 必须有返回值。假设该返回值的对象类为A
>* 对象类 A 中 必须有 next( ) 函数，可以是它自己声明的也可以是继承自父类的
>* 对象类 A 中 还必须有 hasNext( ) 函数，该函数的返回值 为 Boolean类型
>*  而且以上这三个函数都需要标记为 operator 。也就是说这三个方法需要是公共的，可以被其他类访问的。

###（二）、for 循环的基本语法
for 循环的基本语法如下: 
```
for (item in collection) 
    print(item)
```
循环体可以是一个代码块:
```
for (item: Int in ints) {
       // ......代码块......
}
```

####示例1：遍历获取数组中的数据

```
fun forFunc1() {
    var nums = arrayOf(1, 2, 3, 4, 5, 6, 7)  //创建数组
    for (num in nums) { //遍历并打印数据
        println(num)
    }
}
```
####示例2：遍历数组中的索引
```
fun forFunc2() {
    var nums = arrayOf(1, 2, 3, 4, 5)
    for (index in nums.indices) {   //遍历数组的索引
        println(nums[index])    //打印根据索引获得的数据
    }
}
```
**遍历索引时，需要使用 Array.indices( ) 函数** ，该函数返回的是 数组的索引区间。indices( ) 源码如下：
```
/**
 * Returns the range of valid indices for the array.
 */
public val <T> Array<out T>.indices: IntRange
    get() = IntRange(0, lastIndex)
```
所以，遍历索引的本质就是区间的遍历。

####示例3、同时遍历数据和索引
```
fun forFunc3() {
    var nums = arrayOf(1, 2, 3, 4, 5)
    for ((num, index) in nums.withIndex()) {
        println("索引 $index 对应的数据是 $num")
    }
}
```
**同时遍历数据和索引时，需要使用 Array.withIndex( ) 函数。**

###（三）、补充：forEach
kotlin中遍历的时候，我们也可以使用 forEach ( ){  }, 需要注意的是：
>**使用 forEach 函数时, 被遍历到的数据 使用固定的字符 it 表示。**

示例代码如下：
```
fun forFunc4() {
    var nums = arrayOf(1, 2, 3, 4, 5)
    nums.forEach {    //forEach 遍历到的数据使用固定的 it 代表
        println("nums 中的数据包含 $it")
    }
}
```
在上面的代码中，我们使用 forEach 遍历了数组中的数据，在  ` println("nums 中的数据包含 $it")` 中，it 就代码每次遍历到的具体数据。

##四、While 循环
Kotlin 中的 while 和 do...while 与java中的并没有区别，使用的方式是一致的。所以，不在赘述 ，可参考下面示例代码：

示例1：
```
fun whileFunc1() {
    var a = 10
    while (a > 0) {
        println("当前a 的值是 $a")
        a--
    }
}
``` 

示例2：
```
fun whileFunc2() {
    var a = 10
    do {
        println("当前 a 的值是 $a")
        a--
    } while (a > 0)
}
```

 
 
 

本文到此结束，谢谢观看！

---

**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注!**

![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

