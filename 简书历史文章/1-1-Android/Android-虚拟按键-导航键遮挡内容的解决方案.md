## 一、遮挡主体内容

### 方案1：隐藏状态栏

这种方案底部依旧会保留虚拟导航按键。但不保留状态栏

```java
getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
```
![方案1效果图](http://upload-images.jianshu.io/upload_images/2551993-6b9c41eb7bab9f20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 方案2：隐藏状态栏和虚拟导航（全屏）

这种方案不会保留底部虚拟导航按键。也不保留状态栏

```java
/**
 * 180115 隐藏 魅族、Nexus、华为等底部的虚拟导航按键，避免遮挡内容
 *
 * @param activity 需要隐藏底部导航按键的Activity
 */
public static void hideBottomUIMenu(Activity activity) {
    //隐藏虚拟按键，并且全屏  
    if (Build.VERSION.SDK_INT < 19) { // lower api  
        View v = activity.getWindow().getDecorView();
        v.setSystemUiVisibility(View.GONE);
    } else if (Build.VERSION.SDK_INT >= 19) {
        View decorView = activity.getWindow().getDecorView();
        int uiOptions = View.SYSTEM_UI_FLAG_HIDE_NAVIGATION | View.SYSTEM_UI_FLAG_IMMERSIVE_STICKY | View
                .SYSTEM_UI_FLAG_FULLSCREEN;
        decorView.setSystemUiVisibility(uiOptions);
    }
}
```

![方案2效果图](http://upload-images.jianshu.io/upload_images/2551993-6f308f4e0dc12583.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 方案3：让系统动态计算尺寸

这种方案在很多手机中都不生效。
这种方案会保留底部虚拟按键，也会保留状态栏

```xml
 <!-- CnPeng 点击广场列表中的条目后跳转到该界面 -->
<activity
    android:name="com.square.activity.ShareDetailsOfSquareActivity"
    android:screenOrientation="portrait"
    android:windowSoftInputMode="stateHidden|adjustResize"/>
```

## 二、遮挡 popupWindow

### 方案1：动态计算尺寸

```java
popwindow = new PopupWindow(conentView, WindowManager.LayoutParams.MATCH_PARENT, WindowManager.LayoutParams
        .WRAP_CONTENT);
popwindow.setFocusable(true);// 设置窗口的控件可以点击，很重要，要是不能点击只是个显示图片的
popwindow.setWidth(ActionBar.LayoutParams.MATCH_PARENT);// 设置pop的宽度
popwindow.setHeight(ActionBar.LayoutParams.MATCH_PARENT);// 高度
ColorDrawable dw = new ColorDrawable(0xb0000000);// 实例化一个ColorDrawable颜色为半透明
popwindow.setBackgroundDrawable(dw);// 根据实例化的对象，设置颜色为半透明
popwindow.setSoftInputMode(WindowManager.LayoutParams.SOFT_INPUT_ADJUST_RESIZE);   //防止软键盘遮挡内容
popwindow.showAtLocation(conentView, Gravity.BOTTOM | Gravity.CENTER_HORIZONTAL, 0, 0);
```

### 方案2：控制好弹窗的底部偏移量

```java
//实现的效果是，在底部 view —— rl_bottom 的上方展示 popupWindow 弹窗

//popup的高度为底部 view 的 top 坐标值
int btmTop = rl_bottom.getTop();
ViewGroup.LayoutParams la = new ViewGroup.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, btmTop);
mFolderPopWindow = new FolderPopWindow(mActivity, config.mimeType, la);

//CnPeng 2019-07-23 11:25 修复虚拟导航遮挡弹窗的问题. 完整屏幕高度-bottomView 的 top = 垂直偏移量 
int btmTop = findViewById(R.id.rl_bottom).getTop();
int screenHeight = ScreenUtils.getFullScreenHeight(mActivity);
mFolderPopWindow.showAtLocation(findViewById(R.id.rl_root), Gravity.BOTTOM, 0, screenHeight - btmTop);                  
```

* ScreenUtils.getFullScreenHeight(activity) 如下：

```java                  
 /**
 * CnPeng:2019-07-23 11:34 获取完整的高度，包括状态栏、底部虚拟导航栏
 *
 * 参考链接：https://blog.csdn.net/Kikitious_Du/article/details/78584326
 * getRealMetrics(metric) 得到的是包含底部导航和状态栏的完整屏幕高度
 * getMetrics(metric) 得到的是不包含底部虚拟导航栏的高度
 * 上述两个函数中的 metric.heightPixels 相减就能得到底部虚拟导航的高度
 */
public static int getFullScreenHeight(Context context) {
    DisplayMetrics localDisplayMetrics = new DisplayMetrics();
    //        ((Activity) context).getWindowManager().getDefaultDisplay().getMetrics(localDisplayMetrics);
    ((Activity) context).getWindowManager().getDefaultDisplay().getRealMetrics(localDisplayMetrics);
    return localDisplayMetrics.heightPixels;
}  
```


参考：

* [https://www.zhihu.com/question/35292413](https://www.zhihu.com/question/35292413)
* [http://blog.csdn.net/MrZhang_happy/article/details/70057429](http://blog.csdn.net/MrZhang_happy/article/details/70057429)


本文到此结束，谢谢观看！
**如有不足，敬请指正！**

---

>**CnPeng 微信公众号上线了！！**
>
>**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**
>
>**欢迎扫描下方二维码关注!**
>
>![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
