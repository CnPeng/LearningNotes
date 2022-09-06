原文地址：https://gist.github.com/hellojinjie/5651980

# 1. 五种不同的  URL 参数解析方法的性能比较

因为在我们的项目中需要解析日志中的  URL 的参数，所以我对比了一下五种不同
的  URL 参数解析方法的性能。

## 1.1. URL 参数解析方法: 

### 1.1.1. httpclient

org.apache.http.client.utils.URLEncodedUtils

```java
URLEncodedUtils.parse(query, Charset.forName("UTF-8"));
```

### 1.1.2. jettyUtil

org.eclipse.jetty.util.UrlEncoded

```java
MultiMap<String> values = new MultiMap<String>();  
UrlEncoded.decodeTo(query, values, "UTF-8", 1000);
```

> 安卓导入新版jar包使用时，minSDK不能低于24，否则不能使用

### 1.1.3. tomcat

org.apache.catalina.util.RequestUtil

```java
    Map<String, String> values = new HashMap<String, String>();
    RequestUtil.parseParameters(values, query, "UTF-8");
```

### 1.1.4. regex 正则表达式

```java
    String u = URLDecoder.decode(url, "UTF-8");  
    for (String s : parameters) {  
        Pattern p = Pattern.compile(s + "=([^&]*)(&|$)");  
        Matcher m = p.matcher(u);  
        if (m.find()) {  
            m.group(1);  
        }  
    }
```

### 1.1.5. split

使用String 的split 方法对  URL 进行分割，然后用equals 匹配对应的参数

```java
    String u = URLDecoder.decode(url, "UTF-8");  
    for (String s : parameters) {  
        String[] a = new String[100];   
        if (u.indexOf(s) != -1) {  
            a = (u.substring(u.indexOf(s))).split("&");  
            a[0].split("=");  
        }  
    }   
```

前三者是  httpclient, jetty, tomcat 使用的  URL 解析工具。Split  方法是最简单
也是最直观的解析方法，regex 则使用了正则表达式去匹配参数。

## 1.2. 性能比较

用这五种方法分别解析同一个URL 100000遍，得到如下的数据。考虑到  Java  的代
码缓存特性，共运行4遍这样的测试，测试数据取最后一次的结果。   

```
---first---
httpclient: 3063
jettyUtil: 1767
tomcat: 2405
regex: 9226
split: 22905
---second---
httpclient: 2766
jettyUtil: 1618
tomcat: 2229
regex: 9025
split: 23661
---third---
httpclient: 2799
jettyUtil: 1632
tomcat: 2251
regex: 8761
split: 23476
---fouth---
httpclient: 2989
jettyUtil: 1634
tomcat: 2251
regex: 8895
split: 23571 
```

在最后一组数据中，我们可以看到  jettyUtil  的性能最高，split 和  regex 的方法性
能较差。

## 1.3. 总结

jettyUtil  解析URL的性能在五种中最高，如果我们在项目中需要解析  URL ，应该
尽可能的考虑使用  jettyUtil  来解析。

## 1.4. 参考链接

1.  本测试的代码  https://gist.github.com/hellojinjie/5651936
2.  Tomcat RequestUtil  的代码
http://svn.apache.org/viewvc/tomcat/tc6.0.x/trunk/java/org/apache/catalina/util/RequestUtil.java?view=markup
3.  jettyUtil UrlEncoded  的代码
http://git.eclipse.org/c/jetty/org.eclipse.jetty.project.git/tree/jetty-util/src/main/java/org/eclipse/jetty/util/UrlEncoded.java
4.  httpclient URLEncodedUtils  的代码
https://svn.apache.org/repos/asf/httpcomponents/httpclient/tags/4.0-beta1/module-client/src/main/java/org/apache/http/client/utils/URLEncodedUtils.java 



## 1.5. Android中如何解析？

android 中可以借助 Uri 类来实现。

```java
  /**
     * 作者：CnPeng
     * 时间：2018/5/7 上午8:45
     * 功用：取出URI中params,并将这些params放置到hashMap中
     * 说明：URI格式为：ant://article/new/open?keyword=%e6%83%85%e4%ba%ba%e8%8a%82  ， 其中 ？后面的 keyword 表示key,= 后面的为value
     */
    public static HashMap<String, String> getUriParams(String uri) {
        HashMap<String, String> intentParams = new HashMap<String, String>();

        Uri parse = Uri.parse(uri);
        Set<String> parameterNames = parse.getQueryParameterNames();
        for (String paramName : parameterNames) {
            String parameter = parse.getQueryParameter(paramName);
            intentParams.put(paramName, parameter);
        }

        return intentParams;
    }


    /**
     * 作者：CnPeng
     * 时间：2018/5/7 上午8:46
     * 功用：解析出URI中的params，并将这些参数设置给传递进来的intent，然后将intent返回
     * 说明：intent的跳转页面需要再传递进来之前就配置好了，这里只配置 extra 数据
     */
    public static Intent getIntentWithUriParams(String uri, @NonNull Intent intent) {
        HashMap<String, String> intentParams = getUriParams(uri);

        for (Map.Entry<String, String> entry : intentParams.entrySet()) {
            intent.putExtra(entry.getKey(), entry.getValue());
        }

        return intent;
    }
```