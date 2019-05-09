Android项目中，难免会用到WebView 加载网页内容，如果由于网络异常等原因导致页面内容无法加载出来的时候就会报错，默认报错内容如下图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-a5fe3ccf34fb30ff.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上面的图中我们可以看到，如果不做处理的话，报错信息中会包含我们连接到的网址，但实际上我们可能不想让用户看到网址，那么这个时候我们就需要使用自定义的错误提示页面。

既然想使用自定义错误提示，那我们就需要先知道啥时候出现了错误，监测错误的时候我们可以使用 WebViewClient 中的 onReceivedError（） 方法，当有错误时就给出我们自定义的错误提示，具体代码如下：

**WebViewTestActivity01.java**

```
/**
 * Created by CnPeng on 2017/1/5. WebView 测试页面1
 */

public class WebViewTestActivity01 extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.activity_webviewtest01);

        init();
    }

    private void init() {
        WebView webView = (WebView) findViewById(R.id.webviewTest1);
        final String url_ = "http://blog.csdn.net/north1989/article/details/53471439";


        webView.setWebViewClient(new WebViewClient() {  //监测加载状态
            @Override
            public boolean shouldOverrideUrlLoading(WebView view, String url) {
                view.loadUrl(url);      //指明要加载的页面
                return true;        //返回true表示强制使用当前webView 显示网页内容，而不跳转到默认浏览器
            }

            @Override       //当发生错误时调用此方法
            public void onReceivedError(WebView view, WebResourceRequest request, WebResourceError error) {

                // 自定义错误提示页面，灰色背景色，带有文字，文字不要输汉字，由于编码问题汉字会变乱码              
                String errorHtml = "<html><body style='background-color:#e5e5e5;'><h1>Page Not Found " + 
                        "!</h1></body></html>";
                view.loadData(errorHtml, "text/html", "UTF-8");
            }
        });

        webView.loadUrl(url_);
    }
}
```
>注意：
**A**
WebViewClient 中能监测Error 的方法有四个，两个onReceivedError（），其中一个废弃了；一个onReceivedHttpError（）；一个onReceivedSslError（）。我们只用onReceivedError（）方法，为了确保低版本手机上也能过滤到Error ，也可以将废弃的onReceivedError（）写上。千万不要随便用onReceivedHttpError，否则你的页面会一直是你定义的错误提示！！！我也不知道 onReceivedSslError 到底是啥错误，所以。。。

>**B**
上面展示的自定义错误页面其实使我们用字符串定义的一个简单的html页面，加载这个页面的时候，我们用的是 **loadData( ) ** 。在定义这个错误提示的时候，尽量不要用汉字，虽然在loadData()中定义编码为utf-8 但似乎并不起作用，汉字还是变成了乱码

