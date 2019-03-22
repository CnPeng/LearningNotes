

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

Android Studio 对 DataBinding 的代码提供了诸多支持，如：

* 语法高亮 （ xml 中使用数据绑定的部分会高亮显示）
* 语法错误提示 （ xml 中使用数据绑定的部分如果有错误会爆红提示）
* 代码补全 
* 快速跳转 （ Mac 下按住 Cmd , 然后单击数据绑定中使用的 变量或方法，就会跳转到对应的声明位置）

如果我们为数据绑定表达式提供了 `default` 属性及值，那么，在布局编辑器的预览面板中，就会显示 default 的值；程序运行之后，如果数据绑定表达式没有获取到结果，也会展示 default 对应的值。

比如，下面的示例代码中，TextView 的 text 属性绑定的值为 `user.firstName`, 并通过 `default` 关键字配置了默认值为 “彭”。这样，在布局编辑器的预览界面中，该 TextView 中就会显示 “彭” ; 程序部署到手机上运行后，如果 `user.firstName` 的取值为空，那么也会显示 “彭”。

```kotlin
<TextView android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@{user.firstName, default=`彭`}"/>
```

如果我们只是单纯的想在布局编辑器的预览界面中查看布局效果，完全可以使用 `tools` 命名空间来实现。`tools` 命名空间实现的内容只在预览时生效，程序部署运行后无效。

==注意：部分资料中只说了 default 属性值可以在预览界面显示，但没有说明数据绑定表达式取不到值时会使用 default 属性值的情况，此处我之前栽过跟头😭==

关于 tools 命名空间的使用，可以参考如下文章：

[Android:Tools命名空间原来是有大用处的](https://www.jianshu.com/p/2912bcba4465)  

[Tools Attributes Reference](https://developer.android.com/studio/write/tool-attributes.html).


### 3、新的数据绑定编译器

#### (1)、启用新的绑定编译器
从 Android Gradle 插件的 3.1.0-alpha06 版本开始，在生成 绑定类时 使用了新的数据绑定编译器。新的编译器使用增量编译的方式来创建绑定类，极大的提高了程序的构建速度。关于绑定类的更多内容可以查看 <a href="5">生成的绑定类</a> 一节

在老版本的绑定编译器中，生成绑定类的操作 是与 编译你自己的代码 同步的。所以，在之前的版本中，如果我们自己写的代码出了错误，也会导致生成绑定类的操作终止，然后我们就会在 Android Studio 的 Logcat 面板中看见无数个 `找不到绑定类 ( the binding calsses aren't found )` 的错误提示。而在 **新的绑定编译器中，会先处理绑定类的生成操作，然后再去编译我们自己编写的其他代码**。

在 `gradle.properties` 文件中，新增如下内容即可启用新版的绑定编译器：

```kotlin
android.databinding.enableV2=true
```

注意：

在 3.1 的 Gradle 插件中，绑定编译器向下兼容并不完善，我们必须手动启用新版编译器；但 3.2 开始能够完全向下兼容，并且从 3.2 开始默认启用新版编译器。

#### (2)、新的绑定编译器的变更

新的绑定编译器做了如下改变：

* 先生成绑定类，然后再去编译我们自己的代码

* 库 module 中的绑定类会被编译/打包到 AAR 文件中. 引用这个库时，不需要再重新编译和生成这些绑定类。（ 关于 AAR 的更多内容，可以查看：[Create an Android Library.](https://developer.android.com/studio/projects/android-library)）

* 当前 module 中定义的绑定适配器不会影响到 依赖库中的绑定适配器。也就是说，绑定适配器的作用域只在当前 module 中。

* If a layout is included in more than one target resource configuration, the data binding library uses android.view.View as the default view type for all views that share the same resource id but not view type. (这个不是很明白，大概描述的如果某个布局文件被多处 include ，那么 DataBinding 库会把 include 这个布局文件的 View 的类型定义为 android.view.View 而不是它的具体类型。)



<h2 id="3">三、布局和绑定表达式</h2>

在绑定表达式中，我们不只可以绑定数据，也可以绑定用于事件处理的方法/函数。

DataBinding 库会自动生成 将数据/方法 绑定到 view 时所需的绑定类。

DataBinding 的布局文件与普通的布局文件差异很小，DataBinding 的布局文件使用 `<layout></layout>` 作为根节点，其内部又包含两个子节点：

* `<data></data>` 节点，内部包含数据绑定所需要使用的变量或类；
* View 节点，这个节点就是普通布局文件中 View 和 ViewGroup 内容。

下面这个布局文件就是一个 DataBinding 的布局文件：

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"/>
           
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.lastName}"/>
   </LinearLayout>
</layout>
```

上述代码中，我们在 `<data>` 节点内部通过 `<variable />` 声明了一个类型为 `com.example.User` 的 变量，该变量名为 `user`。

在布局文件中，我们使用绑定表达式为 View 的某个属性赋值时，使用的是 `@{ }` 语法。

在上面的示例代码中，我们分别把 `user.firstName` 和 `user.lastName` 作为 TextView 的 `android:text` 属性值。

**注意：绑定表达式要尽量简洁。**  借助 <a href="6">绑定适配器</a> 可以简化绑定表达式。


### 1、数据对象

假设我们现在有一个 User 数据类：

kotlin 版写法：

```kotlin
data class User1(val firstName: String, val lastName: String)
```

java 版写法：

```java
public class User1 {
  public final String firstName;
  public final String lastName;
  public User(String firstName, String lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
  }
}
```

上面示例中，属性被定义为 public final，我们构建一个 User 对象后，其属性值不能被再次改变，而且我们只能通过 `user.firstName` 的方式去访问其属性。

下面的 java 示例代码中，我们把属性值定义为 private final , 然后提供 public 的 getxxx 方法。构建对象后，我们只能通过 getxxx 方法访问其属性值。

kotlin版

```kotlin
// Not applicable in Kotlin. CnPeng 这地方没理解透撤
data class User2(val firstName: String, val lastName: String)
```

java版

```kotlin
public class User2 {
  private final String firstName;
  private final String lastName;
  public User(String firstName, String lastName) {
      this.firstName = firstName;
      this.lastName = lastName;
  }
  public String getFirstName() {
      return this.firstName;
  }
  public String getLastName() {
      return this.lastName;
  }
}
```

对于 DataBinding 来说，我们上面定义的两种 User 是等效的。在 `@{user.firstName}` 表达式中，如果 user 是 User1 的实例，那么，它会直接访问 firstName 属性值；如果是 User2 的实例，则会调用 `getFirstName()` 方法，如果在 User2 中把 `getFirstName()` 改为 `firstName()`, 这样也可以被调用。



### 2、创建和获取绑定类

每一个被绑定的布局文件都会生成一个对应的 binding 类。

默认情况下，绑定类的名字取决于布局文件的名字。如 `activity_main.xml` 将会生成 `ActivityMainBinding` 类。在生成绑定类时，会去除下划线，然后遵循驼峰规则，末尾后缀 Binding . 

`ActivityMainBinding` 类内部持有布局文件中的所有绑定对象，并且会自动把绑定给 View 的值计算出来。

通常我们会在填充布局的时候创建和获取绑定类。示例如下：

#### (1)、Activtiy 

在 activity 中，我们使用 `DataBindingUtil.setContentView()` 替代 `setContentView()`

kotlin版

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)

    val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

    binding.user = User("Test", "User")
}
```

java版

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
   super.onCreate(savedInstanceState);
   ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
   User user = new User("Test", "User");
   binding.setUser(user);
}
```

#### (2)、Fragment、ListView、RecyclerView

在 Fragment、ListView、RecyclerView 中填充视图中，我们使用的是 `DataBindingUtil.inflate()`

kotlin 版

```kotlin
val listItemBinding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false)

```

java版

```java
ListItemBinding binding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false);
```

### 3、数据绑定的表达式语句

#### (1)、支持的关键字和操作

数据绑定的表达式语句和其他普通语句类似，它支持如下操作(符)和关键字

* 算数运算符:`+ - * / %`
* 字符串拼接符: `+`
* 逻辑运算符： `&&  ||`
* 位运算(Binary)： `&  |  ^`
* 一元运算（Unary）：`+ - ! ~`
* 位移运算（shift）: `>>  >>>  <<`
* 比较表达式：`== > < >= <=` ==注意：`<` 需要使用转义字符 `&lt;` 替代==
* `instanceof` 关键字
* 分组符号（Grouping）：`( )`
* 字面值：字符、字符串、数字、null
* 方法调用
* 属性调用
* 数组元素访问 :`[ ]`
* 三目运算： `?:`
* Cast (类型强转)

示例:

```kotlin
android:text="@{String.valueOf(index + 1)}"
android:visibility="@{age > 13 ? View.GONE : View.VISIBLE}"
android:transitionName='@{"image_" + id}'
```

#### (2)、不支持的内容

数据绑定的表达式语句中，不支持如下内容：

* this
* super
* new
* Explicit generic invocation


#### (3)、null 合并操作符——`??`

> CnPeng Swift 中有这种操作符

`??` 的作用是，如果左边的数据不为 null，则以左边的数据为返回值，如果为 null，则以右边的数据作为返回值。

示例:

```kotlin
android:text="@{user.displayName ?? user.lastName}"
```

上述代码等价于下面的代码：

```kotlin
android:text="@{user.displayName != null ? user.displayName : user.lastName}"
```

#### (4)、属性引用

数据绑定表达式在访问 属性（fields）、get方法（getters）、ObservableField 时都使用如下格式：

```kotlin
android:text="@{user.lastName}"
```

#### (5)、空指针异常的规避

在数据绑定自动生成的代码中，会进行 null 检查，并规避空指针异常。

比如：在表达式 `@{user.name}` 中, 如果 `user` 为 null, `user.name` 的值也会为 null .如果我们在绑定表达式中引用了 int 类型的 `user.age`, 如果 `user` 为 null ，那么 `user.age` 的值将会为 0 。

也就是说，如果被引用的对象为空，那么会使用对象字段的默认值。


#### (6)、表达式中的集合（==重点==）

在数据绑定的表达式中，可以通过 `[ ]` 访问 arrays, lists, sparse lists, maps 的元素。

对于 list 和 array , `[ ]` 中传递的是 索引；而对于 map 则需要传递 键名

此外，还可以通过 `@{map.key}` 的方式获取 map 中某个键的值。

此处要注意: `<` 需要用 `&lt;` 替代

```kotlin
<data>
    <import type="android.util.SparseArray"/>
    <import type="java.util.Map"/>
    <import type="java.util.List"/>
    <variable name="list" type="List&lt;String>"/>
    <variable name="sparse" type="SparseArray&lt;String>"/>
    <variable name="map" type="Map&lt;String, String>"/>
    <variable name="index" type="int"/>
    <variable name="key" type="String"/>
</data>
…
android:text="@{list[index]}"
…
android:text="@{sparse[index]}"
…
android:text="@{map[key]}"
``` 

#### (7)、字符串字面值

当绑定表达式中需要使用字符串的字面值时，有两种方式：

##### A: 单引号包裹双引号

如果字符串字面值使用 双引号包裹，那么，绑定表达式就需要使用 单引号包裹。

```kotlin
android:text='@{map["firstName"]}'
``` 

##### B: 双引号包裹反引号

如果使用双引号包裹绑定表达式，那么，字符串字面值就需要使用 反引号包裹。 

（反引号就是英文状态下键盘左上角的 `~` 按键。）

```kotlin
android:text="@{map[`firstName`]}"
```

#### (8)、引用资源

可以通过如下格式访问资源文件

```kotlin
android:padding="@{large? @dimen/largePadding : @dimen/smallPadding}"
```

关于格式化字符串和数量字符串的使用，此处省略。

在绑定表达式中引用资源时和未使用绑定表达式时略有区别，具体如下：

类型|非绑定表达式的引用方式|绑定表达式的引用方式
---|---|---
String[]|	@array |	@stringArray
int[] |	@array|	@intArray
TypedArray |	@array	|@typedArray
Animator | @animator |	@animator
StateListAnimator |	@animator	| @stateListAnimator
color int | @color	| @color
ColorStateList | @color |	@colorStateList


### 4、事件处理

数据绑定表达式也可以绑定一些方法用来处理 View 的事件。

比如，我们可以为 View 的 onClick 属性通过数据绑定表达式绑定一个方法，当触发 View 的 onClick 事件时就会调用 被绑定的方法。

事件对应的属性名称取决于监听器中的回调方法名，比如，`View.OnClickListener` 的回调方法名为 `onClick` , 所以它对应的属性名称就是 `android:onClick`

下面是一些比较特殊的情况，为了避免冲突，Android 为对应的事件定义了额外的属性名称：

类| 监听器 | 属性名
---|---|---
SearchView | setOnSearchClickListener(View.OnClickListener) |android:onSearchClick
ZoomControls | setOnZoomInClickListener(View.OnClickListener) | android:onZoomIn
ZoomControls	| setOnZoomOutClickListener(View.OnClickListener)| android:onZoomOut

通过数据绑定的方式处理 View 的事件时，有两种方式：

* 绑定方法（引用方法）：
	* 被绑定的方法需要和对应的监听方法有同样的参数和返回值。当我们把一个方法与 View 的事件绑定以后，DataBinging 就会把这个方法封装到 事件监听器中，然后把这个监听器设置给 View。 
* 绑定监听器：
	* 绑定监听器时使用了 lambda 表达式，同样也会创建一个监听器，当触发 View 的事件时，就会调用 lambda 表达式。
	
	 
#### (1)、引用函数/方法（绑定方法）

在 Activity 的 layout 布局文件中，我们可以为 View 的 `android:onClick` 属性绑定一个在 Activity 中声明的方法。同样的，我们也可以使用数据绑定的方式为该属性绑定位于任意位置的事件处理的方法。与前一种方式相比，数据绑定的方式会在编译期检查被绑定的方法是否存在，该方法是否与对应监听器的方法具有同样的声明格式（参数和返回值需要一致)，如果不一致就会直接报错。


绑定方法 与 绑定监听器 的主要区别在于：

* 将方法绑定到 view 之后，DataBinding 库就会立即创建一个对应的监听器
* 而绑定监听器时，则是在触发对应的事件时才会创建所需要的监听器。

绑定方法的示例如下：

* 声明方法

```java
// JAVA 版
public class MyHandlers {
    public void onClickFriend(View view) { ... }
}
```

```kotlin
// kotlin 版
class MyHandlers {
    fun onClickFriend(view: View) { ... }
}
```

* 在 `activity_main.xml` 中绑定

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">
   <data>
       <variable name="handlers" type="com.example.MyHandlers"/>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       <TextView android:layout_width="wrap_content"
           android:layout_height="wrap_content"
           android:text="@{user.firstName}"
           android:onClick="@{handlers::onClickFriend}"/>
   </LinearLayout>
</layout>
```

* 为 ActivityMainBinding.class 设置 handlers 对象

在 Activtiy 中构建 MyHandlers 对象，并设置给 ActivityMainBinding

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        val handler = MyHandlers()
        binding.handlers = handler
    }
}
``` 

上面的示例代码中，我们创建了一个 `MyHandlers` 类，并在其中声明了一个方法 `onClickFriend` , 因为 onClick 方法接收的参数是 View， 所以，我们也为 `onClickFriend` 定义了一个 view 参数。

然后我们在布局文件的 <data> 节点内部的 <variable> 节点声明了名称为 `handlers` 的变量，其类型为 `com.example.MyHandlers`——也就是我们自定义的类。所有 <variable> 节点声明的变量都会在对应的 xxxBinding.class 类中生成 getter 和 setter。

然后我们为 TextView 的 onClick 绑定了 `onClickFriend` 方法，在这个绑定表达式中，变量和方法之间使用 `::` 连接。（在绑定表达式中, 变量和属性字段之间使用 `.` 连接，如 `user.firstName` ； 另外，早期的 DataBinding 版本中，引用方法也可以使用 `.` 但现在已经不推荐这么用了）

最后，我们在 Activity 中构建 MyHandlers 对象，然后调用 setter 方法将该对象设置到 绑定类中。

注意：==被绑定的方法必须是 public 的==

#### (2)、绑定监听器

绑定方法时，被绑定的方法必须与对应监听的方法具有相同的参数和返回值；**而在绑定监听器时，只要具有相同的返回值就可以了。**

示例1：

```kotlin
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        val handler = MyHandlers()
        binding.handler = handler
        binding.args = "按钮被点击了"
    }
}

class MyHandlers {

    fun onClickButton(args: String, context: Context) {
        Toast.makeText(context, args, Toast.LENGTH_SHORT).show()
    }
}
```

```kotlin
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>

        <variable
            name="args"
            type="String" />

        <variable
            name="handler"
            type="com.example.android.databinding.basicsample.ui.MyHandlers" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <Button
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="@{()-> handler.onClickButton(args,context)}"
            android:text="点击查看效果" />

    </LinearLayout>
</layout>
```

运行上面的代码后，点击 Button，就会弹出一个吐司。

需要注意的是，**在 DataBinding 的布局文件中，默认会提供一个 `context` 变量，其值为当前跟布局的  `getContext()` 的返回值**。如果我们有需要，可以重新定义一个 context 并指定它的类型。

上述示例代码中，我们并没有声明 `onClick()` 所需要的 view 参数，因为我们在 `onClickButton` 中并没有用到它.

它的完整写法应该是下面的样子：

```kotlin
android:onClick="@{(view)-> handler.onClickButton(args,context)}"
```
上面的这行代码中，我们在 lambda 表达式的括号中定义了一个名为 view 的参数，这是 OnClickListener 中的 onClick 所需要的参数。

对于监听器回调方法的参数，有两种处理方式：直接省略或者==全==都声明出来。声明出来之后，我们就可以在绑定表达式中使用它。

示例2：

```kotlin 
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)

        val binding: ActivityMainBinding = DataBindingUtil.setContentView(this, R.layout.activity_main)

        val handler = MyHandlers()
        binding.handler = handler
    }
}

class MyHandlers {

    fun onClickButton2(args: String, view: View) {
        Toast.makeText(view.context, args, Toast.LENGTH_SHORT).show()
    }
}
```

```kotlin
android:onClick="@{(view)-> handler.onClickButton2(args,view)}"
```

示例3：

```kotlin
<CheckBox android:layout_width="wrap_content" android:layout_height="wrap_content"
      android:onCheckedChanged="@{(cb, isChecked) -> presenter.completeChanged(task,isChecked)}" />
```

如果监听器的回调方法有返回值，那么我们的数据绑定表达式也必须有相同的返回值。比如，我们监听 View 的 longClick 事件时，我们的数据绑定表达式也必须返回一个 boolean 值。

示例4：

```kotlin
class Presenter {
    fun onLongClick(view: View, task: Task): Boolean { }
}
```

```kotlin
android:onLongClick="@{(theView) -> presenter.onLongClick(theView, task)}"
```

在上面的示例代码中，如果 presenter 为 null ，那么，该数据绑定表达式就会返回对应类型的默认值。引用类型会返回 null， int 类型会返回 0， bololean 类型会返回 false 等等。

我们在数据绑定表达式中使用具有断言效果的语句（比如 三目运算）时，我们可以使用 `void` 作为一个分支，示例如下：

```kotlin
android:onClick="@{(v) -> v.isVisible() ? doSomething() : void}"
```

上面这段代码就表示，如果 view 可见，执行 `doSometthing` 方法， 否则执行 void —— 也就是啥也不干。


注意:

在绑定监听器时，绑定表达式一定要简洁。（CnPeng 其实所有的绑定表达式都应该简洁明了）

### 5、import、variale、include

DataBinding 库提供了 import、variable、include 属性。

- import 可以让我们在布局文件中引用类
- variable 可以声明一些我们将要在 绑定表达式中使用的变量
- include 可以实现布局文件的复用

#### (1)、import

就像在 `.java` 或者 `.kt` 文件中一样，如果我们想在布局文件中使用某个类，那么就可以直接通过 import 导入这个类。

示例如下：

```kotlin
<data>
    <import type="android.view.View"/>
</data>
```

我们导入某个类之后，就可以在绑定表达式中使用这个类了，下面的示例引用了 View 的显示和隐藏属性，具体如下：

```kotlin
<TextView
   android:text="@{user.lastName}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"
   android:visibility="@{user.isAdult ? View.VISIBLE : View.GONE}"/>
```

##### A: 类型别名

如果被导入的类具有相同的类名，为了在使用时避免冲突，我们可以为这个类通过 `alias` 定义一个别名。

```kotlin
<import type="android.view.View"/>
<import type="com.example.real.estate.View"
        alias="Vista"/>
```

上面的示例中我们为 `com.example.real.estate.View` 定义了一个别名 `Vista`， 这样当我们引用其中的属性或者方法时，就可以直接使用 `Vista.xxx` 进行引用了。而 `View` 则只会指向 `android.view.View`.

##### B: 使用导入的类

在声明变量时，我们可以直接使用已经 import 的类，如：

```kotlin
<data>
    <import type="com.example.User"/>
    <import type="java.util.List"/>
    <variable name="user" type="User"/>
    <variable name="userList" type="List &lt;User>"/>
</data>
```

我们也可以使用 import 的类进行类型强转，下面的示例中，将 `connection` 属性强转成了 `User` 类型：

```kotlin
<TextView
   android:text="@{((User)(user.connection)).lastName}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

import 的类中如果有静态属性和方法，我么也可以直接在绑定表达式中调用. 下面的示例代码中，调用了 MyStringUtils 中的 capitalize() 静态方法

```kotlin
<data>
    <import type="com.example.MyStringUtils"/>
    <variable name="user" type="com.example.User"/>
</data>
…
<TextView
   android:text="@{MyStringUtils.capitalize(user.lastName)}"
   android:layout_width="wrap_content"
   android:layout_height="wrap_content"/>
```

对于 `java.lang.*` 包下的类，我们在数据绑定的布局文件中，可以直接使用，而不需要 import . 比如  String,

```kotlin
<data>
	<variable name = "uName" type = "String">
</data>
```

#### (2)、variable

`<variable>` 节点表示声明将要在绑定表达式中使用到的变量。

下面的示例代码中，声明了多个绑定变量。

```kotlin
<data>
    <import type="android.graphics.drawable.Drawable"/>
    <variable name="user" type="com.example.User"/>
    <variable name="image" type="Drawable"/>
    <variable name="note" type="String"/>
</data>
```

这些变量的类型是在编译期确定下来的，如果某个变量实现了 Observable 或者就是一个  observable collection, 那么，在它的类型就是 Observable 或 observable collection 。否则，就是原类型。

如果某个布局文件有不同的变体（比如：横屏 或 竖屏）, 那么，其中声明的变量会被合并到一个 Binding 类中，所以，这种情况下，我们在不同的文件中不能定义相同的变量。（ When there are different layout files for various configurations (for example, landscape or portrait), the variables are combined. There must not be conflicting variable definitions between these layout files.）

定义的每一个变量都将是自动生成的 Binding 类的属性，而且会在 Binding 类中生成对应的 setter 和 getter。在 setter 被调用之前，这些变量都会持有一个默认值——引用类型默认为null，int 类型默认为 0 等等。

==`context` 是一个特殊的变量名，默认情况下，它的值是当前布局文件根节点的 `getContext()` 的值。如果我们显示的声明了一个同名的变量，该变量的类型和值就会覆盖默认的类型和值。==


#### (3)、include 

数据绑定的布局文件中声明的变量可以传递给被 include 的布局中，在传递时使用 app 或 bind 命名空间，对应的属性名就是变量名。被 include 的布局也应该是以 `<layout></layout>` 作为根节点。

下面的示例代码，分别把 user 传递到了 `name.xml` 和 `contact.xml` 文件中。

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       
       <include layout="@layout/name"
           bind:user="@{user}"/>
           
       <include layout="@layout/contact"
           bind:user="@{user}"/>
   </LinearLayout>
</layout>
```

name.xml

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   <LinearLayout
       android:orientation="vertical"
       android:layout_width="match_parent"
       android:layout_height="match_parent">
       
       <TextView
       	 android:layout_width="wrap_content"
           android:layout_height="wrap_content"
       	 android:text="@{user.firstName}"
       	 />
   </LinearLayout>
</layout>
```

如果被 `include` 节点是 `mserge` 的子节点，那么，DataBinding 的数据传递将不会生效，如下：

```kotlin
<!--这是一个错误的示例，错误的，错误的-->
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:bind="http://schemas.android.com/apk/res-auto">
   <data>
       <variable name="user" type="com.example.User"/>
   </data>
   
   <!-- 这样不生效 这样不生效 这样不生效 -->
   <merge>
       <include layout="@layout/name"
           bind:user="@{user}"/>
       <include layout="@layout/contact"
           bind:user="@{user}"/>
   </merge>
</layout>
```
 
<h2 id="4">四、使用可观察的数据对象</h2>

所谓可观察就是指当数据本身发生变化时，会通知该数据的调用者去刷新。这类似于监听回调。

DataBinding 库中，我们可以把 对象(objects)、属性（fields）和 集合（collections）做成可观察的。

我们在数据绑定中使用非可观察的数据对象时，当对象内部的数据发生变化后，并不能自动实现 UI 的刷新；而可观察的数据对象在数据发生变化时，会自动触发 UI 的更新。


### 1、Observable fields

当某个类中的字段比较少的时候，我们如果让该类去实现 `Observable` 显得有些大材小用，这种情况下，我们完全可以把这些属性定义为下面这些预置的 Observable 类型：

* `ObservableBoolean`
* `ObservableByte`
* `ObservableChar`
* `ObservableShort`
* `ObservableInt`
* `ObservableLong`
* `ObservableFloat`
* `ObservableDouble`
* `ObservableParcelable`
* `ObservableField<T>`

Observable fields 实际是一个仅包含一个属性的 observable 对象，在访问数据的时候不需要拆装箱。

访问 Observable fields 内部数据时使用 set() 和 get() 

示例：

```kotlin
class TempActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityTempBinding = DataBindingUtil.setContentView(this, R.layout.activity_temp)

        val statusBean = StatusBean(ObservableInt(0), ObservableField("被点击了么？"))
        binding.statusBean = statusBean

        var clickCount = 0
        binding.tvStatus.setOnClickListener {
            //CnPeng 2019/3/21 8:29 PM 设置新值
            statusBean.status.set(1)
            Log.e("status:", "statusBean.status.get() 用来获取 Int 值，其值为：${statusBean.status.get()}")

            clickCount++
            statusBean.desc.set(clickCount.toString())
        }
    }
}

data class StatusBean(
        val status: ObservableInt,
        val desc: ObservableField<String>
)
```

`activity_temp.xml`

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="statusBean"
            type="com.example.android.databinding.basicsample.ui.StatusBean" />
    </data>

    <android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <!--在布局文件中以数据绑定的形式引用 ObservableInt 值时会主动调用其 get() 方法-->
        <TextView
            android:id="@+id/tv_status"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="20dp"
            android:text="@{statusBean.status==1?`点击过了，status 为 1：`:`status 为0，还没有被点击`}"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            tools:text="点击状态" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="20dp"
            android:text="@{`点击次数：`+statusBean.desc}"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/tv_status"
            tools:text="这里显示描述" />

    </android.support.constraint.ConstraintLayout>

</layout>
```

### 2、Observable collections

在 APP 中存储和处理数据时，我们通常使用的都是静态数据结构，也就是提前定义好一个数据解析类，确定好字段个数和字段类型，当收到服务器返回的 JSON 数据时直接进行解析。与之相对的，不需要提前定义数据解析类的方式称为动态数据结构, 通常我们会借助 collections （List、Map）实现动态数据结构.

Observable collections 和 普通 collections 一样，允许我们通过 键 去访问 值 。

#### (1)、ObservableArrayMap

如果 键 是引用类型，比如 String，此时，建议使用 `ObservableArrayMap` 来组织数据：

```kotlin
class TempActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityTempBinding = DataBindingUtil.setContentView(this, R.layout.activity_temp)

        val user = ObservableArrayMap<String, Any>().apply {
            put("name", "张三")
            put("sex", "男")
            put("age", 23)
        }

        binding.user = user
    }
}
```

`activity_temp.xml`

```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="user"
            type="android.databinding.ObservableArrayMap&lt;String,Object>" />
    </data>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="15dp">

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.name}"
            tools:text="姓名" />

        <!--注意：[] 中包裹的键名是字符串时，必须使用引号括起来-->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="20dp"
            android:text="@{user[`sex`]}"
            tools:text="性别" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{String.valueOf((1+safeUnbox((Integer)user.age)))}"
            tools:text="年龄" />

    </LinearLayout>

</layout>
```

#### (2)、ObservableArrayList

如果 键 是 int 类型，建议使用 `ObservableArrayList`

```kotlin
class TempActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityTempBinding = DataBindingUtil.setContentView(this, R.layout.activity_temp)

        val user = ObservableArrayList<Any>().apply {
            add("NAME: CnPeng")
            add("SEX：Man")
            add(17)
        }

        binding.user = user

        var clickCount = 0
        binding.bt.setOnClickListener {
            clickCount++
            user[Fields.NAME] = "NAME: CnPeng, ClickNum:$clickCount"
            user[Fields.SEX] = "SEX：Man, ClickNum:$clickCount"
            user[Fields.AGE] = clickCount
        }
    }
}
```

`activtiy_temp.xml`

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="user"
            type="android.databinding.ObservableArrayList&lt;Object>" />

        <import type="com.databinding.data.Fields" />
    </data>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="15dp">

        <!-- When i use  android:text="@{user[0]}" ，it's ok -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user[Fields.NAME]}"
            tools:text="NAME" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:text="@{user[1]}"
            tools:text="SEX" />

        <Button
            android:id="@+id/bt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:text="Click Here" />
    </LinearLayout>

</layout>
```

Fields.java

```java
public class Fields {
    public static final int NAME=0;
    public static final int SEX=1;
    public static final int AGE=2;
}
```

注意：

上述代码中，Fields 并没有定义成 .kt 文件，是因为不知道该用啥来表示，尝试过 object (单例类)、尝试过 带有 companion object 的 class , 但是，编译时都会报错。 

### 3、Observable objects

Observable objects 表示实现了 Observable 接口的类。

Observable 接口中有 增加 和 删除监听的方法， 类实现 Observable 接口时，还需要注册一个监听器，当该类中的属性发生改变之后，会触发监听器，但何时通知调用者，是由程序员自己决定的。

为了让实现更简单， DataBinding 库提供了 BaseObservable 类，BaseObservable 内部实现了监听器的注册。

#### (1)、实现 BaseObservable 接口

我们为 BaseObservable 实现类中属性的 getter 添加 Bindable 注解，并在 setter 中调用 `notifyPropertyChanged()`, 这样，当该属性值发生变化时，就会触发 `notifyPropertyChanged()` 去通知调用者进行更新

示例如下：

kotlin 写法：

```kotlin
class TempActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        val binding: ActivityTempBinding = DataBindingUtil.setContentView(this, R.layout.activity_temp)

        val user = User()
        user.firstName = "张"
        user.lastName = "三"

        binding.user = user

        var clickCount = 0
        binding.bt.setOnClickListener {
            clickCount++
            user.firstName = "张 $clickCount"
            user.lastName = "三 $clickCount"
        }
    }
}


class User : BaseObservable() {
    @get:Bindable
    var firstName: String = ""
        set(value) {
            field = value
            notifyPropertyChanged(BR.firstName)
        }

    @get:Bindable
    var lastName: String = ""
        set(value) {
            field = value
            notifyPropertyChanged(BR.lastName)
        }
}
```

`activity_temp.xml`

```kotlin
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:tools="http://schemas.android.com/tools">

    <data>

        <import type="com.databinding.ui.User" />

        <variable
            name="user"
            type="User" />

    </data>

    <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:orientation="vertical"
        android:padding="15dp">

        <!-- When i use  android:text="@{user[0]}" ，it's ok -->
        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{user.firstName}"
            tools:text="NAME" />

        <TextView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:text="@{user.lastName}"
            tools:text="SEX" />

        <Button
            android:id="@+id/bt"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginTop="15dp"
            android:text="Click Here" />
    </LinearLayout>

</layout>
```


java 写法

```java
private static class User extends BaseObservable {
    private String firstName;
    private String lastName;

    @Bindable
    public String getFirstName() {
        return this.firstName;
    }

    @Bindable
    public String getLastName() {
        return this.lastName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
        notifyPropertyChanged(BR.firstName);
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
        notifyPropertyChanged(BR.lastName);
    }
}
```

DataBdinding 库会在当前 module 的包中会生成一个 BR 类文件，其中包含了所有数据绑定所需要用到的各种资源的 id。就像为使用数据绑定时自动生成的 R 类文件一样。

在编译时，BataBinding 库会检测所有 Bindable 注解，如果某个属性有这个注解，就会在 BR 文件中生成一个对应的 id。该 id 以当前属性为名称，调用时直接使用 `BR.属性名` 即可，就像上面示例中的 `notifyPropertyChanged(BR.lastName);`


#### (2)、实现 Observable

通常是让 基类 实现 Observable 接口，然后注册一个 PropertyChangeRegistry 监听器。

示例如下：

```kotlin
open class ObservableViewModel : ViewModel(), Observable {

    private val callbacks: PropertyChangeRegistry = PropertyChangeRegistry()

    override fun addOnPropertyChangedCallback(callback: Observable.OnPropertyChangedCallback) {
        callbacks.add(callback)
    }

    override fun removeOnPropertyChangedCallback(callback: Observable.OnPropertyChangedCallback) {
        callbacks.remove(callback)
    }

    /**
     * Notifies listeners that all properties of this instance have changed.
     */
    fun notifyChange() {
        callbacks.notifyCallbacks(this, 0, null)
    }

    /**
     * Notifies listeners that a specific property has changed. The getter for the property
     * that changes should be marked with [Bindable] to generate a field in
     * `BR` to be used as `fieldId`.
     *
     * @param fieldId The generated BR id for the Bindable field.
     */
    fun notifyPropertyChanged(fieldId: Int) {
        callbacks.notifyCallbacks(this, fieldId, null)
    }
}
```

此处实现略为复杂，完整内容可以参考 [官方 DEMO](https://github.com/googlesamples/android-databinding) 中的 `ObservableViewModel `。 



<h2 id="5">五、生成的绑定类</h2>

<h2 id="6">六、绑定适配器</h2>

<h2 id="7">七、将布局视图绑定到架构组件</h2>

<h2 id="8">八、双向数据绑定</h2>

<h2 id="9">九、相关资料</h2>

* [官方数据绑定示例](https://github.com/googlesamples/android-databinding)
* [Android Data Binding codelab](https://codelabs.developers.google.com/codelabs/android-databinding/#0)
* [Data Binding — Lessons Learnt](https://medium.com/androiddevelopers/data-binding-lessons-learnt-4fd16576b719)