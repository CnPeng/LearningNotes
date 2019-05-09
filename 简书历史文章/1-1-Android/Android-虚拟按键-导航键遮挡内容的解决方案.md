
方案1：
这种方案底部依旧会保留虚拟导航按键。但不保留状态栏

```
 getWindow().addFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN);
```
![方案1效果图](http://upload-images.jianshu.io/upload_images/2551993-6b9c41eb7bab9f20.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)






方案2：
这种方案不会保留底部虚拟导航按键。也不保留状态栏
```
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



方案3：
这种方案在很多手机中都不生效。
这种方案会保留底部虚拟按键，也会保留状态栏
```
 <!-- CnPeng 点击广场列表中的条目后跳转到该界面 -->
        <activity
            android:name="com.zjelite.square.activity.ShareDetailsOfSquareActivity"
            android:screenOrientation="portrait"
            android:windowSoftInputMode="stateHidden|adjustResize"/>
```


参考：
https://www.zhihu.com/question/35292413
http://blog.csdn.net/MrZhang_happy/article/details/70057429


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
