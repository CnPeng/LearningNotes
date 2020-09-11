在获取本地视频时，会展示视频时长，时长通常取到的是毫秒值，在转换成时分秒结构时，可以使用SimpleDateFormat进行转换。

```java
 public static String timeParseMinute(long duration) {
        try {
            SimpleDateFormat msFormat = null;
            long hourDuration = 1000 * 60 * 60;
            if (duration < hourDuration) {
                //如果没有超过小时
                msFormat = new SimpleDateFormat("mm:ss");
            } else {
                //如果超过了小时
                msFormat = new SimpleDateFormat("HH:mm:ss");
                // 设置格式化器的时区为格林威治时区，否则格式化的结果不对，中国的时间比格林威治时间早8小时，比如0点会被格式化为8:00
                msFormat.setTimeZone(TimeZone.getTimeZone("GMT+0:00"));
            }
            return msFormat.format(duration);
        } catch (Exception e) {
            e.printStackTrace();
            return "00:00";
        }
    }

```