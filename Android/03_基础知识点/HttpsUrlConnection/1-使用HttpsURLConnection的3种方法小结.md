[原文链接：《使用HttpsURLConnection的3种方法小结》](https://blog.csdn.net/suyimin2010/article/details/81025083)

最近遇到网络安全方面的问题，要将 http 转移到 https，由于在工程中使用了 `HttpURLConnection`，所以要相应的转而使用 `HttpsURLConnection`，当然大部分是参考的网络上一些前辈们的成果，过程中也遇到了一些坑，在这里进行一下总结。

## 1.1 证书介绍

由于 https 涉及到证书的认证方式，这里简单介绍一下： 

* 关于证书，可以简单把它理解为网站的身份证。而给网站颁发身份证的就是`CA（证书颁发机构）`。 
可以颁发证书的 `CA` 有很多（国内外都有），只有少数 `CA` 被认为是权威、公正的，这些 `CA` 颁发的证书，浏览器、操作系统才认为是信得过的。 

* 在 Android 系统中，就有一个根证书信任列表，若我们的证书是由这个列表中的某个根证书的子证书，就不需要在 https 使用过程中特别指定了。 

* 我们自己也可以自己制作证书，例如使用 `OpenSSL` 就可以生成一个 `CA` 根证书，然后用这个根证书颁发两个子证书 `server` 和 `client`，`server` 证书放在服务器端，而这个 `client` 证书就可以用于浏览器或者安卓 app 中。这种自己制作的证书，就必须在 app 中指定了，否则 https 握手是不能成功的。 


接下来按使用证书的不同方式来进行分别说明：

* 1，信任系统提供的证书（权威CA颁发）； 
* 2，全部信任证书； 
* 3，信任指定证书；

## 1.2 信任系统提供的证书

这是最简单的方式，相比较于 http 访问，转到 https 协议，只是将 `HttpURLConnection` 替换为 `HttpsURLConnection` 就够了。 

下面是一个使用 `HttpsURLConnection` 实现的 POST 请求：

```java
public static void httpsPostData(final Context context, final String urlPath, final String content){
        new Thread()
        {
            @Override
            public void run() {
                // TODO Auto-generated method stub
                Looper.prepare();
                URL url;
                try {
                    url = new URL(TimeValidity.addTimeValidityUrl(urlPath));
                    HttpsURLConnection conn = (HttpsURLConnection) url.openConnection();
                    //conn.setSSLSocketFactory(getSSLContext(context).getSocketFactory());
                    conn.setConnectTimeout(TIMEOUT_LONG);//5
                    conn.setReadTimeout(TIMEOUT_LONG);
                    conn.setDoOutput(true);// 设置允许输出
                    conn.setRequestMethod("POST");
                    conn.setRequestProperty("User-Agent", "Mozilla/4.0 (compatible; MSIE 8.0; Windows NT 5.2; Trident/4.0; .NET CLR 1.1.4322; .NET CLR 2.0.50727; .NET CLR 3.0.04506.30; .NET CLR 3.0.4506.2152; .NET CLR 3.5.30729)");
                    conn.setRequestProperty("Charset", "UTF-8");
                    conn.setRequestProperty("Content-Type", "application/x-www-form-urlencoded");
                    OutputStream os = conn.getOutputStream();
                    os.write(content.getBytes());
                    os.close();
 
                    /* 服务器返回的响应码 */
                    int code = conn.getResponseCode();
                    Log.i("https","code="+code);
                    if (code == 200) {
                        BufferedReader in = new BufferedReader(
                                new InputStreamReader(conn.getInputStream(), "UTF-8"));
                        String retData = null;
                        String responseData = "";
                        while ((retData = in.readLine()) != null) {
                            responseData += retData;
                        }
                        in.close();                     
                    } else {
                        Log.i("https","return error");
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();                    
                } catch (IOException e) {
                    e.printStackTrace();
                } catch (Exception e) {
                    e.printStackTrace();                
                }
                Looper.loop();
            }
        }.start();
    }
``` 

调用示例：

```java
httpPostData(MainActivity.this, url, content);
```

## 1.2 信任全部证书

添加 `HTTPSTrustManager` 类，如下：

```java
public class HTTPSTrustManager implements X509TrustManager {
 
    private static TrustManager[] trustManagers;
    private static final X509Certificate[] _AcceptedIssuers = new X509Certificate[] {};
 
    @Override
    public void checkClientTrusted(
            java.security.cert.X509Certificate[] x509Certificates, String s)
            throws java.security.cert.CertificateException {
        // To change body of implemented methods use File | Settings | File
        // Templates.
    }
 
    @Override
    public void checkServerTrusted(
            java.security.cert.X509Certificate[] x509Certificates, String s)
            throws java.security.cert.CertificateException {
        // To change body of implemented methods use File | Settings | File
        // Templates.
    }
 
    @Override
    public X509Certificate[] getAcceptedIssuers() {
        return _AcceptedIssuers;
    }
 
    public static void allowAllSSL() {
        HttpsURLConnection.setDefaultHostnameVerifier(new HostnameVerifier() {
 
            @Override
            public boolean verify(String arg0, SSLSession arg1) {
                // TODO Auto-generated method stub
                return true;
            }
 
        });
 
        SSLContext context = null;
        if (trustManagers == null) {
            trustManagers = new TrustManager[] { new HTTPSTrustManager() };
        }
 
        try {
            context = SSLContext.getInstance("TLS");
            context.init(null, trustManagers, new SecureRandom());
        } catch (NoSuchAlgorithmException e) {
            e.printStackTrace();
        } catch (KeyManagementException e) {
            e.printStackTrace();
        }
        HttpsURLConnection.setDefaultSSLSocketFactory(context.getSocketFactory());
    }
}
```

再在所有 https 开始进行请求之前，执行一次即可：

```java
HTTPSTrustManager.allowAllSSL();//信任所有证书
```

后面就是正常的进行 https 访问就可以了：

```java
httpPostData(MainActivity.this, url, content);
```

## 1.3 信任指定证书

先要获取到证书，我们可以放到 `assert` 目录下，例如这里使用的证书的文件名为 `root.crt`。 
通过如下函数来读取，并返回 `SSLContext`：


```java
public static SSLContext getSSLContext(Context inputContext){
    SSLContext context = null;
    try {
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        InputStream in = inputContext.getAssets().open("root.crt");
        Certificate ca = cf.generateCertificate(in);
        KeyStore keystore = KeyStore.getInstance(KeyStore.getDefaultType());
        keystore.load(null, null);
        keystore.setCertificateEntry("ca", ca);
        String tmfAlgorithm = TrustManagerFactory.getDefaultAlgorithm();
        TrustManagerFactory tmf = TrustManagerFactory.getInstance(tmfAlgorithm);
        tmf.init(keystore);
        // Create an SSLContext that uses our TrustManager
        context = SSLContext.getInstance("TLS");
        context.init(null, tmf.getTrustManagers(), null);
    } catch (Exception e){
        e.printStackTrace();
    }
    return context;
}
```

然后，在使用 `HttpsURLConnection` 的过程中，也就是 `httpsPostData()`函数中，使用指定证书的 `SSLContext` 即可：

```java
conn.setSSLSocketFactory(getSSLContext(context).getSocketFactory());
```

当然，如果仔细看了前面的 `httpsPostData()`函数内容的话，就知道前面的代码中已经有这句话了，只是被注释掉了。打开就可以了，就是使用指定证书的了。

至于调用 POST 请求的地方，是一样的：

```java
httpPostData(MainActivity.this, url, content);
```


## 1.4 总结：

* 全部信任证书：不太安全，Google 也不推荐。但是毕竟是 https，比 http 安全多了，只是还存在被中间人攻击的风险；

* 信任指定证书：这种方式保证了网络传输链路的安全，是可以防住中间人攻击的。 但是问题可能会出在 App 上：这个证书直接放在 app 中，若是由于某些原因导致证书需要更新，就需要更新所有的 app，在用户量较大的情况下，这几乎是不可能完成的任务。

* 信任系统提供的证书（CA颁发）；这种方式最好，既安全又好维护，更换一个 CA 颁发的证书，对代码不需要做任何改动，但是可能需要花钱。也可以找些免费的证书，但是使用期限可能有较大的限制。

这三种方式各有特色，具体采用哪种方式，还是需要根据自己项目的实际情况来确定。