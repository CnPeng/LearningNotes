基于 [Android KTX 官方介绍](https://developer.android.google.cn/kotlin/ktx.html#kotlin) 翻译整理。（建议查看 英文版 文档，因为英文版内容比中文版丰富！！）

## 一、KTX 基本介绍

### 1、KTX 基本介绍
Android KTX 是一系列的 kotlin 扩展程序，属于 JetPack 范畴。它广泛使用了 Kotlin 的语言特性，比如：函数扩展、属性扩展、Lambdas、具名参数、默认参数值 等。


### 2、KTX 简单示例

举个例子，当我们使用 [`SharedPreferences`](https://developer.android.google.cn/reference/android/content/SharedPreferences) 时，在修改其中的数据前必须先获取 `Editor` 对象，修改完以后必须调用 `apply()` 或 `commit()` 将这些修改操作提交，示例代码如下：

```kotlin
getSharedPreferences("CnPengTest", Context.MODE_PRIVATE)
                .edit()
                .putBoolean("flag", true)
                .apply()
```

上述示例中使用了 Kotlin 的 Lambda 表达式，整个 创建、修改、提交 的操作以一个 Lambda 代码块的形式完成，相较于 Java 代码更加简洁和清晰。

如果我们使用 Android KTX Core 中的扩展函数，上面的代码还可以更加精简。

```kotlin
 //CnPeng 2019-09-18 21:46 异步提交，内部调用了 edit.apply()
 getSharedPreferences("CnPengKTX", Context.MODE_PRIVATE)
         .edit { putBoolean("flag", true) }

 //CnPeng 2019-09-18 21:47 同步提交，内部调用了 edit.commit()
 getSharedPreferences("CnPengKTX", Context.MODE_PRIVATE)
         .edit(commit = true) { putString("name", "CnPeng") }
```

上述代码中，实际是调用了 KTK-Core 中为 `SharedPreferences` 扩展的 `edit(,)` 函数。该扩展函数的内部实现如下：

```kotlin
/**
 * Allows editing of this preference instance with a call to [apply][SharedPreferences.Editor.apply]
 * or [commit][SharedPreferences.Editor.commit] to persist the changes.
 * Default behaviour is [apply][SharedPreferences.Editor.apply].
 * ```
 * prefs.edit {
 *     putString("key", value)
 * }
 * ```
 * To [commit][SharedPreferences.Editor.commit] changes:
 * ```
 * prefs.edit(commit = true) {
 *     putString("key", value)
 * }
 * ```
 */
@SuppressLint("ApplySharedPref")
inline fun SharedPreferences.edit(
    commit: Boolean = false,
    action: SharedPreferences.Editor.() -> Unit
) {
    val editor = edit()
    action(editor)
    if (commit) {
        editor.commit()
    } else {
        editor.apply()
    }
}
```

上面的 `edit(,)` 内联扩展函数接收两个参数，一个 boolean , 一个 函数类型的 action。

* 前者表示提交方式，true 时，将修改结果通过 `commit()` 同步提交，fasle 时，将修改结果通过 `apply()` 异步提交，默认 false。
* 后者表示具体的数据操作。也是利用了 Kotlin 的 函数扩展属性，为 `SharedPreferences.Editor` 扩展了一个 空参并且无返回值的 `()->Unit` 函数。
* `inline` 关键字表示该扩展函数是一个 **内联函数** —— 当 kotlin 编译器发现该函数被调用时，需要去 拷贝并粘贴 该函数编译后的字节码（也就是直接引用代码逻辑），而不是每次都创建一个匿名类。

原文对于 `action` 的说明如下：

>The `action` lambda is itself an anonymous extension function on `SharedPreferences.Editor` which returns Unit, as indicated by its signature. This is why inside the block, you are able to perform the work directly on the `SharedPreferences.Editor`

## 二、启用 KTX

### 1、启用步骤

在 项目的 `build.gradle(Project)` 中作如下配置：

```xml
// CnPeng: 如果是新建项目，默认就配置好了；
// 如果是老项目，也基本都配置好了，因为只要引用第三方，这个配置是必不可少的！
repositories {
    google()
}
```

然后在 module（模块） 的 `build.gradle(Module)` 中做如下配置：

```xml
dependencies {
    implementation 'androidx.core:core-ktx:1.0.1'
}
```

### 2、KTX 内部模块的划分

KTX 内部划分了诸多模块，这样我们就可以根据实际需求进行个性化引用。

在上一小节 《启用步骤》 中，我们引用了 `core-ktx` ,它内部包含了对 FrameWork 层通用 API 的一些扩展。除了 `core-ktx` 之外，还包含 `fragment-ktx`、`sqlite-ktx` 等诸多模块。

>With the exception of the core module, all KTX module artifacts replace the underlying Java dependency in your build.gradle file. For example, you can replace a `androidx.fragment:fragment` dependency with `androidx.fragment:fragment-ktx`. This syntax helps to better manage versioning and does not add additional dependency declaration requirements


模块（工件）|	版本	|软件包
---|---|---
androidx.core:core-ktx				|1.0.0| --
androidx.fragment:fragment-ktx	|1.0.0| androidx.fragment.app
androidx.palette:palette-ktx		|1.0.0| androidx.palette.graphics
androidx.sqlite:sqlite-ktx			|2.0.0	|androidx.sqlite.db
androidx.collection:collection-ktx	|1.0.0| androidx.collection
androidx.lifecycle:lifecycle-viewmodel-ktx |2.0.0| androidx.lifecycle
androidx.lifecycle:lifecycle-reactivestreams-ktx |2.0.0| androidx.lifecycle
android.arch.navigation:navigation-common-ktx |1.0.0-alpha06| androidx.navigation
android.arch.navigation:navigation-fragment-ktx |1.0.0-alpha06| androidx.navigation.fragment
android.arch.navigation:navigation-runtime-ktx |1.0.0-alpha06| androidx.navigation
android.arch.navigation:navigation-testing-ktx |1.0.0-alpha06| androidx.navigation.testing
android.arch.navigation:navigation-ui-ktx |1.0.0-alpha06| androidx.navigation.ui
android.arch.work:work-runtime-ktx	|1.0.0-alpha10| androidx.work.ktx

上面表格中的版本号可能不是最新的，可以在 [Google Maven 代码库](https://dl.google.com/dl/android/maven2/index.html) 中查阅最新版本号。

另外，上述表格中最后一列的软件包可以点击 [Android KTX 中文版 的 《模块》](https://developer.android.google.cn/kotlin/ktx.html) 一节中的超链接查看

## 三、KTX 各模块使用示例

### 1、core-ktx

`Core-KTX` 模块提供了对 Android Framework 通用库的扩展，这些通用库没有需要在 `build.gradle` 中添加的 java 版本的依赖。

* 引用方式：

```xml
dependencies {
    implementation 'androidx.core:core-ktx:1.0.1'
}
```

* 使用示例

```kotlin
// Kotlin 版 完整写法
tv_getFile.viewTreeObserver.addOnPreDrawListener(
                object : ViewTreeObserver.OnPreDrawListener {
                    override fun onPreDraw(): Boolean {
                        tv_getFile.viewTreeObserver.removeOnPreDrawListener(this)
                        // doSthYouWant -- 执行预期操作
                        return true
                    }
                })

// KTX 版本写法：
 tv_getFile.doOnPreDraw {
     // doSthYouWant -- 执行预期操作
 }
```

`doOnPreDraw(,)` 的内部实现如下：

```kotlin
inline fun View.doOnPreDraw(crossinline action: (view: View) -> Unit) {
    val vto = viewTreeObserver
    vto.addOnPreDrawListener(object : ViewTreeObserver.OnPreDrawListener {
        override fun onPreDraw(): Boolean {
            action(this@doOnPreDraw)
            when {
                vto.isAlive -> vto.removeOnPreDrawListener(this)
                else -> viewTreeObserver.removeOnPreDrawListener(this)
            }
            return true
        }
    })
}
```

* core-ktx 包含如下内容：
	- [androidx.core.animation](https://developer.android.google.cn/reference/kotlin/androidx/core/animation/package-summary)
	- [androidx.core.content](https://developer.android.google.cn/reference/kotlin/androidx/core/content/package-summary)
	- [androidx.core.graphics](https://developer.android.google.cn/reference/kotlin/androidx/core/graphics/package-summary)
	- [androidx.core.graphics.drawable](https://developer.android.google.cn/reference/kotlin/androidx/core/graphics/drawable/package-summary)
	- [androidx.core.net](https://developer.android.google.cn/reference/kotlin/androidx/core/net/package-summary)
	- [androidx.core.os](https://developer.android.google.cn/reference/kotlin/androidx/core/os/package-summary)
	- [androidx.core.preference](https://developer.android.google.cn/reference/kotlin/androidx/packages)
	- [androidx.core.text](https://developer.android.google.cn/reference/kotlin/androidx/core/text/package-summary)
	- [androidx.core.transition](https://developer.android.google.cn/reference/kotlin/androidx/core/transition/package-summary)
	- [androidx.core.util](https://developer.android.google.cn/reference/kotlin/androidx/core/util/package-summary)
	- [androidx.core.view](https://developer.android.google.cn/reference/kotlin/androidx/core/view/package-summary)
	- [androidx.core.widget](https://developer.android.google.cn/reference/kotlin/androidx/core/widget/package-summary)

### 2、fragment-ktx

* 引用方式：

```xml
implementation "androidx.fragment:fragment-ktx:$rootProject.fragmentVersion"
```

* `$rootProject.fragmentVersion` 表示引用定义在 `build.gradle(project)` 中的属性。示例如下：

```xml
buildscript {
    ext {
    	  // 引用该版本号。
        fragmentVersion = '1.1.0-alpha09'   
    }

    repositories {
        google()
        jcenter()
    }

    dependencies {
        //...
    }
}
```

`fragment-ktx` 提供了一系列 Fragment API 的简化操作。比如：

```kotlin
// 该示例代码简化了  FragmentTransaction 的操作
supportFragmentManager.commit {
    addToBackStack("backStack's name")
    setCustomAnimations(
            R.anim.nav_default_enter_anim,
            R.anim.nav_default_exit_anim
    )
    add(xxFragment,"")
}
```  

### 3、palette-ktx

* 引用方式：

```xml
implementation "androidx.palette:palette-ktx:$rootProject.paletteVersion"
```

Palette 的作用是对 bitmap 中的色值取样。

`palette-ktx` 提供了对调色板的一些常用支持。当我们使用 `Palette` 对象时，可以通过 `[]` 操作符获取指定 `Target` 对象的 `Swatch` 。示例如下：

```kotlin
 val bitmap = resources.getDrawable(R.drawable.temp).toBitmap()
 val palette = Palette.from(bitmap).generate()
 
 palette[Target.DARK_MUTED]?.let {
     btn1.setBackgroundColor(it.rgb)
 }
 palette[Target.DARK_VIBRANT]?.let {
     btn2.setBackgroundColor(it.rgb)
 }
 palette[Target.LIGHT_MUTED]?.let {
     btn3.setBackgroundColor(it.rgb)
 }
 palette[Target.LIGHT_VIBRANT]?.let {
     btn4.setBackgroundColor(it.rgb )
 }
 palette[Target.MUTED]?.let {
     btn5.setBackgroundColor(it.rgb )
 }
 palette[Target.VIBRANT]?.let {
     btn6.setBackgroundColor(it.rgb )
 }
```

上述代码中 `R.drawable.temp` 如下：

![temp.png](pics/1_palette_demo.png)

对应的效果图如下：

![](pics/2_palette_demo2.png)


Palette 的更多使用可以参考：

* [Android Material Design：使用Palette优化界面色彩搭配](https://www.jianshu.com/p/dfa9aac6143d)

### 4、sqlite-ktx

* 引用方式：

```xml
implementation "androidx.sqlite:sqlite-ktx:$version"
```

`sqlite-ktx` 扩展封装了一些与 SQL transaction 相关的代码，简化了模板代码，示例如下：

```kotlin
db.transaction {
    // insert data
}
```

### 5、collection-ktx

* 引用方式

```xml
implementation "androidx.collection:collection-ktx:$version"
```

`collection-ktx` 提供了诸多函数，通过这些函数可以更加高效的操作 `ArrayMap`、`LongParseArray`、`LruCache` 等其他 Android 中 `memory-efficient` 的集合库。

`collection-ktx` 通过 Kotlin 的操作符重载简化了 集合 的部分操作。比如，取两个集合的并集：

```kotlin
// 取并集
val combinedArraySet = arraySetOf(1, 2, 3) + arraySetOf(4, 5, 6)
// 取并集，并得到一个新的 set 集合
val newCombinedArraySet = combinedArraySet + 7 + 8
```

### 6、lifecycle-viewmodel-ktx

* 引用方式

```xml
implementation "androidx.lifecycle:lifecycle-viewmodel-ktx:$version"
```

`lifecycle-viewmodel-ktx` 提供了一个 `viewModelScope()` 函数，该函数可以让我们在 `ViewModel` 中很方便的启用协程——coroutines。 `CoroutineScope` 与`Dispatchers.Main` 绑定，并且当 `ViewModel` 被清除之后该 scope 也会自动取消。 我们可以使用 `viewModelScope()` 为 `ViewModel` 构建 scope ，这样就不用为每个 ViewModel 单独构建 scope 了。

下面的示例中，`viewModelScope()` 启动了一个协程。该协程中在后台线程发起网络请求。

```kotlin
class MainViewModel:ViewModel(){

    // 发起网络请求——不阻塞 UI 线程
    private fun makeNetworkRequest(){
        //通过 viewModelScope 启动协程
        viewModelScope.launch {
            // 此处编写具体的网络请求操作
        }
    }

    // 此处不需要重写 onCleared()
}
```


### 7、reactivestreams-ktx

* 引用方式

```xml
implementation "androidx.lifecycle:lifecycle-reactivestreams-ktx:$version"
```

`lifecycle-reactivestreams-ktx` 可以让我们通过 `ReactiveStreams` 发布者（publisher） 构建一个 可观察的 `LiveData` 流 （observable LiveData stream）

举个例子，假设 数据库 中有少量的用户。在我们的 app 中，我们把数据库加载到内存中，然后在 UI 界面中展示用户数据。在实现这个需求时，我们可能会使用 `RxJava`. Jetpack 的 [Room](https://developer.android.google.cn/topic/libraries/architecture/room) 组件库会把用户数据作为 Flowable（流动数据），在这种情况下，我们必须让 Rx 发布者订阅 fragment 或 activity 的生命周期。

借助 `LiveDataReactiveStreams`, you can benefit from RxJava and its rich set of operators and work-scheduling capabilities while also working with the simplicity of LiveData, as shown in the following example:

```kotlin
val fun getUsersLiveData() : LiveData<List<User>> {
    val users: Flowable<List<User>> = dao.findUsers()
    return LiveDataReactiveStreams.fromPublisher(users)
}
```

### 8、navigation-xx-ktx

* 引用方式

```xml
implementation "androidx.navigation:navigation-runtime-ktx:$version"
implementation "androidx.navigation:navigation-fragment-ktx:$version"
implementation "androidx.navigation:navigation-ui-ktx:$version"
```

Each component of the Navigation library has its own KTX version that adapts the API to be more succinct and Kotlin-idiomatic.

使用扩展函数和属性代理实现携带参数向目标的导航跳转。示例如下：

```kotlin
//cnpeng: 这一块儿没用过，还看不大明白
class MyDestination : Fragment() {

    // Type-safe arguments are accessed from the bundle.
    val args by navArgs<MyDestinationArgs>()

    ...
    override fun onViewCreated(view: View, savedInstanceState: Bundle?) {
        view.findViewById<Button>(R.id.next)
            .setOnClickListener {
                // Fragment extension added to retrieve a NavController from
                // any destination.
                findNavController().navigate(R.id.action_to_next_destination)
            }
     }
     ...

}
```

### 9、WorkManager KTX 

* 引用方式

```xml
implementation "androidx.work:work-runtime-ktx:$rootProject.workVersion"
```

WorkManager KTX 通过为 `Operations` 和 `ListenableFutures ` 增加扩展函数提升了对 kotlin 协程的支持——实现了对当前协程的挂起。示例如下：

```kotlin
// CnPeng 没用过，暂时理解不深
// Inside of a coroutine...

// Run async operation and suspend until completed.
WorkManager.getInstance()
        .beginWith(longWorkRequest)
        .enqueue().await()

// Resume after work completes...
```


