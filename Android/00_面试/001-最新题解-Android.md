# 1. 001-Now题解-Android



## 1.1. 如何理解context

在 Android 中，Context 是一个非常重要的概念，它代表了应用程序的上下文环境。

Context 是一个抽象类，Android 系统提供了多种实现，包括 Activity 、Service 、 Application 等。

Context 提供了**访问应用程序资源的接口**，如访问应用程序的资源文件、获取应用程序的包名、启动Activity等，同时也是 Android 应用程序中许多重要组件（如Activity 、 Servic e、BroadcastReceiver 等）的创建和运行的基础。

在 Android 开发中，对于一些需要 Context 作为参数的方法，需要根据具体的场景选择传递哪种 Context 实例。例如：

* Activity 作为 Context

Activity 是应用程序中最常用的 Context 实现，它是一个可见的用户界面组件，具有生命周期和状态等特性。在 Activity 中可以访问应用程序的资源文件、启动其他 Activity 等。

* ApplicationContext 作为 Context

ApplicationContext 是整个应用程序的上下文环境，它**与应用程序的生命周期相同**，可以访问应用程序的资源文件、启动 Activity 等。

* Service 作为 Context

Service 是一种后台运行的组件，它可以独立于 Activity 存在，**执行一些长时间运行的操作**。在 Service 中可以访问应用程序的资源文件、启动 Activity 等。

需要注意的是，在使用 Context 时需要注意内存泄漏问题，**尽量避免在静态变量中持有 Context 实例**，避免造成不必要的内存开销和安全问题。


## 1.2. 中如何避免内存泄露

以下是避免 Android 内存泄露的一些常见方法：

* 避免静态变量引用 Activity 或 Context 对象，**因为静态变量的生命周期会比 Activity 或 Context 更长**，容易造成内存泄露。

* 及时释放资源，如关闭数据库连接、文件流等，避免长时间占用系统资源。

* **避免不必要的内部类引用外部类，如果必须引用，可以使用 WeakReference 或者静态内部类**。

* 及时取消注册广播接收器、监听器等，避免长时间占用系统资源。

* 使用 `LeakCanary` 等工具检测内存泄露，及时定位和解决问题。

* 使用 Android 中提供的一些优化工具，如 `SparseArray` 、 `ArrayMap` 、 `ViewHolder`  等可以有效减少内存占用。

* 尽量避免使用大量的 Bitmap 对象，可以使用 `Glide` 、 `Picasso` 等第三方库来加载图片，避免图片内存占用过大。

* 在 Activity 的 `onDestroy()` 方法中及时释放资源，避免资源泄露。




## 1.3. 如何避免在静态变量中持有Context实例

在 Android 开发中，如果在静态变量中持有 Context 实例，容易导致内存泄漏问题，因为 **Context 的生命周期通常比静态变量长**，这可能导致 Context 无法被垃圾回收，从而造成内存泄漏。

下面介绍一些避免在静态变量中持有Context实例的方法：

* 使用 ApplicationContext
如果需要在静态变量中使用 Context，可以使用 `ApplicationContext`代替 Activity 的 Context。**`ApplicationContext` 是全局唯一的，它的生命周期与应用程序的生命周期相同，不会因为 Activity 的销毁而被销毁**。

* 使用软引用
`SoftReference` 是 Java 中提供的一种软引用类型，**它可以让对象在内存不足时被回收，避免内存泄漏问题**。可以使用 `SoftReference` 来持有 Context 实例，避免造成内存泄漏。

* 使用依赖注入
依赖注入（`Dependency Injection`，简称 `DI` ）是一种设计模式，它可以将对象的创建和依赖关系的管理交给框架来处理，避免手动管理对象的创建和依赖关系。在 Android 中可以使用 `Dagger` 等依赖注入框架来管理 Context 实例的创建和依赖关系。

* 避免在静态变量中持有 Context 实例
尽量避免在静态变量中持有 Context 实例，避免造成不必要的内存开销和安全问题。如果确实需要使用 Context 实例，可以考虑使用其他方式来获取 Context 实例，如使用 `getApplicationContext()` 方法或传递 Activity 的实例等。

总的来说，避免在静态变量中持有 Context 实例是一种良好的编程习惯，可以避免内存泄漏问题和安全问题。需要根据具体的场景选择合适的方式来获取 Context 实例。

## 1.4. 静态方法中使用context时如何避免内存泄露

使用静态方法时，如果需要使用 Context 对象，**可以使用 Application Context，避免持有 Activity 或 Service 的引用**，从而避免内存泄露。可以通过以下方式获取 Application Context：

```java
public static Context getAppContext() {
    return MyApplication.getInstance().getApplicationContext();
}
```

其中，`MyApplication` 是自定义的 Application 类，需要在 Manifest 文件中指定。

另外，如果静态方法需要使用其他非静态的资源，可以通过传入参数的方式进行调用，**避免使用静态变量引用对象**。例如：

```java
public static void doSomething(Context context, OtherObject otherObject) {
    // do something with context and otherObject
}
```
在调用该静态方法时，需要传入对应的 Context 和 OtherObject 对象。

## 1.5. contex 和 static 谁的生命周期长

Context 是 Android 中的一个重要概念，它表示当前应用程序的运行环境。每个Activity、Service、BroadcastReceiver都有一个 Context 对象，它们都继承自 Context 类。** Context 对象的生命周期与其所属的组件的生命周期相同**。

而 **static 变量是属于类的，它们的生命周期与类的生命周期相同**。当应用程序退出时，类和其中的 static 变量都会被销毁。

因此，相比之下，Context 的生命周期更长。

> CnPeng , 该问题需要分情况：相较于 Application 、Service、BroadcastReceiver 中的 Context 来说，static 成员的生命周期是短的；但相对于普通的子级 Activity 或者组件中的 Context 来说，static 的生命周期长。 

## 1.6. 如何实现沉浸式状态栏

Android 中的沉浸式状态栏是指将状态栏与应用程序界面融合在一起，使得应用程序的界面可以占据整个屏幕，从而达到更好的视觉效果和用户体验。

实现沉浸式状态栏的一般步骤如下：

* 设置状态栏透明
在 Activity 的 `onCreate()` 方法中设置状态栏透明：

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
    getWindow().addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
}
```

* 设置状态栏颜色
在布局文件中添加一个与状态栏高度相同的 View ，设置它的背景色为所需的状态栏颜色：

```java
<View
    android:layout_width="match_parent"
    android:layout_height="@dimen/status_bar_height"
    android:background="@color/status_bar_color" />
```

其中，`status_bar_height` 是状态栏高度的尺寸资源，`status_bar_color` 是状态栏的颜色值。

* 设置状态栏字体颜色
如果状态栏颜色较深，需要设置状态栏字体颜色为白色，可以在 Activity 的 `onCreate()` 方法中设置：

```java
if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
    getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
}
```

其中，`SYSTEM_UI_FLAG_LIGHT_STATUS_BAR` 表示状态栏字体颜色为浅色，即白色。

需要注意的是，实现沉浸式状态栏需要考虑不同版本的兼容性，可以使用 Android 提供的 `SystemBarTint` 库或第三方库如 `StatusBarUtil` 等来简化实现过程。




## 1.7. 禁止对内置的WebView页面进行调试

在  Android内的 WebView 页面中，可以通过启用 WebView 的`setWebContentsDebuggingEnabled()`方法来允许调试程序。如果需要禁止调试，可以在WebView 加载页面前使用该方法来禁用调试。

以下是禁止 WebView 页面调试的代码示例：


```java
WebView webView = findViewById(R.id.webview);
// 启用JavaScript
webView.getSettings().setJavaScriptEnabled(true);
// 禁用调试
webView.setWebContentsDebuggingEnabled(false);
 // 加载网页
webView.loadUrl("http://www.example.com");
```

在上述代码中，`setWebContentsDebuggingEnabled(false)` 方法用于禁用调试。这样，在 WebView 加载页面时，就不会启用调试功能。

需要注意的是，禁止调试并不能完全防止程序被调试，因为还有其他的调试方式可以使用。如果需要保证程序的安全性，应该采取更加严格的安全措施。


## 1.8. android代码混淆规则及示例

> CnPeng 以下内容摘自 chatgpt ， 正确性存疑

Android代码混淆可以帮助开发者保护应用程序的安全性，防止被反编译和盗版。下面是一些常用的Android代码混淆规则及示例：

* 重命名类和方法

```groovy
-keep class com.example.app.** { ; }
-keepclassmembers class com.example.app.* { *; }
```

* 移除无用的类和方法

```groovy
-assumenosideeffects class com.example.app.Logger {
public static void log(java.lang.String);
}
```

* 隐藏字符串

```groovy
-keepclassmembers class * {
@android.support.annotation.Keep public static **[] values();
@android.support.annotation.Keep public static ** valueOf(java.lang.String);
}
```

* 隐藏资源

```groovy
-keepclassmembers class * {
@android.support.annotation.Keep public static final int *;
}
```

* 隐藏特定的类

```groovy
-dontwarn com.example.app.SecretClass
```

* 混淆枚举类

```groovy
-keepclassmembers enum * {
public static **[] values();
public static ** valueOf(java.lang.String);
}
```

* 混淆接口

```groovy
-keep interface com.example.app.MyInterface {
void myMethod();
}
```

* 混淆注解

```groovy
-keep @interface com.example.app.MyAnnotation
```

* 混淆特定的包

```groovy
-keep class com.example.app.** { ; }
-dontwarn com.example.app.*
```

以上是一些常用的 Android 代码混淆规则及示例，可以根据自己的需求进行修改和添加。

## 1.9. MVVM的viewModel与android中的ViewModel的区别

MVVM 的 ViewModel 是一个独立的类，它负责**处理视图和模型之间的交互**，将模型数据转换为视图数据，同时也负责将用户输入转换为模型数据。

而 Android 中的 ViewModel 是一种架构组件，它负责**管理与 UI 相关的数据**，并在配置更改（如旋转屏幕）时保留这些数据。它可以与 LiveData 和 Room 等组件一起使用，以便在数据发生更改时自动更新UI。

因此，MVVM 的 ViewModel 更加通用，可以用于任何框架或平台，而 Android 中的 ViewModel 则更加专门化，主要用于 Android 应用程序的 UI 管理。

