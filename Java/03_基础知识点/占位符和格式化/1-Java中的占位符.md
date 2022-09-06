原文地址 [https://blog.csdn.net/gsls200808/article/details/39968749](https://blog.csdn.net/gsls200808/article/details/39968749)

## 1. 占位符介绍

在Java中貌似很少有占位符(placeholder)这个概念，取而代之的是 fomat 类，另外一些框架也实现了占位符这样的东西。

在Java中有两种占位符%和{},

% 后面可以是d、f、s等中间也可以加其他参数。只能用于String类对象中，不能用于MessageFormat类对象。

{}中的数字要与后面的参数位置对应。只能用于MessageFormat类对象中，不能用于String类对象。

总的来说 String.format() 方法用起来不如 MessageFormat.format() 方法强大。

具体如何使用可以参考官方API文档

[http://docs.oracle.com/javase/7/docs/api/](http://docs.oracle.com/javase/7/docs/api/)

下面的几个例子仅供参考

```java
import java.text.MessageFormat;
import java.util.Date;
 
public class test01 {
 
	public static void main(String[] args) {
		System.out.println("hello");// print hello
 
		// %s占位符,输出字符串
		String username = "user1";
		int num = 3;
		System.out.printf("%s您好,您是第%s位访客\n", username, num); // prints user1您好,您是第3位访客
 
		// %f占位符
		double d = 1.2;
		float f = 1.2f;
		System.out.printf("%f %f", d, f); // prints 1.200000 1.200000
		
 
		// %1$s占位符
		//%n$ms：代表输出的是字符串，n代表是第几个参数，设置m的值可以在输出之前放置空格，也可以设为0m,在输出之前放置m个0 
		System.out.println(String.format("我是%1$s,我来自%2$s,今年%3$s岁", "中国人", "北京",
				"22"));
		// prints 我是中国人,我来自北京,今年22岁
		
 
		// {}占位符，{}内的数字代表第几个参数，参数从0开始
		String url = "www.baidu.com";
		int count = 1000;
		System.out.println(MessageFormat
				.format("该网站{0}被访问了 {1} 次.", url, count));
		// prints 该网站www.baidu.com被访问了 1,000 次.
 
 
		// {}占位符
		String template = "Welcome {0}!  Your last login was {1}";
		String output = MessageFormat.format(template, new Object[] { "Python",
				new Date().toString() });
		System.out.println(output);
		// prints  Welcome Python!  Your last login was Fri Oct 10 20:47:00 CST 2014
 
	}
```


## 2. Android 的 string.xml 中使用

```
//string.xml 声明
 <string name="arrive_notification_message"> Arrived a notification message. Content is \"%1$s\"</string>

//Java中调用
 String log = context.getString(R.string.arrive_notification_message, message.getContent());
```


## 3. 其他内容：

原文地址：[https://www.jianshu.com/p/3d834f3a5e01](https://www.jianshu.com/p/3d834f3a5e01)


在 java 中，可以通过 String 类中的 format 方法对字符串进行格式化和拼接相关操作。

format 方法有两种重载形式：

* format(Stringformat, Object... args) 新字符串使用本地语言环境，制定字符串格式和参数生成格式化的新字符串。
* format(Locale locale, String format, Object... args) 使用指定的语言环境，制定字符串格式和参数生成格式化的字符串。

format 方法通常需要和转换字符进行配合实现不同数据类型到字符串的转换，下表列举部分转换字符串：

转换符	|说明|	示例
---|---|---
%s	|字符串类型	|"mingrisoft"
%c	|字符类型	|'m'
%b	|布尔类型	|TRUE
%d	|整数类型（十进制）|	99
%x	|整数类型（十六进制）|	FF
%o	|整数类型（八进制）|	77
%f	|浮点类型	|99.99
%a	|十六进制浮点类型	|FF.35AE
%e	|指数类型	|9.38E+05
%g	|通用浮点类型（f和e类型中较短的）|	
%h	|散列码	|
%%	|百分比类型|	%
%n	|换行符	|
%tx	|日期与时间类型（x代表不同的日期与时间转换符	|

我们通过一个案例简单的演示一下，参考代码：

![](https://images.gitee.com/uploads/images/2018/0904/103947_a5d35e28_930142.png "屏幕截图.png")


可以看出，java 支持丰富的字符串输出格式。可能唯一不太好的地方就是转义字符太多，不方便记忆。
