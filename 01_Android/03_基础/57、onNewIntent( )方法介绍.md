原文链接地址：http://yhz61010.iteye.com/blog/2389877

阅读难度：中 

阅读前提： 

* 1. 需要了解 Android 的生命周期，每个方法的触发时机以及作用。 
* 2. 需要了解 Activity 的 launchMode 模式和作用。 
* 3. Intent 基本知识及作用。 

Android Activity 的生命周期如下（图片来自 Android 官网 https://developer.android.com/guide/components/images/activity_lifecycle.png）： 
![](https://images.gitee.com/uploads/images/2018/0920/092649_1c0a3915_930142.png "屏幕截图.png")
 
也就是说，初次启动 Activity 时，调用顺序如下： 
>onCreate() -> onStart() -> onResume() 

那么，onNewIntent() 是什么时候被触发的呢，它又有什么用呢？ 
让我们再来看一张图： 
 
![](https://images.gitee.com/uploads/images/2018/0920/092717_f56bfd56_930142.png "屏幕截图.png")

上面这张图直观的说明了 onNewIntent() 是什么时候被调用的。而且其中还表明了最重要的一点：**onCreate() 和 onNewIntent() 不会被同时调用**。 

接下来，我们再来看看官网是如何解释 onNewIntent() 的（https://developer.android.com/reference/android/app/Activity.html#onNewIntent(android.content.Intent)）： 
引用
```
onNewIntent 

added in API level 1 
void onNewIntent (Intent intent) 
This is called for activities that set launchMode to "singleTop" in their package, or if a client used the FLAG_ACTIVITY_SINGLE_TOP flag when calling startActivity(Intent). In either case, when the activity is re-launched while at the top of the activity stack instead of a new instance of the activity being started, onNewIntent() will be called on the existing instance with the Intent that was used to re-launch it. 

An activity will always be paused before receiving a new intent, so you can count on onResume() being called after this method. 

Note that getIntent() still returns the original Intent. You can use setIntent(Intent) to update it to this new Intent. 

Parameters 
intent	Intent: The new intent that was started for the activity.
```
简单说明下：

>如果在 AndroidManifest.xml 中，将 Activity 的 launchMode 设置成了 "singleTop" 模式，或者在调用 startActivity(Intent) 时，设置了 
FLAG_ACTIVITY_SINGLE_TOP 标识，那么，当该 Activity 再次被启动时，如果它依然存在于 Activity 栈中，并且刚好处于栈的最顶层时，那么它将不会被重新创建，而是直接使用原来的实例，此时，onNewIntent(Intent) 将会被调用，后续生命周期中的其它方法，就可以使用 onNewIntent(Intent) 传递过来的新的 Intent 参数了。（也就是说，其它方法可以使用更新后的 Intent 参数） 

也就是说，调用顺序如下： 
>onNewIntent() -> onRestart() -> onStart() -> onResume() 

需要特别注意的是， **如果在 onNewIntent(Intent) 中，不调用 setIntent(Intent) 方法对 Intent 进行更新的话，那么之后在调用 getIntent() 方法时得到的依然是最初的值。**

在实际编写代码过程中，我们往往需要在 onCreate() 和 onNewIntent() 中进行一些相同的处理，因此可以像下面这样做： 
```
@Override  
protected void onCreate(Bundle savedInstanceState) {  
     super.onCreate(savedInstanceState);  
     setContentView(R.layout.activity_schedule);  
     // Do what you want.  
     onNewIntent(getIntent());  
     // Do others  
}  
```
如果你不想直接在 onCreate() 方法中调用 onNewIntent()，那么也可以在 onCreate() 和 onNewIntent() 中同时调用一个自定义方法，也可以达到相同的目的：

```
@Override  
protected void onCreate(Bundle savedInstanceState) {  
     super.onCreate(savedInstanceState);  
     setContentView(R.layout.activity_schedule);  
     // Do what you want.  
     sameProcess(getIntent());  
     // Do others  
}  
  
@Override  
protected void onNewIntent(Intent intent) {  
     // Do what you want.  
     sameProcess(intent);  
     // Do others  
}  
```
最后总结一下，
>**在启动 Activtiy 时，如果希望在 onCreate() 不被触发的的情况下，依然可以对 Intent 进行操作，这就需要使用 onNewIntent()**。 

参考文献： 
* http://extra-vision.blogspot.jp/2016/08/android-activity-onnewintent.html
* https://developer.android.com/reference/android/app/Activity.html#ActivityLifecycle
* https://developer.android.com/guide/components/activities/activity-lifecycle.html