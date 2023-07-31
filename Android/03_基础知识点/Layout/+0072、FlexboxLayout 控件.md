
>文中内容基于flexbox-layout 在GitHub截止到18-12-10日的 README.md 文档 进行二次整理。

## 1、什么是FlexboxLayout

FlexboxLayout可以被理解成一个高级的横向LinearLayout，因为本质上来说，他们都是对子View进行有序的排放。二者的主要区别在于， FlexboxLayout 有一个换行的属性，通过该属性我们可以决定是否需要换行。

也就是说，当我们为 FlexboxLayout 设置了 `flexWrap="wrap"` 时，如果其中包含的子View无法在一行中完全展示，则会换行展示剩余子View。

FlexboxLayout可以协助安卓开发者实现 CSS 中的 Flexible Box Layout 效果（也就是换行效果）。 在CSS中 FlexboxLayout 被广泛使用，Android中的 FlexboxLayout 也是基于相同的设计思路实现的。二者基本相同，但也有许多不同的地方。


## 2、如何集成该控件？
在集成该控件时，分为两种情况，一种是你的项目已经迁移到AndroidX，一种是没有迁移。

*  **项目已经迁移到AndroidX环境**
 
如果你的项目已经迁移到AndroidX环境，那么AndroidX中本身就已经集成了 flexbox:1.1.0 或更高版本，此时，我们不需要做任何操作，直接使用即可。

*  **项目还没有迁移到AndroidX**

那么就使用 1.0.0 版本，如果想使用1.1.0或更高版本，建议迁移到AndroidX环境。

未迁移时，需要修改当前module的build.gradle文件：
```
dependencies {
    implementation 'com.google.android:flexbox:1.1.0'
}
```

## 3、FlexboxLayout使用介绍

### （1）、基本使用
FlexboxLayout是一个容器，在只配置宽高属性时，其效果基本等同于 横向的LinearLayout.
* 当 layout_width="wrap_content" 时，效果等同于横向LinearLayout
* 当 layout_width="match_parent" 时，内容会收缩展示。为了让所有子View都显示到界面上，我们为子View设置的宽高会失效

* **示例1** 
```
<com.google.android.flexbox.FlexboxLayout
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        app:layout_constraintLeft_toLeftOf="parent"
        app:layout_constraintTop_toTopOf="parent">

        <TextView
            android:id="@+id/tv1_flexbox"
            android:layout_width="150dp"
            android:layout_height="44dp"
            android:background="#55ff0000"
            android:gravity="center"
            android:text="tv1_flexbox" />

        <TextView
            android:id="@+id/tv2_flexbox"
            android:layout_width="150dp"
            android:layout_height="44dp"
            android:background="#5500ff00"
            android:gravity="center"
            android:text="tv2_flexbox" />

        <TextView
            android:id="@+id/tv3_flexbox"
            android:layout_width="150dp"
            android:layout_height="44dp"
            android:background="#550000ff"
            android:gravity="center"
            android:text="tv3_flexbox" />

        <TextView
            android:id="@+id/tv4_flexbox"
            android:layout_width="150dp"
            android:layout_height="44dp"
            android:background="#55ffff00"
            android:gravity="center"
            android:text="tv4_flexbox" />
    </com.google.android.flexbox.FlexboxLayout>
```
![输入图片说明](https://images.gitee.com/uploads/images/2018/1211/141555_a63d0c3d_930142.png "屏幕截图.png")



## 4、FlexboxLayoutManager使用介绍
































## 参考内容
* 中文参考：
https://mp.weixin.qq.com/s/Mi3cK7xujmEMI_rc51-r4g

* 官方相关文章：
https://android-developers.googleblog.com/2017/02/build-flexible-layouts-with.html

* 官方GitHub地址：
https://github.com/google/flexbox-layout

* GitHub中的README.md:
https://github.com/google/flexbox-layout/blob/master/README.md



