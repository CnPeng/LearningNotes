## 1、[Android WebView 缓存处理](https://cloud.tencent.com/developer/article/1469248)

我们加载Html时候，会在我们data/应用package下生成database与cache两个文件夹:
我们请求的Url记录是保存在webviewCache.db里，而url的内容是保存在webviewCache文件夹下.
WebView中存在着两种缓存：网页数据缓存（存储打开过的页面及资源）、H5缓存（即AppCache）。

一、网页缓存

1、缓存构成

* /data/data/package_name/cache/
* /data/data/package_name/database/webview.db
* /data/data/package_name/database/webviewCache.db

综合可以得知 webview 会将我们浏览过的网页url已经网页文件(css、图片、js等)保存到数据库表中

缓存模式(5种)

* `LOAD_CACHE_ONLY`:  不使用网络，只读取本地缓存数据
* `LOAD_DEFAULT`:  根据cache-control决定是否从网络上取数据。
* `LOAD_CACHE_NORMAL`: API level 17中已经废弃, 从API level 11开始作用同 `LOAD_DEFAULT` 模式
* `LOAD_NO_CACHE`: 不使用缓存，只从网络获取数据.
* `LOAD_CACHE_ELSE_NETWORK`，只要本地有，无论是否过期，或者 `no-cache`，都使用缓存中的数据。

如：www.taobao.com的 `cache-control` 为 `no-cache` ，在模式 `LOAD_DEFAULT`下，无论如何都会从网络上取数据，如果没有网络，就会出现错误页面；在 `LOAD_CACHE_ELSE_NETWORK`模式下，无论是否有网络，只要本地有缓存，都使用缓存。本地没有缓存时才从网络上获取。

www.360.com.cn 的 `cache-contro`l为 `max-age=60`，在两种模式下都使用本地缓存数据。


总结：根据以上两种模式，建议缓存策略为，判断是否有网络，有的话，使用 `LOAD_DEFAULT` ，无网络时，使用 `LOAD_CACHE_ELSE_NETWORK` 。

设置WebView 缓存模式

```java
    private void initWebView() {  
              
            mWebView.getSettings().setJavaScriptEnabled(true);  
            mWebView.getSettings().setRenderPriority(RenderPriority.HIGH);  
            mWebView.getSettings().setCacheMode(WebSettings.LOAD_DEFAULT);  //设置 缓存模式  
            // 开启 DOM storage API 功能  
            mWebView.getSettings().setDomStorageEnabled(true);  
            //开启 database storage API 功能  
            mWebView.getSettings().setDatabaseEnabled(true);   
            String cacheDirPath = getFilesDir().getAbsolutePath()+APP_CACAHE_DIRNAME;  
    //      String cacheDirPath = getCacheDir().getAbsolutePath()+Constant.APP_DB_DIRNAME;  
            Log.i(TAG, "cacheDirPath="+cacheDirPath);  
            //设置数据库缓存路径  
            mWebView.getSettings().setDatabasePath(cacheDirPath);  
            //设置  Application Caches 缓存目录  
            mWebView.getSettings().setAppCachePath(cacheDirPath);  
            //开启 Application Caches 功能  
            mWebView.getSettings().setAppCacheEnabled(true);  
        }
```        
        
清除缓存

```
    /** 
         * 清除WebView缓存 
         */  
        public void clearWebViewCache(){  
              
            //清理Webview缓存数据库  
            try {  
                deleteDatabase("webview.db");   
                deleteDatabase("webviewCache.db");  
            } catch (Exception e) {  
                e.printStackTrace();  
            }  
              
            //WebView 缓存文件  
            File appCacheDir = new File(getFilesDir().getAbsolutePath()+APP_CACAHE_DIRNAME);  
            Log.e(TAG, "appCacheDir path="+appCacheDir.getAbsolutePath());  
              
            File webviewCacheDir = new File(getCacheDir().getAbsolutePath()+"/webviewCache");  
            Log.e(TAG, "webviewCacheDir path="+webviewCacheDir.getAbsolutePath());  
              
            //删除webview 缓存目录  
            if(webviewCacheDir.exists()){  
                deleteFile(webviewCacheDir);  
            }  
            //删除webview 缓存 缓存目录  
            if(appCacheDir.exists()){  
                deleteFile(appCacheDir);  
            }  
        }
```


完整代码


```java
package com.example.webviewtest;  
  
import java.io.File;  
  
import android.app.Activity;  
import android.graphics.Bitmap;  
import android.os.Bundle;  
import android.util.Log;  
import android.view.View;  
import android.webkit.JsPromptResult;  
import android.webkit.JsResult;  
import android.webkit.WebChromeClient;  
import android.webkit.WebSettings;  
import android.webkit.WebSettings.RenderPriority;  
import android.webkit.WebView;  
import android.webkit.WebViewClient;  
import android.widget.RelativeLayout;  
import android.widget.TextView;  
import android.widget.Toast;  
  
public class MainActivity extends Activity {  
  
    private static final String TAG = MainActivity.class.getSimpleName();  
    private static final String APP_CACAHE_DIRNAME = "/webcache";  
    private TextView tv_topbar_title;  
    private RelativeLayout rl_loading;  
    private WebView mWebView;  
    private String url;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
          
        //url:http://m.dianhua.cn/detail/31ccb426119d3c9eaa794df686c58636121d38bc?apikey=jFaWGVHdFVhekZYWTBWV1ZHSkZOVlJWY&app=com.yulore.yellowsdk_ios&uid=355136051337627  
        url = "http://m.dianhua.cn/detail/31ccb426119d3c9eaa794df686c58636121d38bc?apikey=jFaWGVHdFVhekZYWTBWV1ZHSkZOVlJWY&app=com.yulore.yellowsdk_ios&uid=355136051337627";  
        findView();  
    }  
  
    private void findView() {  
          
        tv_topbar_title = (TextView) findViewById(R.id.tv_topbar_title);  
          
        rl_loading = (RelativeLayout) findViewById(R.id.rl_loading);  
          
        mWebView = (WebView) findViewById(R.id.mWebView);  
          
        initWebView();  
          
        mWebView.setWebViewClient(new WebViewClient() {  
  
            @Override  
            public void onLoadResource(WebView view, String url) {  
                  
                Log.i(TAG, "onLoadResource url="+url);  
                  
                super.onLoadResource(view, url);  
            }  
  
            @Override  
            public boolean shouldOverrideUrlLoading(WebView webview, String url) {  
  
                Log.i(TAG, "intercept url="+url);  
                  
                webview.loadUrl(url);  
  
                return true;  
            }  
  
            @Override  
            public void onPageStarted(WebView view, String url, Bitmap favicon) {  
                  
                Log.e(TAG, "onPageStarted");  
                  
                rl_loading.setVisibility(View.VISIBLE); // 显示加载界面  
            }  
  
            @Override  
            public void onPageFinished(WebView view, String url) {  
  
                String title = view.getTitle();  
  
                Log.e(TAG, "onPageFinished WebView title=" + title);  
  
                tv_topbar_title.setText(title);  
                tv_topbar_title.setVisibility(View.VISIBLE);  
  
                rl_loading.setVisibility(View.GONE); // 隐藏加载界面  
            }  
  
            @Override  
            public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {  
  
                rl_loading.setVisibility(View.GONE); // 隐藏加载界面  
  
                Toast.makeText(getApplicationContext(), "",  
                        Toast.LENGTH_LONG).show();  
            }  
        });  
  
        mWebView.setWebChromeClient(new WebChromeClient() {  
  
            @Override  
            public boolean onJsAlert(WebView view, String url, String message, JsResult result) {  
  
                Log.e(TAG, "onJsAlert " + message);  
  
                Toast.makeText(getApplicationContext(), message, Toast.LENGTH_SHORT).show();  
  
                result.confirm();  
  
                return true;  
            }  
  
            @Override  
            public boolean onJsConfirm(WebView view, String url, String message, JsResult result) {  
  
                Log.e(TAG, "onJsConfirm " + message);  
  
                return super.onJsConfirm(view, url, message, result);  
            }  
  
            @Override  
            public boolean onJsPrompt(WebView view, String url, String message, String defaultValue, JsPromptResult result) {  
  
                Log.e(TAG, "onJsPrompt " + url);  
  
                return super.onJsPrompt(view, url, message, defaultValue, result);  
            }  
        });  
          
        mWebView.loadUrl(url);  
    }  
  
    private void initWebView() {  
          
        mWebView.getSettings().setJavaScriptEnabled(true);  
        mWebView.getSettings().setRenderPriority(RenderPriority.HIGH);  
        mWebView.getSettings().setCacheMode(WebSettings.LOAD_DEFAULT);  //设置 缓存模式  
        // 开启 DOM storage API 功能  
        mWebView.getSettings().setDomStorageEnabled(true);  
        //开启 database storage API 功能  
        mWebView.getSettings().setDatabaseEnabled(true);   
        String cacheDirPath = getFilesDir().getAbsolutePath()+APP_CACAHE_DIRNAME;  
//      String cacheDirPath = getCacheDir().getAbsolutePath()+Constant.APP_DB_DIRNAME;  
        Log.i(TAG, "cacheDirPath="+cacheDirPath);  
        //设置数据库缓存路径  
        mWebView.getSettings().setDatabasePath(cacheDirPath);  
        //设置  Application Caches 缓存目录  
        mWebView.getSettings().setAppCachePath(cacheDirPath);  
        //开启 Application Caches 功能  
        mWebView.getSettings().setAppCacheEnabled(true);  
    }  
      
    /** 
     * 清除WebView缓存 
     */  
    public void clearWebViewCache(){  
          
        //清理Webview缓存数据库  
        try {  
            deleteDatabase("webview.db");   
            deleteDatabase("webviewCache.db");  
        } catch (Exception e) {  
            e.printStackTrace();  
        }  
          
        //WebView 缓存文件  
        File appCacheDir = new File(getFilesDir().getAbsolutePath()+APP_CACAHE_DIRNAME);  
        Log.e(TAG, "appCacheDir path="+appCacheDir.getAbsolutePath());  
          
        File webviewCacheDir = new File(getCacheDir().getAbsolutePath()+"/webviewCache");  
        Log.e(TAG, "webviewCacheDir path="+webviewCacheDir.getAbsolutePath());  
          
        //删除webview 缓存目录  
        if(webviewCacheDir.exists()){  
            deleteFile(webviewCacheDir);  
        }  
        //删除webview 缓存 缓存目录  
        if(appCacheDir.exists()){  
            deleteFile(appCacheDir);  
        }  
    }  
      
    /** 
     * 递归删除 文件/文件夹 
     *  
     * @param file 
     */  
    public void deleteFile(File file) {  
  
        Log.i(TAG, "delete file path=" + file.getAbsolutePath());  
          
        if (file.exists()) {  
            if (file.isFile()) {  
                file.delete();  
            } else if (file.isDirectory()) {  
                File files[] = file.listFiles();  
                for (int i = 0; i < files.length; i++) {  
                    deleteFile(files[i]);  
                }  
            }  
            file.delete();  
        } else {  
            Log.e(TAG, "delete file no exists " + file.getAbsolutePath());  
        }  
    }  
}
```
---



### 1、如何清除缓存 
清除 WebView 缓存时直接调用 ：`mWebView.clearCache(true)`


### 2、确认是否已经清除

以 JSDK 的方式将对应的方法暴露给 H5 ，然后让 H5 写个 测试 DEMO 调用，比如，在 Web 页面中定义一个 Button ，点击 Button 调用原生 清理缓存的方法。

原生通过 WebView 加载了测试页面之后，在 Chrome 浏览器中按如下步骤操作：

* 打开调试工具1

![开启调试工具](https://images.gitee.com/uploads/images/2019/0322/115417_f920faa1_930142.png "屏幕截图.png")


* 打开调试工具2

![链接设备](https://images.gitee.com/uploads/images/2019/0322/115557_575fe6b3_930142.png)

* 调试指定设备

![链接设备2](https://images.gitee.com/uploads/images/2019/0322/115755_a81ec160_930142.png "屏幕截图.png")

* 切换到 netWork 选项卡，点击左侧刷新按钮

如果没有缓存，会显示下图状态

![没有缓存请求网络的情况](https://images.gitee.com/uploads/images/2019/0322/120009_596ad271_930142.png "屏幕截图.png")

* 刷新页面后从缓存加载的状态

如果有缓存，会显示下图状态。

![读取缓存内容](https://images.gitee.com/uploads/images/2019/0322/120104_a0cb7c89_930142.png "屏幕截图.png")


点击测试页面中请求缓存的　Button ， 然后点击上图中的 刷新 按钮，如果显示的不是 `from memory cache` 即表示缓存清理成功。　


### 3、注意事项

我们在编写 JSDK 方法时，需要使用 `@JavascriptInterface` 对方法进行标记，其中的内容并不是运行在 主线程中，而 `mWebView.clearCache(true)` 需要运行在主线程中，所以，我们必须在该 JSDK 方法中通过 hanlder 去触发 `mWebView.clearCache(true)`.


