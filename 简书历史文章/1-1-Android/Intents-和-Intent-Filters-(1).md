## 1 前言
 
在Android 中，intent 的用途是比较广泛的，可以用来开启 activity 、开启 services 、开启 broadCast ，并且在开启这些组件的同时传递数据。IntentFilter 则是定义在清单文件AndroidManifes.xml 中用来匹配组件可以接收哪些intent。

今天就按照 官方API Guides 中的内容，解读intent 和 Intent Filters 的使用。**由于个人知识水平有限，难免有理解不恰当的地方，欢迎指正**。[点击查看官方API Guides :https://developer.android.google.cn/guide/components/intents-filters.html](https://developer.android.google.cn/guide/components/intents-filters.html)

## 2 Intent 简介
安卓中 Intent 是组件之间进行数据通信和交互的一个消息对象，通常我们会将intent 翻译为“意图” 。Intent 的基本应用示例如下：

###（1） 开启Activity
activity启动后会占据整个手机屏幕，如果你想开启另外的activity页面去展示其他内容。就可以先创建一个intent 对象，在intent对象中声明要开启的activity 以及要携带的数据，然后将该intent 传递给 startActiviy( ) 即可。

假设我们在Activity A 中封装一个intent对象去开启Activity B 并且希望在 关闭B 的时候可以将相关数据 反馈给 A 。那么这时候我们就应该 在 A 中将intent 传递给StartAtivityForResult( ),通过这个方法去开启B，并在B 关闭时将数据反馈给A。然后在A 中重写 OnActivityResult( ) 方法，去解析B 反馈回来的数据。

#### 1) startActivityForResult( ) 的示例代码如下：
* IntentUseActivity.java

```
/**
 * Created by CnPeng on 2016/12/25.
 */

public class IntentUseActivity extends AppCompatActivity {
    private final int CODE_ACTIVITY_INTENT_USE = 0;
    private TextView tv;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_intent_use);

        tv = (TextView) findViewById(R.id.tv_msgFromTestActivtiy);
        Button btn_forResult = (Button) findViewById(R.id.btn_forResult);
        btn_forResult.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent(IntentUseActivity.this, TestActviity.class);

                //开启新TestActivity ,并在TestActivity关闭时获取数据
                startActivityForResult(intent, CODE_ACTIVITY_INTENT_USE);
            }
        });
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (resultCode == RESULT_OK) {    //如果返回成功
            switch (requestCode) {  //由于实际项目中 requestCode 不止有一个，所以需要区分
                case CODE_ACTIVITY_INTENT_USE:
                    //解析TestActivity 中返回的数据
                    String msg = data.getStringExtra("msg");

                    //获取到数据后，展示出来
                    tv.setText(msg);
                    break;
            }
        }
    }
}

```

* actvitiy_intent_use.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">

    <Button
        android:id="@+id/btn_forResult"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="StartActivtiyForResult"/>

    <TextView
        android:id="@+id/tv_msgFromTestActivtiy"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:gravity="center"/>
</LinearLayout>
```
* TestActivtiy.java

```
/**
 * Created by CnPeng on 2016/12/25.
 */

public class TestActviity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_testactivtiy);

        final EditText editText = (EditText) findViewById(R.id.et_intentUse);
        Button btn = (Button) findViewById(R.id.btn_passResult);
        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();

                //获取输入框中的内容
                String msg = editText.getText().toString();

                //数据封装进intent
                intent.putExtra("msg", msg);

                //设置结果，用来返回给上一个界面
                setResult(RESULT_OK, intent);

                //关闭当前页
                finish();
            }
        });
    }
}
```

* activity_testactivity.xml

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">

   
    <EditText
        android:id="@+id/et_intentUse"
        android:hint="输入内容"
        android:gravity="top|left"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:maxLines="5"
        android:minLines="5"/>

    <Button
        android:id="@+id/btn_passResult"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:text="关闭当前页并将上面输入的内容反馈给上一个页面"/>

</LinearLayout>
```
> **总结**
A
在 onActivityResult( ) 方法中解析数据时，既要判断resultCode ,也要判断requestCode
B
在获取EditText 输入框中的内容时，getText( ) 拿到的是EditTable对象，需要借助 toString() 转成字符串
C
在xml 中的 EditText 节点中，通过 maxLines 和 minLines 的设置，控制EditText 的显示高度。另外，通过gravity=top | left 将初始输入位置固定在左上角

### （2）开启Service
Service 是没有用户界面的后台运行的一个组件。 Android 5.0（API 21）之后，可以通过 JobScheduler 开启service.

5.0之前，可以通过Service 类中的方法去开启service : 如果你想开启一个只执行一次的service(比如下载)，就传递一个intent对象给startService( ) 方法。这个intent中封装了你要开启的service以及需要携带的数据。

如果在 CS (clietn-server)界面中使用service （也就是说，需要service 长期存活），那么就需要传递intent给bindService（），实现service 与其他组件进行绑定， 

### (3) 发送 broadcast
broadcast 就是app 可以接收的一个消息。android中会针对不相同的事件给出不同的广播，比如 android系统启动的时候 或者 充电的时候都会有对应的广播发出。

将要发送的广播封装进intent 对象，然后传递给 sendBroadCast( )方法或者sendOrderedBroadcast( ) 方法，这样就可以将广播发送出去供其他app或者其他组件去接收

## 3 Intent 分类
根据是否指明要开启的组件，可以将 intent 分为两类 : Explicit intent (显示意图)，Implicit intent (隐式意图).

### (1) Explicit Intent (显示意图)
显示意图 在构造的时候就直接指明了要开启的组件名称（这个名称是要开启的组件 的 .class 字节码名）。通常我们用显示意图去开启自己 app 中的组件，因为你知道你要开启的组件的 .class字节码名称。

###(2) Inplicit Intent (隐式意图)
不需要指明要开启的组件名称，但需要声明一个action,这个action就是其他app中接收该intent的组件需要执行的 Action。比如：你想在地图中展示 用户的地理位置，但是你的app本身不具备这个功能，此时，你就可以使用隐式意图去调用其他app 的相应组件来实现。

## 4 Intent工作的基本原理及intentFilter
### (1) Intent工作原理介绍
如果，你使用 显示意图，那么Android系统会根据 intent 中指定的名称直接找出对应的组件并开启。

如果，你创建了一个隐式意图，那么Android系统会拿着intent 去当前手机上其他app的清单文件 AndroidManifest.xml 中找匹配的intent filter 。 如果匹配成功，那么，就会开启相应的组件并传递数据；如果有多个app中的intent filter 同时匹配上了这个intent，就会弹出一个Dialog 提示，让你选择你想使用哪个APP。
>  * **隐式意图举例：**
假设你在手机上想查看一个PDF文档，当你点击这个文档的时候，系统就会发出一个隐式意图intent，去寻找当前手机中是否有可以打开PDF文档的软件。
>
  如果你的手机中安装了 WPS 也安装了 FOXIT ，那么这两个软件的清单文件AndroidManifest.xml 中都有 能匹配上这个隐式intent 的intentFilter，这时候，就会弹出一个对话框，让你选择你想使用WPS 查看还是使用FOXIT查看。

### (2) IntentFilter 介绍
Intent filter 可以理解为一个过滤器或者匹配器，它指明了一个组件可以接收和处理哪些 intent 对象。

比如说，在你app的清单文件中为activity 声明一个Intent filter, 当其他app中发送的隐式intent 与之匹配时，其他app 就可以直接开启这个activity。当然，如果你没有给这个activity声明IntentFilter，那么你就必须使用显示意图去开启他。
>隐式意图 需要Intent filter的配合才能实现相应的操作。

### (3) 隐式意图与IntentFilter配合使用的原理图
下面的图中，展示了Activity A 通过隐式意图开启activity B 的流程
![Activity A 通过隐式意图开启activity B 的流程原理](http://upload-images.jianshu.io/upload_images/2551993-6b7986cdd9c11fa8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 1) Activity A 中创建一个Intent对象，该对象中封装了要执行的Action 以及要传递的数据，然后调用 StartActivity( )方法将intent传递给android系统。
2) Android 系统去检索当前手机上所有app 的清单文件中的 intent filters ，判断是否有能够匹配这个隐式intent的组件
3) 当Android 系统检索到 Activity B 中的intent filters 与A中的隐式intent匹配时，就会通过反射的方式调用B 中的onCreate()方法，并将intent对象 传递给 B。

**注意：**
>A
为了提高你的APP的安全性，通常会使用显示意图去开启 Service , 并且不要给 Service 声明intent fitlers。
>B
使用隐式意图去开启Service 具有很大的风险，因为，你并不能确定到底会有哪些 Service 可以响应这个隐式意图，而且用户也无法查看哪些服务被开启了。
>C 
从 android 5.0(API  21) 开始，如果你使用bindService( )方法开启Service的时候传递了一个 隐式意图，系统就会抛出异常。

## 5 创建Intent对象
  Intent对象携带了一些信息(比如，组件的.class 字节码名称，组件的category类别等)，Android系统通过这些信息可以确定去开启哪个组件，被开启的组件也可以通过这些信息获取要执行具体的操作和相关数据。

Intent中包含的主要信息如下：
### (1) Component name 
组件名称，也就是要启动的组件的.class 字节码名称。创建显示意图的时候需要用它。如果没有给intent 指定组件名，那么这个intent就是一个隐式意图，会由系统根据其他信息（如action，category, data ）决定哪些组件可以接收这个intent 。

> 可以通过如下方式设置要开启的组件名称：
setComponent( ) , setClass( ), setClassName( ) ,或者直接通过intent的构造方法

###(2) Action 
#### 1) Action 简介
Action 本质是一个字符串，它指明了执行的操作。

在broadCast 的intent 中，action 表明了该广播需要执行和追踪的操作。action在极大程度上的决定了该如何去构建 intent中 data 和 extra 信息。（这地方我理解的不是很透彻，所以附注原文：`In the case of a broadcast intent, this is the action that took place and is being reported. The action largely determines how the rest of the intent is structured—particularly the information that is contained in the data and extras.`).

#### 2) Action 的使用
Android系统在Intent 类和其他框架类中已经预设了一系列字符串常量用来表示 一些常见且通用的Action操作 。下面举了两个开启activity时通用的ACTION：

> ACTION_SEND ，代表分享的操作。
将intent 的action设置为ACTION_SEND 后，传递给StartActivity( )方法，就可启动分享界面，该界面可以将当前手机中所有可以响应分享操作的APP列出来供你选择，然后将你指定的内容分享到你选中的APP上去。
> 常量 ACTION_VIEW，代表展示操作。
如果你想将相册中的照片或者其他内容展示给用户，那么此时就可以将Intent的action 设置为 ACTION_VIEW , 然后通过startActivity( ) 去开启一个展示用的activity.

更多系统预设的通用 Action 常量可以查看 Intent 类的说明，连接：https://developer.android.google.cn/reference/android/content/Intent.html 。 另外，其他 类中也有定义的 action，比如 [Settings](https://developer.android.google.cn/reference/android/provider/Settings.html) 类中就定义的一些action， 可以用来开启 “设置” 中的指定界面。

**可以通过 setAction( )方法 或者 Intent 的构造方法，为一个Intent 指定 action**

#### 3) 自定义Action
除了使用系统预设的Action常量外，也可以使用 **` 自定义的 action `**。（在其他APP中，使用自定义的action时 可以通过反射的方式获取你自己app 中的组件）。
> 自定义 action的时候，需要在自定义的Action常量前加上自己APP的 包名。如：
`static final String ACTION_TIMETRAVEL = "com.example.action.TIMETRAVEL";`

>> 关于如何自定义action并使用， 我现在也不会~~ /(ㄒoㄒ)/~~。如果你有好的教程或者文章可以推荐给我，感激不尽。

### (3) Data 
Data 通常是一个URI 对象。这个URI 对象是对要添加的data 数据、data数据的[MIME type](https://developer.android.google.cn/reference/android/webkit/MimeTypeMap.html#getExtensionFromMimeType(java.lang.String)) 的引用，原文`The URI (a Uri
  object) that references the data to be acted on and/or the MIME type of that data`。

data 的type由 intent 对象中的action 来决定，比如，intent对象中的action常量 是ACTION_DEDIT，那么data 中就必须包含一个被编辑的文档的URI地址。

创建Intent的时候，除了指明 data 的URI ，指明 data 的type (MIME type) 也是非常重要的。比如，一个可以展示图片的activity 不一定能展示音频，即使图片和音频的URI 格式是相似的。 指明 data 的type(MIME type) 有助于 Android 系统找到能够处理你的intent的最佳组件。 

如果一个 data 的URI格式是 “content: ..." ,他的 MIME type 有时也是可以推断出来的。因为"content ：..." 格式的URI 表明，这是存储在设备上的本地数据并且受ContentProvider 控制，所以，这个 data 的 MIME type 是可以被android 系统直接获取到的。

>注意：
**如果只是设置 data ，使用 setData( )；
如果只是设置 MIME type ,使用 setType( ) ; 
如果想同时设置 data 和 type ，**不要直接使用 setData( ) 和 setType( ) ，因为它们二者会使彼此失效。所以这时候**就必须使用 setDataAndType( )。**

### (4) Category 
category 是类别，种类的意思。Category是一个包含了一些附加信息的字符串，这些附加信息更进一步的指明了当前组件可以处理哪些 intent 。intent 中可以声明多种 category , 但实际上大多数intent 都不需要设置category。（原文：`any number of category descriptions can be placed in an intent, but most intents do not require a category. `）

category 举例：
>[CATEGORY_LAUNCHER](https://developer.android.google.cn/reference/android/content/Intent.html#CATEGORY_LAUNCHER)
把要开启的activity 作为应用的主界面。

完整的 category 清单可以去Intent类中查看。

>添加 category 的时候使用 addCategory( ).

上面的列出的 componentName，action ，data ，category 都影响甚至决定了可以响应这个intent 的组件。当然，intent 也可以携带其他对决定响应组件么有影响的信息，如 Extras ，Flags.

### (5) Extras
以键值对的形式通过 putExtra( )来设置附加数据, putExtra( )方法中含两个参数，一个是键名，一个是具体的数据。也可以将Bundle 对象作为附加数据，但这时就需要使用 putExtras( )方法了，该方法只有一个参数，该参数是要传递的bundle 对象。

>官方文档中，后面还有一部分关于Extras 和Flags 的介绍，没看明白，就先不写了，后期搞明白了再补充。

## 6 Intent的使用示例

### (1) 显示意图 示例：
我们通常用的最多的就是显示意图，显示意图中，除了componentName (组件名)之外，其他上面所介绍的属性都是可选的。

假设你在自己的应用中创建了一个服务，这个服务可以从网络上下载东西，如果你想开启这个服务，那么就可以这么写：
```
// Executed in an Activity, so 'this' is the [Context](https://developer.android.google.cn/reference/android/content/Context.html)
// The fileUrl is a string URL, such as "http://www.example.com/image.png"

Intent downloadIntent = new Intent(this,DownloadService.class);
downloadIntent.setData(Uri.parse(fileUrl));
startService(downloadIntent);
```

###(2) 隐式意图 示例：
隐式意图会指明一个action, 当前手机设备上所有能通过intent filters 匹配该 intent 的app 都能执行该 action。当你自己的app 不能实现某个功能但其他app能够实现的时候，使用隐式意图是非常有用的。

假设你有一些内容想分享给其他人，那么你就可以创建一个intent ，设置intent的 action 为  `ACTION_SEND`, 并且指明要分享的 data 数据以及 Extras 附加信息，然后将该intent对象传递给startActivity( ),这样系统就会检索当前手机上所有可以执行分享操作的app，然后以列表的形式展示出来供用户选择。

>**注意：**
用户的手机上可能没有能够执行你指定Action的 app ，或者由于权限的原因而导致不能执行该Action，这样就会使你的app崩溃。为了避免出现上面描述的情况，在执行startActivity( ) 前，需要先调用intent 对象的 `resolveActivity( )` 判断是否有能够执行你指定action 的app , 如果没有就会返回 null , 如果有就可以放心安全的调用 startActivity( ) 。下面的例子就展示了怎么去判断，在下面的例子中，**没有使用setData( )去设置直接 URI数据 对象，而是 通过指定 type 确定 extras 中所能携带的数据类型，并通过extra 设置了type 中指定类型的数据**。

#### 1）通过隐式意图实现分享
```
 Intent intent = new Intent();
 intent.setAction(Intent.ACTION_SEND);
 intent.putExtra(Intent.EXTRA_TEXT, "随便写的");
 intent.setType("text/plain");

 if (intent.resolveActivity(getPackageManager()) != null) {
     startActivity(intent);
 }
```
在上面的代码中，指定了 intent 的action  为 ACTION_SEND , 表示发送或者分享；Extra 中的键 用的是 安卓系统预定义的，值是自己设置的 ； Type 中指定了Extra 中值 的MIME  type 为 “text/plain” ；然后调用 intent 的 resolveActivity( ) 方法判断是否有可以执行该intent 的activity存在，参数是 PackageManager 对象，如果该方法的返回值不为 null ，才去执行 startActivity（） 。

当startActivity( ) 被调用之后，系统就会检索本地所有已安装的app，确定到底哪些app能够响应这个intent。如果只有一个app能够执行该intent 就会直接跳转到该app界面；如果有多个app可以执行该intent，就会弹出一个列表供选择，如下图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-a4afecc9598b9b16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 2）隐式意图实现分享并强制开启app选择界面
在上面的例子的图示中，只要你点击一个app，就可以通过该app将数据分享出去。

我们也可以通过 点击 上图中的“始终” 选择一个默认的分享工具，这样以后有分享就会直接调用 QQ，而不再弹出这个选择界面（不同的手机设置默认的方式不一样，我当前用的是nexus 5x）。

但是，有些用户希望能够在每次分享的时候都能弹出这个选择界面从而将数据分享到不同的app，那么，这种情况下我们就需要强制开启 app 选择界面，强制开启之后，用户就不能再设置默认的分享工具了。

**强制开启app 选择界面时，需要 通过createChooser( ) 方法创建一个intent对象，该方法传递两个参数，第一个是intent对象（该对象中封装了具体的action和数据），第二个是 弹出的选择界面 的标题；然后 将该intent 传递给 startActivity( )** 代码如下：
```
Intent intent = new Intent();
intent.setAction(Intent.ACTION_SEND);
intent.putExtra(Intent.EXTRA_TEXT, "点击有惊喜：www.baidu.com");
intent.setType("text/plain");
//强制开启选择器
Intent chooserIntent = Intent.createChooser(intent, "您要分享到哪里去？");

if (intent.resolveActivity(getPackageManager()) != null) {
      startActivity(chooserIntent);   //注意这里开启activity时使用的是chooerIntent
}
```







 
