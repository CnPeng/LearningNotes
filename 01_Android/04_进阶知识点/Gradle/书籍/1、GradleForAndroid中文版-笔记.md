## 一、Gradle 和 AndroidStudio 入门

### 1、理解 Gradle 基础

####（1）、Gradle 基础

* 使用 Gradle 构建 Android 项目时，必然要有一个构建脚本，即 `build.gradle`。
* Gradle 有 **约定优于配置的原则**，即 为设置和属性提供默认值。
* Gradle 构建脚本的书写是基于 Groovy 的领域专用语言（DSL）。
* Groovy 是一种基于 Java 虚拟机的动态语言。所以，我们可以用 Java 或其他基于 Java 虚拟机的语言来编写自定义插件。
* Gradle 中，项目和任务是两个最重要的概念。每次构建都至少有一个项目，每一个项目包含一个或多个任务。
* 每个 build.gradle 都代表一个项目。

#### (2)、构建生命周期

* 执行一个 Gradle 构建的最简单的形式是，只执行任务中的动作，而这些任务又依赖于其他任务。
* 为简化构建过程，构建工具会生成一个动态模型流——Directed Acyclie Graph (DAG)。也就是说，任务会顺序执行。
* 构建过程中通常会优先构建没有依赖的任务。
* 在构建的配置阶段会生成依赖关系图。

一个 Gradle 的偶见通常由如下三个阶段：

* 初始化，该阶段会创建项目实例。如果项目中有多个模块，且每个模块都有对应的 build.gradle , 就会创建多个项目实例。
* 配置，该阶段执行构建脚本，并为每个项目创建和配置任务。
* 执行，该阶段 Gradle 会决定哪个任务被执行。（哪些任务被执行取决于开始该次构建的参数配置和该 Gradle 文件的当前目录。）

### 2、Gradle Wrapper 入门

#### （1）、获取 Gradle Wrapper

在 MAC  版 AndroidStudio 的终端执行 `./gradlew -v` 可以检查项目中的 Gradle Wrapper 是否可用，并显示 Gradle 版本号及其他信息。（如果 Gradle 不是新版，该命令还会执行下载逻辑）。

> Windows 上应该是执行：`gradlew.bat`

`gradle-wrapper.properties` 文件中包含了参数配置，能决定使用哪一个版本的 Gradle.

#### (2)、运行基本构建任务

> 后续如无特殊说明，相关命令都是在 MAC 版 AndroidStudio 中的 terminal 执行。

命令|含义|备注
---|---|---
`gradlew tasks` |查看所有可用的任务列表| MAC 和 Linux 下，gradlew 完整写法为  `./gradlew` ; Windows 下需要先执行 `gradlew.bat` 才可以
`gradlew tasks --all` | 查看所有任务列表及其依赖|
`gradlew assembleDebug` | 创建 Debug 版的 apk | 默认保存在 `项目名/build/outputs/apk/debug` 目录下
`gradlew check` | 运行所有的检查|
`gradlew build`  | 触发 assemble 和 check | 
`gradlew clean` | 清除项目的输出|

## 二、基本自定义构建

### (1)、理解 Gradle

###（2）、任务入门

### (3)、自定义构建


## 三、依赖管理
