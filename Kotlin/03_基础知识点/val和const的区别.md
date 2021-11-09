# 1. val和const的区别

原文：[const和val有什么区别](https://20100117.cn/?p=358)

## 1.1. const

`const` 用于声明那些本质上不可变的只读属性，这些属性的值**必须是在编译时就已知的**。也就是说，`const` 修饰的是**编译期常量**，`const` 修饰的属性不允许在运行时分配值。

属性必须满足以下条件才能成为 const 属性：

* 必须是顶级对象，或者是对象的成员，或者是伴随对象的成员
* 必须使用 String 类型或原始类型进行初始化
* 没有自定义获取器（getter）

```kotlin
// 声明编译期常量
const val companyName = "MindOrks"

// 这样不行！！！const 修饰的属性，不允许在运行时赋值。
const val companyName = getCompanyName() 
```

## 1.2. val

`val` 也是用于声明只读属性。但 val 既可以声明编译期常量，也可以声明运行期常量（在程序运行时完成赋值）。

```kotlin
// 编译期常量
val comapanyname = "MindOrks"

// 运行期常量——只有当程序运行起来时，才会确定 companyName 的具体值
val companyName = getCompanyName()
```

## 1.3. 示例

```kotlin
YourClassName {
    companion object {
        const val FILE_EXTENSION = ".png"

        val FILENAME: String
        get() = "Img_" + System.currentTimeMillis() + FILE_EXTENSION
    }
}
```

## 1.4. 补充

摘自：[Kotlin 攻略 (深度解读版) | 开发者说·DTalk](https://mp.weixin.qq.com/s/lqWSA_tTFmgGsJcX11tlHw)

kotlin 中声明常量有两种方式:

* 使用 `const`
* 使用 `@JvmField` 注解

```kotlin
class Person {
    companion object{
        //使用const修饰符
        const val TAG = "Person"
    }

    //使用@JvmField注解方式
    //其内部原理是抑制编译器生成相应的getter方法并且无法重写val的get方法
    @JvmField
    val TAG = "Person"
}
```