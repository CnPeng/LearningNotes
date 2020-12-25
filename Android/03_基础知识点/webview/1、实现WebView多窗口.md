[参考链接：Android 同一个WebView 打开 js window.open或者 a标签创建的新窗口](https://blog.csdn.net/weixin_38737912/article/details/91857663)

## 1、问题现象

APP 内通过 WebView 展现了一个页面，页面内有超链接，该超链接在 Web 端打开之后会新开一个窗口去展示内容。但是，在 APP 内通过 WebView 代开该超链接时，界面只是闪动一下，并不能如期展示内容。

## 2、解决方案

当前并未能找到完美的解决方案，之所以这么说，是因为基于如下两种方案会有如下问题：

* 如果我们在顶层页面中点开超链接，那么可以正常的在 APP 端以新开的页面展示内容；
*  **如果我们是二级页面内点开这么一个超链接，虽然也可以在新的页面中展示内容，但关闭的时候会关闭新开的页面及二级页面，直接回到最顶层一级页面。**

###（1）、方案一

```java
WebView web=findViewById(R.id.webView);
WebSettings webSettings = web.getSettings();

// 设置支持javascript
webSettings.setJavaScriptEnabled(true);
//支持js调用window.open方法
webSettings.setJavaScriptCanOpenWindowsAutomatically(true);
// 设置不允许开启多窗口
webSettings.setSupportMultipleWindows(false);

web.setWebViewClient(new WebViewClient());
web.loadUrl("你自己的连接地址");
```

### (2)、方案二

注意：下面方案中 `webSettings.setSupportMultipleWindows(falg);`  中的参数取值为 `false`。

```java
WebView web = findViewById(R.id.webView);
WebSettings webSettings = web.getSettings();

// 设置支持javascript
webSettings.setJavaScriptEnabled(true);
//支持js调用window.open方法
webSettings.setJavaScriptCanOpenWindowsAutomatically(true);
// 设置允许开启多窗口
webSettings.setSupportMultipleWindows(true);

web.setWebChromeClient(new WebChromeClient() {
        @Override
        public boolean onCreateWindow(WebView view, boolean isDialog, boolean isUserGesture, Message resultMsg) {
		
		//新创建一个webview
            WebView newWeb = new WebView(web.getContext());
            newWeb.setWebViewClient(new WebViewClient(){
                @Override
                public boolean shouldOverrideUrlLoading(WebView view, String url) {
                	   //将拦截到url交由第一个WebView打开
                    web.loadUrl(url);
                    return true;
                }
            });
            
            WebView.WebViewTransport transport = (WebView.WebViewTransport) resultMsg.obj;
            //以下的操作应该就是让 新的webview 去加载对应的url等操作。
            transport.setWebView(web2);
            resultMsg.sendToTarget();
            return true;
        }
    });
    
web.setWebViewClient(new WebViewClient());
web.loadUrl("你自己的连接地址");
```


