打开之前已经存在并能正常运行的项目时，突然报错 `Unable to resolve dependency for ':app@debug/compileClasspath'`

解决方案是：

进入到 `gradle.properties(Global Properties)` 中强制删除 https 代理配置。

之所以说是强制删除，是因为我们做这个操作的时候会出警告提示，我们依旧选择删除就好了。