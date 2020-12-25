[原文链接](https://developer.android.com/guide/topics/resources/complex-xml-resources?hl=zh-CN)

有一些类型的资源文件是对其他多个资源文件的整合，整合的时候通常是借助 xml 文件实现的。

就比如带有动画效果的 vector 图片。

## 一、带有动画效果的 vector 的基本实现方式

基本实现方式至少需要三个 xml 文件，一个 `<animated-vector>`, 一个 `<vector>`, 一个 `<animator>`

### 1、`res/drawable/vector_animated.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:drawable="@drawable/vectordrawable" >
    <target
        android:name="rotationGroup"
        android:animation="@anim/rotation" />
</animated-vector>
```

上述代码中，

* `<animated-vector>` 节点中的 `drawable` 表示将要被执行动画效果的静态 vector 图像。 
* `<target>` 节点中的 `name` 表示静态 vector 中 `<group>` 的 `name` ; 
* `<target>` 节点中的 `animation` 表示将要给 `<group>` 执行的动画。 

上述代码的意思是说：在名称为 `vectordrawable` 的 vector 图像中，让名称为 `<rotationGroup>` 的 `<group>` 节点执行名称为 `rotation` 的动画

### 2、`res/drawable/vectordrawable.xml`

#### (1)、代码解析

```xml
<?xml version="1.0" encoding="utf-8"?>
<vector xmlns:android="http://schemas.android.com/apk/res/android"
    android:height="64dp"
    android:width="64dp"
    android:viewportHeight="600"
    android:viewportWidth="600" >

   <group
        android:name="rotationGroup"
        android:pivotX="300.0"
        android:pivotY="300.0"
        android:rotation="45.0" >
        <path
            android:fillColor="#000000"
            android:pathData="M300,300 l 0,-70 70,70 0,0 -70,70z" />
             <!--android:pathData="M300,300 l 0,-70 70,70 -70,70z" /> -->
   </group>
</vector>
```

上述代码中，构建了一个 vector 图像，

* `<vector>` 节点中
	- `height` 和 `width` 定义了图像的整体大小，
	- `viewportHeight` 和 `viewportWidth` 定义了将这个大小在水平和垂直方向上划分为多少份，然后 group 和 path 中的坐标就是以这个份数为最大坐标系统  
* `<group>` 用来定义一组 path , 通常是将具有相同操作/动画的 path 放在一起，而且还可以扩展动画效果（ path 标签没有 translateX 和 translateY 属性，而 group 有）

#### (2)、`<path>` 中的指令

[原文链接：](https://www.jianshu.com/p/e3614e7abc03)


* 支持的指令：

指令|对应的函数|含义
---|---|---
M | moveto(M X,Y) | 将画笔移动到指定的坐标位置
L | lineto(L X,Y) | 画直线到指定的坐标位置
H | horizontal lineto(H X) |画水平线到指定的X坐标位置
V | vertical lineto(V Y) |画垂直线到指定的Y坐标位置
C | curveto(C X1,Y1,X2,Y2,ENDX,ENDY) |三次贝赛曲线
S | smooth curveto(S X2,Y2,ENDX,ENDY) |
Q | quadratic Belzier curve(Q X,Y,ENDX,ENDY) |二次贝赛曲线
T | smooth quadratic Belzier curveto(T ENDX,ENDY) |映射
A | elliptical Arc(A RX,RY,XROTATION,FLAG1,FLAG2,X,Y) | 弧线
Z | closepath() |关闭路径


* 使用原则:

	- 坐标轴为以 (0,0) 为中心，X 轴水平向右，Y 轴水平向下
	- 所有指令大小写均可。
		- 大写绝对定位，参照全局坐标系；
		- 小写相对定位，相对于当前坐标点的位置
	- 指令和数据间的空格可以省略
	- 同一指令出现多次可以只用一个


**注意，'M'处理时，只是移动了画笔， 没有画任何东西。**

### 3、`res/anim/rotation.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/android"
    android:duration="6000"
    android:propertyName="rotation"
    android:valueFrom="0"
    android:valueTo="360" />
```

## 二、整合之后的带有动效的 vector

* `vector_animated.xml`

```xml
<?xml version="1.0" encoding="utf-8"?>
<animated-vector xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:aapt="http://schemas.android.com/aapt">

    <aapt:attr name="android:drawable">
        <vector
            android:width="64dp"
            android:height="64dp"
            android:viewportWidth="600"
            android:viewportHeight="600">

            <group
                android:name="rotationGroup"
                android:pivotX="300.0"
                android:pivotY="300.0"
                android:rotation="45.0">

                <path
                    android:fillColor="#00f"
                    android:pathData="M300,300  l 0,-70 70,70 -70,70z" />
                <!--android:pathData="L 0,70 70,70 70,35 140,35 140,0z" />-->

            </group>
        </vector>
    </aapt:attr>

    <target android:name="rotationGroup">
        <aapt:attr name="android:animation">
            <objectAnimator
                android:duration="6000"
                android:propertyName="rotation"
                android:valueFrom="0"
                android:valueTo="360" />
        </aapt:attr>
    </target>
</animated-vector>
```

上述代码中，核心的部分在于使用了 `<aapt:attr>` 属性。

## 三、使用动效 vector

### 1、示例1：

```xml
<ImageView
    android:id="@+id/iv"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    app:srcCompat="@drawable/vector_animated" />
```

```kotlin
val animtable = (iv.drawable) as Animatable
animtable.start()
```

### 2、示例2

```java
ImageView imageView = (ImageView) findViewById(R.id.iv);
AnimatedVectorDrawableCompat animatedVectorDrawableCompat = AnimatedVectorDrawableCompat.create(
        this, R.drawable.vector_animated
);
imageView.setImageDrawable(animatedVectorDrawableCompat);
((Animatable) imageView.getDrawable()).start();
```

## 四、相关参考

[Android Vector曲折的兼容之路](https://www.jianshu.com/p/e3614e7abc03)