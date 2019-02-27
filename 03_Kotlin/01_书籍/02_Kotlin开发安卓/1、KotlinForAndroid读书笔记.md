

该文件夹下内容都是基于 Kotlin For Android 总结的读书笔记。

KFA中文版：https://wangjiegulu.gitbooks.io/kotlin-for-android-developers-zh/ba_mainactivity_zhuan_huan_cheng_kotlin_dai_ma.html

在 KFA 书中，以一个天气预报的例子呈现了Kotlin在安卓编码中的应用


## DAY1
对应书中的章节：**编写你的第一个类**

```
使用Kotlin配合RecyclerView实现简单列表
```


### MainActivity.kt

新建一个 activity之后，不要忘了手动导入 `import kotlinx.android.synthetic.main.activity对应的xml文件名.*`

它的作用是可以让你直接通过xml中的id引用控件，不用再去写 find(resId) 

```
//注意：不要忘了加上这句导包语句——import kotlinx.android.synthetic.main.activity_main.*  

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val items = listOf<String>("星期1", "星期2", "星期3", "星期4", "星期5", "星期6", "星期7")

        recyclerView.layoutManager = LinearLayoutManager(this)
        recyclerView.adapter = RvAdapter(items)

    }
}
```


### activity_main.xml
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".firstClass.MainActivity">

    <android.support.v7.widget.RecyclerView
        android:id="@+id/recyclerView"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />

</android.support.constraint.ConstraintLayout>
```

### RvAdapter.kt
```

/**
 * 作者：CnPeng
 * 时间：2018/5/10
 * 功用：
 * 其他：
 */
class RvAdapter(val items: List<String>) : RecyclerView.Adapter<RvAdapter.ViewHolder>() {

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(TextView(parent.context))
    }

    override fun getItemCount(): Int = items.size

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        holder.textView.text = items[position]
    }

    class ViewHolder(val textView: TextView) : RecyclerView.ViewHolder(textView)
}
```

---

## DAY2
对应KFA书中的章节：**变量和属性**

## 1、基本数据类型与Java的区别
* 字符 Char 不能直接作为一个数字来使用，必须先通过 toInt() 转换。（这一点与Java不同）
* 位运算：或运算--or(Java中用|)，与运算——and(Java中用&)
* String字符串可以向数组一样执行切片和遍历


## 2、Anko库
* Anko是为了替代xml的，使用代码来生成布局的一个库。其中还有很多函数和库，可以让我们少些许多模板代码


## 3、扩展函数
* 扩展函数的意思就是指，在不改变现有类的代码的基础上，在外部为该类定义一个方法。
* 扩展函数看起来就像属于这个类一样，而且我们可以使用this关键字，也可以直接调用该类中的public方法


### （1）、扩展函数的示例——为Context类扩展一个 toast 方法

```
/**
* 作者：CnPeng
* 时间：2018/5/10 下午9:20
* 功用：为 Context类扩展 toast 方法。这样所有的Context子类都可以直接调用 toast 方法
* 说明：扩展函数的格式——类名.扩展方法名()
*/
fun Context.toast(message: CharSequence, duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this, message, duration).show()  //这里的this 指向的是Context
}
```

**注意：上面我们为Context 扩展的toast方法在 Anko 中已经包含了，所以我们直接引入Anko库即可**


### (2)、使用Kotlin扩展函数实现简单的网络请求
在执行简单数据的网络请求时，我们可以直接使用Kotlin帮我们扩展的一些函数：

* **URL(url).readText()** 

读取指定url 的内容。——不再需要我们从头写一个 HttpUrlConnection 了


* **doAsync{ }**

执行异步任务。——不再需要去写一个Thread了，内部封装了

* **uiThread{ }**

在主线程中执行任务（回归主线程）。——不再需要我们写Handler 了 ，内部封装了

```

//网络请求要记得加权限：<uses-permission android:name="android.permission.INTERNET"/>

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val url = "https://www.baidu.com"

        doAsync {    //执行异步任务。主线程不能执行耗时操作，所以使用 doAsync 执行异步任务
            getDataFromServer(url)
            uiThread {  //回到主线程
                AnkoLogger("TAG").error { "回到主线程" }  //连Log都封装了。。。
            }
        }
    }

    private fun getDataFromServer(url: String) {
        val jsonStr = URL(url).readText()
        AnkoLogger("网络数据").error(jsonStr)
    }
}
```

>UiThread 与调用者具有相同的生命周期。
假设在Activity中调用了 UiThread , 如果 activity.isFinishing 返回true , 将不会再执行UiThread 中的内容。这样在activity销毁时就不会产生崩溃

---


## DAY3


### 1、数据类的基本概念
* 数据类关键字：data
* 数据类定义格式：
```
data class 类名(val 变量名：变量类型，val 变量名：变量类型)
```

示例：
```
//定义一个天气预报类，包含：date 日期，temperature 温度，details 详情
data class ForeCast(val date: Date, val temperature: Float, val details: String)
```

### 2、数据类的复制--copy()

假设我们已有了一个对象A，我们还需要一个对象B，对象B中只有一个属性值与A不一致，此时，我们可以考虑使用数据类的复制。代码如下：

```
val foreCast = ForeCast(Date(), 27.5f, "天气晴朗")
val foreCast1 = foreCast.copy(temperature = 33.0f)  //copy之后返回一个新的对象

AnkoLogger("TAG").error { "日期${foreCast.date},气温${foreCast.temperature},天气状况${foreCast.details}" }
AnkoLogger("TAG").error { "日期${foreCast1.date},气温${foreCast1.temperature},天气状况${foreCast1.details}" }
AnkoLogger("TAG").error { "copy是否是同一个对象：${foreCast == foreCast1}" }
```
在上面的代码中，我们使用 copy() 复制除了一个新的对象，并赋予了新的 temperature 属性值。

运行结果截图：
![](https://gitee.com/uploads/images/2018/0511/100922_bae51b84_930142.png "屏幕截图.png")


### 3、属性映射

将属性值赋值给指定的变量。格式：
```
val (变量1，变量2，变量3)=被映射的对象      
```
**注意：**
* = 前面（ ）中的变量数量不能超过 被映射对象中声明的属性数量
* 被映射对象中的属性值会按照顺序依次赋值给 变量1、变量2、变量3...


```
val foreCast = ForeCast(Date(), 27.5f, "天气晴朗")
val (curDate, curTemperature, curDetails) = foreCast

AnkoLogger("TAG").error { "日期:$curDate,气温：${curTemperature}，天气状况：$curDetails" }
```

上述代码的映射操作，本质上等价于：
```
val curDate1 = foreCast.component1()    //取出 foreCast对象中第一个属性值
val curTemperature1 = foreCast.component2()
val curDetails1 = foreCast.component3()
```

### 4、将json串转换成数据类

#### （1）、Companion Objects (伴生对象)

伴生对象的代码块中声明的内容基本等同于Java中的静态代码块。

```
Companion Object{
    //属性、常量、函数等
}
```

#### （2）、将json解析成数据bean

KFA  中所使用的天气API地址：https://openweathermap.org/    。

`由于按照书中代码无法调通相应的接口，所以，此处直接使用固定的json串演示`


**请求类**

`直接创建 ForeCastRequest 类文件`
```
/**
 * 作者：CnPeng
 * 时间：2018/5/15
 * 功用：
 * 其他：将json字符串解析成对象
 */
class ForeCastRequest() {
    
    //伴生对象
    companion object {
        //在KFA书中，此处定义了相应的请求地址
        val forecastJsonStr = "{\"id\":6940463,\"name\":\"JiNan\",\"country\":\"CN\"}"
    }

    //使用Gson解析数据
    fun execute(): City {
        //在KFA书中，此处定义了一个获取网路数据的方法
        AnkoLogger("TAG").error { "Json字符串是$forecastJsonStr" }
        return Gson().fromJson(forecastJsonStr, City::class.java)
    }
}
```

**响应数据被解析成的数据类**

`新建 ForeCastResponse.kt 文件，然后在其中创建 data 数据类`
```
/**
 * 作者：CnPeng
 * 时间：2018/5/15
 * 功用：
 * 其他：数据bean类
 */

data class City(
    val id: Int,
    val name: String,
    val country: String
)
```

**调用**
```
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val jiNanForeCast = ForeCastRequest().execute()
        AnkoLogger("TAG").error {
            "城市名称${jiNanForeCast.name},城市所属国家${jiNanForeCast.country}"
        }
    }
}
```

#### (3)、JsonToKotlinClass——Kotlin下的GsonFormat


插件地址：https://github.com/wuseal/JsonToKotlinClass
在上述地址中与介绍集成和使用的方法


### 5、构建 domain 层

#### (1)、指定别名
当我们使用了两个相同名字的类，我们可以给其中一个指定别名，这样我们在调用的时候就不需要写完整的包名了。
```
import com.cnpeng.androidwithkotlin.firstClass.bean.ForeCastRequest as Request

//调用的时候直接使用 Request 即可。最终还是指向 ForeCastRequest
```

#### （2）、Demain集合的转换
```
   fun convertForecastListToDomain(list: List<Forecast>): List<ModelForecast> {

        //遍历现有集合并返回一个新的集合
        return list.map {
            convertForecastItemToDemain(it)
        }
    }

    private fun convertForecastItemToDemain(forecast: Forecast): ModelForecast {
        //返回通过构造方法创建的 ModelForecast 对象
        return ModelForecast(forecast.dt, forecast, weather[0].description, forecast.temp.max.toInt(), forecast.temp.min.toInt());
    }

```

### 6、在UI中绘制数据
#### （1）、异步获取网络数据，然后主线程中调用结果

```
doAsync {
     //开启异步任务：RequestForecastCommand("94043")——从网络获取编码为94043的城市的天气预报。.execute()——将数据转换长Demain集合
     val result = RequestForecastCommand("94043").execute()
     uiThread {
          //在主线程中调用result
          forecastList.adapter = ForecastListAdapter(result)
     }
}
```

#### (2)、使用kotlin实现RecyclerView的适配器

```
class ForecastListAdapter(val weekForecast: ForecastList) : RecyclerView.Adapter<ForecastListAdapter.ForecastViewHolder>() {
    override fun getItemCount(): Int = weekForecast.dailyForecast.size

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ForecastViewHolder {
        return ForecastViewHolder(TextView(parent.context))
    }

    override fun onBindViewHolder(forecastHolder: ForecastViewHolder, position: Int) {
        //这里引用了 with(){} 函数，小括号中接收一个对象，大括号中包含一个引用该对象的函数
        with(weekForecast.dailyForecast[position]) {
            forecastHolder.textView.text = "$date - $description - $high/$slow "    //直接引用对象中的函数
        }
    }

    class ForecastViewHolder(val textView: TextView) : RecyclerView.ViewHolder(textView)
}
```

### (3)、with(){} 函数
with(){} 函数是 kotlin 标准库中的函数，（）小括号中接收一个数据对象，{} 大括号中接收一个函数。这个大括号中的函数可以直接引用小括号中的数据对象，并作为该对象的扩展函数。



---
## DAY4

kotlin 中有一些固定含义的操作符可以供我们使用。


### 1、操作符及其方法对照表

#### （1）、对照表
![](https://gitee.com/uploads/images/2018/0515/204329_5a4d4dd0_930142.png "屏幕截图.png")

![](https://gitee.com/uploads/images/2018/0515/204401_7de5dce4_930142.png "屏幕截图.png")

![](https://gitee.com/uploads/images/2018/0515/204424_a797e5c4_930142.png "屏幕截图.png")

#### （2）、操作符重载
关键字：operator
格式举例：`operator fun get(position: Int): Forecast = dailyForecast[position]`


**重载操作符**
```
data class ForecastList(val city: String, val country: String, val dailyForecast: List<Forecast>) {
    
    //重载 a[i] 操作符。这样重载之后我们在 adapter 中想要取 dailyForecast数据时就不用再次去获取dailyForecast了
    operator fun get(position: Int): Forecast = dailyForecast[position]
    fun size(): Int = dailyForecast.size
}

data class Forecast(val date: String, val description: String, val heigh: Double, val low: Double)

```

**重构适配器**
```
class RvAdapter2(val items: ForecastList) : RecyclerView.Adapter<RvAdapter2.ViewHolder>() {
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ViewHolder {
        return ViewHolder(TextView(parent.context))
    }

    override fun getItemCount(): Int = items.size()    //这里直接取集合大小

    override fun onBindViewHolder(holder: ViewHolder, position: Int) {
        with(items.get(position)) {    //这里直接拿到的是 dailyForecast集合中 Forecast对象数据
            holder.textView.text = "$date - $description - $heigh - $low"
        }
    }

    class ViewHolder(val textView: TextView) : RecyclerView.ViewHolder(textView)
}
```

**调用适配器**
```
class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val forecast1 = Forecast("180515", "大雨", 34.0, 23.0)
        val forecast2 = Forecast("180516", "中雨", 33.0, 22.0)
        val forecast3 = Forecast("180517", "小雨", 32.0, 21.0)
        val forecast4 = Forecast("180518", "没雨", 31.0, 20.0)
        val forecast5 = Forecast("180519", "晴天", 30.0, 19.0)
        val forecastListBean = ForecastList("济南", "中国", listOf(forecast1, forecast2, forecast3, forecast4, forecast5));

        recyclerView.layoutManager = LinearLayoutManager(this);
        val adapter = RvAdapter2(forecastListBean);
        recyclerView.adapter = adapter;
    }

}
```

### 2、扩展函数中的操作符
为已经存在的 ViewGroup 类扩展操作符函数，我们就可以向访问List中的元素一样去访问 ViewGroup的子View。

```
//为 ViewGroup 扩展一个 get(position) 方法
operator fun ViewGroup.get(position: Int): View = getChildAt(position)

val container = find<ViewGroup>(R.id.container)
//调用扩展函数取出其中的0索引子view
val chilidView = container[0]    
```

### 3、为RecyclerView条目添加点击事件

#### （1）、为View扩展 ctx
在 Anko 中为 Activity 和 Fragment 等扩展了一个 ctx 属性，该属性对应的值就是 context 。但是并没有为 View 扩展这个属性，所以，就需要我们手动的去为 View扩展这个属性。

**新建 ViewExtensions.kt File文件**
```
val View.ctx:Context    //声明ctx的类型
get()=context    //为 ctx 声明 get() 方法，对应的值是 View.getContext(),简写为 context 
```

#### (2)、为天气预报的条目增加点击事件

整体思路：

* 在 Adapter中声明一个 内部类OnItemClickListener 接口
* 在 Adapter的构造中加入 OnItemClickListener 参数
* 在 ViewHolder的构造中传入 OnItemClickListenr 参数
* 在 ViewHolder中为条目设置点击事件
* 在 Activity中构造 Adapter对象


**内部类接口**

```
  public interface OnItemClickListener {
        operator fun invoke(forecast: Forecast)
    }
```

**activity中初始化适配器——注意匿名对象的构造方式**

```
recyclerView.adapter = ForecastListAdapter(forecastList, object : ForecastListAdapter.OnItemClickListener {
            override fun invoke(forecast: Forecast) {
                toast(forecast.description)
            }
        })
``` 

**其他代码省略**


---

## DAY5

Lambdas 表达式是一种很简单的定义匿名函数的方法。

Lambdas 避免我们去写一些包含了某些函数的抽象类或者接口，然后在函数中去实现这些抽象类或接口。

Kotlin中我们把一个函数作为另一个函数的参数。

### 1、简化setOnCliclListener

#### (1)、完整的点击事件
```
  //点击事件的完整写法
    bt1.setOnClickListener(object : OnClickListener {
      override fun onClick(v: View?) {
        toast(getString(R.string.action_click))
      }
    })
```

#### （2）、转为 Lambdas 表达式

```
// 因为 onClickListener 接口中只有一个函数，所以可以与 setOnClickListener 合并成一个。下面{ } 中的内容表 示 OnClickListener接口及其重写的方法，箭头左边的view表示 onClick中传递的参数，箭头后面表示我们自定义的事件处理

bt1.setOnClickListener({ view -> toast(getString(R.string.action_click)) })

```

#### （3）、Lambdas —— 省略未使用的参数
```
//因为箭头左边的参数 view 未被使用，所以，可以直接省略这个参数和箭头，只保留我们的事件处理函数
bt1.setOnClickListener({ toast(getString(R.string.action_click)) })
```

#### （4）、Lambdas —— 末尾参数为函数时可以外移
```
//setOnClickListener 中最后一个参数是 一个函数，所以，可以将这个函数挪到小括号外面
bt1.setOnClickListener() { toast(getString(R.string.action_click)) }
```

#### （5）、Lambdas —— 只有一个参数并且是函数，省略小括号
```
//setOnClickListener 中只有一个参数并且是个函数，所以，我们可以省略小括号
bt1.setOnClickListener { toast(getString(R.string.action_click)) }
```

#### （6）、代码提示中的 setOnClickListener
![](https://gitee.com/uploads/images/2018/0517/110449_afe4cbc0_930142.png "屏幕截图.png")

上图中的第一种方式是没有省略小括号的。
第二种方式是省略小括号之后的。


### 2、使用 Lambda 修改 ForecastListAdapter

####（1）、ForecastListAdapter修改
```
class ForecastListAdapter(val weekForecast: ForecastList, val itemClickEvent: (Forecast) -> Unit) : RecyclerView.Adapter<ForecastListAdapter.ForecastViewHolder>() {
    override fun getItemCount(): Int = weekForecast.dailyForecast.size

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ForecastViewHolder {
        return ForecastViewHolder(TextView(parent.context), itemClickEvent)
    }

    override fun onBindViewHolder(forecastHolder: ForecastViewHolder, position: Int) {
        //这里引用了 with(){} 函数，小括号中接收一个对象，大括号中包含一个应用该对象的函数
        with(weekForecast.get(position)) {
            forecastHolder.textView.text = "$date - $description - $heigh - $low "    //直接引用对象中的函数
        }
    }

    class ForecastViewHolder(val textView: TextView, val itemClick: (Forecast) -> Unit) : RecyclerView.ViewHolder(textView)

// 我们都不用再声明接口了，直接在需要click 事件的地方直接用  变量名:(参数类型)->Unit 替代即可
//    public interface OnItemClickListener {
//        operator fun invoke(forecast: Forecast)
//    }
}
```

![](https://gitee.com/uploads/images/2018/0517/113013_95b3fb08_930142.png "屏幕截图.png")
根据上面的代码和图示，我们可以知道，图中用红色圈出来的内容就表示声明了一个点击事件参数。




#### （2）、Activity中构建Adapter的修改

```

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        val forecastList = ForecastList("济南", "中国", listOf(Forecast("180516", "雨", 10.0, 10.0)))

        //正常 Lambda 写法
        recyclerView.adapter = ForecastListAdapter(forecastList, { forecast -> toast(forecast.description) })

        //因为itemClick函数只接收一个参数，所以可以用it关键字，这样，就不用声明箭头和箭头左边的参数
        recyclerView.adapter = ForecastListAdapter(forecastList, { toast(it.description) })

        //末尾参数为函数，函数可外移
        recyclerView.adapter = ForecastListAdapter(forecastList) { toast(it.description) }
    }

    data class ForeCast(
            val date: Date,
            val temperature: Float,
            val details: String
    )
}
```


### 3、扩展语言

#### （1）、内联函数 —— with(){ } 


* **with（）{} 函数的源码解析**
```
/**
 * Calls the specified function [block] with the given [receiver] as its receiver and returns its result.
 */
@kotlin.internal.InlineOnly
public inline fun <T, R> with(receiver: T, block: T.() -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    return receiver.block()
}
```

with 函数时一个内联函数，接收连个参数，一个时 T 类型的 receiver , 一个是 block（） 函数。返回的结果是 receiver 调用 block() 之后得到的数据。

因为第二参数是一个函数，所以，可以将函数体挪到 小括号外面。

由于扩展函数是针对于参数的，所以，扩展函数中可以直接引用参数中的属性和方法

* **with(){ } 函数的调用示例**

```
with(forecast){
    Picasso.with(itemView.ctx).load(iconUrl).into(iconView)
    dateView.text=date  //此处实际等价于 forecast.date, with(){} 函数中扩展函数是针对于 参数的，所以，可以直接引用参数的属性和方法
    descriptionView.text=description
    maxTemperatureView.text="$high"
    minTemperatureView.text="$slow"
    itemView.setOnClickListener{
        itemClick(this) //这里的this就代表 forecast 对象
    }
}

```

#### （2）、内联函数

* 内联函数用 inline 修饰
* 内联函数会在编译的时候被替换，而不是真正的方法调用。使用内联函数有利于鉴于内存分配和运行时开销。
* 普通函数在编译时会被编译成相应的函数对象。

上面使用的 with(){ } 就是一个内联函数

#### （3）、自定义内联函数

* **定义内联函数，该函数只有 Lollipop以上版本可以调用**
```
/**
 * 作者：CnPeng
 * 时间：2018/5/17 下午3:18
 * 功用：创建内联函数，只有 Lollipop 以上版本才会执行 code() 函数
*/
inline fun supportLollipop(code: () -> Unit) {
     if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
         code()
     }
}
```

* **内联函数调用**
```
supportLollipop {
    //如果版本高于 Lollipop 将状态栏改为 GREEN 
    window.statusBarColor = Color.GREEN
}
```

---

## DAY6

### 1、修饰符

* public ：公开可用

* private ：私有可用，最低一级的范围内可用

* protected ：同一个包中可用

* internal ：module范围内可用
    


### 2、构造器
* 构造函数默认都是 public 的
* 构造函数可以通过 private 变成私有的,示例如下：
```
//私有构造方法：关键字——private constructor
class User private constructor(a:Int){
    //todo sth
}
```

* 将属性变成私有的
```
//将属性变成私有的
class User(private var a: Int) {

}
```


---

## DAY7

[Kotlin Android Extensions(点击查看kotlin官方介绍)](https://kotlinlang.org/docs/tutorials/android-plugin.html) 是 Kotlin团队开发的可以提升Android开发效率的插件。

它里面实现了对View的绑定，有了这个绑定之后，我们在使用view的时候，就不需要再去 findViewById ，而是直接使用 view 的 id 即可获取这个view对象。


### 1、如何使用 Kotlin Android Extensions

>注意：以下两种方式任选其一即可

#### (1)、步骤1：gradle文件中增加依赖

* **方式1：在当前module的gradle中增加如下内容**
```
//在当前module的gradle文件中 android 节点外部插入该内容
apply plugin: 'kotlin-android-extensions'
```

* **方式2：在当前project的gradle中增加依赖**

```
buildscript {
    ext.kotlin_version = '1.2.41'
    ext.support_version = '27.1.1'
    ext.anko_version = '0.10.5'


    repositories {
        google()
        jcenter()
    }
    dependencies {
        //其他依赖省略。。。下面这个是新增的
        classpath "org.jetbrains.kotlin:kotlin-android-extensions:$kotlin_version"
    }
}
```

#### （2）、步骤2：导入 kotlinx.android.synthetic.xxx 实现布局文件的视图绑定

##### 1)、导入格式：

在我们的 activity 或者 fragment 中手动输入如下 import 语句
```
import kotlinx.android.synthetic.main.布局文件名.*
```

##### 2）、绑定 普通布局文件视图
假设我们有一个 activity_main.xml文件，如果我们想在对应的 MainActivity.kt 文件中使用 KAE，则导入语句如下：
```
import kotlinx.android.synthetic.main.activity_main.*
```

##### 3）、绑定 include 的布局文件视图

假设我们在布局文件中通过 include 将 temp.xml 布局文件引入到了 activity_main.xml中，那么，我们也可以实现include 布局的视图绑定。

* 布局文件
```
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/container"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    tools:context=".firstClass.activity.MainActivity">


    <!--include 一个布局文件-->
    <include layout="@layout/temp" />

</android.support.constraint.ConstraintLayout>
```

* 导入语句实现视图绑定
```
//注意：如果单纯的只是需要 被 include 中的view，可以不用 import ...activity_mian.*
import kotlinx.android.synthetic.main.temp.*
```


#### （3)、绑定inflate后得到的view的视图属性

##### 1)、绑定的格式
在自定义View或者适配器中，需要使用inflate 得到一个view对象，如果需要绑定其中的视图属性，需要按如下格式：
```
import kotlinx.android.synthetic.main.布局文件名.view.*
```

##### 2）、示例代码：

* 完整代码
```

//注意，对于inflate得到的view，如果想绑定视图，就需要用这种格式
import kotlinx.android.synthetic.main.temp.view.*

/**
 * 作者：CnPeng
 * 时间：2018/5/16
 * 功用：
 * 其他：
 */
class ForecastListAdapter(val weekForecast: ForecastList, val itemClickEvent: (Forecast) -> Unit) : RecyclerView.Adapter<ForecastListAdapter.ForecastViewHolder>() {
    override fun getItemCount(): Int = weekForecast.dailyForecast.size

    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): ForecastViewHolder {
        val itemView = LayoutInflater.from(parent.ctx).inflate(R.layout.temp, parent, false)

        return ForecastViewHolder(itemView, itemClickEvent)
    }

    override fun onBindViewHolder(forecastHolder: ForecastViewHolder, position: Int) {
        forecastHolder.setData(weekForecast[position])
    }

    class ForecastViewHolder(val textView: View, val itemClick: (Forecast) -> Unit) : RecyclerView.ViewHolder(textView) {
        fun setData(forecast: Forecast) {
            with(forecast) {
                //注意：这个itemView是对 RecyclerView.ViewHodler 的公共常量 —— itemView的引用
                itemView.bt2.text = "$date - $description - $heigh - $low "
            }
        }
    }
}
```

* 核心代码：

```
//注意，对于inflate得到的view，如果想绑定视图，就需要用这种格式
import kotlinx.android.synthetic.main.temp.view.*
```

```
 //注意：这个itemView是对 RecyclerView.ViewHodler 的公共常量 —— itemView的引用
itemView.bt2.text = "$date - $description - $heigh - $low "
```


---

## DAY8

### 1、Application 单例化——普通定义模式

#### （1）、定义单例Application
```
class App : Application() {

    companion object {
        var instance: Application? = null   //？表示该对象可以为null
        fun instance() = instance!!     // !! 表示断言非空
    }

    override fun onCreate() {
        super.onCreate()
        instance = this
    }
}
```

#### （2）、清单文件中引用

```
    <application
        android:name=".firstClass.App"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
   
    </application>
```


### 2、属性委托

所谓属性委托，即是将属性值委托给其他对象

当我们使用属性的set()/get()时，会触发属性委托对象的 setValue()/getValue()

* 写法示例：
```
class Example {
    var p: String by Delegate()
}
```

其他参考：http://ebnbin.com/2017/10/23/kotlin-delegated-properties/


### 3、标准委托

kotlin标准委托库中提供了如下几种委托方法：

#### （1）、lazy()
* 它包含一个 Lambda ，当第一次执行 getValue 的时候会调用这个lambda。
* 也就是说，**通过 lazy() 委托的属性可以延迟初始化 —— 第一次真正调用的时候被初始化，之后不会再执行初始化操作**。这样，有利于内存的节省。
* lazy() 是线程安全的

* 示例代码：
```
class App : Application() {

    val dbHelper: SQLiteOpenHelper by lazy {
        //applicationContext 指向 ContextWrapper 类的 getApplicationContext（）
        MyDataBaseHelper(applicationContext)
    }

    override fun onCreate() {
        super.onCreate()

        //因为 dbHelper是通过 lazy() 委托的，所以，在声明dbHelper的时候并没有执行初始化，当此次调用dbHelper的时候才执行了它的初始化
        val db = dbHelper.writableDatabase
    }
}
```

#### （2）、observable()
* 它可以帮我们监测某个属性的变化
* **当被监测的属性的set方法被调用时，它会执行我们执行的 Lambda表达式**
* 在执行 Lambda 表达式时会暴露三个值：被委托的属性、属性的旧值、属性的新值

```
class User(val db: MyDataBase) {

    //在 observable（）的lambda 表达式中，接收两个参数，一个是被委托属性的默认值，一个是函数。在函数中会暴露三个参数：被委托的属性，属性的旧值，属性的新值
    var name by Delegates.observable("") { 
        property, oldValue, newValue ->

        //将被委托的属性、属性的旧值、属性的新值存储到数据库中
        db.saveChanges(property, oldValue, newValue)
    }
}
```

#### (3)、vetoable()

veto : 否决、禁止

* 这是一个特殊的 observable()
* Lambda回调中需要返回一个boolean值，只有该值为true时才会改变被代理的属性值，否则， 即便回调被触发了也不会改变被代理的属性值。

##### 1）**摘录 vetoable() 方法的注释：**
>The value of the property hasn't been changed yet, when this callback is invoked.
>
>If the callback returns `true` the value of the property is being set to the new value,and if the callback returns `false` the new value is discarded and the property remains its old value.

vetoable（）接收两个参数，第一个参数是默认值，第二个参数是一个表达式。

##### 2）示例代码

在下面的代码中，只有新值 >=2 时才会将新值赋给被代理的属性
```
 var positiveNum = Delegates.vetoable(0) {
        property, oldValue, newValue ->

        //1、Lambda表达式中，最后一句即为返回值。而且此处不用加 return ，加了也不能被编译
        //2、vetoable()代理中，最后一句必须返回一个boolean值，true时才会改变被代理的属性值，反之，不保存
        newValue >= 2
    }
```


#### （4）、notNull()

##### 1)、notNull()代理的使用场景
[该段内容摘录自：try.kotlin.org](https://try.kotlinlang.org/#/Examples/Delegated%20properties/NotNull%20property/NotNull%20property.kt)


>Users frequently ask what to do when you have a non-null var, but you don't have an appropriate value to assign to it in constructor (i.e. it must be assigned later)?
>
译：在kotlin中当我们需要定义一个 非 null 的变量，但是它的值只能通过次构造函数或其他方式设置时，我们该怎么做呢？
（ 补充：kotlin中有主构造和次构造之分，主构造中不能包含表达式。类名后面跟括号，括号中有参数的方式属于主构造，次构造是一个init函数，参考：https://www.kotlincn.net/docs/reference/classes.html ）

>You can't have an uninitialized non-abstract property in Kotlin. You could initialize it with null, but then you'd have to check every time you access it. Now you have a delegate to handle this. If you read from this property before writing to it, it throws an exception,after the first assignment it works as expected.
>
译：在 kotlin 中，所有非抽象的属性都必须具有初值。你可能会想，我们可以直接给这个属性设置的初值为null, 但这样就需要我们在每次使用时做一次非空判断，其实，我们完全可以使用 notNull()代理方式实现这种非空处理


##### 2）、notNull()的方法说明
摘自 Delegates 类中的 notNull()方法注释：
>Returns a property delegate for a read/write property with a non-`null` value that is initialized not during object construction time but at a later time. 
>
>Trying to read the property before the initial value has been assigned results in an exception.

译：在对象构造完成之后会返回一个被代理的属性值——这个值是 non_null的（注意：重点是——构造完成之后，即属性初始化之后）。如果是在属性初始化之前去调用这个变量，会抛出一个异常——` throw IllegalStateException("Property ${property.name} should be initialized before get.")`


##### 3）、示例代码
[该段内容摘录自：try.kotlin.org](https://try.kotlinlang.org/#/Examples/Delegated%20properties/NotNull%20property/NotNull%20property.kt)
```
import kotlin.properties.Delegates

class User {
    var name: String by Delegates.notNull()

    fun init(name: String) {
        this.name = name
    }
}

fun main(args: Array<String>) {
    val user = User()
    //因为没有初始化name属性就直接调用，所以此处会报错
    //println(user.name)

    //通过 init 构造函数初始化 name 属性
    user.init("Carl")  
    //初始化之后再调用不会报错。  
    println(user.name)
}
```

##### 4）、优化Application单例
在该篇文章开始的时候，我们给 App 类中的 instance 赋予了一个 null 值，这样，我们在每次使用的时候都需要先判断是否为空。既然我们现在已经知道了 notNull() 代理，下面，我们就改造一下：

```
class App : Application() {
    companion object {
        var instance: App by Delegates.notNull()
    }

    override fun onCreate() {
        super.onCreate()
        instance = this
    }
}
```


#### （5）、map委托
* 将属性委托给一个 map 集合，内容通过map映射的方式取出对应的属性值。
* map中的 key 就是属性名，value 就是属性对应的值
* 这种方式通常用在 json解析等动态的事项中

[参考——把属性储存在映射中](https://www.kotlincn.net/docs/reference/delegated-properties.html)

```

class User1(val map: Map<String, Any?>) {
    //如果属性声明为 val ,则 主构造中需要使用 Map。Any类似于Java中的Object，是一个超级父类；？表示可空
    val name1: String by map
    val age1: Int by map
}

class User2(val map: MutableMap<String, Any?>) {
    //属性声明为 var , 主构造函数中使用 MutableMap,表示一个可以被修改的map
    var name2: String by map
    var age2: Int by map
}

fun main(array: Array<String>) {
    val user1 = User1(mapOf(
            //多个 map 元素之间用逗号间隔
            "name1" to "张三",
            "age1" to 23
    ))

    val user2 = User2(mutableMapOf(
            "name2" to "李四",
            "age2" to 24

    ))
    println("用户1姓名是${user1.name1},年龄是${user1.age1}")
    println("用户2姓名是${user2.name2},年龄是${user2.age2}")
}
```


### 4、自定义委托
##### 1）、自定义委托类有两种方式：
* 继承 ReadWriteProperty 或 ReadOnlyProperty
* 通过操作符重载关键字 operator 重写 getValue / setValue 函数

##### 2）、补充：非空三目运算
格式：`a ?: b`

含义：判断 a 是否为 null，如果不为null返回 a 的值，否则返回 b 的值


##### 3）、示例代码：模仿 notNull() 代理实现自定义代理类
```
/**
 * 自定义代理类——方式1。
 * 自定义代理类时：如果该代理类可以获取和设置属性值，就继承 ReadWriteProperty;如果只获取不设置，则继承 ReadOnlyProperty
 *
 * 该类包含一个属性 mValue
 * 我们实现的效果是：被该 mValue 代理的属性，只能赋值一次，之后不允许再被赋值。这其实是模仿了 notNull() 代理
 */
class NotNullSingleValueVar<T> : ReadWriteProperty<Any?, T> {
    private var mValue: T? = null

    //基本写法：
    // override fun getValue(thisRef: Any?, property: KProperty<*>): T {
    //    return mValue ?: throw IllegalStateException("${property.name}还没有被初始化")
    // }

    //因为此处的函数体只是一个表达式，所以，直接省略大括号并将表达式的值直接赋给T
    override fun getValue(thisRef: Any?, property: KProperty<*>): T =
            mValue ?: throw IllegalStateException("${property.name}还没有被初始化")


    override fun setValue(thisRef: Any?, property: KProperty<*>, value: T) {
        //只允许为 mValue 变量赋值一次，
        this.mValue = if (null == mValue) value else throw IllegalStateException("${property.name}已经被初始化了")
    }
}

/**
 * 自定义代理类——方式2。
 * 直接通过 操作符重载关键字 operator 来重写 getValue 和 setValue 方法
 *
 * 该代理类实现的效果与 NotNullSingleValueVar 一致。
 */
class NotNullSingleValuevar2<T> {
    private var mValue: T? = null

    operator fun getValue(thisRef: Any?, kProperty: KProperty<*>): T =
            mValue ?: throw IllegalStateException("${kProperty.name}还没有被初始化")

    operator fun setValue(thisRef: Any?, kProperty: KProperty<*>, value: T) {
        mValue = if (null == mValue) value else throw IllegalStateException("${kProperty.name}已经被初始化了")
    }
}
```

### 5、利用自定义委托完善单例的 Application

在 notNull() 代理一节中，我们使用 notNull() 代理优化了 单例Application，但是，由于 instance 是一个 var ,还是可以在应用中被修改 —— 而 **notNull() 代理只能代理 var 属性，不能代理 val 属性**。所以，此处可以使用我们自定义的 代理类继续完善 单例 Application。

##### 1）、创建一个单例代理类对象
```
/**
 * 创建单例类，在单例类中定义一个获取自定义代理类对象的方法
 */
object CustomDelegate {

    //标准写法
    fun <T> notNullSingleValue() {
        NotNullSingleValueVar<T>()
    }

    //缩略写法：大括号中只有一句表达式，所以省略
    fun <T> notNullSingleValue2() = NotNullSingleValueVar<T>()
}
```

##### 2）、修改 Application单例

```
class App : Application() {

    companion object {
        var instance: App by CustomDelegate.notNullSingleValue()
    }

    override fun onCreate() {
        super.onCreate()
        instance = this
    }
}
```


---

## DAY9

### 1、ManagedSQLiteOpenHelper

ManagedSQLiteOpenHelper 是 Anko提供的一个简化 SQLite 数据库操作的 抽象帮助类，内部为我们做了一层封装，可以让我们不再写一些SQL语句。


### 2、定义表

创建 object 类，其中包含一些属性，这些属性就是数据表的列名。然后创建表的时候直接引用，这样可以避免我们列名拼写错误。

> 在 KFA书中，是定义了一个 Tables.kt 的 file 文件，然后该文件中包含如下两个 object

```
object CityFrocastTable{
    val NAME="CityForecast"
    val ID="_id"
    val CITY="city"
    val COUNTRY="country"
}

object DayForecastTable{
    val NAME="DayForeCast"
    val ID="_id"
    val DATE="date"
    val DESCRIPTION="description"
    val HIGH="high"
    val LOW="low"
    val ICON_URL="iconUrl"
    val CITY_ID="cityId"
}
```


### 3、实现SQLiteOpenHelper

自定义 OpenHelper类，实现 ManagedSQLiteOpenHelper.

#### （1）、完整示例代码
`App.instance 是我们之前定义的 Application单例类中的属性`

```
class MyForecastDbHelper : ManagedSQLiteOpenHelper(App.instance, MyForecastDbHelper.DB_NAME, null, MyForecastDbHelper.DB_VERSION) {
    companion object {
        val DB_NAME = "forecast.db"
        val DB_VERSION = 1

        //使用 lazy{ } 代理延时初始化 instance。被调用之前不执行始化。lazy{} 是线程安全的
        val instance: MyForecastDbHelper by lazy { MyForecastDbHelper() }
    }
    
    //注意，默认重写 onCreate 时，db 是可空的。要么使用时手动判断非空，要么直接去掉末尾的？
    override fun onCreate(db: SQLiteDatabase?) {
        //db!! 断言db非空，
        db!!.createTable(CityForecastTable.NAME, true,
                Pair(CityForecastTable.ID, INTEGER + PRIMARY_KEY),
                Pair(CityForecastTable.CITY, TEXT),
                Pair(CityForecastTable.COUNTRY, TEXT)
        )

        //db?. 表示安全调用，在db不为空的时候调用后面的函数
        db?.createTable(DayForecastTable.NAME, true,
                DayForecastTable.ID to INTEGER + PRIMARY_KEY + AUTOINCREMENT,
                DayForecastTable.DATE to INTEGER,
                DayForecastTable.DESCRIPTION to TEXT,
                DayForecastTable.HIGH to INTEGER,
                DayForecastTable.LOW to INTEGER,
                DayForecastTable.ICON_URL to TEXT,
                DayForecastTable.CITY_ID to INTEGER
        )
    }

    //这里的db：SQLiteDatabase? 中的 ？表示可空，如果带有这个？在调用时就要判断，如果不判断就把 ？去掉
    override fun onUpgrade(db: SQLiteDatabase?, oldVersion: Int, newVersion: Int) {
        //注意，实际编写应用时可不能直接这么删表
        db?.dropTable(CityForecastTable.NAME, true)
        db!!.dropTable(DayForecastTable.NAME, true)
        onCreate(db)
    }
}
```

#### （2）、内容解析

##### 1）、ManagedSQLiteOpenHelper 构造中接收的参数

我们继承 ManagedSQLiteOpenHelper的时候需要传递四个参数，其实这四个参数就是 SQLiteOpenHelper 构造中所接收的参数，分别是：
* 第一个参数：Context 上下文
* 第二个参数：String  数据库表名，表名中带有 .db 后缀
* 第三个参数：CursorFactory cursor工厂类，通常传入 null
* 第四个参数：int    数据库版本号


##### 2）、db.onCreate()函数
接收四个参数，分别是：
* 第一个参数：String 数据库表名
* 第二个参数：Boolean 是否检查被创建的表是否存在，
* 第三个参数：vararg 可变参数，是列名。基本写法是用 Pair()对象包裹列名和值得数据类型


##### 3）、Pair() 
Pair(A,B) 只是对两个数据的包裹，并没有实际含义。onCreateTable()接收到pair 之后，会将其转成map.

* **Pair类中的注释**
```
Represents a generic pair of two values.
There is no meaning attached to values in this class, it can be used for any purpose.
Pair exhibits value semantics, i.e. two pairs are equal if both components are equal.
```

* **onCreateTable源码实现**
```
fun SQLiteDatabase.createTable(tableName: String, ifNotExists: Boolean = false, vararg columns: Pair<String, SqlType>) {
    val escapedTableName = tableName.replace("`", "``")
    val ifNotExistsText = if (ifNotExists) "IF NOT EXISTS" else ""
    execSQL(
        //此处将pair转成了 List
        columns.map { col ->
            "${col.first} ${col.second.render()}"
        }.joinToString(", ", prefix = "CREATE TABLE $ifNotExistsText `$escapedTableName`(", postfix = ");")
    )
}
```

* **.map()的方法注释**
```
Returns a list containing the results of applying the given [transform] function
to each element in the original array.
```

##### 4)、to()

* **to()函数源码**
```
/**
 * Creates a tuple of type [Pair] from this and [that].
 *
 * This can be useful for creating [Map] literals with less noise, for example:
 * @sample samples.collections.Maps.Instantiation.mapFromPairs
 */
public infix fun <A, B> A.to(that: B): Pair<A, B> = Pair(this, that)
```

通过上述代码可以很明显的看出来，这个方法是返回了一个 Pair()对象。

A 对应 this,B 对应 that



### 4、依赖注入

#### (1)、什么是依赖注入？

此处内容来自知乎：https://www.zhihu.com/question/32108444/answer/220819349

依赖注入（DI）和控制反转（IOC）基本是一个意思，因为说起来谁都离不开谁。

简单来说，a依赖b，但a不控制b的创建和销毁，仅使用b，那么b的控制权交给a之外处理，这叫控制反转（IOC），而a要依赖b，必然要使用b的instance，那么
* 通过a的接口，把b传入；
* 通过a的构造，把b传入；
* 通过设置a的属性，把b传入；

这个过程叫依赖注入（DI）。

那么什么是IOC Container？随着DI的频繁使用，要实现IOC，会有很多重复代码，甚至随着技术的发展，有更多新的实现方法和方案，那么有人就把这些实现IOC的代码打包成组件或框架，来避免人们重复造轮子。所以实现IOC的组件或者框架，我们可以叫它IOC Container。


#### （2）、修改 MyForecastDbHelper 中 context 为依赖注入模式

> Android 中 依赖注入比较好的框架是 Dagger ,但是，对于一些简单的依赖注入，我们可以不使用它

在 MyForecastDbHelper 中，实现依赖注入的最简单方式就是提供一个 **带有默认参数的构造方法**。
这样，如果我们调用时传入了 ctx ，就会使用传入的ctx ； 如果没有传入，就是用默认的 APP.instance

```
class MyForecastDbHelper(ctx: Context = App.instance) : ManagedSQLiteOpenHelper(ctx, MyForecastDbHelper.DB_NAME, null, MyForecastDbHelper.DB_VERSION) {
    //其他内容省略
}
```

修改了上述的构造方法之后，我们构造 MyForecastDbHelper 对象的时候就有了两种方式：

```
  val dbHpler: MyForecastDbHelper = MyForecastDbHelper()
  val dbHpler1: MyForecastDbHelper = MyForecastDbHelper(xxxcontext)    //xxxcontext是 Context对象
```



---

## DAY10

Kotlin中与集合相关的类主要有：
 * Iterable
 *  MutableIterable
 *   Collection
 *   MutableCollection
 *   List
 *   MutableList
 *   Set
 *   MutableSet
 *   Map
 *   MutableMap


### 1、总数操作符

#### （1）、any()

* 只要有一个元素符合给出的条件就返回 true

```
val list = listOf(1, 2, 3, 4, 5, 6)

//只要集合有元素就返回 true
list.any()

//只要集合中有任意元素符合{ } 中的条件就返回true
list.any { it % 2 == 0 }
```

#### (2)、all()
* 全部元素都符合判断条件才返回true

```
val list = listOf(1, 2, 3, 4, 5, 6)
//返回true
list.all { it < 10 }
//返回false
list.all { it % 2 == 0 }
```

#### (3)、count()
* 返回符合条件的元素个数
```
list.count { it % 2 == 0 }
```

#### (4)、fold()

* 在一个初始值的基础上，将集合元素按照{ } 中指定的函数进行运算，并返回最终结果.
* fold() 中接收两个参数，一个是起始值，一个是运算函数。
    * 运算函数中又包含两个参数，第一个是变量，第二个是 lambda运算式。
    * 起始值也会参与一次运算

```
val list = listOf(1, 2, 3)

// 等价于 0+1+2+3 = 6
list.fold(0) { a, b -> b + a }
// 等价于 2*1*2*3 = 12
list.fold(2) { a, b -> b * a }
```

#### （5）、foldRight()
* 和fold一样，也是将元素按照指定的函数进行运算，但，是从最后一个元素开始，从右向左倒序运算

##### 1)、示例代码
```
val list = listOf(1, 2, 3)

//返回0
list.foldRight(2) { a, b -> a - b }
```

##### 2）、foldRight() 函数的具体实现
```
public inline fun <T, R> List<T>.foldRight(initial: R, operation: (T, acc: R) -> R): R {
    var accumulator = initial
    if (!isEmpty()) {
        val iterator = listIterator(size)
        while (iterator.hasPrevious()) {
            accumulator = operation(iterator.previous(), accumulator)
        }
    }
    return accumulator
}
```

##### 3）、分析示例代码
根据 foldRight() 函数的源码实现，我们可以知道`list.foldRight(2){a,b->a-b}` 的 运算过程如下：

```
//2 是初始值
acc=2 
acc=(3-acc)=1
acc=(2-acc)=1
acc=(1-acc)=0
```

#### （6）、forEach()
* 遍历元素，并执行{ } 中指定的函数

```
val list = listOf(1, 2, 3)

//it 就是被遍历出来的元素，这一个固定的标识
list.forEach { println(it) }
```

#### （7）、forEachIndexed()
* 同时遍历元素和索引

```
val list = listOf(1, 2, 3)
list.forEachIndexed { index, data -> println("索引是$index,元素值是$data") }
```

#### (8)、max()
* 返回最大的一项，如果没有则返回 null

```
list.max()
```

#### (9)、maxBy()
* 根据指定函数运算之后返回最大的元素，没有则返回null

```
 val list = listOf(1, 2, 3)
 println(list.maxBy { it / 2 })
```

在上述代码中，我们得到的是2 ，因为，2/2=1 , 3/2=1后者的商不大于前者，所以直接返回前者。如果再加一个元素 4，那么我们得到的返回值将是4，因为 4/2=2 ，得到的商大于前两者。


#### （10）、min()
* 返回最小的一个元素，没有则返回null
```
list.min()
```

#### (11)、minBy()
* 根据指定函数运算之后返回最小的元素，没有则返回null

```
 val list = listOf(1, 2, 3)

//返回值1
 println(list.minBy { it / 2 })
```

#### (12)、none()
* 如果没有任何元素符合指定的函数运算，返回 true
```
val list = listOf(1, 2, 3)
 
//返回true
println(list.none { it / 2 == 3 })
```

#### (13)、reduce()
* 类似于fold(),也是从第一个元素起执行指定的运算函数。但是，reduce()不接收默认值

```
val list = listOf(1, 2, 3)
//返回6
println(list.reduce { a, b -> a + b })
```

#### （14）、reduceRight()
* 类似于 foldRight(),也是从最后一个元素起执行指定的运算函数，但是，reduceRight()不接收默认值

```
val list = listOf(1, 2, 3)
println(list.reduceRight { a, b -> a + b })
```

#### (15)、sumBy()
* 先执行{}中指定的运算函数，然后再将结果累加

```
val list = listOf(1, 2, 3)

//返回 9
println(list.sumBy { it + 1 })
```

### 2、过滤操作符

#### （1）、drop()
* 移除前X个元素, X 是 drop()接收的参数

```
val list = listOf(1, 2, 3)

//移除前两个元素，只剩了一个3
println(list.drop(2))
```

#### （2）、dropWhile()
* 从第一项开始去除符合表达式的全部元素

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)
    //将 <3 的元素移除，剩余输出 [3, 4, 5]
    println(list.dropWhile { it < 3 })
}
```

#### （3）、dropLastWhile()
* 从最后一项开始去掉符合表达式的全部元素

**注意:根据下面的示例可以发现，有时候貌似输出的结果并不是我们期望的结果啊。。。**

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)
    //    [1, 2, 3, 4, 5] 这一个似乎并不是我们期望的输出结果啊。。。。。
    println(list.dropLastWhile { it < 3 })
    //    [1, 2, 3, 4]
    println(list.dropLastWhile { it > 4 })
}
```

#### （4）、filter()
* 过滤并保留所有符合给定表达式的元素
```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)
    //输出结果 [1, 2]
    println(list.filter { it < 3 })
    //输出结果 [4, 5]
    println(list.filter { it > 3 })
}
```

#### （5）、filterNot()
* 过滤并保留所有不符合条件的元素

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)
    //输出结果 [3,4,5]
    println(list.filterNot { it < 3 })
    //输出结果 [1,2,3]
    println(list.filterNot { it > 3 })
}
```

#### （6）、filterNotNull()
* 过滤并保留所有非空元素

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)
    //输出结果 [1,2,3,4,5]
    println(list.filterNotNull())
}
```

注意：按照我们上面的写法， IDEA会报虚线，提示我们`This inspection reports filter-like calls on already filtered collections, e.g. listOf("abc").filterNotNull()`

我们按照提示可知， filterNotNull() 主要是在定义 list 的时候调用，示例如下：

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5).filterNotNull()
    println(list)
}
```
但是，像上面这种写法，依旧会报虚线，但这次是提示我们上面的代码可以简化， 简化之后如下：
```
fun main(args: Array<String>) {
    val list = listOfNotNull(1, 2, 3, 4, 5)
    println(list)
}
```


#### (7)、slice()
* 过滤并保留指定索引的元素
```
fun main(args: Array<String>) {
    val list = listOfNotNull(1, 2, 3, 4, 5)

    //将需要过滤的索引先组成一个list,该 list 作为 slice 的参数。输出结果[1,2,3]
    println(list.slice(listOf(0, 1, 2)))
}
```


#### (8）、take()
* 取出前 X 个元素，X是take()接收的参数

```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)

    //取出前3个元素，输出 [1,2,3]
    println(list.take(3))
}
```

#### （9）、takeLast()
* 取出后 X 个元素
```
fun main(args: Array<String>) {
    val list = listOf(1, 2, 3, 4, 5)

    //取出后3个元素，输出 [3, 4, 5]
    println(list.takeLast(3))
}
```

#### （10）、takeWhile()
* 取出符合给定条件的元素
> **注意：是从第一个元素开始，判断元素是否符合表达式，符合则继续判断；如果不符合则不再继续判断，直接将结果输出**

```
fun main(args: Array<String>) {
    val list = listOf(2, 1, 4, 3, 5)

    //输出 [2, 1]，第三个元素 4 不小于4 ，所以终止判断
    println(list.takeWhile { it < 4 })
    //输出 [2]，第二个元素 1 不小于 1 ，所以终止判断
    println(list.takeWhile { it > 1 })
}
```


### 3、映射操作符

#### （1）、flatMap()
* 遍历list中的每一个元素，然后按照指定规则将这些元素分别组成list,最后将这些list合并

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3)
    //输出结果：[1, 2, 2, 3, 3, 4]
    println(list1.flatMap { listOf(it,it+1) })
}
```

#### （2）、groupBy()
* 根据给定表达式进行分组,返回的是 map

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出结果：{odd=[1, 3, 5], even=[2, 4]}
    println(list1.groupBy { if (it % 2 == 0) "even" else "odd" })
} 
```

#### (3)、map（）
* 先将元素按照指定表达式进行转换，然后将转换后的结果组成一个新的list

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出结果：[2, 4, 6, 8, 10]
    println(list1.map { it * 2 })
}
```

#### (4)、mapIndexed()
* 先将元素按照指定规则进行转换，然后将转换后的结果组成一个新的list.(与 map 的区别在于，索引会参与运算)

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出结果：[1, 3, 5, 7, 9]
    println(list1.mapIndexed { index, data -> index + data })
}
```

#### （5）、mapNotNull()
* 将所有非null元素按照指定规则转换，然后组成新的 List . 参考 filterNotNull() 

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    println(list1.mapNotNull { it * 2 })
}
```


### 4、元素操作符

#### （1）、contains()
* 是否包含某个元素

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //返回 true
    println(list1.contains(2))
}
```

#### (2)、elementAt()
* 返回指定索引对应的元素。如果越界，会报索引越界异常

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 3 
    println(list1.elementAt(2))
}
```

#### （3）、elementAtOrElse()
* 返回指定索引对应的元素。如果索引越界，则按照 Else 中的内容给出一个默认值。

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 999。elementAtOrElse 接收两个参数：第一个是要查看的索引，第二个是默认值表达式，
    println(list1.elementAtOrElse(8, { 999 }))
}
```

#### （4）、elementAtOrNull()
* 返回指定索引对应的元素，如果索引越界，返回 null

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出结果 null
    println(list1.elementAtOrNull(8))
}
```

#### (5)、first()
* 返回符合给定条件的第一个元素。如果不存在，则报错：NoSuchElementException

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出2 
    println(list1.first { it % 2 == 0 })
}
```

#### （6）、firstOrNull()
* 返回符合给定条件的第一个元素，如果没有符合条件的元素则返回null

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出null
    println(list1.firstOrNull { it % 6 == 0 })
}
```

#### (7)、indexOf()
* 返回元素对应的索引

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 1
    println(list1.indexOf(2))
}
```

#### (8)、indexOfFirst()
* 返回第一个符合给定条件元素对应的索引。不存在则返回 -1 

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 1
    println(list1.indexOfFirst { it % 2 == 0 })
    //不存在则返回 -1 
    println(list1.indexOfFirst { it % 6 == 0 })
}
```

#### (9)、indexOfLast()

* 返回最后一个符合给定条件的元素对应的索引，不存在则返回 -1 

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 3
    println(list1.indexOfLast { it % 2 == 0 })
    //不存在则返回 -1
    println(list1.indexOfLast { it % 6 == 0 })
}
```

#### （10）、last()
* 返回符合给定条件的最后一个元素，

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 4
    println(list1.last { it % 2 == 0 })
    //不存在则报错 NoSuchElementException
    println(list1.last { it % 6 == 0 })
}
```

#### （11）、lastIndexOf()
* 返回指定元素最后一次出现的位置

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 3
    println(list1.lastIndexOf(4))
    //不存在则输出 -1
    println(list1.lastIndexOf(7))
}
```

#### (12)、lastOrNull()
* 返回符合指定条件的最后一个元素，不存在则返回 null

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 4
    println(list1.lastOrNull { it % 2 == 0 })
    //不存在则输出 null
    println(list1.lastOrNull { it % 7 == 0 })
}
```

#### （13）、single()
* 返回符合指定条件的单个元素。如果没有符合条件的 或者 有多个元素符合条件，报错：IllegalArgumentException

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 3,只有单个符合条件时输出该元素
    println(list1.single { it % 3 == 0 })
    //有多个元素同时符合条件时，报错：IllegalArgumentException: Collection contains more than one matching element
    println(list1.single { it % 2 == 0 })
}
```

#### （14）、singleOrNull()
* 返回符合条件的单个元素，如果没有符合条件的或者有多个元素符合条件，返回 null

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    //输出 3,只有单个符合条件时输出该元素
    println(list1.singleOrNull { it % 3 == 0 })
    //有多个元素同时符合条件时，返回 null
    println(list1.singleOrNull { it % 2 == 0 })
}
```

### 5、生产操作符
#### (1)、zip()

* 合并两个集合中的元素得到一个新集合。新集合的大小以两个集合中小的那个集合决定。两个集合中相同索引的元素会按照指定的运算函数进行运算，将结果赋值给新集合对应的索引。
* 未指定表达式时，返回的是两个集合中相同索引元素组成的 Pair 的集合

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    val list2 = listOf(2, 3, 4, 5)

    // 未指定运算函数时的结果为：   [(1, 2), (2, 3), (3, 4), (4, 5)]
    println(list1.zip(list2))
    // 指定运算函数之后的结果为：    [3, 5, 7, 9]
    println(list1.zip(list2) { it1, it2 -> it1 + it2 })
}
```

> KFA 书中介绍合并功能时使用的是 merge(),但是kotlin 1.0 之后已经将该函数废弃了，改用 zip(),参考地址：参考文章：https://stackoverflow.com/questions/37448110/kotlin-error-unresolved-reference-merge-for-list-merge。


#### （2）、partition()
* 根据指定条件拆分集合，满足条件的元素组成一个集合，不满足条件的又组成一个集合，最后，将两个集合组成Pair作为返回值。Pair 中第一个元素是符合条件的集合，第二个元素为不符合条件的集合

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)

    //返回一个Pair  ([1, 2], [3, 4, 5])
    println(list1.partition { it < 3 })
}
```

#### (3)、plus (即 + 运算符)
* 拼接两个集合中的元素组成一个新的集合。**注意：这个 + 操作不等同于 Java中的 addAll()**

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    val list2 = listOf(6, 7, 8)
    
    //两个集合相加得到的结果为：[1, 2, 3, 4, 5, 6, 7, 8]
    val list3 = list1 + list2
    
    println(list3)
    
    //返回false,从而得知，+ 运算得到的是一个新的集合。所以，+ 并不等同于Java中的addAll()
    println(list1.equals(list1 + list2))
}
```

#### （4）、unZip()
* 从包含 Pair 的list中拆分出包含List 的 Pair 

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 2, 3, 4, 5)
    val list2 = listOf(6, 7, 8)

    val list3 = list1.zip(list2)

    //合并集合得到新的集合，新集合中元素为多个Pair，索引相同的组成一个Pair.  [(1, 6), (2, 7), (3, 8)]
    println(list3)

    //从包含Pair的List中，拆分出包含List的Pair.   ([1, 2, 3], [6, 7, 8])
    println(list3.unzip())
}
```

### 6、顺序操作符

#### (1)、reversed() / asReversed()


```
fun main(args: Array<String>) {
    val list1 = listOf(1, 3, 2, 4, 5)

    //    [5, 4, 2, 3, 1]
    println(list1.reversed())
    //    [5, 4, 2, 3, 1]
    println(list1.asReversed())

    //false
    println(list1 == list1.reversed())
    //false
    println(list1 == list1.asReversed())
}
```


* reversed()/asReversed()的区别
```
/**
 * Returns a reversed read-only view of the original List.
 * All changes made in the original list will be reflected in the reversed one.
 * @sample samples.collections.ReversedViews.asReversedList
 */
public fun <T> List<T>.asReversed(): List<T> = ReversedListReadOnly(this)

```

```
/**
 * Returns a list with elements in reversed order.
 */
public fun <T> Iterable<T>.reversed(): List<T> {
    if (this is Collection && size <= 1) return toList()
    val list = toMutableList()
    list.reverse()
    return list
}
```

#### (2)、sorted()
* 按照自然规则排序

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 3, 2, 4, 5)

    //    [1, 2, 3, 4, 5]
    println(list1.sorted())
    //    false
    println(list1 == list1.sorted())
}
```

#### (3)、sortedBy()
* 按照指定规则排序

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 3, 2, 4, 5)

    //   [2, 4, 1, 3, 5]
    println(list1.sortedBy { it % 2 })
    //    false
    println(list1 == list1.sortedBy { it % 2 })
}
```

#### （4）、sortedDescending()
* 自然降序排列

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 3, 2, 4, 5)

    //   [5, 4, 3, 2, 1]
    println(list1.sortedDescending())
    //    false
    println(list1 == list1.sortedDescending())
}
```

#### (5)、sortedByDescending()
* 根据指定条件降序排列

```
fun main(args: Array<String>) {
    val list1 = listOf(1, 3, 2, 4, 5)

    //   [1, 3, 5, 2, 4]
    println(list1.sortedByDescending { it % 2 })
    //    false
    println(list1 == list1.sortedByDescending { it % 2 })
}
```


---

## DAY11


### 1、创建数据库Model类

通过map代理创建Model类，将基本属性委托给 map.


* 下面的示例代码放置在 DbClasses.kt 文件中
`查阅KFA的示例DEMO发现，.kt 文件一般用来编写 model 类。.kt 类 用来编写一些工具类或者业务类`

```
/**
 * 作者：CnPeng
 * 时间：2018/6/5
 * 功用：数据库model类
 * 其他：
 */

class CityForeCast(val map: MutableMap<String, Any?>, val dayForCastList: List<DayForecast>) {

    //基本属性通过 Map 代理实现

    //城市id
    var id: Long by map
    var city: String by map
    var country: String by map

    //此处 constructor 标记的是次构造函数。次构造函数需要委托给主构造函数，委托时使用 this 关键字，并传入主构造需要的参数
    constructor(id: Long, city: String, country: String, dayForCastList: List<DayForecast>) : this(HashMap(), dayForCastList) {
        this.id = id
        this.city = city
        this.country = country
    }
}

class DayForecast(var map: MutableMap<String, Any?>) {
    var id: Long by map
    var cityId: Long by map
    var date: Long by map
    var description: String by map
    var high: Int by map
    var low: Int by map
    var iconUrl: String by map

    constructor(id: Long, cityId: Long, date: Long, descriotion: String, high: Int, low: Int, iconUrl: String) : this(HashMap()) {
        this.id = id
        this.cityId = cityId
        this.date = date
        this.description = description
        this.high = high
        this.low = low
        this.iconUrl = iconUrl
    }
}
```

### 2、写入和查询数据库


#### (1)、从数据库查询数据

##### 1）、ForecastDb.kt 类
```
/**
 * ForecastDbHelper 是自定义的继承自 ManagedSQLiteOpenHelper 的数据库帮助类。
 * DbDataMapper 是自定义的将数据库使用的 model 转换成普通 data model 的帮助类
 */
class ForecastDb(val forecastDbHelper: ForecastDbHelper = ForecastDbHelper.instance, val dataMapper: DbDataMapper) {

    /**
     * 查询某个城市指定日期时间段的天气预报情况
     */
    fun requestForecastByZipcode(zipCode: Long, date: Long) = forecastDbHelper.use {
        val selectCondition = "${DayForecastTable.CITY_ID}=? AND ${DayForecastTable.DATE}>=?"
        val dayForecastList = select(DayForecastTable.NAME)

                //设定查询条件。还有一个 where 查询方法，接收查询条件和 pair ,但是已经标记为废弃
                .whereSimple(selectCondition, zipCode.toString(), date.toString())

                //结果转换。这里使用的是一个扩展函数，内部实现没大看懂啊.DayForecast是前面定义的数据库使用的model
                .parseList { DayForecast(HashMap(it)) }

        val cityForecast = select(CityForecastTable.NAME)
                .whereSimple("${CityForecastTable.ID}=?", zipCode.toString())

                //这里使用的也是一个扩展函数，内部实现没大看懂，头疼.CityForecast 是前面定义的数据库使用的model
                .parseOpt { CityForecast(HashMap(it), dayForecastList) }

        //在 lambda 中最后一句作为返回值
        if (null != cityForecast) dataMapper.convertToDomain(cityForecast) else null
    }
}
```

##### 2）DbMapper.kt 类
```
class DbDataMapper {

    /**
     * 将数据库使用的 代理 Model 转换成程序使用的普通 data Model
     */
    fun convertToDomain(forecast: CityForecast) = with(forecast) {

        //dailyForecast 是定义在 CityForecast 中的常量集合。.map（）是将数据库使用的代理model 转换成 data Model 集合
        val dailyForecastList = dailyForecast.map { convertDayToDomain(it) }
        //lambda 表达式的最后一句是返回值，即，返回一个 ForecastList 对象
        ForecastList(_id, city, country, dailyForecastList)
    }
}
```

##### 3）、DatabaseExtensions.kt 文件

```
/**
 * 扩展函数
 */
public fun <T : Any> SelectQueryBuilder.parseList(parser: (Map<String, Any?>) -> T): List<T> =
        parseList(object : MapRowParser<T> {
            override fun parseRow(columns: Map<String, Any?>): T = parser(columns)
        })

/**
 * 扩展函数：将map中的内容转换成被代理的 bean
 */
fun <T : Any> SelectQueryBuilder.parseOpt(parser: (Map<String, Any?>) -> T): T? =
        parseOpt(object : MapRowParser<T> {
            override fun parseRow(columns: Map<String, Any?>): T = parser(columns)
        })
```

#### (2)、保存数据到数据库

















