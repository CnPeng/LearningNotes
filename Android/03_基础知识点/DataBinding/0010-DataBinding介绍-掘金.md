# 0010-DataBinding介绍-掘金

[原文：findViewById不香吗？为什么要把简单的问题复杂化？为什么要用DataBinding？](https://juejin.cn/post/7208817904560013373?utm_source=gold_browser_extension)


Android-MVVM-Databinding的原理、用法与封装
前言
说起 DataBinding/ViewBinding 的历史，可谓是一波三折，甚至是比 Dagger/Hilt 还要传奇。
说起依赖注入框架 Dagger2/Hilt ，也是比较传奇，刚出来的时候火的一塌糊涂，各种攻略教程，随后发现坑多难以使用，随之逐渐预冷，近几年在 Hilt 发布之后越发的火爆了。
而 DataBinding/ViewBinding 作为 Android 官方的亲儿子库，它的经历却更加的离奇，从发布的时候火爆，然后到坑太多直接遇冷，随之被其他框架替代，再到后面 Kotlin 出来之后是更加的冷门了，全网是一片吐槽，随着 Kotlin 插件废弃之后 ViewBinding 的推出而再度翻火...都够拍一部大片了。😅
说到这里了，在Android开发者，特别是没用过 DataBinding 的开发者心中可能都有一个大致的印象，DataBinding太坑了，太老了，更新慢，都是缺点，跑都跑不起来，狗都不用...😅😅

这也是 DataBinding/ViewBinding 框架的发展历程导致的，几起几落结果就给开发者留下了全是缺点这么个印象。
那么作为官方主推的 MVVM 架构指定框架 DataBinding 真的有这么不堪吗?😂
在目前看来 Android 客户端开发还没有进化到 Compose，我们目前的主流布局方案还是XML,而基于VMMV架构的 DataBinding 框架还是很有必要学习与使用的。💪
老话这么说，我可以不用，但是我要会。就算自己不用，至少也要能看懂别人的代码吧。
闲话不多说，下面就简单从几点分析一下，为什么Googel推荐使用 DataBinding/ViewBinding ，如何使用，以及基本的原理，最后推荐一些 DataBinding 的封装简化使用流程。

一、之前的方案有哪些不足
只要是 Android 开发的从业者，从开始学习起就知道找控件的方式是 findViewById，下面先讲讲它的大致原理。
我们以Activity中使用 findViewById 为例：
androidx.appcompat.app
    @Override
    public <T extends View> T findViewById(@IdRes int id) {
        return getDelegate().findViewById(id);
    }
复制代码
可以看到是通过委派类调用的，其实是调用到 Window 类中的 findViewById 方法：
    public <T extends View> T findViewById(int id) {
        if (id == NO_ID) {
        return null;
        }
        return findViewTraversal(id);
    }
复制代码
内部又调用到 ViewGroup 的 findViewTraversal 方法。内部又是遍历找 id 的逻辑

如果布局正好在此 ViewGroup 中那只遍历一次，如果嵌套的很深，则会一层一层的遍历去找 id ，这是会稍稍影响性能的。
并且我们在使用 findViewById 的时候是可能出现的错误问题：

需要强转的问题。
调用时机错误的问题。
响应式布局中由于布局差异导致空指针的问题。
Activity+Fragment架构中，Fragment初始化了但是没有添加到Activity中导致的问题。
如果一个Activity中有多个Fragment，Fragment中的控件名称又有重复的，直接使用findViewById会爆错。
同样的问题再Dialog与PopuoWindow都可能存在已初始化但没添加的问题。
当前Activity找到其他Activity的相同id，但真实不存在的问题。
由于重建、恢复导致的控件空指针问题。

等等，当然了，其中很多问题是逻辑问题导致的空指针，锅不能都扣到 findViewById 头上。就算我们使用其他的包括 DataBinding 的方案时也并不能完全避免空指针的，只能说尽量避免空指针。
这都不说了，关键是当布局中的 ID 很多的时候，需要写大量的 findViewById 模板代码。这简直是要命了，所以就引申出了很多框架或插件。
例如 XUtils，ButterKnife，FindViewByMe（插件）等。
虽然 XUtils，ButterKnife 这类插件可以专门对 findviewbyid 方法进行简化，但是还是需要写注解让控件与资源绑定，当然后期还专门有针对绑定的插件。
但是其本质还是 findViewById 那一套，再后来随着组件化与插件化的火热，类似 ButterKnife 在这样的架构中或多或少的有一些其他的问题 R R1 R2...总感觉乖乖的，有点鸡肋的意思，用的人也是越来越少了。
而随着 Kotlin 的流行，和 kotlin-android-extensions 插件的诞生，一切又不一样了，开发者也有了新的选择。
Kotlin 直接从语言层面支持 Null 安全，于是 DataBinding 在 Kotlin 语言的项目中基本上是销声匿迹了。
很多人可能就是因为 kotlin-android-extensions 插件从而使用 Kotlin 的，不需要手动 findviewbyid 了，实在是太爽了。
kotlin-android-extensions 是如何实现的，我们查看一下 Kotlin Bytecode 的字节码：
public final class MainActivity extends AppCompatActivity {
   private HashMap _$_findViewCache;

   protected void onCreate(@Nullable Bundle savedInstanceState) {
      super.onCreate(savedInstanceState);
      this.setContentView(1300023);
      TextView var10000 = (TextView)this._$_findCachedViewById(id.textView);
      var10000.setText((CharSequence)"Hello");
   }

   public View _$_findCachedViewById(int var1) {
      if (this._$_findViewCache == null) {
         this._$_findViewCache = new HashMap();
      }
      View var2 = (View)this._$_findViewCache.get(var1);
      if (var2 == null) {
         var2 = this.findViewById(var1);
         this._$_findViewCache.put(var1, var2);
      }
      return var2;
   }
}
复制代码
kotlin-android-extensions插件会帮我们生成一个_$_findCachedViewById()函数，优先从内存缓存 HashMap 中找控件，找不到就会调用原生的 findViewById 添加到内存缓存中，是的，就是我们常用的很简单的缓存逻辑。

后期的发展大家也知道了，随着 apply plugin: 'kotlin-android-extensions' 插件被官方背弃了，至于为什么被废弃，我个人大致猜测可能是：

底层还是基于 findViewById，还是会有 findViewById 的弊端，只是多了缓存的处理。
就算是多了缓存看起来很美，但缓存并不好用，在部分需要回收再次使用的场景，例如 RV.Adapter.ViewHolder 中存在缓存失效每次都 findViewById 而导致的性能问题（还不如不要呢）。
每一个 Page/Item 都需要一个 HashMap 来保存 View 实例，占用内存过大。
xml 中的 ID 没有跟页面绑定，一样有 findViewById 的那些问题，在当前 Activity 可以找到其他页面的 ID。

再而后 2019 年 Google 推出了 ViewBinding 终结一切，如果布局中的某个 View 实例隐含 Null 安全隐患，则编译时 ViewBinding 中间代码为其生成 @Nullable 注解。从而最大限度避免控件的空指针异常。并且由于视图绑定会创建对视图的直接引用，因此不存在因视图的 ID 无效而引发空指针异常。并且每个绑定类中的字段均具有与它们在 xml 文件中引用的视图相匹配的类型。这意味着不存在发生类转换异常的风险。
而 DataBinding 作为 ViewBinding 的老大哥则又一次登上了舞台。

**DataBinding VS ViewBinding ：**两者都能做 binding UI layouts 的操作，但是 DataBinding 还支持一些额外的功能，如双向绑定，xml中使用变量等。ViewBinding不会添加编译时间，而 DataBinding 会添加编译时间，并且 DataBinding 会少量增加 apk 体积, ViewBinding 不会。总的来说ViewBinding更加的轻量。
题外话：ButterKnife 的作者已经宣布不维护 ButterKnife，作者推荐使用 ViewBinding 了。
二、ViewBinding/DataBinding如何使用
由于 DataBinding 是与 AGP(Android Gradle 插件) 捆绑在一起的，所以我们不需要导依赖包，只需要在配置中启动即可。
老版本定义如下（4.0版本以下）：
android {
    viewBinding {
        enabled = true
    }
    dataBinding{
        enabled = true
    }
}
复制代码
新版本定义如下（4.0版本以上）：
android {
    buildFeatures {
        dataBinding = true
        viewBinding = true
    }
}
复制代码
配置完成之后在我们的xml根布局标签上 alt + enter,就可以提示转换为 DataBindingLayout了。

转换完成就是这样：
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:binding="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    tools:ignore="RtlHardcoded">

    <data>

    </data>

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/white"
        tools:viewBindingIgnore="true">

        <ImageView
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_gravity="center"
            android:src="@drawable/splash_center_blue_logo" />

    </FrameLayout>

</layout>

复制代码
可以看到多了一个data的标签，我们就可以在data中定义变量与变量的类型。
    <data>
        <import type="android.util.SparseArray"/>
        <import type="java.util.Map"/>
        <import type="java.util.List"/>
        <import type="android.text.TextUtils"/>
        <variable name="list" type="List&lt;String&gt;"/>
        <variable name="sparse" type="SparseArray&lt;String&gt;"/>
        <variable name="map" type="Map&lt;String, String&gt;"/>
        <variable name="index" type="int"/>
        <variable name="key" type="String"/>
    </data>
复制代码
import 是定义导入需要的类，variable是定义需要的变量是由外部传入，我们可以使用多种方式传入定义的variable对象。
例如：
    <data>

        <variable
            name="viewModel"
            type="com.hongyegroup.cpt_auth.mvvm.vm.UserLoginViewModel" />

        <variable
            name="click"
            type="com.hongyegroup.cpt_auth.ui.UserLoginActivity.ClickProxy" />

        <import type="com.guadou.lib_baselib.utils.NumberUtils" />

    </data>
复制代码
使用起来如下：
    <TextView
        android:id="@+id/tv_get_code"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginRight="@dimen/d_15dp"
        android:background="@{NumberUtils.isStartWithNumber(viewModel.mCountDownLD)?@drawable/shape_gray_round7:@drawable/shape_white_round7}"
        android:enabled="@{!NumberUtils.isStartWithNumber(viewModel.mCountDownLD)}"
        android:paddingLeft="@dimen/d_12dp"
        android:paddingTop="@dimen/d_5dp"
        android:paddingRight="@dimen/d_12dp"
        android:paddingBottom="@dimen/d_5dp"
        android:text="@={viewModel.mCountDownLD}"
        android:textColor="@{NumberUtils.isStartWithNumber(viewModel.mCountDownLD)?@color/white:@color/light_blue_text}"
        android:textSize="@dimen/d_13sp"
        binding:clicks="@{click.getVerifyCode}"
        tools:background="@drawable/shape_white_round7"
        tools:text="Get Code"
        tools:textColor="@color/light_blue_text" />
复制代码
页面的数据都保存在ViewModel中，页面的事件都封装在Click对象中，还能通过NumberUtils直接使用内部的方法了。
在Activity中就可以绑定 Activity 与 DataBinding 了，代码如下：
class MainActivity : AppCompatActivity() {
    private lateinit var mainBinding: ActivityMainBinding
    private lateinit var mainViewModel: MainViewModel by viewModels()

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        mainBinding = DataBindingUtil.setContentView<ActivityMainBinding>(this, R.layout.activity_main)
        mainBinding.lifecycleOwner = viewLifecycleOwner

        //设置变量（更容易理解）
        mBinding.setVariable(BR.viewModel,mainViewModel)

        //设置变量（更方便）
        mainBinding.viewModel = mainViewModel
         
    }
}
复制代码
其中 ActivityMainBinding 这个类就是系统生成的，生成规则是布局文件名称转化为驼峰大小写形式，然后在末尾添加 Binding 后缀。如 activity_main 编译为 ActivityMainBinding 。
现在的绑定比刚开始的 DataBinding 真的已经方便很多了。而 Fragment 的绑定有些许不同。
class MainFragment : Fragment() {
    private lateinit var mainBinding: FragmentMainBinding
    private lateinit var mainViewModel: MainViewModel by viewModels()

    override fun onCreateView(inflater: LayoutInflater, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        return setContentView(container)
    }

    fun setContentView(container: ViewGroup?): View {

        mainBinding = DataBindingUtil.inflate<ActivityMainBinding>(layoutInflater, R.layout.fragment_main, container, false)
        mainBinding.lifecycleOwner = viewLifecycleOwner

        //设置变量（更容易理解）
        mBinding.setVariable(BR.viewModel,mainViewModel)

        //设置变量（更方便）
        mainBinding.viewModel = mainViewModel

        return mBinding.root
    }
}
复制代码
如何在xml使用变量呢？
集合的使用：

android:text="@{list[index]}"

android:text="@{sparse[index]}"

android:text="@{map[key]}"

复制代码
文本的使用：
android:text="@{user.firstName, default=PLACEHOLDER}"

//常用的三元与判空
android:text="@{user.name != null ? user.name : user.nickName}"

android:text="@{user.name ?? user.nickName}"

android:visibility="@{user.active ? View.VISIBLE : View.GONE}"
复制代码
事件的简单处理：
android:onClick="@{click::onClickFriend}"

android:onClick="@{() -> click.onSaveClick(task)}"

android:onClick="@{(theView) -> click.onSaveClick(theView, task)}"

android:onLongClick="@{(theView) -> click.onLongClick(theView, task)}"

//控件隐藏不设置点击，显示才设置点击
android:onClick="@{(v) -> v.isVisible() ? doSomething() : void}"
复制代码
双向绑定：@= 与 @ 的区别
    <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@={click.etLiveData}" />

    <Textview
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@{click.etLiveData}" />    
复制代码
使用单向绑定的时候@{}，viewModel中的数据变化了，就会影响到TextView的显示。而双向绑定则是当EditText内部的文本发生变化了也同样会影响到viewModel中的数据变化。
三、DataBinding的进阶使用
关于 DataBinding 的基础使用，相信大家或多或少都有看过或者用过，知道基础使用就能在开发中实际开发了吗？太年轻了！
详细用过 DataBinding 的或多或少都遇到过一些坑，作为一个常年使用 DataBinding 的开发者，我对下面几点实际开发中遇到的一些印象深刻的知识点做一些实用的引申。
3.1 RV.Adapter中使用
与 Fragment 的使用方式类似，我们只需要绑定了 View 之后设置给ViewHodler即可。
class UserAdapter(users: MutableList<User>, context: Context) :
    RecyclerView.Adapter<UserAdapter.MyHolder>() {

    class MyHolder(val binding: TextItemBinding) : RecyclerView.ViewHolder(binding.root)
​
    private var users: MutableList<User> = arrayListOf()
    private var context: Context
​
    init {
        this.users = users
        this.context = context
    }
​
    override fun onCreateViewHolder(parent: ViewGroup, viewType: Int): MyHolder {
        val inflater = LayoutInflater.from(context)
        val binding: TextItemBinding = DataBindingUtil.inflate(inflater, R.layout.text_item, parent, false)
        return MyHolder(binding)
    }
​
    override fun onBindViewHolder(holder: MyHolder, position: Int) {
        holder.binding.user = users[position]
        holder.binding.executePendingBindings()
    }

    override fun getItemCount() = users.size
}
复制代码
3.2 自定义View的使用
比如我定义一个自定义View,在内部使用了自定义的属性，需要在 xml 中赋值，
<com.guadou.kt_demo.demo.demo12_databinding_texing.CustomTestView
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    binding:clickProxy="@{click}"
    binding:testBean="@{testBean}" />
复制代码
我们再自定义View的类中就可以通过 setXX 拿到这个赋值的属性了。
class CustomTestView @JvmOverloads constructor(context: Context?, attrs: AttributeSet? = null, defStyleAttr: Int = 0) :
    LinearLayout(context, attrs, defStyleAttr) {


    init {
        orientation = VERTICAL

        //传统的方式添加
        val view = CommUtils.inflate(R.layout.layout_custom_databinding_test)
        addView(view)

    }

    //设置属性
    fun setTestBean(bean: TestBindingBean?) {

        bean?.let {
            findViewById<TextView>(R.id.tv_custom_test1).text = it.text1
            findViewById<TextView>(R.id.tv_custom_test2).text = it.text2
            findViewById<TextView>(R.id.tv_custom_test3).text = it.text3
        }


    }

    fun setClickProxy(click: Demo12Activity.ClickProxy?) {
        findViewById<TextView>(R.id.tv_custom_test1).click {
            click?.testToast()
        }
    }

}
复制代码
如果我们的自定义View不是写在 XML 中，而是通过Java代码手动 add 到布局中，一样的可以通过 new 对象，设置自定义属性来实现一样的效果：
    //给静态的xml，赋值数据,赋值完成之后 include的布局也可以自动显示
    mBinding.testBean = TestBindingBean("haha2", "heihei2", "huhu2")

    //动态的添加自定义View
    val customTestView = CustomTestView(mActivity)
    customTestView.setClickProxy(clickProxy)
    customTestView.setTestBean(TestBindingBean("haha3", "heihei3", "huhu3"))

    mBinding.flContent.addView(customTestView)
    
复制代码
3.3 include与viewStub的使用
include 和 viewStub 的用法差不多，这里以 include 为例：
例如我们在 Activity 的 xml 布局中添加一个 include 的布局。
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:binding="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    tools:ignore="RtlHardcoded">

    <data>
        <variable
            name="testBean"
            type="com.xx.xx.demo.TestBindingBean" /> 
    </data>

    <FrameLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:background="@color/white"
        tools:viewBindingIgnore="true">

      ...

         <include
            layout="@layout/include_databinding_test"
            binding:click="@{click}"
            binding:testBean="@{testBean}" />

    </FrameLayout>

</layout>
复制代码
我们可以直接把 Activity 的自定义属性 testBean 传入到 include 布局中。
include_databinding_test:
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:binding="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="testBean"
            type="com.guadou.kt_demo.demo.demo12_databinding_texing.TestBindingBean" />

        <import
            alias="textUtlis"
            type="android.text.TextUtils" />
    </data>

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="vertical">

        <TextView
            android:layout_marginTop="15dp"
            android:text="下面是赋值的数据"
            binding:clicks="@{click.testToast}"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"/>

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{testBean.text1}" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{testBean.text2}" />

        <TextView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:text="@{testBean.text3}" />

    </LinearLayout>

</layout>

复制代码
这样在 include 的 xml 中能直接使用自定义属性来显示了。
而如果动态的 inflate 布局就和自定义 View 的处理方式类似了：
    mBinding.testBean = TestBindingBean("haha", "heihei", "huhu")

    //获取View
    val view = CommUtils.inflate(R.layout.include_databinding_test)
    //绑定DataBinding 并赋值自定义的数据
    DataBindingUtil.bind<IncludeDatabindingTestBinding>(view)?.apply {
        testBean = TestBindingBean("haha1", "heihei1", "huhu1")
    }

    //添加布局
    mBinding.flContent.addView(view)    
复制代码
3.4 自定义事件与属性
重点就是自定义的属性与事件处理了，一些喜欢在 xml 中写逻辑的都是基于此方式实现的，下面一起看看如何使用自定义属性：
Java语言的实现:
public class BindingAdapter {

    @android.databinding.BindingAdapter("url")
    public static void setImageUrl(ImageView imageView, String url) {
        Glide.with(imageView.getContext())
                .load(url)
                .into(imageView);
    }

}
复制代码
方法名不是关键，关键的是注解上面的值 "url"，才是在xml中显示的自定义属性，而方法中的参数，第一个是限定在哪一个控件上生效的，是固定的比传的参数，而第二个参数 String url 才是我们自定义传入的参数。
这个例子很简单，就是传入url，在 ImageView 上通过 Glide 显示图片。
用Kotlin的方法实现就更简单了：
@BindingAdapter("url")
fun setImageUrl(view: ImageView, url: String?) {
    if (!url.isNullOrEmpty()) {
        Glide.with(view.context)
                .load(imageUrl)
                .into(view)
    }
}
复制代码
或者使用Kotlin的顶层扩展函数也能实现：
@BindingAdapter("url")
fun ImageView.setImageUrl(url: String?) {
     if (!url.isNullOrEmpty()) {
        Glide.with(view.context)
                .load(imageUrl)
                .into(this)
    }
}
复制代码
三种定义的方式都是相同的，除此之外，我们除了加一个参数，我们还能加入多个参数，甚至还能指定可选参数和必填参数：
  @android.databinding.BindingAdapter(value = {"imgUrl", "placeholder"}, requireAll = false)
    public static void loadImg(ImageView imageView, String url, Drawable placeholder) {
        GlideApp.with(imageView)
                .load(url)
                .placeholder(placeholder)
                .into(imageView);
    }
复制代码
使用：
    <ImageView
        android:id="@+id/img_view"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:adjustViewBounds="true"
        binding:imgUrl="@{user.url}"
        binding:placeholder="@{@drawable/ic_launcher_background}"
    />
复制代码
这里 requireAll = false 表示我们可以使用这两个两个属性中的任一个或同时使用，如果 requireAll = true 则两个属性必须同时使用，不然会在编译器报错，现在也 AS 会明确的指出错误地方方便修改的。
3.5 自定义转换器
Converters 转换器其实是用的比较少，但是在一些特别的场景有奇效，特别是做一些多主题，国际化的时候。
<Button
    android:onClick="toggleIsError"
    android:text="@{isError ? @color/red : @color/white}"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
复制代码
这样就可以根据颜色来显示不同的文本：
@BindingConversion
public static int convertColorToString(int color) {
    switch (color) {
        case Color.RED:
            return R.string.red;
        case Color.WHITE:
            return R.string.white;
    }
    return R.string.black;
}
复制代码
3.6 DataBinding中字符串的各种特殊处理
如果说 DataBinding 用的最多的控件，那必然是 TextView ，而文本的显示有多样的方式，国际化、占位符、Html/Span等多样的文本如何在 DataBinding 的 xml 中展示又是一个新的问题。
经过前面的基本使用和部分高级的使用，这里就直接放代码了。
1. databinding使用string format 占位符：
<string name="Generic_Text">My Name is %s</string>
android:text= "@{@string/Generic_Text(Profile.name)}"
复制代码
当然也可以直接使用字符串的，但是外面的一层要用单引号
 android:text='@{viewModel.mHoldAccount,default="22"}'
复制代码
2. 使用Html标签
<![CDATA[<font color=\'#FF9900\'>作品阅读次数<font color=\'#333333\'> %1$s </font>次</font>]]>

<data>
    <import type="android.text.Html"/>
</data>
...
 android:text="@{Html.fromHtml(@string/sxx_user_rank(user.readTimes))}"
复制代码
3.Html中使用三元表达式
错误方式：
android:text="@{task.title_total>0?Html.fromHtml(@string/task_title(task.title,task.title_num,task.title_total)):task.title}"
复制代码
正确方式：
android:text="@{Html.fromHtml(task.title_total>0?@string/task_title(task.title,task.title_num,task.title_total):task.title)}"
复制代码
4.default的实现
类似tools的实现:
android:text="@{viewModel.mYYPayLiveData.reward_points,default=@string/normal_empty}"
复制代码
等同于:
android:text="@{viewModel.mYYPayLiveData.reward_points}"
tools:text="@string/normal_empty"
复制代码
类似hilt的实现:
binding:text="@{viewModel.mSelectBankName}"
binding:default="@{@string/normal_empty}"
tools:text="@string/normal_empty"
复制代码
使用自定义属性完成：
@BindingAdapter("text", "default", requireAll = false)
fun setText(view: TextView, text: CharSequence?, default: String?) {
    if (text == null || text.trim() == "" || text.contains("null")) {
        view.text = default
    } else {
        view.text = text
    }
}
复制代码
四、DataBinding的简单原理
ViewBinding的生成过程，就是一系列处理 Tag 的逻辑。将布局中的含有databinding赋值的 Tag 控件存入bindings的Object的数组中并返回。

在 ActivityMainBindingImpl 生成类中该方法中将获取的 View 数组赋值给成员变量。（相比 findViewById 只遍历了一次）
DataBinding 通过布局中的 Tag 将控件查找出来，然后根据生成的配置文件进行对应的同步操作，设置一个全局的布局变化监听来实时更新，通过他的set方法进行同步。

所以我们才说 DataBinding 不参与视图逻辑，仅负责通知末端 View 状态改变，仅用于规避 Null 安全问题。
总的来说，DataBinding 的原理没有什么黑科技，就是是基于数据绑定和观察者模式的。它通过生成代码来完成UI组件和数据对象之间的绑定，并使用观察者模式来保持UI和数据之间的同步。
五、简化DataBinding的使用（封装）
可能有同学看了基本的使用和一些进阶的使用之后，更坚定了心中的想法，可去你的吧，使用这么麻烦，狗都不用...😅😅
别急，我们还能对一些固定的场景化的用法做一些封装嘛，反正常用的几种方法，有限并不包括于一些字符串处理，图片处理，数据适配器的处理，UI的处理等一些方法定义好了或者封装好了使用起来就是so easy！
5.1 Activity/Fragment主页面封装
一般关于Activity/Fragment 我们主要是封装的 DataBinding 与 ViewModel。
不同的人有不同的封装方法，有的用泛型+传参的方式，有的用泛型+反射的方式，有的封装了 DataBinding 的填充自定义属性逻辑。
下面分别演示不同的封装方式：
abstract class BaseVDBActivity<VM : ViewModel,VB : ViewBinding>(
   private val vmClass: Class<VM>， private val vb: (LayoutInflater) -> VB,
) : AppCompatActivity() {
 
    //由于传入了参数，可以直接构建ViewModel
    protected val mViewModel: VM by lazy {
        ViewModelProvider(viewModelStore, defaultViewModelProviderFactory).get(vmClass)
    }
 
    //如果使用DataBinding，自己再赋值
}

复制代码
这种方法使用了泛型+传参，使用的时候需要填入构造参数：
class MainActivity : BaseVDBActivity<ActivityMainBinding, MainViewModel>(
    ActivityMainBinding::inflate,
    MainViewModel::class.java
) {
    //就可以直接使用ViewBinding与ViewModel 
    fun test() {
        mBinding.iconIv.visibility = View.VISIBLE
        mViewModel.data1.observe(this) {
        }
    }
}
复制代码
如果是使用的 DataBinding，我们还能把 DataBinding 的属性赋值逻辑进行封装：
封装一个Config对象
class DataBindingConfig(
    private val layout: Int,
    private val vmVariableId: Int,
    private val stateViewModel: BaseViewModel
) {

    private var bindingParams: SparseArray<Any> = SparseArray()

    fun getLayout(): Int = layout

    fun getVmVariableId(): Int = vmVariableId

    fun getStateViewModel(): BaseViewModel = stateViewModel

    fun getBindingParams(): SparseArray<Any> = bindingParams

    fun addBindingParams(variableId: Int, objezt: Any): DataBindingConfig {
        if (bindingParams.get(variableId) == null) {
            bindingParams.put(variableId, objezt)
        }
        return this
    }

}
复制代码
使用 Config 对象给 DataBinding 赋值自定义属性的封装：
abstract class BaseVDBActivity<VM : BaseViewModel, VDB : ViewDataBinding> : BaseVMActivity<VM>() {

    protected lateinit var mBinding: VDB

    protected abstract fun getDataBindingConfig(): DataBindingConfig

    override fun getLayoutRes(): Int = -1

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        mBinding = DataBindingUtil.setContentView(this, getDataBindingConfig().getLayout())
        mBinding.lifecycleOwner = this
        mBinding.setVariable(
            getDataBindingConfig().getVmVariableId(),
            getDataBindingConfig().getStateViewModel()
        )
        val bindingParams = getDataBindingConfig().getBindingParams()
        bindingParams.forEach { key, value ->
            mBinding.setVariable(key, value)
        }
        init(savedInstanceState)
    }

}
复制代码
Fragment的封装也是大同小异：
abstract class BaseVDBFragment<VM : BaseViewModel, VDB : ViewDataBinding> : BaseVMFragment<VM>() {

    protected lateinit var mBinding: VDB

    override fun getLayoutRes(): Int = -1

    protected abstract fun getDataBindingConfig(): DataBindingConfig

    override fun onCreateView(
        inflater: LayoutInflater,
        container: ViewGroup?,
        savedInstanceState: Bundle?
    ): View? {
        mBinding =
            DataBindingUtil.inflate(inflater, getDataBindingConfig().getLayout(), container, false)
        mBinding.lifecycleOwner = viewLifecycleOwner
        mBinding.setVariable(
            getDataBindingConfig().getVmVariableId(),
            getDataBindingConfig().getStateViewModel()
        )
        val bindingParams = getDataBindingConfig().getBindingParams()
        bindingParams.forEach { key, value ->
            mBinding.setVariable(key, value)
        }
        return mBinding.root
    }

}
复制代码
我们使用的时候就直接赋值自定义属性：
class ProfileFragment : BaseFragment<ProfileViewModel, FragmentProfileBinding>() {

    override fun getDataBindingConfig(): DataBindingConfig {
        return DataBindingConfig(R.layout.fragment_profile, BR.viewModel, mViewModel)
            .addBindingParams(BR.click, ClickProxy())
    }

    private val articleAdapter by lazy { ArticleAdapter(requireContext()) }
    
    ...
       
        
}
复制代码
具体的代码太多了，可以参照文章结尾的项目。
5.2 RV.Adapter的封装
其实在之前的 RV.Adapter 使用中，我们也能基于这个 Adapter 封装，但是我们项目中使用的还是BRVAH，所以我们就基于此封装的。
open class BaseBindAdapter<T>(layoutResId: Int, br: Int)
    : BaseQuickAdapter<T, BaseBindAdapter.BindViewHolder>(layoutResId) {

    private val _br: Int = br

    override fun convert(helper: BindViewHolder, item: T) {
        helper.binding.run {
            setVariable(_br, item)
            executePendingBindings()
        }
    }

    override fun getItemView(layoutResId: Int, parent: ViewGroup?): View {
        val binding = DataBindingUtil.inflate<ViewDataBinding>(mLayoutInflater, layoutResId, parent, false)
                ?: return super.getItemView(layoutResId, parent)
        return binding.root.apply {
            setTag(R.id.BaseQuickAdapter_databinding_support, binding)
        }
    }

    class BindViewHolder(view: View) : BaseViewHolder(view) {
        val binding: ViewDataBinding
            get() = itemView.getTag(R.id.BaseQuickAdapter_databinding_support) as ViewDataBinding
    }
}
复制代码
使用的时候，可以选择继承这个基类实现：
class HomeArticleAdapter(layoutResId: Int = R.layout.item_article_constraint) :
        BaseBindAdapter<Article>(layoutResId, BR.article) {

    override fun convert(helper: BindViewHolder, item: Article) {
        super.convert(helper, item)

        helper.addOnClickListener(R.id.articleStar)
        helper.setImageResource(R.id.articleStar, if (item.collect) R.drawable.timeline_like_pressed else R.drawable.timeline_like_normal)
        else helper.setVisible(R.id.articleStar, false)

        helper.setText(R.id.articleAuthor,if (item.author.isBlank()) "分享者: ${item.shareUser}" else item.author)
        Timer.stop(APP_START)
    }
}
复制代码
甚至在一些简单的布局展示逻辑，我们都无需继承基类实现，直接：
 private val systemAdapter by lazy { BaseBindAdapter<SystemParent>(R.layout.item_system, BR.systemParent) }
复制代码
5.3 常用的自定义属性与事件效果
EditText:
/**
 * EditText的简单监听事件
 */
@BindingAdapter("onTextChanged")
fun EditText.onTextChanged(action: (String) -> Unit) {
    addTextChangedListener(object : TextWatcher {
        override fun afterTextChanged(s: Editable?) {
        }

        override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
        }

        override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
            action(s.toString())
        }
    })
}

var _viewClickFlag = false
var _clickRunnable = Runnable { _viewClickFlag = false }

/**
 * Edit的确认按键事件
 */
@BindingAdapter("onKeyEnter")
fun EditText.onKeyEnter(action: () -> Unit) {
    setOnKeyListener { _, keyCode, _ ->
        if (keyCode == KeyEvent.KEYCODE_ENTER) {
            KeyboardUtils.closeSoftKeyboard(this)

            if (!_viewClickFlag) {
                _viewClickFlag = true
                action()
            }
            removeCallbacks(_clickRunnable)
            postDelayed(_clickRunnable, 1000)
        }
        return@setOnKeyListener false
    }
}

/**
 * Edit的失去焦点监听
 */
@BindingAdapter("onFocusLose")
fun EditText.onFocusLose(action: (textView: TextView) -> Unit) {
    setOnFocusChangeListener { _, hasFocus ->
        if (!hasFocus) {
            action(this)
        }
    }
}

/**
 * 设置ET小数点2位
 */
@BindingAdapter("setDecimalPoints")
fun setDecimalPoints(editText: EditText, num: Int) {
    editText.filters = arrayOf<InputFilter>(ETMoneyValueFilter(num))
}
复制代码
ImageView:
/**
 * 设置图片的加载
 */
@BindingAdapter("imgUrl", "placeholder", "isOriginal", "roundRadius", "isCircle", requireAll = false)
fun loadImg(
    view: ImageView, url: Any?, placeholder: Drawable? = null, isOriginal: Boolean = false, roundRadius: Int = 0,
    isCircle: Boolean = false
) {
    url?.let {
        view.extLoad(
            it,
            placeholder = placeholder,
            roundRadius = CommUtils.dip2px(roundRadius),
            isCircle = isCircle,
            isForceOriginalSize = isOriginal
        )
    }
}

@BindingAdapter("loadBitmap")
fun loadBitmap(view: ImageView, bitmap: Bitmap?) {
    view.setImageBitmap(bitmap)
}
复制代码
TextView:
//为空的时候设置默认值
@BindingAdapter("text", "default", requireAll = false)
fun setText(view: TextView, text: CharSequence?, default: String?) {
    if (text == null || text.trim() == "" || text.contains("null")) {
        view.text = default
    } else {
        view.text = text
    }
}

//设置Html字体
@BindingAdapter("textHtml")
fun setTextHtml(textView: TextView, text: String?) {
    if (!TextUtils.isEmpty(text)) {
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.N) {
            textView.text = Html.fromHtml(text, Html.FROM_HTML_MODE_LEGACY)
        } else {
            textView.text = Html.fromHtml(text)
        }
    } else {
        textView.text = ""
    }
}

/**
 * 设置左右的Drawable图标
 */
@BindingAdapter("setRightDrawable")
fun setRightDrawable(textView: TextView, drawable: Drawable?) {
    if (drawable == null) {
        textView.setCompoundDrawables(null, null, null, null)
    } else {
        drawable.setBounds(0, 0, drawable.minimumWidth, drawable.minimumHeight)
        textView.setCompoundDrawables(null, null, drawable, null)
    }
}

@BindingAdapter("setLeftDrawable")
fun setLeftDrawable(textView: TextView, drawable: Drawable?) {
    if (drawable == null) {
        textView.setCompoundDrawables(null, null, null, null)
    } else {
        drawable.setBounds(0, 0, drawable.minimumWidth, drawable.minimumHeight)
        textView.setCompoundDrawables(drawable, null, null, null)
    }
}

复制代码
View:
/**
 * 设置控件的隐藏与显示
 */
@BindingAdapter("isVisibleGone")
fun isVisibleGone(view: View, isVisible: Boolean) {
    view.visibility = if (isVisible) View.VISIBLE else View.GONE
}

@BindingAdapter("isInVisibleShow")
fun isInVisible(view: View, isVisible: Boolean) {
    view.visibility = if (isVisible) View.VISIBLE else View.INVISIBLE
}

/**
 * 点击事件防抖动的点击
 */
@BindingAdapter("clicks")
fun clicks(view: View, action: () -> Unit) {
    view.click { action() }
}

/**
 * 重新设置高度
 */
@BindingAdapter("layoutHeight")
fun layoutHeight(view: View, targetHeight: Float) {
    val height = view.layoutParams.height

    if (height != targetHeight.toInt()) {
        view.apply {
            this.layoutParams = layoutParams.apply {
                this.height = targetHeight.toInt()
            }
        }
    }
}

//设置动画设置高度
@SuppressLint("Recycle")
@BindingAdapter("layoutHeightAnim")
fun layoutHeightAnim(view: View, targetHeight: Float) {
    val layoutParams = view.layoutParams
    val height = layoutParams.height

    if (height != targetHeight.toInt()) {

        //值的属性动画
        val animator = ValueAnimator.ofInt(height, targetHeight.toInt()).apply {

            addUpdateListener {
                val heightVal = it.animatedValue as Int
                layoutParams.height = heightVal
                view.layoutParams = layoutParams
            }

            duration = 250
        }

        //不能再子线程中更新UI，如果是其他的值是可以的比如Tag
        AsyncAnimUtil.instance.startAnim(view.findViewTreeLifecycleOwner(), animator, false)
    }
}

复制代码
由于篇幅原因只贴出了自用的相对重要的部分，如果想要查看完整的可以去文章末尾查看源码展示。
总结
DataBinding 对比 findviewbyid 对比的优缺点：
优点：

简化 findviewbyid 模板代码，更简洁易懂。
支持双向绑定与单向绑定，可选可配置，更灵活。
xml布局与页面的一一对应，尽量减少空指针异常，配合 Kotlin 的非空校验更舒适。
通过生成的绑定类减少代码执行时间，内部还注册对象的懒加载，可以带来一定的性能优化。
方便做换肤与国际化，可以通过适配器更精细的操作样式与文本。

缺点：

兼容性问题（升级AS版本与Gradle版本）
不方便调试（再次推荐不要在XML里写逻辑,并且目前AS升级后已经能明确指出大部分的问题）
编译时间更长了（特别是第一次需要生成很多的Bind类文件，再次运行有缓存和增量更新会好一点）
少量增加APK体积（毕竟多了很多类）

使用DataBinding的一些小Tips：
1.想用双向绑定就用，不想用双向绑定就用单向绑定，都不想用只用findviewbyid也是可以的。完全看大家的喜欢，当然不用DataBinding/ViewBinding 也行的，可以用其他的框架或者原生的findviewbyid都行的。
2.如果要启动 DataBinding ，推荐你顺便加上 ViewBinding
buildFeatures {
  viewBinding = true
  dataBinding = true
}
复制代码
DataBinding是 ViewBinding 的超集，如果只想替换findviewbyid的功能，那你可以使用使用 ViewBinding ，如果想强制指定不生成 ViewBinding 编译文件，可以加上tools:viewBindingIgnore="true" 。
3.DataBinding虽然支持可以在xml里面写复杂的计算逻辑，但还是推荐大家尽量只做数据的绑定，逻辑计算尽量不要卸载xml里面，如果真要写逻辑，最多只做三元的逻辑判断。以免出现一些性能问题与调试问题。
4.DataBinding配合ViewModel和LiveData食用更舒适，可以绑定生命周期也推荐大家要绑定到lifecycleOwner，它可以自动销毁资源，在此场景中 Flow 反而没有 LiveData 好用，并且在部分版本中 LiveData 反而兼容性更好。
5.xml 的标签尽量把自定义属性的 app 标签与 DataBinding 标签 databinding 区分开来便于后期的维护和同事的协同开发。
6.善用 BindingAdapter 进行数据绑定与设置监听。
总的来说用还是不用 DataBinding 还真是存乎一心，都行，只是我个人觉得在当下这个时间点看的话是利大于弊。再往后我也不好说，毕竟 Compose 把整个 xml 体系都给革命了。
说到这里请容许我挣扎一下先给自己叠个甲：
我认为原生 Android 的未来一定是 Compose ，但是多少年之后能走向主流不好说，3年？5年？毕竟 Kotlin 语言推出到今年这么多年了也只和 Java 55开而已，甚至我认识的好多5年以上的开发者都没用过 Kotlin，反而目前主流的 MVVM 中还是很多是使用 DataBinding 的，就算我们不用也是需要了解的。
可能真的有很多人对 DataBinding 不喜欢、不感冒，也能理解。其实我也是各种机缘巧合下才入的坑，我也是从开始的嫌弃，到真香，再放弃，最后一直使用至今。
没有最好的框架，只有最合适的框架。
结局惯例，我如有讲解不到位或错漏的地方，希望同学们可以指出。如果有更好的使用方式或封装方式，或者你有遇到的坑也都可以在评论区交流一下，互相学习进步。
如果感觉本文对你有一点点的帮助，还望你能点赞支持一下,你的支持是我最大的动力。
本文的部分代码可以在我的 Kotlin 测试项目中看到，【传送门】。你也可以关注我的这个Kotlin项目，我有时间都会持续更新。
关于 MVVM 架构 和 DataBinding 框架与其他 Jetpack 的实战项目，如果大家有兴趣可以看看大佬的项目 难得一见 Jetpack MVVM 最佳实践 。


作者：newki
链接：https://juejin.cn/post/7208817904560013373
来源：稀土掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。