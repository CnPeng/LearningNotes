从手机浏览器中唤醒原生 Android APP 的实现逻辑，即 深链 的实现。


## 一、H5 页面中的唤醒代码

190418 测试结果如下：

```html
<html>
       <head>
       <meta http-equiv="Content-Type" content="text/html; charset=UTF-8"> 
                      <title>Insert title here</title>
       </head> 
        <body> 
			<a href="cnpeng://newinfo/detail/open?detailID=8044">
			 scheme 方式: 
			</a>
			<br/>
			目前测试结果如下：
				<li> 魅蓝2 —— 5.0 支持
				<li> 小米 note4——7.0 支持
				<li> OPPO R15 ——8.0 支持
				<li> 华为荣耀10 —— 9.0 支持 
				<li> 小米 6x —— 8.0 支持
			<br/>
			<br/>
			<a href="intent://newinfo/detail/open?detailID=8044#Intent;scheme=cnpeng;package=com.zjelite.antlinkercampus;S.browser_fallback_url=http%3A%2F%2www.antlinker.com%2down.html;end"> Intent 方式</a>
			<br>
			目前测试结果如下：
			<br>
				<li> 魅蓝2 —— 5.0 支持
				<li> 小米 note4——7.0 支持
				<li> OPPO R15 ——8.0 支持；
				<li> 华为荣耀10 —— 9.0 不支持 
				<li> 小米 6x —— 8.0 支持		
		<br>
		按照上述测试结果，貌似 scheme 方式兼容性更好一下，但是，根据 <a href="https://juejin.im/post/5b7efb2ee51d45388b6af96c#heading-18">https://juejin.im/post/5b7efb2ee51d45388b6af96c#heading-18</a>	 中描述，在部分浏览器中，scheme 方式可能会被屏蔽，并且如果 APP 未安装时需要自行处理重定向逻辑。	
</html>
```

## 二、Android 原生代码

### 1、清单文件中的内容

```xml
<activity
     android:name="com.xxx.welcome.activity.WelcomeActivity"
           
     <!--CnPeng 2019/4/18 11:35 AM 支持从外部浏览器直接打开 APP-->
     <!-- android.intent.category.BROWSABLE 必须加，否则打不开 -->
     <intent-filter android:autoVerify="true">
           <action android:name="android.intent.action.VIEW" />
           <category android:name="android.intent.category.DEFAULT" />
           <category android:name="android.intent.category.BROWSABLE"/>
           <data android:scheme="cnpeng" />
      </intent-filter>
</activity>
```

Android 应用内部自定义的 URI 格式为：`cnpeng://newinfo/detail/open?detailID=8044`

### 2、获取隐式 Intent 中的内容

```java
Uri uri = getIntent().getData();

if(uri != null) {

 // 完整的url信息
 String url = uri.toString();
 Log.e(TAG, "url: "  + uri);

 // scheme部分
 String scheme = uri.getScheme();
 Log.e(TAG, "scheme: "  + scheme);

 // host部分
 String host = uri.getHost();
 Log.e(TAG, "host: "  + host);

 //port部分
 int port = uri.getPort();
 Log.e(TAG, "host: "  + port);

 // 访问路劲
 String path = uri.getPath();
 Log.e(TAG, "path: "  + path);
 List<String> pathSegments = uri.getPathSegments();

 // Query部分
 String query = uri.getQuery();
 Log.e(TAG, "query: "  + query);

 //获取指定参数值
 String goodsId = uri.getQueryParameter("goodsId");
 Log.e(TAG, "goodsId: "  + goodsId);

}
```

## 三、参考链接：

[H5唤起APP指南(附开源唤端库)](https://juejin.im/post/5b7efb2ee51d45388b6af96c)

[通过H5（浏览器/WebView/其他）唤起本地app](https://blog.csdn.net/u014727709/article/details/78770422)

[Android Intents with Chrome](https://developer.chrome.com/multidevice/android/intents)

[通过Scheme协议打开APP界面](https://www.jianshu.com/p/57f79fc83233)