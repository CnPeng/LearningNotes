原文链接：https://blog.csdn.net/xiaochenuu/article/details/80245931


两种方法解决：

1，在系统偏好-》网络-》找到对应的连接网络-》高级-》代理中去除勾选解决。（似乎不太靠谱，因为当时是开着蓝灯，在去除后AS中错误提示是没了，但是蓝灯是用不了）



2，根据stackOverflow 上的回答，打开终端，输入指令:`JAVA_OPTS="$JAVA_OPTS -DsocksProxtPort"`  ，之后`AS File->invalitDate caches/restart`  之后就差不多好使了。

