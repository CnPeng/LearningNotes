原文链接：[== 与 equals 居然结果不一样！](https://mp.weixin.qq.com/s/uihp6PVW1cW9nIeQgIKXNg)

Kotlin 中，== 与 equals 是等价的，使用 equals 的地方可以使用 == 替换，

但是，如下示例是个特殊情况：

```kotlin
// false
val flag1:Boolean = Float.NaN==Float.NaN 
// true
val flag2:Boolean = Float.NaN.equals(Float.NaN)
```

这是因为 

```kotlin
NaN = Not a Number 
```

表示，NaN 不等于任意数值，包括它自己.

并且，`Float.NaN == Float.NaN` 的字节码也对应 Java 中的 `Float.NaN == Float.NaN`,所以返回 false。
