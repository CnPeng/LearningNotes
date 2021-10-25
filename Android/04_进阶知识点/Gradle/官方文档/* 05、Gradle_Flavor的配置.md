* 基本介绍参考:

https://developer.android.com/studio/build/build-variants


* 完整属性：

http://google.github.io/android-gradle-dsl/current/com.android.build.gradle.internal.dsl.ProductFlavor.html#com.android.build.gradle.internal.dsl.ProductFlavor:manifestPlaceholders

<hr>

## 1、基本使用

创建产品风味与创建构建类型类似：只需将它们添加到 productFlavors {} 代码块并配置您想要的设置。

产品风味支持与 defaultConfig 相同的属性，这是因为 **defaultConfig 实际上属于 ProductFlavor 类** 。这意味着，您可以在 defaultConfig {} 代码块中提供所有风味的基本配置，每种风味均可更改任何这些默认值，例如 applicationId。

注意：虽然我们可以再flavor中指定applicationId,但仍必须在清单文件中使用 package 属性指定程序包名称。这个包名用来生成 R 类或者解析任何相关的 Activity 或服务注册。这样，我们就可以使用 applicationId 为每个产品风味分配一个唯一的 ID，以用于打包和分发，而不必更改您的源代码。

以下代码示例创建了一个“演示”和“完整”产品风味，以赋予其自己的 applicationIdSuffix 和 versionNameSuffix：

```
android {
    ...
    defaultConfig {...}
    buildTypes {...}
    productFlavors {
        demo {
            applicationIdSuffix ".demo"
            versionNameSuffix "-demo"
        }
        full {
            applicationIdSuffix ".full"
            versionNameSuffix "-full"
        }
    }
}
```

当我们在应用市场中上架APP并维系更新时，需要配置相同的applicationID,然后更改versionCodej即可。

如果要基于一套源代码打出不同的安装包变体，并在应用市场中同时上架，就需要为每一个变体分配不同的 applicationId。

在创建和配置您的产品风味之后，在通知栏中点击 Sync Now。在同步完成后，Gradle 会根据您的构建类型和产品风味自动创建构建变体，并 **按照 <product-flavor><Build-Type> 的格式命名这些变体** 。

例如，如果您创建了“演示”和“完整”这两种产品风味并保留默认的“调试”和“发布”构建类型，Gradle 将创建以下构建变体：

- 演示调试
- 演示发布
- 完整调试
- 完整发布


您可以将构建变体更改为您要构建并运行的任何变体，只需转到 Build > Select Build Variant，然后从下拉菜单中选择一个变体。然而，要开始自定义每个构建变体及其功能和资源，您需要了解如何创建和管理源集。

## 2、组合多个产品风味

某些情况下，您可能希望组合多个产品风味中的配置。

例如，您可能希望基于 API 级别为“完整”和“演示”产品风味创建不同的配置。为此，您可以通过适用于 Gradle 的 Android 插件创建产品风味组，称为 **风味维度** 。

构建您的应用时 **，Gradle 会将您定义的每个风味维度中的产品风味配置与构建类型配置组合来创建最终构建变体。Gradle 不会组合属于相同风味维度的产品风味。** 

提示 **：要根据 ABI 和屏幕密度创建不同版本的应用时，您应配置 APK 拆分，而不是使用产品风味。** 

下面的代码示例使用  **flavorDimensions** 属性创建了“mode”和“api”两个维度。"mode"维度被应用到demo/full风味中，api 则应用到 minApixxx 风味中。

```
android {
  ...
  buildTypes {
    debug {...}
    release {...}
  }

  /**
   * 配置需要的falvor dimension. 
   * 定义维度时的顺序决定了它们的优先级，依次从高到低。Gradle构建工具合并变体资源和配置时会使用该优先级
   * 配置了维度之后，每一个风味必须指定一个维度
   */
  flavorDimensions "api", "mode"

  productFlavors {
    demo {
      // 指定该flavor的维度为"mode"
      dimension "mode"
      ...
    }

    full {
      dimension "mode"
      ...
    }

    /**
     * “api”维度对应的flavor 会重写“mode”维度对应flavor的相关配置，也会重写defaultConfig {}代码块中的配置。
     * 因为在定义维度时“api”的顺序先于“mode”,所以，“api”的优先级高于“mode”,所以，会冲洗
     */ 
    minApi24 {
      dimension "api"
      minSdkVersion '24'
      /**
       * 为了确保目标设备能够基于API版本更精确的匹配到合适的APP变体，在指定API版本时需要采用倒序的方式，也就是从高到低。
       * 更多APP更新的内容可参考：https://developer.android.com/google/play/publishing/multiple-apks#HowItWorks
       */  
      versionCode 30000 + android.defaultConfig.versionCode
      versionNameSuffix "-minApi24"
      ...
    }

    minApi23 {
      dimension "api"
      minSdkVersion '23'
      versionCode 20000  + android.defaultConfig.versionCode
      versionNameSuffix "-minApi23"
      ...
    }

    minApi21 {
      dimension "api"
      minSdkVersion '21'
      versionCode 10000  + android.defaultConfig.versionCode
      versionNameSuffix "-minApi21"
      ...
    }
  }
}
...
```

以上面的构建配置为例，Gradle 可以使用以下命名方案创建总共 12 个构建变体：

构建变体：[minApi24, minApi23, minApi21][Demo, Full][Debug, Release]

对应 APK：app-[minApi24, minApi23, minApi21]-[demo, full]-[debug, release].apk

例如，

构建变体：minApi24DemoDebug

对应 APK：app-minApi24-demo-debug.apk

<br>

注意：
* **定义维度时的顺序决定了它们的优先级，依次从高到低。Gradle构建工具合并变体资源和配置时会使用该优先级** 
* **如果使用的是3.0.0以上的Gradle构建工具，那么，每一个flavor必须归属于一个 dimension.** 
* Gradle 创建的构建变体数量等于每个风味维度中的风味数量与您配置的构建类型数量的乘积。

* **在 Gradle 为每个构建变体或对应 APK 命名时，属于较高优先级风味维度的产品风味首先显示，之后是较低优先级维度的产品风味，再之后是构建类型。** 

<br>
除了可以为各个产品风味和构建变体创建源集目录外，您 **也可以为每个产品风味组合创建源集目录** 。

例如，您可以创建 Java 源并将其添加到 src/demoMinApi24/java/ 目录中，Gradle 仅会在构建组合了这两种产品风味的变体时使用这些源。

与属于各个产品风味的源集相比，您为 **产品风味组合创建的源集拥有更高的优先级** 。要详细了解源集和 Gradle 如何合并源，请阅读有关如何创建源集的部分。


## 3、过滤变体

Gradle 会基于产品风味和构建类型的任意组合创建出不同的构建变体。

如果某些变体并不是我们想要的，那么，就可以在模块级 build.gradle 文件中创建一个 **变体过滤器，以移除某些构建变体配置** 。

以上一部分中的构建配置为例，假设您计划为演示版本的应用仅支持 API 级别 23 和更高级别。您可以使用 variantFilter {} 代码块过滤出组合了“minApi21”和“演示”产品风味的所有构建变体配置：

```
android {
  ...
  buildTypes {...}

  flavorDimensions "api", "mode"
  productFlavors {
    demo {...}
    full {...}
    minApi24 {...}
    minApi23 {...}
    minApi21 {...}
  }

  variantFilter { variant ->
      def names = variant.flavors*.name
      //如果要检查 build type 的名称,使用 variant.buildType.name == "<buildType>"
      if (names.contains("minApi21") && names.contains("demo")) {
          // Gradle会忽略掉任意满足上述条件的变体
          setIgnore(true)
      }
  }
}
...
```

在您向构建配置添加变体过滤器并点击通知栏中的 Sync Now 后，Gradle 将忽略满足您指定的条件的任何构建变体，在您点击菜单栏中的 Build > Select Build Variant（或工具窗口栏中的 Build Variants）时，这些构建变体将不会再  显示在下拉菜单中。


## 4、属性介绍

 **A: applicationId** 	

取值类型：String

应用id

<br>

 **B: applicationIdSuffix** 

取值类型：String
	
应用id的后缀。

在生成变体时会追加到基准应用id（defaultConfig 中配置的应用id）的后面。

In case there are product flavor dimensions specified, the final application id suffix will contain the suffix from the default product flavor, followed by the suffix from product flavor of the first dimension, second dimension and so on. All of these will have a dot in between e.g. "defaultSuffix.dimension1Suffix.dimensions2Suffix".

<br>

 **C: consumerProguardFiles** 

取值类型：List<File>

生成AAR时所包含的 混淆规则文件。`ProGuard rule files to be included in the published AAR.`

如果某个application项目引用了AAR，并且该application项目中启用了混淆，那么这些混淆规则文件将会对AAR生效。

这些混淆规则文件指定了AAR中的压缩和混淆规则。

这些文件中的规则文件，仅对 Library 库生效，对application本身无效。

<br>

 **D: dimension** 

取值类型：String

风味维度。

如果在Android plugin 3.0.0 及以上版本中使用了 product flavor, 那么至少需要设定一个 dimension(维度)。

使用 flavorDimensions 属性声明维度, 然后为每一个 flavor 指定一个维度。否则, 会报出下列错误：

```
Error:All flavors must now belong to a named flavor dimension.
The flavor 'flavor_name' is not assigned to a flavor dimension.
```

* **默认情况下，如果只声明了一个 dimension, 那么所有的flavor都会被默认的指向该维度。**

* **如果指定了多个维度，那么就必须手动的为 flavor 指定维度。** 

示例代码和其他相关知识可参考上面的 `2、组合多个产品风味`

<br>

 **E: externalNativeBuild** 	

> 这一个没看懂啥意思

取值类型：ExternalNativeBuildOptions

Encapsulates per-variant CMake and ndk-build configurations for your external native build.

To learn more, see [Add C and C++ Code to Your Project](https://developer.android.com/studio/projects/add-native-code#).

<br>

 **F: javaCompileOptions** 	

取值类型：JavaCompileOptions

Java 编译配置选项。

<br>

 **G: manifestPlaceholders** 	

取值类型：Map<String, Object>

将要在清单文件中引用的占位符。

详细可参考：

https://gitee.com/CnPeng_1/LearningNotes/wikis/14%E3%80%81Gradle_%E8%AE%BE%E7%BD%AE%E5%BA%94%E7%94%A8ID?sort_id=1161786

https://developer.android.com/studio/build/manifest-build-variables

<br>

 **H: matchingFallbacks** 

取值类型：List<String>
	
Specifies a sorted list of product flavors that the plugin should try to use when a direct variant match with a local module dependency is not possible.


Specifies a sorted list of product flavors that the plugin should try to use when a direct variant match with a local module dependency is not possible.

Android plugin 3.0.0 and higher try to match each variant of your module with the same one from its dependencies. For example, when you build a "freeDebug" version of your app, the plugin tries to match it with "freeDebug" versions of the local library modules the app depends on.

However, there may be situations in which, for a given flavor dimension that exists in both the app and its library dependencies, your app includes flavors that a dependency does not. For example, consider if both your app and its library dependencies include a "tier" flavor dimension. However, the "tier" dimension in the app includes "free" and "paid" flavors, but one of its dependencies includes only "demo" and "paid" flavors for the same dimension. When the plugin tries to build the "free" version of your app, it won't know which version of the dependency to use, and you'll see an error message similar to the following:

Error:Failed to resolve: Could not resolve project :mylibrary.
Required by:
    project :app
In this situation, you should use matchingFallbacks to specify alternative matches for the app's "free" product flavor, as shown below:

// In the app's build.gradle file.
android {
    flavorDimensions 'tier'
    productFlavors {
        paid {
            dimension 'tier'
            // Because the dependency already includes a "paid" flavor in its
            // "tier" dimension, you don't need to provide a list of fallbacks
            // for the "paid" flavor.
        }
        free {
            dimension 'tier'
            // Specifies a sorted list of fallback flavors that the plugin
            // should try to use when a dependency's matching dimension does
            // not include a "free" flavor. You may specify as many
            // fallbacks as you like, and the plugin selects the first flavor
            // that's available in the dependency's "tier" dimension.
            matchingFallbacks = ['demo', 'trial']
        }
    }
}
Note that, for a given flavor dimension that exists in both the app and its library dependencies, there is no issue when a library includes a product flavor that your app does not. That's because the plugin simply never requests that flavor from the dependency.

If instead you are trying to resolve an issue in which a library dependency includes a flavor dimension that your app does not, use missingDimensionStrategy.

<br>

 **I: multiDexEnabled** 
Whether Multi-Dex is enabled for this variant.

multiDexKeepFile	
Text file that specifies additional classes that will be compiled into the main dex file.

multiDexKeepProguard	
Text file with additional ProGuard rules to be used to determine which classes are compiled into the main dex file.

ndk	
Encapsulates per-variant configurations for the NDK, such as ABI filters.

proguardFiles	
Specifies the ProGuard configuration files that the plugin should use.

signingConfig	
Signing config used by this product flavor.

testApplicationId	
Test application ID.

testFunctionalTest	
See instrumentation.

testHandleProfiling	
See instrumentation.

testInstrumentationRunner	
Test instrumentation runner class name.

testInstrumentationRunnerArguments	
Test instrumentation runner custom arguments.

vectorDrawables	
Options to configure the build-time support for vector drawables.

versionCode	
Version code.

versionName	
Version name.

versionNameSuffix	
Version name suffix. It is appended to the "base" version name when calculating the final version name for a variant.

wearAppUnbundled	
Returns whether to enable unbundling mode for embedded wear app. If true, this enables the app to transition from an embedded wear app to one distributed by the play store directly.


