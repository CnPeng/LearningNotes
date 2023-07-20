降级安装.


APK内部升级时一直提示安装失败/无法安装等。

通过 `adb install xxx.apk` 命令执行一次安装，这样可以看到具体的错误原因为：INSTALL_FAILED_VERSION_DOWNGRADE


导致该问题的原因大致如下：

* VersionCode 低于上一版
    * 如果使用 gitCode 作为versionCode ，那么更换代码仓库之后，gitCode 会重新从1计数
    * 开启 **手机分身** 功能的手机中，A分身中如果装了高版本的软件，B分身中再装低版本的软件时也会报错 （如小米6手机，开启分身的步骤为：设置--手机分身）
* targetVersion 低于上一版
* 

