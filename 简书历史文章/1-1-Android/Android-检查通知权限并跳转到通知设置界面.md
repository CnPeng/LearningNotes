>**声明：该方案只对API19及以上版本有效**

##一、目标需求
最近项目中在完善推送功能，需要进入APP时检测一下是否开启了推送权限，如果没有开启弹窗提醒，当用户点击弹窗时直接跳转到APP的通知设置界面，就像下面这种：

![就是这种效果](https://upload-images.jianshu.io/upload_images/2551993-e5cfe8247518cebc.gif?imageMogr2/auto-orient/strip)

##二、需求实现

### 1、检测是否开启通知权限
接到需求时一脸懵，不知道咋实现，先是一番搜索，搜索后得知可以通过`NotificationManagerCompat` 中的 `areNotificationsEnabled()`来判断是否开启通知权限。

查阅官方文档可知 [NotificationManagerCompat](https://developer.android.com/reference/android/support/v4/app/NotificationManagerCompat) 在 `android.support.v4.app`包中，是[API 22.1.0](https://developer.android.com/topic/libraries/support-library/revisions.html) 中加入的。而 [areNotificationsEnabled()](https://developer.android.com/reference/android/support/v4/app/NotificationManagerCompat.html#areNotificationsEnabled())则是在 [API 24.1.0](https://developer.android.com/topic/libraries/support-library/revisions)之后加入的。

**注意：**
> areNotificationsEnabled 只对 API 19 及以上版本有效，低于API 19 会一直返回true

### 2、跳转到通知设置界面
假设没有开启通知权限，点击之后就需要跳转到 **APP的通知设置界面**，对应的Action是：`Settings.ACTION_APP_NOTIFICATION_SETTINGS`, 这个Action是 API 26 后增加的。APP的通知设置界面如下图：

![APP的通知设置界面](https://upload-images.jianshu.io/upload_images/2551993-7bec83d095874df5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如果在部分手机中无法精确的跳转到 **APP对应的通知设置界面**，那么我们就考虑直接跳转到 **APP信息界面**，对应的Action是：`Settings.ACTION_APPLICATION_DETAILS_SETTINGS`。APP信息界面如下图：

![APP信息界面](https://upload-images.jianshu.io/upload_images/2551993-5cb63ef5ded2b72d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3、代码实现：
不多说了，代码其实很简单，注释也很明了，直接上代码：

####(1)、java版（使用了DataBinding-数据绑定）
```

/**
 * 作者：CnPeng
 * 时间：2018/7/11
 * 功用：检测在设置中是否开启了APP的推送
 * 其他：
 *
 * 参考链接：
 * https://stackoverflow.com/questions/32366649/any-way-to-link-to-the-android-notification-settings-for-my-app
 * https://blog.csdn.net/ysy950803/article/details/71910806
 * https://juejin.im/post/5a2508656fb9a0450407b638
 */
public class CheckNotifyActivity extends AppCompatActivity {
    ActivityCheckNotifyBinding mBinding;

    @Override
    protected void onCreate(@Nullable Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_check_notify);
        initClickListener();
    }

    @Override
    protected void onResume() {
        super.onResume();
        checkNotifySetting();
    }

    /**
     * 作者：CnPeng
     * 时间：2018/7/12 上午8:02
     * 功用：初始化点击事件
     * 说明：
     */
    private void initClickListener() {
        //CnPeng 2018/7/12 上午7:08 跳转到通知设置界面
        mBinding.tvMsg.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    // 根据isOpened结果，判断是否需要提醒用户跳转AppInfo页面，去打开App通知权限
                    Intent intent = new Intent();
                    intent.setAction(Settings.ACTION_APP_NOTIFICATION_SETTINGS);
                    //这种方案适用于 API 26, 即8.0（含8.0）以上可以用
                    intent.putExtra(EXTRA_APP_PACKAGE, getPackageName());
                    intent.putExtra(EXTRA_CHANNEL_ID, getApplicationInfo().uid);

                    //这种方案适用于 API21——25，即 5.0——7.1 之间的版本可以使用
                    intent.putExtra("app_package", getPackageName());
                    intent.putExtra("app_uid", getApplicationInfo().uid);

                    // 小米6 -MIUI9.6-8.0.0系统，是个特例，通知设置界面只能控制"允许使用通知圆点"——然而这个玩意并没有卵用，我想对雷布斯说：I'm not ok!!!
                    //  if ("MI 6".equals(Build.MODEL)) {
                    //      intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
                    //      Uri uri = Uri.fromParts("package", getPackageName(), null);
                    //      intent.setData(uri);
                    //      // intent.setAction("com.android.settings/.SubSettings");
                    //  }
                    startActivity(intent);
                } catch (Exception e) {
                    e.printStackTrace();
                    // 出现异常则跳转到应用设置界面：锤子坚果3——OC105 API25
                    Intent intent = new Intent();

                    //下面这种方案是直接跳转到当前应用的设置界面。
                    //https://blog.csdn.net/ysy950803/article/details/71910806
                    intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
                    Uri uri = Uri.fromParts("package", getPackageName(), null);
                    intent.setData(uri);
                    startActivity(intent);
                }
            }
        });
    }

    /**
     * 作者：CnPeng
     * 时间：2018/7/12 上午9:02
     * 功用：检查是否已经开启了通知权限
     * 说明：
     */
    private void checkNotifySetting() {
        NotificationManagerCompat manager = NotificationManagerCompat.from(this);
        // areNotificationsEnabled方法的有效性官方只最低支持到API 19，低于19的仍可调用此方法不过只会返回true，即默认为用户已经开启了通知。
        boolean isOpened = manager.areNotificationsEnabled();

        if (isOpened) {
            mBinding.tvMsg.setText("通知权限已经被打开" +
                    "\n手机型号:" + android.os.Build.MODEL +
                    "\nSDK版本:" + android.os.Build.VERSION.SDK +
                    "\n系统版本:" + android.os.Build.VERSION.RELEASE +
                    "\n软件包名:" + getPackageName());

        } else {
            mBinding.tvMsg.setText("还没有开启通知权限，点击去开启");
        }
    }
}
```
#### （2）、kotlin版
```
/**
 * 作者：CnPeng
 * 时间：2018/7/12
 * 功用：检查通知推送是否已经被打开
 * 其他：
 */
public class PushCheckActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_push_check)

        initClickListener()
    }

    override fun onResume() {
        super.onResume()
        checkPushSwitchStatus()
    }

    /**
     * 作者：CnPeng
     * 时间：2018/7/12 下午3:43
     * 功用：检查通知推送的开关状态
     * 说明：
     */
    private fun checkPushSwitchStatus() {
        val notificationManager: NotificationManagerCompat = NotificationManagerCompat.from(this);
        val isOpend = notificationManager.areNotificationsEnabled()
        if (isOpend) {
            tv_msg.text = "通知权限已经被打开" +
                    "\n手机型号:${android.os.Build.MODEL}" +
                    "\nSDK版本:${android.os.Build.VERSION.SDK_INT}" +
                    "\n系统版本:${android.os.Build.VERSION.RELEASE}" +
                    "\n软件包名:${getPackageName()}"
        } else {
            tv_msg.text = "通知权限没有被开启，点击去开启"
        }

    }

    private fun initClickListener() {
        tv_msg.setOnClickListener {
            val intent: Intent = Intent()
            try {
                intent.action = Settings.ACTION_APP_NOTIFICATION_SETTINGS

                //8.0及以后版本使用这两个extra.  >=API 26
                intent.putExtra(Settings.EXTRA_APP_PACKAGE, packageName)
                intent.putExtra(Settings.EXTRA_CHANNEL_ID, applicationInfo.uid)

                //5.0-7.1 使用这两个extra.  <= API 25, >=API 21
                intent.putExtra("app_package", packageName)
                intent.putExtra("app_uid", applicationInfo.uid)

                startActivity(intent)
            } catch (e: Exception) {
                e.printStackTrace()

                //其他低版本或者异常情况，走该节点。进入APP设置界面
                intent.action = Settings.ACTION_APPLICATION_DETAILS_SETTINGS
                intent.putExtra("package", packageName)

                //val uri = Uri.fromParts("package", packageName, null)
                //intent.data = uri
                startActivity(intent)
            }
        }
    }
}
```

### 4、踩坑记录
##### A: com.android.support包的版本
因为 NotificationManagerCompat 是 22.1.0才有的，其中的 areNotificaitonEnabled() 是 24.1.0 才有的，Settings.ACTION_APP_NOTIFICATION_SETTINGS 是 26 才有的，所以，为了保证这些内容在不同版本中生效，最好在 gradle文件中 support 的版本升级到最新。如：
>implementation 'com.android.support:appcompat-v7:27.1.1'

##### B: 部分国产手机中没有APP通知设置页面
 在部分国产手机系统中，Settings.ACTION_APPLICATION_DETAILS_SETTINGS对应的Activity是不存在的，比如：锤子坚果3——OC105 API25。

所以，在坚果3手机上，最终会走我们代码中的 catch 节点，然后进入到 应用信息界面。

下面两张图分别是 锤子坚果3 手机的截图。第一张是 **设置--通知中心**的界面，点击之后只是一个开关的开启和关闭，并没有再进入详细的通知设置界面。第二张是 **应用管理--应用程序管理--应用信息*界面*， 点击其中的 **允许推送通知**时也只是开关的开启和关闭。

![设置--通知中心](https://upload-images.jianshu.io/upload_images/2551993-5cbe3a77d48c8043.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![应用信息](https://upload-images.jianshu.io/upload_images/2551993-140e98ee7d13ebc6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#####C: 部分国产手机 APP通知设置界面中没有开启和关闭的操作
部分国产手机中 Settings.ACTION_APPLICATION_DETAILS_SETTINGS对应的Activity并不是我们期望的通知设置界面。比如，小米6。小米6中 Settings.ACTION_APPLICATION_DETAILS_SETTINGS对应的通知设置界面如下：
![](https://upload-images.jianshu.io/upload_images/2551993-d3d7d479ac17caef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这完全不是我们需要的界面啊。。。里面并没有我们想要的开关啊。而且，在小米6中 Settings.ACTION_APPLICATION_DETAILS_SETTINGS 对应的应用信息界面中，点击其中的 通知管理 之后跳转的也是上面图中的样子。

但是，如果我们手动的从 **设置--通知和状态栏--通知管理** 进入我们应用的通知设置界面时，就可以正常的看到 允许通知的开关，如下图:
![](https://upload-images.jianshu.io/upload_images/2551993-e5e5270622e52cfd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>对于小米6手机的这个情况，分析了一阵子之后还是没找到解决办法。本来想着通过log确认一下上图中的界面到底是哪个Activity，但非常郁闷的是Log中只得到了`com.android.settings/.SubSettings` 这么一个地址，之前没见过这个地址，然后继续搜索。
>
>在看完 https://www.cnblogs.com/Lefter/archive/2013/04/27/3048010.html 和 https://blog.csdn.net/hfreeman2008/article/details/52778992 之后，明白了 .SubSettings 是干啥的了。也大致推断出为啥在小米6上得不到我们想要的界面了——**他们在定制系统时更改了通知设置界面对应的Fragment！！！！**
>
>此时，真想对雷布斯说一句：I'm not ok!!!!

###三、附录
####1、测试结果说明
手机型号|系统版本|测试结果
---|---|---
Vivo X9s|7.1.2|正常跳转到通知设置界面
荣耀10|8.1.0|正常跳转到通知设置界面
红米note4x|7.0|正常跳转到通知设置界面
Oppo R7 plus|5.0|正常跳转到通知设置界面
ZTE BA910|5.1|正常跳转到通知设置界面
Oppo R15|8.1.0|正常跳转到通知设置界面
三星盖乐世On5|7.1.1|正常跳转到通知设置界面
360Vizza|7.1.1|正常跳转到通知设置界面
魅族Mx3|4.4| 进入APP设置界面
华为荣耀4X|4.4|进入APP设置界面
锤子坚果3|7.1.2|进入APP设置界面
小米6|8.0.0|进入的页面中没有通知开关！！！！

####2、参考链接
#####（1）通知设置的参考链接
* https://stackoverflow.com/questions/32366649/any-way-to-link-to-the-android-notification-settings-for-my-app
* https://blog.csdn.net/ysy950803/article/details/71910806
* https://juejin.im/post/5a2508656fb9a0450407b638

####（2）SubSettings 和 Settings 的参考链接
* https://www.cnblogs.com/Lefter/archive/2013/04/27/3048010.html
* https://blog.csdn.net/hfreeman2008/article/details/52778992

####3、文中代码的GitHub地址
文中代码分别对应下列仓库中的：b_34_checkNotify、b_34_pushcheck 
Java版：https://github.com/CnPeng/CnPengAndroid.git
Kotlin版：https://github.com/CnPeng/CnPengKotlin.git

---
本文到此结束，谢谢观看！
**如有不足，敬请指正！**
