
```java
con = (HttpURLConnection) new URL(url).openConnection();
```

如果被请求的地址是 http 开头的就必须强转成 HttpURLConnection ；如果是 https 开头的请求，就必须强转成 HttpsURLConnection.

否则，如果请求地址是 http 的，但强转成了 Https ,就会报错误：

`HttpURLConnectionImpl cannot be case to javax.net.ssl.HttpURLConnecton`