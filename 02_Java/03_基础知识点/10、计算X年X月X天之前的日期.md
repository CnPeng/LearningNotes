计算 X 年之前，X 月 之前，X 天之前的方法。

西面示例中使用了 kotlin, 本质还是调用 java 的 API


```kotlin
//CnPeng 2019-06-20 14:13 日期的处理
  val curDate: Date = Date()
  val ca = Calendar.getInstance()
  // 设置时间为当前时间
  ca.time = Date();
  //ca.set(2011, 11, 17) // 月份是从0开始的，所以11表示12月
  //ca.add(Calendar.YEAR, -1) // 年份减1
  //ca.add(Calendar.MONTH, -1) // 月份减1

  val sdf = SimpleDateFormat("yyyy/MM/dd", Locale.CHINA)
  val curDateStr = sdf.format(curDate)

  ca.add(Calendar.YEAR, -1)
  ca.add(Calendar.DAY_OF_MONTH, +1)
  val lastYearDateStr = sdf.format(ca.time)
  
  // 最终显示效果：2018/06/21 - 2019/06/20
  tv_date.text = "$lastYearDateStr - $curDateStr"
```