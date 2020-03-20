[官方文档参考（中文）：](https://developer.android.google.cn/about/versions/10)

[android10获取相册图片](https://www.cnblogs.com/wang-jingyuan/p/12174051.html)
[Android 10分区存储介绍及百度APP适配实践](https://mp.weixin.qq.com/s/djTZykAvPc3uWcdvAjHZMw)

[Android10填坑适配指南，实际经验代码，拒绝翻译](https://juejin.im/post/5ddb2b5b51882573461819e0#heading-7)


---

关于在 Android 10 下如何访问文档文件的思路整理：

### 1、方案1 ：[临时停用分区存储](https://developer.android.google.cn/training/data-storage/files/external-scoped)

`targetApi = 29` 或更高版本时，可以考虑启用暂停分区存储，需要在 manifest.xml 文件中做如下修改：

```
<manifest ... >
      <!-- This attribute is "false" by default on apps targeting
           Android 10 or higher. -->
      <application android:requestLegacyExternalStorage="true" ... >
        ...
      </application>
    </manifest>
```

但是，[官方文档](https://developer.android.google.cn/training/data-storage/files/external-scoped)中也有如下描述：

>警告：明年（这个明年应该就是指的 2020 年），主要平台版本将要求所有应用都使用分区存储，无论应用的目标 SDK 级别是多少。因此，您应该提前确保您的应用能够使用分区存储。为此，请确保针对搭载 Android 10（API 级别 29）及更高版本的设备启用了该行为。

### 2、方案2：仅读取私有目录下的文档内容

目前 QQ 是这么干的，在聊天中分享文件时，QQ 取了公有目录下的图片和视频，但是取了私有目录的文件。

实现虽然方便，代码改动量少，但取的文件不全。

### 3、方案3：使用系统自带的文件选择器

通过 intent 的 Action 打开系统的文件选择器，代码如下：

```
 // ACTION_OPEN_DOCUMENT is the intent to choose a file via the system's file
            // browser.
            Intent intent = new Intent(Intent.ACTION_OPEN_DOCUMENT);

            // Filter to only show results that can be "opened", such as a
            // file (as opposed to a list of contacts or timezones)
            intent.addCategory(Intent.CATEGORY_OPENABLE);

            // Filter to show only images, using the image MIME data type.
            // If one wanted to search for ogg vorbis files, the type would be "audio/ogg".
            // To search for all documents available via installed storage providers,
            // it would be "*/*".
            //            intent.setType("image/*");
            intent.setType("*/*");

            startActivityForResult(intent, 6666);
```

虽然这种方式实现简单，但是 UI 不统一（不同厂商可能会有不同的定制），而且一次仅能选择一个文件， 使用这种方式的话，现有的部分代码逻辑也需要重新修改。

### 4、钉钉的实现方式

按照钉钉聊天界面中选择文件时的呈现方式，推测钉钉大概实现方式可能有两种：

* 方案一：暂时停用了分区存储。
* 方案二：
	* 读取私有目录下的文件
	* 读取 QQ 和 微信 目录下的文件，QQ 和 微信 可能有提供 [`DocumentsProvider` ](https://developer.android.google.cn/guide/topics/providers/create-document-provider)的实现类，通过该实现类可以读取 QQ 和 微信 私有目录下的文件（此处仅是猜测，暂未查阅到相关资料）。
	* 钉钉的文件选择页面有三个 TAB ，分别为 所有、微信、QQ，在最后一个 QQ TAB 下从右向左滑动时，新开一个页面，该页面展示系统目录树。

### 5、微信的实现方式

微信聊天界面中传输文件时，打开的也是一个目录树，但这个目录树仅是微信私有目录或者腾讯集团其他产品的目录树。  

### 6、实现方案：

综合考虑到代码修改量以及现有 UI 的兼容，临时采用第一种方案——暂时停用分区存储。后续在继续考虑其他方案。
 
---

对于 Android 10 的适配，按照官方文档说明分为三块：隐私功能、行为变更、新功能和API

## 一、隐私功能

### 1、存储

#### (1)、限定 APP 对于存储空间的访问

当应用的 targetSdkVersion>=29 时，在默认情况下该应用只能看到本应用的专有目录以及特定类型的媒体目录。

该专有目录可以理解为沙盒，这种限定访问空间的策略被称为 `分区存储`。我们可以通过 `Context.getExternalFilesDir()` 访问该目录下的内容。

可以访问的媒体内容包括：照片、视频、音频，在访问这些内容时我们需要借助 `MediaStore`。


#### (2)、申请访问存储空间的权限

文件位置 | 所需权限 | 访问方法  | 卸载应用时是否移除文件？
---|---|---|---
特定于应用的目录 | 无 | `getExternalFilesDir()` | 是
媒体集合（照片、视频、音频）| `READ_EXTERNAL_STORAGE`（仅当访问其他应用的文件时）| `MediaStore` | 否
下载内容（文档和电子书籍）| 无 | 存储访问框架（SAF——加载系统的文件选择器）| 否


> 我们可以使用 [存储访问框架（SAF）](https://developer.android.google.cn/guide/topics/providers/document-provider)访问上表中列举的每一个位置，且不需要请求任何权限。

#### (3)、需要修改的内容

[https://developer.android.google.cn/training/data-storage/files/external](https://developer.android.google.cn/training/data-storage/files/external)


---

## 其他参考内容

* [Android 10 加载手机本地图片](https://juejin.im/post/5d80ef726fb9a06aeb10f223?utm_source=gold_browser_extension)

Android 10 由于文件权限的关系不能使用图片路径直接加载手机储存卡内的图片，除非图片是在应用的私有目录下。可以通过图片uri来加载图片，这种方式在 Android 10也是可以的。 先将图片路径转换成uri 。

* [Android 调用相机拍照，适配到Android 10](https://juejin.im/post/5d80edb76fb9a06b1c746176?utm_source=gold_browser_extension)