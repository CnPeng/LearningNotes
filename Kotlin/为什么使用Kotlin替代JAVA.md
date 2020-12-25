为什么要逐步的以 kotlin 来替代 Java 呢？

* 代码简洁
* 工具库充足
* AndroidStudio 提供了完美支持
* Android 官方推荐（目前官方新 DEMO 基本都是使用纯 kotlin 编写）

下面以 Java 和 Kotlin 对比的形式展示 Kotlin 的特点/优点：

## 一、Kotlin 与 Java 基本语法对比
### 1.类型推导

变量声明时，Java 必须声明类型，Kotlin 可以省略类型——类型推导

java 版写法:

```
String name = "张三";
```
Kotlin 写法:

```kotlin
var name:String = "张三"

// 省略类型，末尾不需要分号
val name = "张三"
```

注意：

* 如果声明变量时就赋初值，可以不写类型，此时，类型推导将生效
* 如果声明变量时没有赋初值，则必须写明类型，否则会报错。格式：`var a:Int`


### 2、函数/方法的声明

java版

```java
// 有返回值
public int getMax(int a, int b){
	return a>b?a:b;
}

// 无返回值
public void cusFunc(){
	int a = getMax(3,5);
}
···

kotlin 版：

```kotlin
// 有返回值
fun getMax(a: Int, b: Int): Int {  
    return if (a > b) a else b
}

// 无返回值
fun cusFunc(){
    val a=getMax(3,5)
}
```

* Kotlin 中函数声明基本格式：`fun 函数名（参数：参数类型）{函数体} `  
* Kotlin 中函数默认 public

注意：Kotlin 中没有 三目运算

### 3、字符串和字符串模板 (字符串格式化)

#### (1)、原样字符串

* `“ ” `, 被两个双引号包裹的内容是普通字符串，支持转义字符
* `“”“  ”“” `，被一对三引号包裹的内容是原样字符串，不支持转义字符，其中的内容被定义成什么样，输出的时候就是什么样。

如果字符串中需要换行时，Kotlin 可以直接使用 原样字符串

```kotlin
fun printPoem() {
    val poemStr = """
        窗前明月光，
        疑是地上霜。
        举头望明月，
        低头思故乡.
    """.trimIndent()

    println(poemStr)
}
```

执行结果如下：

```kotlin
窗前明月光，
疑是地上霜。
举头望明月，
低头思故乡

Process finished with exit code 0
```
#### (2)、字符串模板

**字符串模板格式：${占位字符串}**

大括号内部可以传递简单变量，也可以传递表达式

```kotlin
fun main() {
    val max=getMax(3,5)
  	 // 传递简单变量时可以省略大括号
    println("3 和 5 中的大值为：$max")

    val user = User("张三")
    // 大括号内部引用 变量的属性
    println("用户姓名为：${user.name}")
 	 // 大括号内部 为表达式 
    println("5 和 6 中的大值为：${getMax(5,6)}")
}

class User(val name: String)
```

如果是在 java 中，字符串模板中针对不同的数据类型会有不同的占位符：如 `%s` ， Kotlin 的 `${}` 既简单还强大

### 4、条件表达式

Kotlin 中 if 、when 这些条件语句都是有返回值的

Kotlin 中没有 switch

#### (1)、if

Kotlin 中 if 语句本身是个表达式，具有返回值

所以，对于前面我们声明过的 `getMax(a:Int,b:Int)` 可以简化为如下样式：

kotlin 版：

```kotlin
fun getMax2(a:Int,b:Int)=if (a>b) a else b
```

if/else 同理，也是有返回值，使用方法与 java 无异 

#### (2)、When 

- **when 既可以当做表达式使用，也可以当做语句使用。**
  - 当做语句使用时，效果等同于 java 中的 switch 
  - 当做表达式使用时，会有返回值，符合条件的分支的值就是整个表达式的值。
  - 当做表达式使用时, 必须要有 else 分支
  - 当做表达式使用时，when 的各个分支不仅可以是常量，也可以是表达式。

* 多个分支有相同的处理方式时，**可以把多个分支条件放在一起，用逗号分隔**。
* 分支可以是表达式

示例1：

```kotlin
// 此处的 when 等价于 switch
fun whenFunc(a: Int) {
    when (a) {
        // 多个条件具有相同处理
        1, 2 -> println("传入的值为 $a")
        // 分支可以是表达式
        getMax2(3,5)->println("这个分支是一个表达式")
        else -> {
            println("传入的值不是1，也不是2")
        }
    }
}
```

when 可以作为表达式使用：——有返回值

```kotlin
fun main() {
    val a = 5
    val b = 6

	// 此处的 when 等价于 if (a>b){a} else {b}
    val max = when {
        a > b -> a
        else -> b
    }

    println("两者中的较大值为：$max")
}
```


### 5. 空安全检查

#### (1)、kotlin 中默认非空，

```kotlin
// 非空字符串，如果给 str1 赋值为 null, 编译器就会报错
var str1:String = "张三"

// String? 表示可空
var str2:String? = null
```

#### (2)、空安全检查：

*  ? 表示可空，如果空直接返回 null

java版写法：

```java
if (null!=user){
	String name=user.getName();
}  
```

```kotlin
// 如果 user 为 null，user?name 直接返回 null， 否则，返回 user.name
val name = user?.name
```

#### (3)、`?:` 空合并操作符

* `?:` 即 `if (null!= X ) { A } else{ B }` 的简写模式

java 版示例：

```java
// java 中使用 @Nullable 注解参数可空
public void sendMessageToClient(
    @Nullable Client client,
    @Nullable String message,
    @NotNull Mailer mailer
) {
    if ( null == client || null == message) return;
    PersonalInfo personalInfo = client.getPersonalInfo();
    if (personalInfo == null) return;
    
    String email = personalInfo.getEmail();
    if (email == null) return;
    mailer.sendMessage(email, message);
}
```

kotlin 版示例：

```kotlin
// Kotlin 中使用 XX？表示 XX 可空，不带 ？即表示非空——默认非空
fun sendMessageToClient( client: Client?, message: String?, mailer: Mailer) {
    if (client == null || message == null) return
    // ?: 空合并操作符：如果 ?: 前面的内容不为空，返回其值，否则，返回 ?: 后面的内容
    var personalInfo = client.personalInfo ?: return
    var email = personalInfo.email ?: return
    mailer.sendMessage(email, message)
}
```

爽翻天的可空链式调用：

```kotlin
val name = post?.author?.friends?.get (0)?.name ?: "none"
```

上面这个可空链式调用如果用 java 来写，那就是一连串的 `if (null != xx){ } else { } `

另外，kotlin 中使用 `!!` 来断言非空，但是慎用，示例如下：

```kotlin
val name=user!!.name
```

### 6、for 循环

java 中的格式：

```java
for (x=0;x<10;x++){
	//DO STH
}
```

kotlin 中的格式：

```kotlin
// 0 util 10 表示前闭后开的区间，等价于 0<=x<10, 0..10 表示 0-10 的闭区间
for (x in 0 util 10){
	// DO STH
}
```

kotlin 中带有步进的 for:

```kotlin
// 0..10 表示 0-10 的闭区间，等价于 0<=x<=10. step 表示步进，此处 step 等价于 x+2 
for (x in 0..10 step 2){
	//DO STH
}
```


### 7、函数默认参数与具名调用

#### (1)、默认参数 与 java 中的重载

```kotlin
fun main() {
    defaultParamsFun(23)
}

@JvmOverloads
fun defaultParamsFun(age: Int, name: String = "张三") {
    println("姓名：$name, 年龄：$age")
}
```

上述示例中，

* defaultParamsFun 函数接收两个参数，其中，name 具有默认值，即：如果调用方不传递 name 的值，name 则为 ”张三“
* `@JvmOverloads` 注解，表示在生成的 java 字节码文件中会生成重载函数，等价于java 中的重载： `defaultParamsFun(int age)` 和 `defaultParamsFun(int age, String name)`

#### (2)、具名调用

当具有默认值的参数不是最后一个参数时，可以使用具名调用

```kotlin
fun main() {
	 // 具名调用： 
    defaultParamsFun2(age = 23, sex = 1)
}


@JvmOverloads
fun defaultParamsFun2(age: Int, name: String = "张三", sex: Int) {
    println("姓名：$name, 年龄：$age, 性别：${if (0 == sex) """男""" else """女"""}")
}
```

### 8、类

#### (1)、类的声明

```java
public class User{
	private String name;
	
	public String getName(){
		return name;
	}
	
	public void setName(String name){
		this.name=name;
	}
}
```

```kotlin
class User(var name:String)
```

#### (2)、类的继承

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


### 9、单例

* **单例关键字：object**
`我们在定义一个类时，使用 object 替换 class 来修饰这个类，就表示，这是一个单例类`

java 版写法:

```java
public final class Student {
   public static final Student INSTANCE;

   public final void cusPrint() {
      String var1 = "单例中的 cusPrint 被执行了";
      System.out.println(var1);
   }

   private Student() {
   }

   static {
      Student var0 = new Student();
      INSTANCE = var0;
   }
}
```

kotlin 版写法：

```kotlin
fun main() {
    Student.cusPrint()
}

object Student {
    fun cusPrint() {
        println("单例中的 cusPrint 被执行了")
    }
}
```

上述 Java 版写法实际是 Kotlin 版编译后对应的 java 字节码内容


### 10、Kotlin 其他进阶内容

1. 扩展函数
2. 代理/委托
3. 协程
4. 函数类型 和 高阶函数
5. 简化写法：let with run apply also

## 二、Kotlin 提供的 Android 开发工具库

### 1、anko

[anko 介绍](https://github.com/Kotlin/anko)

anko 库内部封装了诸多的常用开发工具，包括：

* 通用工具（如 intent、dialog、toast 等）
* 布局文件工具
* SQLite 工具
* 协程工具


```kotlin
// Java
view.setOnClickListener(new View.OnClickListener() {
 @Override
 public void onClick(View v) {
 		System.out.println("This is horrible");
 }
});

// Kotlin
view.onClick {
    println("WAT")
}
```

## 2、ktx

ktx ([Kotlin Android Extensions](https://kotlinlang.org/docs/tutorials/android-plugin.html)) 

* 中内置了视图绑定，不用 fbc 了，
* 提供了对 Parcelize 的内部实现
* 还有其他诸多内容。。。


## 三、AndroidStudio 中对 Kotlin 的支持

### (1)、快速转换的支持

支持整个文件夹或者单个文件的转换

![Aqlakj.png](https://s2.ax1x.com/2019/04/12/Aqlakj.png)


### (2)、粘贴支持

从其他地方把 JAVA 代码粘贴到 kotlin 文件之后，AndroidStudio 会自动转换为对应的 kotlin 代码

---

## 四、其他相关

### (1)、Java 入门 Kotlin 资料
[From Java to Kotlin(重点参考)](https://fabiomsr.github.io/from-java-to-kotlin/classes.html)


[Kotlin 教程 和 Kotlin 开发 Android 教程](https://github.com/CnPeng/LearningNotes/tree/master/03_Kotlin/01_%E4%B9%A6%E7%B1%8D%E5%92%8C%E6%95%99%E7%A8%8B)

![AqlMtA.png](https://s2.ax1x.com/2019/04/12/AqlMtA.png)

### (2)、其他相关文章

[Kotlin 作为 Android 开发语言相比传统 Java 有什么优势](https://www.zhihu.com/question/37288009)

[正面示例 https://kaixue.io/kotlin-overview/](https://kaixue.io/kotlin-overview/)

[反面示例：https://blog.csdn.net/csdnnews/article/details/80746096](https://blog.csdn.net/csdnnews/article/details/80746096)

[怎么用Kotlin去提高生产力：Kotlin Tips](https://juejin.im/post/5abe031af265da238059c18c)

[过滤集合元素的示例](https://www.jianshu.com/p/6783d3fe2c19)

