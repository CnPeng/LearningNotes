##一、问题现象
APK内部升级时一直提示安装失败/无法安装等。

##二、问题原因
通过 `adb install xxx.apk` 命令执行一次安装，这样可以看到具体的错误信息为：`INSTALL_FAILED_VERSION_DOWNGRADE`

导致该问题的原因大致如下：

* VersionCode 低于上一版
    * 如果使用 gitVersion 作为versionCode ，那么**更换代码仓库之后，gitVersion 会重新从1计数**（这一次发版栽在这里了）
    * 开启 **手机分身** 功能的手机中，A分身中如果装了高版本的软件，B分身中再装低版本的软件时也会报错 （如小米6手机，开启分身的步骤为：设置--手机分身）
* targetVersion 低于上一版

##三、附录
###（1）、moudle.gradle中配置 gitVersion 作为 versionCode
```
android {
 defaultConfig {
        def gitCode = getGitVersion()
        versionCode gitCode
       //其他内容省略。。。
    }
}
//获取Git版本号
def getGitVersion() {
    def cmd = 'git rev-list HEAD --first-parent --count'
    def gitVersion = cmd.execute().text.trim().toInteger() + 1500
    return gitVersion
    //return 10000;      //如果需要运行FindBugs 此处需要换成一个比较大的数值，同时注释掉上面两行即可
}
```
