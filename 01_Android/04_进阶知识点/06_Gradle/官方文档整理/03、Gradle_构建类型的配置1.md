* 基本概述参考：

https://developer.android.com/studio/build/build-variants#build-types

* 完整属性参考：

http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.BuildType.html

---
<br>

## 1、基本介绍：

构建类型是基于开发阶段进行设置的，如：开发、测试、生产（发布）。

新建项目时，AndroidStudio默认会为我们配置 debug、release 两种类型。
* debug 类型默认不可见，其中启用了 debuggable ,并且默认生成和使用一个debug模式的签名文件。
* release 类型默认启用资源压缩——`minifyEnabled true`,并配置混淆规则。

我们可以根据实际需求对该类型进行新增或修改。

示例：

```kotlin
android {
    ...
    defaultConfig {...}
    buildTypes {
        release {
            minifyEnabled true
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }

        debug {
            //修改debug类型的应用id。
            applicationIdSuffix ".debug"
        }

        /**
         * 'initWith' 属性允许我们拷贝另一个构建类型的参数配置, 这样我们就不必重新配置一些值相同的参数/属性。
         * 下列示例中，在初始化时拷贝了 debug 中的参数配置。但又通过applicationIdSuffix改变了其中的 应用id；通过 jniDebuggable 改变了jni的可调式状态
         */
        jnidebug {

            //此处拷贝了debug构建类型中的参数和签名配置
            initWith debug

            applicationIdSuffix ".jnidebug"
            jniDebuggable true
        }
    }
}
```

## 2、属性介绍

**A: applicationIdSuffix** 

应用id后缀。 

取值类型为：字符串

在构建变体时，会追加到 applicationID 后面。后缀可以有多个，多个后缀之间使用 “.” 隔开。

<br>

**B: consumerProguardFiles** 

在生成AAR文件时使用的混淆文件。

取值类型为：List<File>

这些混淆文件中的混淆规则会被引用该AAR的应用所使用——前提是该应用启用了代码混淆（`These proguard rule files will then be used by any application project that consumes the AAR (if ProGuard is enabled).`）

这些混淆文件可以指明AAR的压缩和混淆规则。 **这些文件只对要Library Project(依赖库)有效，对 Application project无效** 

<br>

 **C: crunchPngs** 

是否压缩PNG图片文件。

取值类型为：Boolean

如果为true表示启用PNG图片资源压缩，但是会增加构建时间。

在构建 release 类型的APP时，默认开启；而debug类型 默认关闭。

<br>

 **D: debuggable** 

生成的Apk是否允许调试。

取值类：Boolean

<br>

 **E: embedMicroApp**

在构建该类型的变体时，是否嵌入关联的 Android Wear app 。

取值类型：boolean

关联 Wear APP时可以参考如下代码：
 
```
dependencies {
  freeWearApp project(:wear:free') // applies to variant using the free flavor
  wearApp project(':wear:base') // applies to all other variants
}
```

<br>

 **F: jniDebuggable**

取值类型：boolean

该构建类型生成的APK是否可以对 native code 进行调试

<br>

 **H: manifestPlaceholders** 

在清单文件中被引用的占位资源的集合。

取值类型：Map<String, Object>

详细说明可以参考：https://gitee.com/CnPeng_1/LearningNotes/wikis/15%E3%80%81Gradle_%E6%B8%85%E5%8D%95%E6%96%87%E4%BB%B6%E4%B8%AD%E5%BC%95%E7%94%A8%E6%9E%84%E5%BB%BA%E5%8F%98%E9%87%8F?sort_id=1162498

<br>

 **I: matchingFallbacks** 

当我们的项目依赖了一个本地Module，在构建当前类型的变体时，如果被依赖module中没有这个类型，那么就可以通过该字段指定一个备选类型的集合。

取值类型：List<String> 

Android plugin 3.0.0 和更高的版本中，会从dependencies中匹配每一个你的module中所包含的变。

比如：当我们构建一个 "freeDebug" 版本的Apk时, 构建插件也会匹配被依赖 modules的 “freeeDebug” 版本。

但是，被依赖的module中不可能包含我们当前APP的所有构建类型。

假设我们的APP中包含了一个 "stage"的构建类型, 但是被依赖的module中仅包含 "debug" 和 "release"。当我们要构建 "stage"类型的APP时, 构建工具从被依赖项中找不到对应的类型，此时，就会报错，错误信息如下：

```
Error:Failed to resolve: Could not resolve project :mylibrary.
Required by:
    project :app
```

在这种情况下，我们就可以使用“matchingFallbacks”指定被依赖module中可以替代“stage”的构建类型,示例代码如下：

```
// In the app's build.gradle file.
android {
    buildTypes {
        release {
            // 由于被依赖项中通常情况下都会包含 debug 和 release 类型，所以此处不需要配置。
        }
        stage {
            
            /**
             * 当被依赖项目中不存在 stage 构建类型时，指定一个有序的可以替代的构建类型。
             * 可以定义多个，但是匹配到第一个可用的之后，就不在继续匹配
             */
            matchingFallbacks = ['debug', 'qa', 'release']
        }
    }
}
```

 **注意** ：

如果被依赖项中包含了我们APP中没有的构建类型，这样并不会报错。因为，构建工具永远引用不到这些构建类型。


<br>

 **G: minifyEnabled**

是否清理无用JAVA代码。

取值类型：Boolean

默认false——不清理。

<br>

 **H: multiDexEnabled**  

是否启用Multi-Dex

取值类型：boolean

<br>

 **I: multiDexKeepFile** 

取值类型：File

该file指定一些需要编译到 main dex 的额外的 classes。

这些被指定的 Classes 会在AAPT编译时被追加到 main dex 中.

在file中设置classes时， **一行只能包含一个classes** 

格式入下:
```
com/example/MyClass.class
```

AAPT可参考：https://www.jianshu.com/p/8d691b6bf8b4

<br>

 **J: multiDexKeepProguard**

取值类型：File

文件中配置额外的 ProGuard 规则，这些规则会决定哪些 classes 会被编译到 main dex.

该文件中的这些规则会与构建系统中的默认规则合并使用。

<br>

 **K: name** 

取值类型：字符串

当前构建类型的名称。

<br>

 **L: proguardFiles**

取值类型：List<File>

指定构建时所使用的包含混淆规则的文件。 

构建时默认会使用如下两个混淆规则文件：
* proguard-android.txt
* proguard-android-optimize.txt

除了 optimizations（优化） 是否被启用之外，`proguard-android-optimize.txt` 和 `proguard-android.txt` 是完全一样的

我们可以使用 `getDefaultProguardFile(String filename)`获取该文件的完整路径。

<br>

 **M: pseudoLocalesEnabled** 

取值类型：boolean

构建时是否启用伪定位并生成相应的资源。
>pseudo  ['sjuːdəʊ] 冒充的，假的 ; local 位置，定位

在做APP本地化时，pseudolocale 可以用来模拟因为语言变化导致的UI、布局、和其他本地化问题。方便我们在开发阶段都本地化进行测试。

Pseudolocales有助于提升开发效率，能够帮助我们在做最终的本地化操作之前作出相应的测试和UI调整。 

当我们向下面的示例代码一样设置了该属性值为true，构建工具就会生成相关资源。这些资源可以模拟位置，从而可以让我们使用Android设备中的语言布局模式属性： en-XA and ar-XB.

```
android {
    buildTypes {
        debug {
            pseudoLocalesEnabled true
        }
    }
}
```

当我们构建APP时，构建工具会将 pseudolocale资源打包到APK中. 

如果APK中不包含这些资源，就需要确认构建配置中是否限制了哪些 locale 资源不被打包，比如：使用了resConfigs属性移除未使用的locale资源。

* 补充：
>en（XA）：在基础英语UI文本中添加拉丁语重音，通过添加非重音文本扩展原始文本，并括起每个消息单元以显示扩展文本中的潜在问题。潜在的问题可能是布局破坏和格式错误的消息语法，如一个句子分成多个部分所示，显示为多个括号内的消息。英语（XA）pseudolocale如图1所示。
>
>AR（XB）：将原始从左到右消息的文本方向设置为从右到左的方向，这反转了原始消息中字符的顺序。AR（XB）pseudolocale如图2所示。
>
><p>即使您不编写或使用任何RTL语言，Pseudolocales也可以帮助您制作应用程序的RTL版本
![图1](https://images.gitee.com/uploads/images/2018/1220/104653_a53c0659_930142.png "屏幕截图.png")
![图2](https://images.gitee.com/uploads/images/2018/1220/104703_ec664267_930142.png "屏幕截图.png")


pseudolocales的详细使用可参考：
https://www.daimapi.com/android/guide/topics/resources/pseudolocales/

移除未使用的相关资源可以参考:
https://developer.android.com/studio/build/shrink-code#unused-alt-resources

<br>

 **N: renderscriptDebuggable** 

取值类型：boolean

当前构建类型生成的APK是否可以对 渲染脚本（RenderSrcipt）进行调试。

<br>

 **O:renderscriptOptimLevel** 

取值类型：int

编译渲染脚本（renderscript）时使用的代码优化级别。

>Optimization  [,ɒptɪmaɪ'zeɪʃən] 最佳化，优化

<br>

 **P: shrinkResources** 

取值类型：boolean

是否压缩未使用的资源。默认false

<br>

 **Q: signingConfig** 

取值类型：SigningConfig

APP签名配置

<br>

 **R: testCoverageEnabled** 

取值类型：boolean
> coverage  英  ['kʌv(ə)rɪdʒ]   n. 覆盖，覆盖范围；新闻报道

当前构建类型是否启用：test coverage（单元测试覆盖率的分析）

启用之后，会使用 Jacoco 对单元测试覆盖率进行分析，并在build目录下生成一个报告文件。

可通过如下方式配置 Jacoco 的版本：
```
android {
  jacoco {
    version = '0.6.2.201302030002'
  }
}
```
>JaCoCo（Java Code Coverage）就是一种分析单元测试覆盖率的工具，使用它运行单元测试后，可以给出代码中哪些部分被单元测试测到，哪些部分没有没测到，并且给出整个项目的单元测试覆盖情况百分比.详细可参考：https://blog.csdn.net/carolzhang8406/article/details/62891235

<br>

 **S: useProguard** 

取值类型：Boolean

是否启用代码混淆和 资源压缩 。

>原文：Specifies whether to always use ProGuard for code and resource shrinking.

默认情况下，如果已经通过 minifyEnabled 启用了代码压缩，构建工具也会自动使用混淆。

如果在部署APK到设备时使用了 Instant Run, 由于 Instant Run 不支持混淆,构建工具会自动使用自定义的实验性的代码压缩规则。（ the plugin switches to using a custom experimental code shrinker.）

如果在使用 experimental 的代码压缩规则时碰到了问题, 可以将该属性设置为true，这样，在使用 Instant Run 时就会禁用代码压缩。

>关于代码和资源压缩可参考：https://developer.android.com/studio/build/shrink-code

<br>

 **T: versionNameSuffix** 

取值类型：String

版本名称后缀。

在生成变体时，会自动追加到基准版本名称后面。基准版本名称就是定义在 defaultConfig{}中的versionName

如果指定了 构建风味维度（product flavor dimensions), 最终的版本名称会包含默认风味的后缀, 后面还会依次追加第一风味维度的后缀、第二风味维度的后缀等等。

<br>

 **U：zipAlignEnabled**
 
取值类型：boolean

是否启用 zipalign(对齐压缩)，从而更高效的访问所需的资源

>关于zipalign可参考：https://blog.csdn.net/ljchlx/article/details/52473297


## 3、方法介绍

 **A: buildConfigField()** 
>void buildConfigField(String type, String name, String value)

在生成的 BuildConfig 类中添加新的字段

生成的字段格式为：` <type> <name> = <value>;`

也就是说每一个字段必须是合法有效的 Java 类型. 比如:如果 type 是 String,那么 value 必须用引号括起来。

<br>

 **B: consumerProguardFile()** 
>BuildType consumerProguardFile(Object proguardFile)

添加混淆规则文件到将要生成的AAR文件中

这个混淆规则文件将会被所有引用该AAR的应用项目所使用。——前提是对应的应用项目启用了混淆

这个混淆规则文件通常指定了压缩和混淆规则（包括哪些应用混淆，哪些不应用等）。

仅对Library库有效，对Application项目无效

<br>

 **C: consumerProguardFiles()** 
>BuildType consumerProguardFiles(Object... proguardFiles)

添加多个混淆规则文件到将要生成的AAR文件中。接收的是一个可变数组。

其他说明同 consumerProguardFile


 **D: externalNativeBuild()** 
>ExternalNativeBuildOptions externalNativeBuild(Action<ExternalNativeBuildOptions> action)

配置 native 构建参数。

<br>

 **E: initWith()** 

>DefaultBuildType initWith(BuildType that)

拷贝另一个 build Type 中的全部属性。

示例如下：
```
android.buildTypes {
    customBuildType {
        initWith debug
            // customize...
        }
}
```

<br>

 **F: proguardFile()** 

>BuildType proguardFile(Object proguardFile)

添加新的混淆规则文件`Adds a new ProGuard configuration file.`

如： `proguardFile getDefaultProguardFile('proguard-android.txt')`可以获取默认的混淆规则文件的完整路径。

默认的混淆规则文件有如下两个：

* proguard-android.txt
* proguard-android-optimize.txt

它们被放置在 SDK 中，使用 `getDefaultProguardFile(String filename)`可以获取该文件的完整路径。

除了是否启用 optimizations之外，这两个文件完全一样。

<br>

 **G: proguardFiles** 

>BuildType proguardFiles(Object... files)

添加多个混淆文件。

<br>

 **H: resValue()** 

>void resValue(String type, String name, String value)

添加一个新的资源文件。

等价于在 res/values 目录下新增资源文件。

可添加的资源可以参考：https://developer.android.com/guide/topics/resources/available-resources

>equivalent 等价于，于...一样

<br>

 **I: setProguardFiles()** 
>BuildType setProguardFiles(Iterable<?> proguardFileIterable)

配置混淆规则文件。

其他说明参考：proguardFile()


## 4、示例

```
android {
   buildTypes {
        debug {
            debuggable true
            // 显示Log
            buildConfigField "boolean", "LOG_DEBUG", "true"
            //log不写入到sd卡
            buildConfigField "boolean", "LOG_WRITE", "true"
            //bugly不上传
            buildConfigField "boolean", "LOG_BUGLY", "false"
            buildConfigField "int", "LOG_LEVEL", "0"

            minifyEnabled false
            zipAlignEnabled false
            shrinkResources false

            applicationIdSuffix ".debug"
            signingConfig signingConfigs.debug//设置签名信息
            buildConfigField "int", "CONFIG_URL", "R.string.CommonConfigDevUrl"
        }

        antTest {
            initWith(debug)

            applicationIdSuffix ".test"
            buildConfigField "int", "CONFIG_URL", "R.string.CommonConfigTestUrl"
            signingConfig signingConfigs.antTest//设置签名信息
        }

        // 调试生产环境版本时使用
        debugRelease {

            initWith(debug)

            applicationIdSuffix ".debugRelease"
            signingConfig signingConfigs.release
            buildConfigField "int", "CONFIG_URL", "R.string.CommonConfigAppUrl"

            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }

        release {
            debuggable false

            // 不显示Log
            buildConfigField "boolean", "LOG_DEBUG", "false"
            //log不写入到sd卡
            buildConfigField "boolean", "LOG_WRITE", "false"
            //bugly不上传
            buildConfigField "boolean", "LOG_BUGLY", "true"
            buildConfigField "int", "CONFIG_URL", "R.string.CommonConfigAppUrl"
            buildConfigField "int", "LOG_LEVEL", "6"

            //移除无用的resource文件（如果使用getIdentifier函数的需要关闭）
            shrinkResources false
            minifyEnabled false
            //Zipalign优化
            zipAlignEnabled true

            applicationIdSuffix ".release"
            signingConfig signingConfigs.release
            proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
        }
    }
}
```



