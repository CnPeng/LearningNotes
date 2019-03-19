

基于 [Data Binding Library 官方指南](https://developer.android.com/topic/libraries/data-binding) 翻译整理。

目录结构：

章节|官方文档
---|---|
<a href="#1">一、概览</a> | [data-binding](https://developer.android.com/topic/libraries/data-binding)
<a href="#2">二、入门</a> | [data-binding/start](https://developer.android.com/topic/libraries/data-binding/start)
<a href="#3">三、布局和绑定表达式</a> | [data-binding/expressions](https://developer.android.com/topic/libraries/data-binding/expressions)
<a href="#4">四、可观察的数据对象</a> | [data-binding/observability](https://developer.android.com/topic/libraries/data-binding/observability)
<a href="#5">五、生成的绑定类</a> | [data-binding/generated-binding](https://developer.android.com/topic/libraries/data-binding/generated-binding)
<a href="#6">六、绑定适配器</a> | [data-binding/binding-adapters](https://developer.android.com/topic/libraries/data-binding/binding-adapters)
<a href="#7">七、将布局视图绑定到架构组件</a> | [data-binding/architecture](https://developer.android.com/topic/libraries/data-binding/architecture)
<a href="#8">八、双向绑定</a> | [data-binding/two-way](https://developer.android.com/topic/libraries/data-binding/two-way)

<h2 id="1">一、概览</h2>

### 1、DataBinding 基本介绍

DataBinding 库可以让我们以非编码的形式将布局和数据进行绑定。

通常情况下， 我们把数据绑定到布局文件时，需要借助 UI 框架层的一些函数。比如，下面的代码通过 `findViewById()` 获取 TextView 控件，然后把 viewModel 对象中的 userName 设置给该控件。

* kotlin版代码

```kotlin
findViewById<TextView>(R.id.sample_text).apply {
    text = viewModel.userName
}
```

* java版代码

```java
TextView textView = findViewById(R.id.sample_text);
textView.setText(viewModel.getUserName());
```

下面的示例代码，则展示了如何使用 DataBinding 将数据直接在布局文件中绑定给对应的控件。通过下面的这种方式，我们省略了上面示例中的 java/Kotlin 代码。注意  `@{ }` 是 DataBinding 的固定格式，花括号内部是具体需要绑定的数据内容。

```kotlin
<TextView
    android:text="@{viewmodel.userName}" />
```

### 2、如何使用 DataBinding 库

下面介绍一下我们将要学习的 DataBidning 的内容。

#### <a href="#2">(1)、入门</a>
主要介绍如何基于　AndroidStudio 搭建 DataBinding 的开发环境。

#### <a href="#3">(2)、布局和绑定表达式</a>

绑定表达式能够让我们将 数据变量 和 view 直接进行关联。然后 DataBinding 库会自动生成关联二者时所需的类（classes）。

DataBinding 库提供了可以让我们在布局文件中使用的 `import`、`variable`、`include` 属性, 这些属性用来声明绑定到 View 的数据及其类型。 这些属性的父节点为 `data`, 而 `data` 的父节点为 `layout`, `layout` 的另一个子节点就是 View 的根节点。示例如下:

```kotlin
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable
            name="viewmodel"
            type="com.myapp.data.ViewModel" />
    </data>
    
    <ConstraintLayout... >
    	 <!-- View/ViewGroup 的根节点 -->
    </ConstraintLayout... >
</layout>
```

#### <a href="#4">(3)、可观察的数据对象</a> 

所谓可观察的数据对象，就是当数据发生变化时，能够自动通知布局文件进行刷新。这就类似于回调或监听器的作用。

DataBinding 库提供了一些类和函数，这些类和函数能够实现对数据变化的监听，进而由该库自动实现布局文件的刷新。DataBinding 库可以实现对 对象（object）、字段（field）、集合（collection）的监听。

#### <a href="#5">(4)、生成的绑定类</a>

DataBinding 库会自动生成绑定 数据变量 和 View 时所需要的绑定类（binding classes）。我们也可以自定义这个绑定类。

#### <a href="#6">(5)、绑定适配器</a>

布局文件中使用的每一个绑定表达式都有一个对应的绑定适配器（bingding adapter）。该绑定适配器内部实现了属性或者监听器与视图的绑定。比如，绑定适配器内部会调用 `setText()` 为 TextView 设置 `android:text` 的属性值，或者调用 `setOnClickListener()` 设置点击监听。

[`android.databinding.adapters`](https://android.googlesource.com/platform/frameworks/data-binding/+/studio-master-dev/extensions/baseAdapters/src/main/java/androidx/databinding/adapters) 包中定义了常用的一些绑定适配器。当然了，我们也可以根据需要自定义一些绑定适配器。示例如下：

* kotlin 版示例

```kotlin
@BindingAdapter("app:goneUnless")
fun goneUnless(view: View, visible: Boolean) {
    view.visibility = if (visible) View.VISIBLE else View.GONE
}
```

* java 版示例

```java
@BindingAdapter("app:goneUnless")
public static void goneUnless(View view, Boolean visible) {
    view.visibility = visible ? View.VISIBLE : View.GONE;
}
```

#### <a href="#7">(6)、将布局视图绑定到架构组件</a>

Android Support 库中包含的 [`Architecture Components`](https://developer.android.com/topic/libraries/architecture/index.html) 可以帮助我们构建出更加健壮(robust)、易于测试(testable)、方便维护(maintainable) 的 app。

`Architecture Components` 和 DataBinding 的组合使用，可以进一步简化 UI

#### <a href="#8">(7)、双向数据绑定</a>

DataBinding 库支持双向数据绑定。也就是说，当 View 的属性发生变化时可以通知对应的绑定数据进行更新，同理，当绑定数据发生变化时也可以通知 View 去更新对应的属性值。

比如，我们使用双向绑定为 RadioButton 绑定一个文本。当 RadioButton 选中时会显示 “选中”，取消选中时显示 “未选中”；同理，当我们变更文本时，双向绑定就会自动更新 RadioButton 的选中状态。


<h2 id="2">二、入门</h2>

DataBinding 库是以 support 库的形式提供的，具有良好的兼容性，适用于 Andorid 4.0(API  14) 及以上版本。

使用 DataBinding 时最好保持 Gradle 插件为最新版本。(从 1.5.0 版本的 Gradle 插件开始，都可以支持 DataBinding )。[点击此处可以查看如何升级 Gradle 插件](https://developer.android.com/studio/releases/gradle-plugin.html#updating-plugin)


### 1、启用数据绑定

在 module 的 `build.gradle` 文件的 android 节点中增加 dataBinding 节点即可启用数据绑定。具体如下：

```kotlin
android {
    ...
    dataBinding {
        enabled = true
    }
}
```

注意：
==**如果当前 module 所依赖的 libraries 中使用了数据绑定，那么，不论 该module 是否使用了数据绑定，都必须在当前 module 的 gradle 文件中启用数据绑定。**==

### 2、Android Studio 中对数据绑定的支持


### 1、
### 1、
### 1、


<h2 id="3">三、布局和绑定表达式</h2>

<h2 id="4">四、使用可观察的数据对象</h2>

<h2 id="5">五、生成的绑定类</h2>

<h2 id="6">六、绑定适配器</h2>

<h2 id="7">七、将布局视图绑定到架构组件</h2>

<h2 id="8">八、双向数据绑定</h2>

<h2 id="9">九、相关资料</h2>

* [官方数据绑定示例](https://github.com/googlesamples/android-databinding)
* [Android Data Binding codelab](https://codelabs.developers.google.com/codelabs/android-databinding/#0)
* [Data Binding — Lessons Learnt](https://medium.com/androiddevelopers/data-binding-lessons-learnt-4fd16576b719)