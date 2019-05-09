>190325 补充：莫名问题的解决
>181106 补充：修改未迁移成功的三方库

## 1、AndroidX简介

[点击查看Android文档中对androidx的简介](https://developer.android.com/topic/libraries/support-library/androidx-overview)

按照官方文档说明 androidx 是对 android.support.xxx 包的整理后产物。由于之前的support包过于混乱，所以，google推出了 androidX。

由于在后续版本中，会逐步放弃对support 的升级和维护，所以，我们必须迁移到 androidX.对此，官方描述如下：

```
Existing packages, such as the Android Support Library, are being refactored into AndroidX.
Although Support Library versions 27 and lower are still available on Google Maven,
all new development will be included in only AndroidX versions 1.0.0 and higher.
```


## 2、迁移步骤

### 2.1 修改gradle.properties
```
android.useAndroidX=true
android.enableJetifier=true
```

其中：
* `android.useAndroidX=true` 表示当前项目启用 androidx
* `android.enableJetifier=true` 表示将依赖包也迁移到androidx 。如果取值为false,表示不迁移依赖包到androidx，但在使用依赖包中的内容时可能会出现问题，当然了，如果你的项目中没有使用任何三方依赖，那么，此项可以设置为false

### 2.2 如何迁移

[点击查看官方迁移指南](https://developer.android.com/topic/libraries/support-library/refactor#migrate)

在AndroidStudio 3.2 或更高版本（目前最新正式版为3.2，其他更高版为alpha版）中执行如下操作：
* Refactor > Migrate to AndroidX
![image](http://upload-images.jianshu.io/upload_images/2551993-5b5f41cc0cd35ba6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "屏幕截图.png")

> 在执行该操作时会提醒我们是否将当前项目打包备份。如果你提前已经做好了备份，可以忽略；如果没有备份，则先备份。


## 3 迁移后续

### 3.1 手动修改错误包名
由于 `Migrate to AndroidX` 执行之后，部分控件的包名/路径名转换的有问题，所以还需要我们手动调整（包括修改xml布局文件和.java/.kt 文件）。

如：ViewPager,RecyclerView 等，这些内容在迁移完成之后，包名是 `androidx.core.weight.xxxx`,这是一个错误的包名，我们必须手动修改，否则，无法正常编译——点击绿色 Run(运行) 按钮时会详细报出哪里有错误。

>**此处需要注意，在 AndroidStudio 的 build 选项卡中一次最多只会报 50条错误！！** 所以，可能在你修完第一批之后，后面还有N个50。此处要保持一个平静的💗。

### 3.2 修复DataBinding中的错误（重名id错误）
在 AndroidStudio3.2 + androidx 环境下，对错误的检查和处理更为严格。如果同一个xml布局文件中存在同名id，在之前的版本中，我们可以正常编译和运行，但是，在新的环境下， 必然会报错，错误信息如下：

![image](http://upload-images.jianshu.io/upload_images/2551993-450ddd6da8488bc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240 "屏幕截图.png")

在上图的错误信息中，我们以 DecibelBinding 为例，简述修复过程。
* 如上图，`无法将xxxBinding 构造器中的xxxBinding应用到指定类型`指明了出错的 Binding类 为 DecibelBinding

* 按照DataBinding类名的生成规则，我们可以知道，DecibelBinding 对应的xml文件名应该是 decibel.xml (如果你在xml中通过 class="xxxBinding" 指定了DataBinding的生成类名，那么就全局搜索吧)

* 在确定了xml之后，我们还需要知道到底哪里出了错误，那么，就继续看图中的 `错误：找不到符号 符号：变量 xxx`.这个变量就是控件的id名称。

* DataBinding转换控件id名的规则是：去除下划线连接符，然后将原下划线后面的第一个字母大写。所以，图中的 fragmentDiscoverGridItemRelativeLayout1 对应的控件id应该是：`@+id/fragment_discover_grid_item_relative_layout`,后面之所以有一个1 ，是因为重复了。然后，我们在对应的xml文件中搜索这个控件名，然后删除重复即可。

### 3.3 去除 attr.xml 中重复的属性名称
在迁移到 androidX 之前，我们为自定义控件编写自定义属性时，可以与android已有的属性重名，但是，在AndroidX环境下不行了，如果存在重名的情况， 必然会报错——会提示你重复定义（详细错误信息没截图，但翻译过来就是重复定义了attr/xxx）。

* 错误示例：

```
<declare-styleable name="RoundImageView">
    ...
    <!-在迁移到androidx之前，这样写虽然不规范，但是能用，不报错->
    <attr name="textSize" format="Integer" />
    ...
</declare-styleable>
```

* 正确示例

```
<declare-styleable name="RoundImageView">
    ...
    <!-迁移到androidX之后，必须使用android:xxx 属性，不能定义android已有的属性->
    <attr name="android:textSize" />
    ...    
</declare-styleable>
```
> 关于重名属性，在 androidX 中不知道哪个控件中包含了一个 **mode** 属性，所以，如果之前你的自定义控件中有 **attr/mode**，需要手动改成其他。

### 3.4 Glide中的注解不兼容androidX

迁移到 androidX 之后，Glide中使用的 `android.support.annotation.CheckResult` 和 `android.support.annotation.NonNull `这两个注解无法迁移。之前有用户在Glide中提过issue: https://github.com/bumptech/glide/issues/3185

在上述issue 中有用户表示，将Glide升级到 4.8.0 之后，可以正常迁移。但是，我这边并不行。然后，我先升级了Glide ,又在 gralde文件中增加了support.annotation ，这样才能正常编译通过。貌似在后续Glide 5.x 版本中会完成对 androidx的完全兼容。

我的临时解决方案：

```
 //图片加载——Glide
implementation "com.github.bumptech.glide:glide:4.8.0
annotationProcessor "com.github.bumptech.glide:compiler:4.8.0

//CnPeng 2018/9/26 下午8:38 这两行是为了解决 https://github.com/bumptech/glide/issues/3185 ——Glide 中的注解还没有完全兼容androidx
implementation "com.android.support:support-annotations:28.0.0-alpha3"
annotationProcessor "com.android.support:support-annotations:28.0.0-alpha3"

```

### 3.5 规范包名（即文件夹名）
这里所说的包名，指的是项目中的文件夹名称。在之前版本中，我们命名包名时可能会出现大写字母，虽然这并不符合Java命名规范，但起码能正常编译和运行。然而，升级到 AndroidStudio3.2 + androidX 环境后，必须严格遵守命名规范，否则，可能报错，从而导致不能正常编译和运行。

错误示例：

![](https://upload-images.jianshu.io/upload_images/2551993-d512bff20918a45c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

正确示例：
![](https://upload-images.jianshu.io/upload_images/2551993-55ce7b7df6310ab8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> 对于包名的命名规范，好像要求并非十分严格。因为我发现，部分包含大写字母的报名在编译时会报错，部分不报错。但是，遵守规范总会更好一点！

### 3.6 修改未自动迁移的三方库
181108补充：
虽然我们从gradle中配置了迁移三方库的参数，但是，由于三方库的版本更新问题，也可能会迁移失败。在三方库迁移失败时，如果使用了数据绑定，通常会报如下错误：

![](https://upload-images.jianshu.io/upload_images/2551993-0fc40387de9198ea.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

碰到上述错误之后，我们可以按下列步骤处理：

* 1、在gradle文件中，将可升级的三方库升级（通常情况下，可升级的三方库会有黄色提示）
* 2、如果gradle中可升级的库都升级之后依旧报上述错误，那么，可以新建一个项目，然后将gradle中的依赖库逐个拷贝到新项目中，没拷贝一个编译一次，这样可以确认是哪个三方库有问题。（实际操作时可以使用二分法的方式进行，每次拷贝一半的依赖库，然后编译）。然后就可以有针对性的处理了

### 3.7 莫名问题的解决

190325 补充：

迁移过程中如果爆出一些 android 包本身或者其他莫名其妙的问题时，先去 xml 布局文件 或 `.java` 文件中找一下，是否有继续引用 `xxx.support.xxx` 的情况，如果有，记得替换成 `androidx.xxx.xxx` 包下的对应控件。（ xxx 泛指任意内容）


