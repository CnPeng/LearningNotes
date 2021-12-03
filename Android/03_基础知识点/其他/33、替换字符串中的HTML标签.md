https://ask.helplib.com/android/post_9292166

核心代码：

```java
Pattern tags = Pattern.compile ("</?[^>]+>");
Matcher match = tags.matcher (yourContent);//here you specify the string you want to modify (HTML)
String result = match.replaceAll("");
```

其他相关：

https://www.androiddev.net/java%E6%AD%A3%E5%88%99%E8%A1%A8%E8%BE%BE%E5%BC%8F%E5%8E%BB%E9%99%A4html%E4%B8%AD%E6%89%80%E6%9C%89%E7%9A%84%E6%A0%87%E7%AD%BE%E5%92%8C%E7%89%B9%E6%AE%8Ahtml%E5%AD%97%E7%AC%A6%EF%BC%88%E4%BB%A5/ 