原文：[AndroidManifest.xml中含盖的安全问题](https://blog.csdn.net/samlirongsheng/article/details/104904522)

## 1. 关于AndroidManifest.xml

AndroidManifest.xml 是每个android程序中必须的文件。它位于整个项目的根目录，Manifest文件提供有关应用程序到Android系统的基本信息，系统必须具有该信息才能运行任何应用程序的代码。换句话说APP是跑在Android系统上，既然要跑在其上，就必须提供信息给Android System，这些信息就存在AndroidManifest中。AndroidManifest.xml 存放在 app/src/main/ 目录下。在反编译APK文件后，其文件是以乱码格式存在，需要进行转换才能正常查看。

### 1.1. AndroidManifest.xml的主要功能

* 命名应用程序Java包，软件包名称作为应用程序的唯一标识符;
* 描述了应用程序的组件，其中包括构成应用程序的Activity，Service，Broadcast Receiver和Content Provider；它还命名实现每个组件并发布其功能的类，例如Intent可以处理的消息。这些声明通知Android系统的组件及其可以启动的条件;
* 决定哪些processes主持application;
* 宣告这个App有哪些权限，它声明应用程序必须拥有的权限才能访问API的受保护部分并与其他应用程序交互。它还声明其他人为了与应用程序的组件交互而需要的权限; 
* 它列出了Instrumentation在应用程序运行时提供概要分析和其他信息的类。这些声明仅在应用程序正在开发中才会存在，并在应用程序发布之前被删除; 
* 它声明了应用程序需要的最低级别的Android API;
* 它列出了应用程序必须链接的库。

```xml
<?xml version="1.0" encoding="utf-8" standalone="no"?><manifest xmlns:android="http://schemas.android.com/apk/res/android" package="com.mwr.example.sieve">
 <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE"/>
 <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
 <uses-permission android:name="android.permission.INTERNET"/>
 <permission android:label="Allows reading of the Key in Sieve" android:name="com.mwr.example.sieve.READ_KEYS" android:protectionLevel="dangerous"/>
 <permission android:label="Allows editing of the Key in Sieve" android:name="com.mwr.example.sieve.WRITE_KEYS" android:protectionLevel="dangerous"/>
 <application android:allowBackup="true" android:debuggable="true" android:icon="@drawable/ic_launcher" android:label="@string/app_name" android:theme="@style/AppTheme">
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:exported="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_file_select" android:name=".FileSelectActivity"/>
 <activity android:excludeFromRecents="true" android:label="@string/app_name" android:launchMode="singleTask" android:name=".MainLoginActivity" android:windowSoftInputMode="adjustResize|stateVisible">
 <intent-filter>
 <action android:name="android.intent.action.MAIN"/>
 <category android:name="android.intent.category.LAUNCHER"/>
 </intent-filter>
 </activity>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:exported="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_pwlist" android:name=".PWList"/>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_settings" android:name=".SettingsActivity"/>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_add_entry" android:name=".AddEntryActivity"/>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_short_login" android:name=".ShortLoginActivity"/>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_welcome" android:name=".WelcomeActivity"/>
 <activity android:clearTaskOnLaunch="true" android:excludeFromRecents="true" android:finishOnTaskLaunch="true" android:label="@string/title_activity_pin" android:name=".PINActivity"/>
 <service android:exported="true" android:name=".AuthService" android:process=":remote"/>
 <service android:exported="true" android:name=".CryptoService" android:process=":remote"/>
 <provider android:authorities="com.mwr.example.sieve.DBContentProvider" android:exported="true" android:multiprocess="true" android:name=".DBContentProvider">
 <path-permission android:path="/Keys" android:readPermission="com.mwr.example.sieve.READ_KEYS" android:writePermission="com.mwr.example.sieve.WRITE_KEYS"/>
 </provider>
 <provider android:authorities="com.mwr.example.sieve.FileBackupProvider" android:exported="true" android:multiprocess="true" android:name=".FileBackupProvider"/>
 </application>
</manifest>
```

## 2. 1.2 AndroidManifest.xml 风险点分析

### 2.1. 1.2.1 allowBackup 设置风险

Android API Level 8 （Android 2.1）及其以上Android系统提供了为应用程序数据的备份和恢复功能，此功能的开关决定于该应用程序中 AndroidManifest.xml 文件中的 allowBackup 属性值，其属性值默认是 true。当allowBackup的属性值没有显示设置为false时，攻击者可通过 adb backup 和 adb restore 来进行对应用数据的备份和恢复，从而可能获取明文存储的用户的敏感信息。

```xml
android:allowBackup=["true" | "false"]
```


```
$ adb backup -nosystem -noshared -apk -f com.example.demo
$ adb restore com.example.demo
```

- nosystem表示不备份系统应用
- noshared表示不备份应用存储在SD中的数据
- apk表示备份应用APK安装包
- f表示备份的.ab文件路径和文件名，最后是要备份应用的packageName
- restore是恢复备份的数据

### 2.2. 1.2.2、debuggable设置风险

该属性用于指定应用程序是否能够被调试，即使是以用户模式运行在设备上的时候，如果设置为true，则可以被调试；但是现在Android版本均默认debuggable的属性值为false，所以建议使用默认配置。

```xml
android:debuggable=["true" | "false"]
```

### 2.3. 1.2.3、组件导出风险

四大组件

* Activity
* Broadcast Receive
* Service
* Content Provider

可导出的组件能被第三方APP任意调用，导致敏感信息泄露，并可能被利用于绕过认证、恶意代码注入、sql注入、拒绝服务等攻击；

Activity中exported的默认值

* 没有intent filter时，默认为false
* 有intent filter时，默认为true

而intent filter标签代表是主Activity，而每个APP都会有一个主Activity，所以当应用的Activity不必要导出时，或者配置了intent filter标签，建议显示设置 android:exported="false"。如果组件必须要导出给其他应用使用，建议对组件进行权限控制。

Broadcast Receive和Service的默认值都跟Activity的一样。

Content Provider中exported的默认值

>当minSdkVersion或者targetSdkVersion小于16时，默认为true 大于17时，默认为false

### 2.4. 1.2.4、自定义权限风险

在Android系统的安全模型中，应用程序在默认的情况下不可以执行任何对其他应用程序、系统或用户带来负面影响的操作。如果应用需要执行某些操作，就需要声明使用这个操作对应的权限，也就是在AndroidManifest.xml文件中添加<uses-permission>标记,当然也可以自定义属于自己的permission。但是如果权限控制不当，那么就可能导致各种越权等安全问题。

```xml
<uses-permission android:name="android.permission.INTERNET"/>
<permission android:label="Allows reading of the Key in Sieve" android:name="com.mwr.example.sieve.READ_KEYS" android:protectionLevel="dangerous"/>
```


```
android:protectionLevel=["normal" | "dangerous" | "signature" | "signatureOrSystem"]
```

* normal：这是最低风险的权限，如果应用声明了此权限，系统直接默认该应用有此权限,也不会提示安装应用的用户授权；
* dangerous：系统在安装此类权限声明的应用时会提示用户，但是所有APP都能访问和共享此权限；
* signature：这种权限级别叫做高级权限或者系统权限，只有当发请求的应用和接收此请求的应用使用同一签名文件，并且声明了该权限才会授权，并且是默认授权，不会提示用户授权
* signatureOrSystem：这种权限应该尽量避免使用，偏向系统级

## 3. 1.3 AndroidManifest.xml结构

```xml
<?xmlversion="1.0"encoding="utf-8"?>
  
<manifest>
 <application>
 <activity>
 <intent-filter>
 <action/>
 <category/>
 </intent-filter>
 </activity>
 <activity-alias>
 <intent-filter></intent-filter>
 <meta-data/>
 </activity-alias>
 <service>
 <intent-filter></intent-filter>
 <meta-data/>
 </service>
 <receiver>
 <intent-filter></intent-filter>
 <meta-data/>
 </receiver>
 <provider>
 <grant-uri-permission/>
 <meta-data/>
 </provider>
 <uses-library/>
 </application>
 <uses-permission/>
 <permission/>
 <permission-tree/>
 <permission-group/>
 <instrumentation/>
 <uses-sdk/>
 <uses-configuration/>
 <uses-feature/>
 <supports-screens/>
</manifest>
```


## 4. 1.4 AndroidManifest.xml分节介绍

### 4.1. 1.4.1、manifest

```xml
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
 package="com.woody.test"
 android:sharedUserId="string"
 android:sharedUserLabel="string resource"
 android:versionCode="integer"
 android:versionName="string"
 android:installLocation=["auto" | "internalOnly" | "preferExternal"] >
</manifest>
```

### 4.2. 1.4.2、application

```xml
<application android:allowClearUserData=["true" | "false"]
 android:allowTaskReparenting=["true" | "false"]
 android:backupAgent="string"
 android:debuggable=["true" | "false"]
 android:description="string resource"
 android:enabled=["true" | "false"]
 android:hasCode=["true" | "false"]
 android:icon="drawable resource"
 android:killAfterRestore=["true" | "false"]
 android:label="string resource"
 android:manageSpaceActivity="string"
 android:name="string"
 android:permission="string"
 android:persistent=["true" | "false"]
 android:process="string"
 android:restoreAnyVersion=["true" | "false"]
 android:taskAffinity="string"
 android:theme="resource or theme" >
</application>
```

### 4.3. 1.4.3、activity

```xml
<activity android:allowTaskReparenting=["true" | "false"]
 android:alwaysRetainTaskState=["true" | "false"]
 android:clearTaskOnLaunch=["true" | "false"]
 android:configChanges=["mcc", "mnc", "locale",
   "touchscreen", "keyboard", "keyboardHidden",
   "navigation", "orientation", "screenLayout",
   "fontScale", "uiMode"]
 android:enabled=["true" | "false"]
 android:excludeFromRecents=["true" | "false"]
 android:exported=["true" | "false"]
 android:finishOnTaskLaunch=["true" | "false"]
 android:icon="drawable resource"
 android:label="string resource"
 android:launchMode=["multiple" | "singleTop" |
  "singleTask" | "singleInstance"]
 android:multiprocess=["true" | "false"]
 android:name="string"
 android:noHistory=["true" | "false"]
 android:permission="string"
 android:process="string"
 android:screenOrientation=["unspecified" | "user" | "behind" |
   "landscape" | "portrait" |
   "sensor" | "nosensor"]
 android:stateNotNeeded=["true" | "false"]
 android:taskAffinity="string"
 android:theme="resource or theme"
 android:windowSoftInputMode=["stateUnspecified",
   "stateUnchanged", "stateHidden",
   "stateAlwaysHidden", "stateVisible",
   "stateAlwaysVisible", "adjustUnspecified",
   "adjustResize", "adjustPan"] > 
</activity>
```


### 4.4. 1.4.4、intent-filter

```xml
<intent-filter android:icon="drawable resource"
 android:label="string resource"
 android:priority="integer" >
<action />
<category />
<data />
</intent-filter>
```

### 4.5. 1.4.5、meta-data

```xml
<meta-data android:name="string"
  android:resource="resource specification"
  android:value="string"/>
```  
  
### 4.6. 1.4.6、activity-alias

```xml
<activity-alias android:enabled=["true" | "false"]
  android:exported=["true" | "false"]
  android:icon="drawable resource"
  android:label="string resource"
  android:name="string"
  android:permission="string"
  android:targetActivity="string">
  
<intent-filter/>
<meta-data/>
</activity-alias>
```

### 4.7. 1.4.7、service

```xml
<service android:enabled=["true" | "false"]
android:exported[="true" | "false"]
android:icon="drawable resource"
android:label="string resource"
android:name="string"
android:permission="string"
android:process="string">
</service>
```

### 4.8. 1.4.8、receiver

### 4.9. 1.4.9、provider

```xml
<provider android:authorities="list"
android:enabled=["true" | "false"]
android:exported=["true" | "false"]
android:grantUriPermissions=["true" | "false"]
android:icon="drawable resource"
android:initOrder="integer"
android:label="string resource"
android:multiprocess=["true" | "false"]
android:name="string"
android:permission="string"
android:process="string"
android:readPermission="string"
android:syncable=["true" | "false"]
android:writePermission="string">
<grant-uri-permission/>
<meta-data/>
</provider>
```

### 4.10. 1.4.10、uses-library 
### 4.11. 1.4.11、supports-screens

```xml
<supports-screens android:smallScreens=["true" | "false"]
   android:normalScreens=["true" | "false"]
   android:largeScreens=["true" | "false"]
   android:anyDensity=["true" | "false"] />
```
   
### 4.12. 1.4.12、uses-configuration和uses-feature

```xml
<uses-configuration android:reqFiveWayNav=["true" | "false"]
   android:reqHardKeyboard=["true" | "false"]
   android:reqKeyboardType=["undefined" | "nokeys" | "qwerty" | "twelvekey"]
   android:reqNavigation=["undefined" | "nonav" | "dpad" | "trackball" | "wheel"]
   android:reqTouchScreen=["undefined" | "notouch" | "stylus" | "finger"] />
  
<uses-feature android:glEsVersion="integer"
  android:name="string"
  android:required=["true" | "false"] />
```
  
### 4.13. 1.4.13、uses-sdk

```xml
<uses-sdk android:minSdkVersion="integer"
  android:targetSdkVersion="integer"
  android:maxSdkVersion="integer"/>
```
  
### 4.14. 1.4.14、instrumentation

```xml
<instrumentation android:functionalTest=["true" | "false"]
   android:handleProfiling=["true" | "false"]
   android:icon="drawable resource"
   android:label="string resource"
   android:name="string"
   android:targetPackage="string"/>
```   
   
### 4.15. 1.4.15、<permission>、<uses-permission>、<permission-tree />、<permission-group />区别

最常用的当属 <uses-permission>，当我们需要获取某个权限的时候就必须在我们的manifest文件中声明，此<uses-permission>与<application>同级，具体权限列表请看此处

通常情况下我们不需要为自己的应用程序声明某个权限，除非你提供了供其他应用程序调用的代码或者数据。这个时候你才需要使用<permission> 这个标签。很显然这个标签可以让我们声明自己的权限。比如：

```xml
<permission android:name="com.teleca.project.MY_SECURITY" . . . />
```

那么在activity中就可以声明该自定义权限了，如：

```xml
<application . . .>
 <activity android:name="XXX" . . . >
   android:permission="com.teleca.project.MY_SECURITY"> </activity>
 </application>
```

当然自己声明的permission也不能随意的使用，还是需要使用<uses-permission>来声明你需要该权限<permission-group> 就是声明一个标签，该标签代表了一组permissions，而<permission-tree>是为一组permissions声明了一个namespace。这两个标签可以看之前的系列文章。


相关参考：[Android静态安全检测 -> Activity组件暴露](https://blog.csdn.net/u013107656/article/details/51889227)