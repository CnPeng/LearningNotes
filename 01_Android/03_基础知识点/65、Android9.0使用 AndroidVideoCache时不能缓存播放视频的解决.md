## 一、问题现象：
项目中使用 https://github.com/danikula/AndroidVideoCache 作为视频缓存组件，但是在9.0手机上无法正常缓存，并且报错：

### 1、详细错误截图
![输入图片说明](https://images.gitee.com/uploads/images/2018/1120/110445_bfc83838_930142.png "屏幕截图.png")


### 2、详细错误文本描述
```
2018-11-16 14:35:19.781 31012-32296/com.xxx.xxx E/Pinger: Error reading ping response
    com.danikula.videocache.ProxyCacheException: Error opening connection for http://127.0.0.1:34851/ping with offset 0. Version: 2.7.0
        at com.danikula.videocache.HttpUrlSource.open(HttpUrlSource.java:75)
        at com.danikula.videocache.Pinger.pingServer(Pinger.java:101)
        at com.danikula.videocache.Pinger.access$100(Pinger.java:33)
        at com.danikula.videocache.Pinger$PingCallable.call(Pinger.java:123)
        at com.danikula.videocache.Pinger$PingCallable.call(Pinger.java:119)
        at java.util.concurrent.FutureTask.run(FutureTask.java:266)
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1167)
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:641)
        at java.lang.Thread.run(Thread.java:784)
     Caused by: java.io.IOException: Cleartext HTTP traffic to 127.0.0.1 not permitted
        at com.android.okhttp.HttpHandler$CleartextURLFilter.checkURLPermitted(HttpHandler.java:115)
        at com.android.okhttp.internal.huc.HttpURLConnectionImpl.execute(HttpURLConnectionImpl.java:458)
        at com.android.okhttp.internal.huc.HttpURLConnectionImpl.getResponse(HttpURLConnectionImpl.java:407)
        at com.android.okhttp.internal.huc.HttpURLConnectionImpl.getResponseCode(HttpURLConnectionImpl.java:538)
        at com.danikula.videocache.HttpUrlSource.openConnection(HttpUrlSource.java:160)
        at com.danikula.videocache.HttpUrlSource.open(HttpUrlSource.java:68)
        at com.danikula.videocache.Pinger.pingServer(Pinger.java:101) 
        at com.danikula.videocache.Pinger.access$100(Pinger.java:33) 
        at com.danikula.videocache.Pinger$PingCallable.call(Pinger.java:123) 
        at com.danikula.videocache.Pinger$PingCallable.call(Pinger.java:119) 
        at java.util.concurrent.FutureTask.run(FutureTask.java:266) 
        at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1167) 
        at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:641) 
        at java.lang.Thread.run(Thread.java:784) 
2018-11-16 14:35:19.783 31012-31012/com.xxx.xxx E/Pinger: Error pinging server (attempts: 3, max timeout: 280). If you see this message, please, report at https://github.com/danikula/AndroidVideoCache/issues/134. Default proxies are: [DIRECT]
    com.danikula.videocache.ProxyCacheException: Error pinging server (attempts: 3, max timeout: 280). If you see this message, please, report at https://github.com/danikula/AndroidVideoCache/issues/134. Default proxies are: [DIRECT]. Version: 2.7.0
        at com.danikula.videocache.Pinger.ping(Pinger.java:73)
        at com.danikula.videocache.HttpProxyCacheServer.isAlive(HttpProxyCacheServer.java:183)
        at com.danikula.videocache.HttpProxyCacheServer.<init>(HttpProxyCacheServer.java:82)
        at com.danikula.videocache.HttpProxyCacheServer.<init>(HttpProxyCacheServer.java:67)
        at com.xxx.mqtt.tools.CusApplication.newProxy(CusApplication.java:192)
        at com.xxx.mqtt.tools.CusApplication.getProxy(CusApplication.java:182)
        at com.xxx.video.activity.VideoPreviewActivity.onCreate(VideoPreviewActivity.java:59)
        at android.app.Activity.performCreate(Activity.java:7444)
        at android.app.Activity.performCreate(Activity.java:7434)
        at android.app.Instrumentation.callActivityOnCreate(Instrumentation.java:1286)
        at android.app.ActivityThread.performLaunchActivity(ActivityThread.java:3269)
        at android.app.ActivityThread.handleLaunchActivity(ActivityThread.java:3474)
        at android.app.servertransaction.LaunchActivityItem.execute(LaunchActivityItem.java:86)
        at android.app.servertransaction.TransactionExecutor.executeCallbacks(TransactionExecutor.java:108)
        at android.app.servertransaction.TransactionExecutor.execute(TransactionExecutor.java:68)
        at android.app.ActivityThread$H.handleMessage(ActivityThread.java:2115)
        at android.os.Handler.dispatchMessage(Handler.java:109)
        at android.os.Looper.loop(Looper.java:207)
        at android.app.ActivityThread.main(ActivityThread.java:7439)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.RuntimeInit$MethodAndArgsCaller.run(RuntimeInit.java:524)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:958)
```

## 二、问题原因：
**根本原因是从Android9.0开始，出于完全因素考虑，默认不再支持http网络请求，需要使用 https。**

但是，在AndroidVideoCache中使用了一个缓存代理地址：http://127.0.0.1，所以，就会报错。


## 三、解决方案：

解决的基本思路是：**对指定的网址进行过滤，强制允许指定网址继续使用http请求**

* 参考地址1：
https://stackoverflow.com/questions/45940861/android-8-cleartext-http-traffic-not-permitted

* 参考地址2：https://developer.android.com/training/articles/security-config#CleartextTrafficPermitted

* 参考地址3： https://koz.io/android-m-and-the-war-on-cleartext-traffic/


下列解决步骤基于参考地址1整理：

### 1、步骤1 

#### A:创建 res/xml/network_security_config.xml
```
<?xml version="1.0" encoding="utf-8"?>
<network-security-config>
    <domain-config cleartextTrafficPermitted="true">
        <domain includeSubdomains="true">Your URL(ex: 127.0.0.1)</domain>
    </domain-config>
</network-security-config>
```

注意：
上述代码的意思是解除对指定网址的限制。

在解除上述视频缓存库报错信息时，<domain> 节点的具体取值如下：
```
 <domain includeSubdomains="true"> 127.0.0.1</domain>
```

如果需要过滤某个地址，则只写域名即可，不需要写  **http://**,如：想过滤的地址为：http://www.baidu.com/123，则<demain>节点如下：
```
<domain includeSubdomains="true"> www.baidu.com</domain>
```


#### B:修改 AndroidManifest.xml

配置 android:networkSecurityConfig

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        ...
        android:networkSecurityConfig="@xml/network_security_config"
        ...>
        ...
    </application>
</manifest>
```

### 2、步骤2

#### A：修改AndroidManifest.xml 

启用 android:usesCleartextTraffic

```
<?xml version="1.0" encoding="utf-8"?>
<manifest ...>
    <uses-permission android:name="android.permission.INTERNET" />
    <application
        ...
        android:usesCleartextTraffic="true"
        ...>
        ...
    </application>
</manifest>
```

### 3、步骤3

在 @david.s'的回答中指出 `android:targetSandboxVersion` 也可能会导致该问题 -

在 [Manifest Docs  https://developer.android.com/guide/topics/manifest/manifest-element#targetSandboxVersion](https://developer.android.com/guide/topics/manifest/manifest-element#targetSandboxVersion) 中对于 `android:targetSandboxVersion`有如下描述
>android:targetSandboxVersion
>
>The target sandbox for this app to use. The higher the sandbox version number, the higher the level of security. Its default value is 1; you can also set it to 2. Setting this attribute to 2 switches the app to a different SELinux sandbox. The following restrictions apply to a level 2 sandbox:
>
>The default value of usesCleartextTraffic in the Network Security Config is false.
>Uid sharing is not permitted.


所以，如果你在 AndroidManifest.xml 的 <manifest> 节点中配置了 `android:targetSandboxVersion`,需要将它的值置为1.

#### A:修改AndroidManifest.xml

降低 android:targetSandboxVersion 的版本

```
<?xml version="1.0" encoding="utf-8"?>
<manifest android:targetSandboxVersion="1">
    <uses-permission android:name="android.permission.INTERNET" />
    ...
</manifest>
```