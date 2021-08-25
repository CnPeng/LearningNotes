# 1. 2-加载https页面显示空白

## 1.1. 现象

使用原生的 WebView 直接加载 https 地址时，页面空白，不显示内容。

这是因为当加载有 ssl 层的 https 页面时，如果该网站的安全证书在 Android 无法得到认证，WebView 就会变成一个空白页，并不会像 PC 浏览器那样跳出一个风险提示框。

## 1.2. 解决方案

### 1.2.1. 有点怪啊

测试手机——三星 Galaxy S8，Android 9

最开始是按照下面这个代码写的，结果加载 https 页面时显示空白，所以才有了后面几种方案。

但是，为什么隔了一天之后，下面这种方式又可以加载了呢？

```java
binding.webView.getSettings().setJavaScriptEnabled(true);
binding.webView.loadUrl(targetUrl);
```

### 1.2.2. 方案1

```java
// 下面这两个 settings 是必须的，否则无法加载 https 的网址
binding.webView.getSettings().setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
binding.webView.getSettings().setJavaScriptEnabled(true);
binding.webView.loadUrl(targetUrl);
```

在安卓5.0之后，默认不允许加载 http 与 https混 合内容，需要设置webview允许其加载混合网络协议内容，即 setMixedContentMode() 。

### 1.2.3. 方案2

这种方案和方案1基本一致，只是 setMixedContentMode() 的调用位置不同。

```java
webView.setWebViewClient(new WebViewClient(){
  @Override
  public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error){
      webView.getSettings().setMixedContentMode(WebSettings.MIXED_CONTENT_ALWAYS_ALLOW);
  }
);
// 这行启用 js 的代码一定加上
binding.webView.getSettings().setJavaScriptEnabled(true);
binding.webView.loadUrl(targetUrl);
```

### 1.2.4. 方案3

```java
webView.setWebViewClient(new WebViewClient(){

@override
public void onReceivedSslError(WebView view, SslErrorHandler handler, SslError error){
    //handler.cancel(); 默认的处理方式，WebView变成空白页
    handler.process();接受证书
    //handleMessage(Message msg); 其他处理
}

// 这行启用 js 的代码一定加上
webView.getSettings().setJavaScriptEnabled(true);
binding.webView.loadUrl(targetUrl);
```

注意：

* 添加 handler.proceed() 方法虽然能解决问题，但如果在 GooglePlay 上架，会被警告，甚至可能会被下架

### 1.2.5. 方案4

> 这种方案暂未实测。但前述三种方案都实测可用。

```java
webSettings.setLoadWithOverviewMode(true);
webSettings.setDomStorageEnabled(true);
webSettings.setBlockNetworkImage(false);
webSettings.setUseWideViewPort(true);

// 这行启用 js 的代码一定加上
webView.getSettings().setJavaScriptEnabled(true);
binding.webView.loadUrl(targetUrl);
```

## 1.3. 参考

[Android webview加载https链接错误或无响应](https://blog.csdn.net/u014727709/article/details/78968141)