* 比较A日期是否早于B日期

```java
// 如果 endDate 早于 startDate 会返回 -1 ，两者都是 Date 对象
-1 == endDate.compareTo(startDate)
```



* Java 8 中的日期处理

 [原文地址：](https://www.jianshu.com/p/2949db9c3df5)

在Java 8以前，日期和时间处理一直被广大java程序员抱怨太难用，首先是java.util和java.sql中，都包含Date类，如果要进行时间格式化，还需要java.text.DateFormat类处理。同时java.util.Date中既包含了日期，又包含了时间，所以java8新的日期和时间库，很好的解决了以前日期和时间类的很多弊端。并且也借鉴了第三方日期库joda很多的优点。

在java8中，java.time包下主要包含下面几个主要的类：

Instant：时间戳
Duration：持续时间，时间差
LocalDate：只包含日期，比如：2016-10-20
LocalTime：只包含时间，比如：23:12:10
LocalDateTime：包含日期和时间，比如：2016-10-20 23:14:21
Period：时间段
ZoneOffset：时区偏移量，比如：+8:00
ZonedDateTime：带时区的时间
Clock：时钟，比如获取目前美国纽约的时间
以及java.time.format包中的

DateTimeFormatter：时间格式化
下面我们通过例子来看如何使用java8新的日期时间库

1、获取今天的日期
LocalDate todayDate = LocalDate.now();
System.out.println("今天的日期："+todayDate);
//结果
今天的日期：2016-10-20
2、指定日期，进行相应操作
//取2016年10月的第1天
LocalDate firstDay = oneday.with(TemporalAdjusters.firstDayOfMonth());
System.out.println(firstDay);
        
//取2016年10月的第1天，另外一种写法
LocalDate firstDay2 = oneday.withDayOfMonth(1);
System.out.println(firstDay2);
        
//取2016年10月的最后1天，不用考虑大月，小月，平年，闰年
LocalDate lastDay = oneday.with(TemporalAdjusters.lastDayOfMonth());
System.out.println(lastDay);
        
//当前日期＋1天
LocalDate tomorrow = oneday.plusDays(1);
System.out.println(tomorrow);

//判断是否为闰年
boolean isLeapYear = tomorrow.isLeapYear();
System.out.println(isLeapYear);

//运行结果
2016-10-20
2016-10-01
2016-10-01
2016-10-31
2016-10-21
true
3、生日检查或者账单日检查
开发过程中，经常需要为过生日的用户送上一些祝福，例如，用户的生日为1990-10-12，如果今天是2016-10-12，那么今天就是用户的生日（按公历／身份证日期来算），那么通过java8新的日期库，我们该如何来进行判断？

在java8中，可以使用MonthDay，该类不包含年份信息，当然还有一个类是YearMonth

LocalDate birthday = LocalDate.of(1990, 10, 12);
MonthDay birthdayMd = MonthDay.of(birthday.getMonth(), birthday.getDayOfMonth());
MonthDay today = MonthDay.from(LocalDate.of(2016, 10, 12)); 
        System.out.println(today.equals(birthdayMd));
//结果
true
4、获取当前的时间
日期主要是使用LocalTime，该类不包含日期，只有时间信息

//获取当前的时间
LocalTime nowTime = LocalTime.now(); //结果14:29:40.558
        
//如果不想显示毫秒
LocalTime nowTime2 = LocalTime.now().withNano(0); //14:43:14
        
//指定时间
LocalTime time = LocalTime.of(14, 10, 21); //14:10:21
LocalTime time2 = LocalTime.parse("12:00:01"); // 12:00:01
        
//当前时间增加2小时
LocalTime nowTimePlus2Hour = nowTime.plusHours(2); //16:47:23.144
//或者
LocalTime nowTimePlus2Hour2 = nowTime.plus(2, ChronoUnit.HOURS);
5、日期前后比较
比较2个日期哪个在前，哪个在后，java8 LocalDate提供了2个方法，isAfter(),isBefore

LocalDate today = LocalDate.now();
LocalDate specifyDate = LocalDate.of(2015, 10, 20);
System.out.println(today.isAfter(specifyDate)); //true
6、处理不同时区的时间
java8中，将日期，时间，时区都很好的进行了分离。

//查看当前的时区
ZoneId defaultZone = ZoneId.systemDefault();
System.out.println(defaultZone); //Asia/Shanghai
        
//查看美国纽约当前的时间
ZoneId america = ZoneId.of("America/New_York");
LocalDateTime shanghaiTime = LocalDateTime.now();
LocalDateTime americaDateTime = LocalDateTime.now(america);
System.out.println(shanghaiTime); //2016-11-06T15:20:27.996
System.out.println(americaDateTime); //2016-11-06T02:20:27.996 ，可以看到美国与北京时间差了13小时
    
//带有时区的时间
ZonedDateTime americaZoneDateTime = ZonedDateTime.now(america);
System.out.println(americaZoneDateTime); //2016-11-06T02:23:44.863-05:00[America/New_York]
7、比较两个日期之前时间差
在项目中，经常需要比较两个日期之间相差几天，或者相隔几个月，我们可以使用java8的Period来进行处理。

LocalDate today = LocalDate.now();
LocalDate specifyDate = LocalDate.of(2015, 10, 2);
Period period = Period.between(specifyDate, today);

System.out.println(period.getDays());  //4
System.out.println(period.getMonths()); //1
System.out.println(specifyDate.until(today, ChronoUnit.DAYS)); //401
//输出结果
4
1
401
我们可以看到，我们使用Period类比较天数，比较奇怪，他返回的值，并不是2个日期之间总共的天数差，而是一个相对天数差，比如，5月1日，和10月2日，他比较的是仅仅2个天之间的差，那1号和2号，相差1天，而实际上，因为中间相差了好几个月，所以真正的天数差肯定不是1天，所以我们可以使用until，并指明精度单位是days，就可以计算真正的天数差了。

8、日期时间格式解析、格式化
在java8之前，我们进行时间格式化主要是使用SimpleDateFormat，而在java8中，主要是使用DateTimeFormatter,java8中，预定义了一些标准的时间格式，我们可以直接将时间转换为标准的时间格式：

String specifyDate = "20151011";
DateTimeFormatter formatter = DateTimeFormatter.BASIC_ISO_DATE;
LocalDate formatted = LocalDate.parse(specifyDate,formatter); 
System.out.println(formatted); 
//输出
2015-10-11
当然，很多时间标准的时间格式可能也不满足我们的要求，我们需要转为自定义的时间格式

DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("YYYY MM dd");
System.out.println(formatter2.format(LocalDate.now()));
//结果
2015 10 11
9、java8 时间类与Date类的相互转化
在转换中，我们需要注意，因为java8之前Date是包含日期和时间的，而LocalDate只包含日期，LocalTime只包含时间，所以与Date在互转中，势必会丢失日期或者时间，或者会使用起始时间。如果转LocalDateTime，那么就不存在信息误差。

//Date与Instant的相互转化
Instant instant  = Instant.now();
Date date = Date.from(instant);
Instant instant2 = date.toInstant();
        
//Date转为LocalDateTime
Date date2 = new Date();
LocalDateTime localDateTime2 = LocalDateTime.ofInstant(date2.toInstant(), ZoneId.systemDefault());
        
//LocalDateTime转Date
LocalDateTime localDateTime3 = LocalDateTime.now();
Instant instant3 = localDateTime3.atZone(ZoneId.systemDefault()).toInstant();
Date date3 = Date.from(instant);

//LocalDate转Date
//因为LocalDate不包含时间，所以转Date时，会默认转为当天的起始时间，00:00:00
LocalDate localDate4 = LocalDate.now();
Instant instant4 = localDate4.atStartOfDay().atZone(ZoneId.systemDefault()).toInstant();
Date date4 = Date.from(instant);
总结，其实想说的这，这篇文章是一篇难产很久的文章，自从十一假期后，一直找不到写代码的感觉，所以这篇文章拖了1个月。

作者：陶清清
链接：https://www.jianshu.com/p/2949db9c3df5
來源：简书
简书著作权归作者所有，任何形式的转载都请联系作者获得授权并注明出处。