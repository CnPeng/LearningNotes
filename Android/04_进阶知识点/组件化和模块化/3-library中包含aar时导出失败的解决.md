当我们想把某个功能[抽取成单独的 `module` 库](https://developer.android.google.cn/studio/projects/android-library?hl=zh-cn)实现复用时，有三种方式：

* 直接在项目中导入该 `module` 库，然后添加 `module` 依赖。
* [发布到网络](2、将aar发布到网络.md)，然后以 gradle 或 maven 的形式依赖。
* 将该 `module` 库导出为 `aar` 文件，其他项目中引入该 `aar` 文件即可。

前两种方式会暴露源码，可以给自己公司内部项目使用；第三种方式可以混淆源码，可以暴露给任意项目使用。

本文主要讨论的是 `module` 库中依赖了其他 `aar` 文件，然后我们将该 `module` 库再导出为 `aar` 时报错的问题。

## 1. 3.1 问题现象

我们创建了一个 `library` , 在其中引用了 `aar` 文件。我们最终想把该 `library` 导出为一个 `aar` 文件供其他项目使用。

该 `library` 的结构如下：

![](pics/3-1-library中添加aar依赖.png)

> * 虽然上图在 `libs` 目录下也引用了 `jar` , 但我们在生成该 `library` 对应的 `aar` 文件时，`jar` 包可以正常打入到 `aar` 包中。
> * gradle 中使用 `api` 依赖是为了实现依赖穿透。假如我们不导出 `aar` 文件，而是让当前项目中的 `app` 直接以 `module` 的方式依赖 `pushlib` ， 那么 `pushlib` 可以使用依赖包中的内容，`app` 也可以使用依赖包中的内容。

生成该 `library` 对应 `aar` 文件的方式如下：

![](pics/3-2-生成aar.png)

执行上图中的 `assmbleRelease` 时会出现如下错误：

![](pics/3-3-aar中不能包含aar.png)

## 2. 3.2 问题原因

前面一张图中具体的错误信息为：

>Execution failed for task ':pushlib:bundleReleaseAar'.
> 
> Direct local .aar file dependencies are not supported when building an AAR. The resulting AAR would be broken because the classes and Android resources from any local .aar file dependencies would not be packaged in the resulting AAR. Previous versions of the Android Gradle Plugin produce broken AARs in this case too (despite not throwing this error). The following direct local .aar file dependencies of the :pushlib project caused this error: /Users/cnpeng/CnPeng/04_Demos/07_AndroidLib/JgtPush/pushlib/libs/com.heytap.msp.aar, /Users/cnpeng/CnPeng/04_Demos/07_AndroidLib/JgtPush/pushlib/libs/vivo_pushsdk_v3.0.0.0_480.aar

重点是其中的 `Direct local .aar file dependencies are not supported when building an AAR.` ，意思是，直接依赖的 `aar` 文件不能再打入 `aar` 文件中。

## 3. 3.3 解决方案

### 3.1. 旧版本AS中的方案

解决方式是以 `module` 的形式依赖 `aar` 文件，具体步骤如下：

![](pics/3-4-创建module.png)

![](pics/3-5-导入aar或jar.png)

![](pics/3-6-导入aar2.png)

然后我们就看到下图左侧中刚刚以 `module` 形式导入的 `aar`。

![](pics/3-7-打开projectStruct.png)

点击上图右上角的 `project struct` 图标之后，让我们自定义的 `library` 以 `module` 的形式依赖刚导入的 `aar`:

![](pics/3-8-添加module依赖.png)

选择需要依赖的 `module`:

![](pics/3-9-选择要依赖的module.png)

添加依赖成功之后，点击下图中的 `apply` 和 `ok`，让依赖最终生效：

![](pics/3-10-应用依赖.png)

最终的项目结构如下：

![](pics/3-11-重新依赖aar后的结构.png)

**如果有其他 `aar` 依赖项，重复上述操作，全部以 `module` 的形式导入并添加依赖。**

此时，我们再执行生成 `aar` 的 `gradle` 命令时就不再报错，如下图：

![](pics/3-12-生成aar成功.png)

### 3.2. Android Studio Flamingo | 2022.2.1 Patch 2 

> 2023-07-04 基于 Android Studio Flamingo | 2022.2.1 Patch 2 版本。

#### 3.2.1. 新建 module

在 Android Studio Flamingo | 2022.2.1 Patch 2 中，新建 Module 时已经没有上一小节中的方式了，所以，我们需要按照如下方式操作：

在菜单栏中一次选择 `File`-`New`-`New Module`：

![](_v_images/20230704204514712_399340591.png)

#### 3.2.2. 删除module中的全部内容

等待 module 创建完成之后，删除该 module 目录下的全部内容，仅保留 module 目录名。


#### 3.2.3. 拷贝aar并编辑gradle文件

然后将 aar 文件拷贝到 module 目录下，并创建一个新的 `build.gradle` 文件。

将如下内容编辑到新建的 `build.gradle` 文件中，如下：

```groovy
configurations.maybeCreate("default")
artifacts.add("default", file('依赖包的名称.aar'))
```

以 小米推送的 aar 为例，编辑完成之后的情况如下：

![](_v_images/20230704205400622_830916062.png)



## 4. 3.4 在项目中引用导出的 aar

将生成的 `aar` 文件导入到我们的项目中，并在 `gradle` 中添加依赖：

![](pics/3-13-项目中引用aar并依赖.png)

但是，我们此时会发现，**我们在项目中无法引用生成的 aar 包中所依赖的其他三方 aar !!!**，如下图：

![](pics/3-14-无法引用嵌套aar内容.png)

这是因为，我们在 `library` 中引用的其他 `aar` 资源内容并没有被加入到我们生成的 `aar` 中，那么如何解决该问题呢？请参考 [《4-fat-aar-android的使用.md》](4-fat-aar-android的使用.md)


 