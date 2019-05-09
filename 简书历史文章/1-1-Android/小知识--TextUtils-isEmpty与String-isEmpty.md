今天在腾讯Buggly中查看我司APP错误日志时发现这么一个错误：
`Attempt to invoke virtual method 'boolean java.lang.String.isEmpty()' on a null object reference
com.zjelite.login.utils.WebKeyUtils$4.handleMessage(WebKeyUtils.java:354)`

追了下代码，发现是因为在对字符串判断非空的时候使用了 java中String类下的isEmpty()，而不是android 中的TextUtils.isEmpty( )。详细原因如下：

>java中，String 类下的 isEmpty( ) 返回的只是 字符串的长度是否为0，如果 字符串为null 就会直接报空指针。源码如下：
`public boolean isEmpty() { 
    return count == 0;
}`

>android中，TextUtils.isEmpty(要判断的字符串)  会对 null 和长度进行判断，所以不会报空指针。源码如下：
`public static boolean isEmpty(@Nullable CharSequence str) { 
      if (str == null || str.length() == 0) 
          return true; 
      else return false;
}`





