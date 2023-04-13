# 1. 44-AS的ArcticFox2020.3.1中报错Could not resolve或Could not download

在将 AndroidStudio 升级到 ArcticFox2020.3.1 之前，项目正常编译和运行。

但升级 AS 并同时升级 Gradle 到 7.0.2 之后，再次编译项目时报错大量 Could not resolve 和 Could not download 错误。

经过分析发现，是因为项目根目录下的 build.gradle 文件中使用了阿里和华为的 maven 仓库，但仓库都是 http 的，所以报错了，修改为 https 的 maven 仓库之后，编译和运行正常。

修改之后的 build.gradle 文件如下：

```
buildscript {
    repositories {
        google()
        maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }//阿里云镜像服务
        maven { url 'https://developer.huawei.com/repo/' }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.5.0'
        classpath 'com.jfrog.bintray.gradle:gradle-bintray-plugin:1.8.4'
        classpath 'com.huawei.agconnect:agcp:1.2.0.300'
        classpath 'com.github.dcendents:android-maven-gradle-plugin:2.1'
    }
}

allprojects {
    repositories {
        google()
        maven { url 'https://jitpack.io' }
        maven { url 'https://developer.huawei.com/repo/' }
        maven { url 'https://maven.aliyun.com/nexus/content/groups/public/' }//阿里云镜像服务
    }
}

task clean(type: Delete) {
    delete rootProject.buildDir
}
```