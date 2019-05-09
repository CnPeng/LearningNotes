##一、问题描述
当前APP中有很多跳转H5的情况，使用的是WebView去加载。如果某个H5页面不存在，那么就会跳转到WebView默认的404页面，在某些手机上这个页面可能就是一个空白页面。

然后，我司测试给设计说，不行了，太丑了！要设计一个好看点的，然后就有了本文的需求——定制404页面。

初始的时候直接调用了onReceivedError 发现无法正常监测错误码，后来又调用onReceivedHttpError 也是有点问题，然后搜索，发现了这么一篇文章：[http://blog.csdn.net/lsyz0021/article/details/56677132](http://blog.csdn.net/lsyz0021/article/details/56677132)，才明白原来6.0之前和之后的处理是有差别的，原文已经写的很好，现摘录并补充如下：

##二、解决方案
###1、6.0以上版本解决方案
好在Google在Android6.0修复了这个问题。根据google官网提供的最新的官网文档，我们可以重写onReceivedHttpError()方法可以捕获http Error。

```
 webView.setWebViewClient(new WebViewClient() {    
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                if (url.startsWith("tel:")) {
                    return true;
                }
                return false;
            }

            @TargetApi(android.os.Build.VERSION_CODES.M)    //171016 处理404错误
            @Override
            public void onReceivedHttpError(WebView view, WebResourceRequest request,
                                            WebResourceResponse errorResponse) {
                super.onReceivedHttpError(view, request, errorResponse);
                // 这个方法在6.0才出现
                int statusCode = errorResponse.getStatusCode();
                System.out.println("onReceivedHttpError code = " + statusCode);
                if (404 == statusCode || 500 == statusCode) {
                    view.loadUrl("about:blank");// 避免出现默认的错误界面
                    view.loadUrl(errorUrl_404);
                }
            }
        });
```
###2、6.0之前版本的解决方案
这是google关于这个issue的： [https://code.google.com/p/android/issues/detail?id=968](https://code.google.com/p/android/issues/detail?id=968)
下面是google开发人员回复:
![这里写图片描述](http://upload-images.jianshu.io/upload_images/2551993-5bb0a7e8c42e609f?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Google虽然在Android6.0解决了这个问题，但是在Android6.0以下我们是不能直接获取到404或者500的。那么在Android6.0以下的系统我们如何处理404这样的问题呢？

两种解决方案：

####(1)、方案一（推荐）通过获取网页的title，判断是否为系统默认的404页面
我们可以在WebChromeClient()子类中可以重写他的onReceivedTitle()方法
```
 webView.setWebChromeClient(new WebChromeClient() {
            @Override
            public void onReceivedTitle(WebView view, String title) {
                super.onReceivedTitle(view, title);

                //171016 处理404错误 android 6.0 以下通过title获取  
                if (Build.VERSION.SDK_INT < Build.VERSION_CODES.M) {
                    if (title.contains("404") || title.contains("500") || title.contains("Error")) {
                        view.loadUrl("about:blank");// 避免出现默认的错误界面
                        view.loadUrl(errorUrl_404);
                    }
                }
            }
     }
```

###(2)、方案二(推荐) 服务器后台是能获取404、500的，最好的办法是让后台捕获

最好的方案就是让H5或后台去处理，因为他们也能获取到404或500。


##三、补充：

###(1)、判断断网、超时等情况
``` 
@Override
    public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {
        super.onReceivedError(view, errorCode, description, failingUrl);
        // 断网或者网络连接超时
        if (errorCode == ERROR_HOST_LOOKUP || errorCode == ERROR_CONNECT || errorCode == ERROR_TIMEOUT) {
            view.loadUrl("about:blank"); // 避免出现默认的错误界面
            view.loadUrl(mErrorUrl);
        }
    }
```
上面的那个 onReceivedErrod已经被废弃，用下面这个代替，代码如下：
```
 webView.setWebViewClient(new WebViewClient() {     
            @Override
            public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error) {
                super.onReceivedError(view, request, error);

                int errorCode = error.getErrorCode();
                // 断网或者网络连接超时
                if (errorCode == ERROR_HOST_LOOKUP || errorCode == ERROR_CONNECT || errorCode == ERROR_TIMEOUT) {
                    view.loadUrl("about:blank"); // 避免出现默认的错误界面
                    view.loadUrl(mErrorUrl);
                }
            }
        });
```

##四、附录
参考链接：[http://blog.csdn.net/lsyz0021/article/details/56677132](http://blog.csdn.net/lsyz0021/article/details/56677132)
