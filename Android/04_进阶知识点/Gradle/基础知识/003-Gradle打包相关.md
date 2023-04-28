|  文章标题  |链接 |备注|
| --- | --- |---
|  Android - Gradle 使用干货 之 生成版本号，打包重命名和多渠道  |  [链接](https://blog.csdn.net/LABLENET/article/details/70864843)  |这个简直太棒了
| Android Studio gradle打包实践   |  [链接](https://www.jianshu.com/p/c5f69437100a)  |基本了解
| android gradle自动化打包方案完整总结   | [链接](https://www.jianshu.com/p/62557edf658b)   |还没看
| 一个项目如何编译多个不同签名、包名、资源等，的apk   | [链接](https://juejin.im/entry/5bd7a9b2e51d457a845f9a42?utm_source=gold_browser_extension)   |还没看

<br>
<hr>



文章摘录：

# 1.  Gradle 使用干货 之 生成版本号，打包重命名和多渠道


>作者：LABLENET 
>
>原文：https://blog.csdn.net/LABLENET/article/details/70864843 

## 1.1. 版本号
* version code
* version name

目标生成的 version name 如下所示 ：

```
// debug
1.0.78.170427.153740.8fe425
// release
1.0.78.170427.8fe425
```

 **解析：**

* 大版本号 ： 1 （手动）
* 小版本号 ：0 （手动）
* 修改版本号：78 （自动，git 提交次数）
* 编译日期： 170427（自动，年月日）, 153740 (自动，时分秒)
* 提交记录： 8fe425 (自动，git 最后一次提交记录后6位)

<br>

### 1.1.1. 获取 git 提交次数

```groovy
// 获取修订版本 git 提交次数
static def getRevisionNumber() {
    Process process = "git rev-list --count HEAD".execute()
    process.waitFor()
    return process.getText().toInteger()
}
```

 **注意：`git rev-list HEAD --first-parent --count` 是获取当前分支的git提交次数！！** 


### 1.1.2. 获取提交记录后6位

获取提交记录后6位：没有则返回 yyDDmm 日期

```groovy
// 获取修订版本最后一次 git 记录后6位
static def getRevisionDescription() {
    String desc = 'git describe --always'.execute().getText().trim()
    return (desc == null || desc.size() == 0) ? new Date().format("yyMMdd") : desc.substring(desc.size() - 6)
}
```

### 1.1.3. 获取 version code

```groovy
// 获取 version code
static def getVersionCode(boolean isDebug) {
    if (isDebug) {
        return Integer.parseInt(new Date().format("yyMMddHHmm"))
    }
    return getRevisionNumber()
}
```

### 1.1.4. 获取 version name

```groovy
// 获取 version name
def getVersionName(boolean isDebug) {
    String version = appConfig.appmajor +
            '.' + appConfig.appminor +
            '.' + getRevisionNumber()
    String today = new Date().format('yyMMdd')
    String time = new Date().format('HHmmss')
    if (isDebug) {
        return version + ".$today.$time." + getRevisionDescription()
    }
    return version + ".$today." + getRevisionDescription()
}
```

### 1.1.5. 调用上述方法
 
```groovy
 versionCode getVersionCode(true)
 versionName getVersionName(true)
```

## 1.2. 打包重命名


### 1.2.1. 名称示例

* debug 生成名称 : `appName_buildType.apk`

* release 生成名称：`appName_channelName_buildType_vesionCode.apk`


### 1.2.2. gradle 中的配置信息

 配置如下 ：

```groovy
 //打包命名
    applicationVariants.all {
        variant ->
            variant.outputs.each {
                output ->
                    if (variant.buildType.name == 'release') {
                        variant.mergedFlavor.versionCode = getVersionCode(false)
                        variant.mergedFlavor.versionName = getVersionName(false)
                        // release
                        def apkName = "${project.getName()}_${variant.flavorName}_${buildType.name}_v${variant.versionCode}.apk";
                        output.outputFile = new File(output.outputFile.parent, apkName);
                    } else {
                        variant.mergedFlavor.versionCode = getVersionCode(true)
                        variant.mergedFlavor.versionName = getVersionName(true)
                        // debug
                        def apkName = "${project.getName()}_${buildType.name}.apk";
                        output.outputFile = new File(output.outputFile.parent, apkName);
                    }
            }
    }
```


## 1.3. 多渠道

这里有两个版本 ： beta 和 production , 当然也可以写多个版本；

```groovy
productFlavors {
        beta {
            buildConfigField "String", "API_BETA", "\"$appConfig.betaAPI\""
            applicationId appConfig.appId + ".beta"
            manifestPlaceholders = [APP_NAME: appConfig.betaName,
                                    APP_LOGO: appConfig.betaLogo,
                                    PACKAGE : appConfig.appId
            ]
        }
        production {
            buildConfigField "String", "API_PRO", "\"$appConfig.proAPI\""
            applicationId appConfig.appId
            manifestPlaceholders = [APP_NAME: appConfig.proName,
                                    APP_LOGO: appConfig.proLogo,
                                    PACKAGE : appConfig.appId
            ]
        }
        WDJ {
           // 豌豆荚
        }
    }
```

配合 AndroidManifest.xml 使用，不同的版本可以不同的 logo 和 name :

```groovy
 <application
        android:name=".MainApplication"
        android:allowBackup="true"
        android:icon="${APP_LOGO}"
        android:label="${APP_NAME}"
        android:roundIcon="${APP_LOGO}"
        android:supportsRtl="true"
        android:theme="@style/TestTheme"
        tools:replace="android:label,icon"></application>
```


## 1.4. 全部配置(完整示例)

### 1.4.1. 配置 config.gradle

使用 配置如下

```groovy
ext.versions = [
        // 本地仓库版本
]

// app 相关信息总配置
ext.appConfig = [
        appmajor     : 1,
        appminor     : 0,
        appId        : 'com.smartahc.android.template.demo',
        keyAlias     : 'template',
        keyPassword  : 'template',
        storeFile    : 'template.jks',
        storePassword: 'template',
        betaAPI      : 'http://beta.labelnet.cn',
        betaName     : '@string/app_name',
        betaLogo     : '@mipmap/ic_launcher_round',
        proAPI       : 'http://pro.labelnet.cn',
        proName      : '@string/app_name',
        proLogo      : '@mipmap/ic_launcher',
]
```

### 1.4.2. build.gradle

```groovy
apply plugin: 'com.android.application'
apply from: "$rootDir/config.gradle"


android {
    compileSdkVersion versions.complieSdk
    buildToolsVersion versions.buildTools
    // APP 配置
    defaultConfig {
        applicationId appConfig.appId
        minSdkVersion versions.minSdk
        targetSdkVersion versions.targetSdk
        versionCode getVersionCode(true)
        versionName getVersionName(true)

        // 65535
        multiDexEnabled true
    }

    lintOptions {
        abortOnError false
    }

    dataBinding {
        enabled = false
    }

    // 签名 配置
    signingConfigs {
        debug {

        }
        release {
            keyAlias appConfig.keyAlias
            keyPassword appConfig.keyPassword
            storeFile file(appConfig.storeFile)
            storePassword appConfig.storePassword
        }
    }
    // 编译类型
    buildTypes {
        buildTypes {
            debug {
                applicationIdSuffix '.debug'
                buildConfigField 'boolean', 'IS_DEBUG', 'true'
                debuggable true
                zipAlignEnabled true
                minifyEnabled false
                shrinkResources false
                signingConfig signingConfigs.debug
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
            release {
                buildConfigField 'boolean', 'IS_DEBUG', 'false'
                debuggable false
                zipAlignEnabled true
                minifyEnabled false
                signingConfig signingConfigs.release
                proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
            }
        }
    }
    //打包命名
    applicationVariants.all {
        variant ->
            variant.outputs.each {
                output ->
                    if (variant.buildType.name == 'release') {
                        variant.mergedFlavor.versionCode = getVersionCode(false)
                        variant.mergedFlavor.versionName = getVersionName(false)
                        // release
                        def apkName = "${project.getName()}_${variant.flavorName}_${buildType.name}_v${variant.versionCode}.apk";
                        output.outputFile = new File(output.outputFile.parent, apkName);
                    } else {
                        variant.mergedFlavor.versionCode = getVersionCode(true)
                        variant.mergedFlavor.versionName = getVersionName(true)
                        // debug
                        def apkName = "${project.getName()}_${buildType.name}.apk";
                        output.outputFile = new File(output.outputFile.parent, apkName);
                    }
            }
    }

    productFlavors {
        beta {
            buildConfigField "String", "API_BETA", "\"$appConfig.betaAPI\""
            applicationId appConfig.appId + ".beta"
            manifestPlaceholders = [APP_NAME: appConfig.betaName,
                                    APP_LOGO: appConfig.betaLogo,
                                    PACKAGE : appConfig.appId
            ]
        }
        production {
            buildConfigField "String", "API_PRO", "\"$appConfig.proAPI\""
            applicationId appConfig.appId
            manifestPlaceholders = [APP_NAME: appConfig.proName,
                                    APP_LOGO: appConfig.proLogo,
                                    PACKAGE : appConfig.appId
            ]
        }
    }
}

dependencies {
    compile fileTree(include: ['*.jar'], dir: 'libs')
    compile project(':core_ui')
}

// versionCode  : git commit 次数 ;
// versionName : 大版本号.小版本号.修改版本号.编译版本号 ;

// 获取 version code
static def getVersionCode(boolean isDebug) {
    if (isDebug) {
        return Integer.parseInt(new Date().format("yyMMddHHmm"))
    }
    return getRevisionNumber()
}

// 获取 version name
def getVersionName(boolean isDebug) {
    String version = appConfig.appmajor +
            '.' + appConfig.appminor +
            '.' + getRevisionNumber()
    String today = new Date().format('yyMMdd')
    String time = new Date().format('HHmmss')
    if (isDebug) {
        return version + ".$today.$time." + getRevisionDescription()
    }
    return version + ".$today." + getRevisionDescription()
}

// 获取修订版本 git 提交次数
static def getRevisionNumber() {
    Process process = "git rev-list --count HEAD".execute()
    process.waitFor()
    return process.getText().toInteger()
}

// 获取修订版本最后一次 git 记录后6位
static def getRevisionDescription() {
    String desc = 'git describe --always'.execute().getText().trim()
    return (desc == null || desc.size() == 0) ? new Date().format("yyMMdd") : desc.substring(desc.size() - 6)
}
```




