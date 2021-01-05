[原文：Goland无法启动](https://blog.csdn.net/NetRookieX/article/details/107243643?utm_medium=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-BlogCommendFromBaidu-1.control)


* 操作系统 ： Mac (其他系统类似，删除掉 `goland.vmoptions` 文件最后的 jar 包路径即可)

* 原因: 使用 jar 包 po 解 Jetbrains

* 解决办法:

删除 `/Users/<用户名>/Library/Application\ Support/JetBrains/GoLand2020.1/`

注意：`Application\ Support` 目录名中有一个空格，反斜杠用于转义