《精通IOS开发_第8版》 笔记

[本书英文版网站](https://www.apress.com)

[本书源码地址](https://github.com/apress/beg-iphone-dev-w-swift-3)

## 一、欢迎来到IOS和Swift世界

IOS的文件系统为每个应用分配一块独立的区域，称为沙盒(sand box).该区域用来存储当前应用的文档、偏好设置等内容。每个应用只能读写自己沙盒内的内容。


---

## 二、创建第一个APP

### 1、创建 HelloWorld 项目

#### (1)、创建 HelloWorld 项目

![](https://images.gitee.com/uploads/images/2019/0213/113152_e21611ca_930142.png)


![](https://images.gitee.com/uploads/images/2019/0213/113152_bed5f5c0_930142.png)

![k3SaVJ.png](https://images.gitee.com/uploads/images/2019/0213/113152_0306ca88_930142.png)

然后在选择该项目在本地的存储路径。


#### (2)、Xcode项目窗口
##### A、工具栏

![](https://images.gitee.com/uploads/images/2019/0213/113152_f591aec6_930142.png)

上图为工具栏。其中的 1、2、3 用来控制项目编辑器的样式和种类，含义分别为:

* Standard Editor 标准编辑器。编辑文件或者项目相关的配置
* Assistant Editor 辅助编辑器。功能强大，一句两句说不清，后面会多次用到。
* Version Editor 版本编辑器。与 Git 协同展示代码版本信息


##### B、导航视图

![](https://images.gitee.com/uploads/images/2019/0213/113152_48644880_930142.png)

上图中红色圈出来的部分为导航视图，含义分别为:

编号|英文名称|中文名称|含义和特点
---|---|---|---
1 | Project Navigator| 项目导航面板 | 展示项目的所有文件
2 | Source control Navigator | 源码控制导航面板 |
3 | Symbol Navigator | 符号导航面板 | 展示工作区中定义的所有符号。符号就是编译器能识别的内容。类、枚举、方法、属性等都属于符号
4 | Find Navigator | 查找导航面板 | 可以对工作去的所谓文件执行搜索。
5 | Issues Navigator | 问题导航面板 | 展示项目构建过程中出现的错误和警告
6 | Test Navigator | 测试导航面板 | 使用 Xcode 内置的单元测试时，此处展示测试结果
7 | Debug Navigator | 调试导航面板 | 调试代码时使用
8 | BreakPoint Navigator | 断点导航面板 | 展示已经设置的断点
9 | Report Navigator | 日志导航面板 | 显示构建结果的日志以及历史记录

  
##### C、跳转栏

![](https://images.gitee.com/uploads/images/2019/0213/113152_f8987bc6_930142.png)

可以快速跳转到某个文件

上图最左侧的那个图标被点击之后会弹出一个菜单，其中包含最近编辑的文件等内容。该菜单大致包括的内容如下：

* Recent Files : 最近编辑的文件
* Unsaved Files : 未保存的文件
* Countersparts : 关联文件
* Superclasses : 父类
* Subclasses : 子类
* Siblings : 兄弟类
* Categories : 类别
* Includes : 引用的头文件

另外，图标右侧的被 `>` 链接的路径也可以点击，点击之后如果是文件夹也会弹出菜单，菜单中展示文件夹下的其他文件。

##### D、默认快捷键

快捷键|含义
---|---
Cmd + B | 构建应用
Cmd + N | 新建文件
Shift + Cmd + O | 搜索类名

### 2、Interface Builder简介

Interface Builder 其实就是故事板界面。

![](https://images.gitee.com/uploads/images/2019/0213/113154_9ae35d47_930142.png)

#### (1)、文件格式

Interface Builder 支持的文件类型有：

* .nib , 是一种二进制格式
* .xib ，是这一种基于 xml 的衍生格式

由于 .nib 出现的时间比较久，所以，不论是 nib格式 还是 xib格式，习惯上统称 Interface Builder 的文件为 nib 文件。

一个 nib 文件可以包含任意数量的对象，但在 IOS 项目中，一个 nib 文件通常只包含一个视图（一般都是全屏视图）以及相关联的控制器和对象。这可以对应用进行划分，只在某个视图需要显示的时候才去加载它的 nib 文件 。这样有利于节约应用运行时的内存。

目前，Interface Builder 支持一种新的文件类型——storyborad。

可以把 storyboard 想象成一个 元nib文件（meta_nib file），它可以包含多个视图和控制器，以及如何在应用运行时的进行相互连接的配置信息。

与一次就加载完所有内容的 nib 文件不同，storyboard 不会一次性加载所有内容。你需要在加载视图和某控制器时向它请求特定内容。



#### (2)、storyboard

storyboard 由一个或多个视图控制器构成，且每个视图控制器至少有一个视图。

视图就是眼睛能看到的各种控件；视图控制器就是对视图交互进行控制的代码内容。

##### A: 切换预览设备

在故事板主编辑器左下角，点击下图中的 `View as:xxx` 可以弹出底部的各种设置，点击一个之后，故事板中用来预览的设备就被变成了你选中的那个。

![](https://images.gitee.com/uploads/images/2019/0213/113154_69c12ce8_930142.png)


##### B: 预览设备顶部的三个按钮

点击故事板主编辑区的任意位置，会看到下图中的三个按钮

![k3MGDJ.png](https://images.gitee.com/uploads/images/2019/0213/113155_0cc880f9_930142.png)

它们分别为：View Controller(视图控制器)、First Responsder(第一响应者)、Exit(离开)。

**视图控制器** 代表一个控制器对象，会从文件中加载控制器以及相关的视图。它的任务是管理用户在屏幕上看到的内容。一个应用通常有多个视图控制器，每个界面各一个。

**第一响应者** 就是用户当前正在进行交互的对象。如果用户正在向文本框中输入内容，那么第一个响应者就是这个文本框。它会随着用户与界面的交互而不停的变化。

##### C: 文档略图/大纲区

![](https://images.gitee.com/uploads/images/2019/0213/113155_3ca70b40_930142.png)

#### (3)、实用工具

库面板：

![](https://images.gitee.com/uploads/images/2019/0213/113155_92369d57_930142.png)

#### (4)、在视图中添加标签

![](https://images.gitee.com/uploads/images/2019/0213/113155_db7290c5_930142.png)

#### (5)、修改属性

![](https://images.gitee.com/uploads/images/2019/0213/113157_0f50e71d_930142.png)


### 3、美化 iPhone 应用

Assets.xcassets 目录下存放是应用使用的图片内容。

下图中的 AppIcon 表示配置 APP 的 Logo

![](https://images.gitee.com/uploads/images/2019/0213/113157_d92a59c7_930142.png)

### 4、启动界面

在不做任何配置的情况下，启动页面默认是空白的。

我们可以在启动页配置一个启动图片，也可以配置一个启动文件。

**启动文件** 其实是一个包含启动界面 UI 的 nib 文件或者 storyboard 。从 ios8 开始，会优先使用启动文件。

在项目导航面板中，`LaunchScreen.storyboard` 文件就是启动文件。

编辑该故事版，设置背景色为橙色，并添加 Label 文本标签。

![](https://images.gitee.com/uploads/images/2019/0213/113158_72d65695_930142.png)

添加 Label 文本标签被为其添加约束

![](https://images.gitee.com/uploads/images/2019/0213/113158_f675c702_930142.png)

上图中演示了添加约束，添加约束的目的是为了让 Label 文本标签能够在不同尺寸的屏幕上始终居于我们预设的位置。

添加约束的方式为：按住 control键，然后在左侧大纲区域把名为为”欢迎页面“的 Label 标签向它的父级——View 拖动，此时，弹出菜单，按住 shift 键，依次选中图中红色圈起来的部分。这样就成功的为这个文本标签添加了约束。（圈起来的部分，分别表示相对于父级视图的左、上、右、下的布局位置）

此时，不论在什么尺寸的设备上，上图中的 ”欢迎页面“ 文本就会一直居于屏幕中间。 


### 5、在设备上运行应用
#### (1)、确保有team

![](https://images.gitee.com/uploads/images/2019/0213/113158_1588f86b_930142.png)

#### (2)、修改 development target

![](https://images.gitee.com/uploads/images/2019/0213/113159_3e13942c_930142.png)

#### (3)、信任开发商

![](https://images.gitee.com/uploads/images/2019/0213/113201_4d341561_930142.png)

打开手机，然后依次点选：

设置——通用——描述文件与设备管理——点选 ”开发商应用“ 中你的开发者标识——点击 `信任”xxxx“`——信任

---

## 三、基本的用户交互

### 1、MVC模式

Model-View-Controller (视图-模型-控制器)

* 模型，保存应用程序数据的类
* 视图，肉眼可见并与用户产生交互的控件
* 控制器，将模型展示在视图中并控制用户交互行为的代码

### 2、创建 ButtonFun 项目

内容略。

### 3、视图控制器

左侧项目视图中的 ViewController.swit 就是视图控制器。

它是 UIViewController 的子类。

#### (1)、输出接口和操作方法

本节中将要学习如何让视图控制器类中的代码与 storyboard 中的控件进行交互。

##### A: 输出接口 @IBOutlet

被 @IBOutlet 标记的属性表示，该属性将要关联到 storyboard 或者 nib 文件中的视图对象上。

如：

```
@IBOutlet weak var mButton : UIButton!
```

UIButton 后面加 `!` 是因为，**Swift 要求所有的属性必须在执行完类的初始化函数之后有值**。但是，我们为 mButton 赋值的操作是在 storyboard 把视图对象加载出来之后，而 storyboard 加载视图的操作晚于类的初始化函数。跟一个`!`的含义就是说，该属性一定不会为 nil ,我们迟早会为 mButton 赋值——类似于Kotlin 中的 latinit（延迟初始化）.

或者也可以在后面跟 `?`，表示 mButton 可能为 nil , 但这样的话，我们在后面使用的时候就必须手动的展开/拆包（也就是判断非空），所以，还是用 `!`吧。

weak 表示弱引用。表示该属性不在具有强引用的时候，可以被自动回收。

>更多 Swift 知识点可以参考 [Swift权威编程指南_第二版](08_IOS/01_Swift/Swift权威编程指南-笔记.md)

##### B: 操作方法 @IBAction

被 @IBAction 修饰的函数表示，该函数可以被 storyboard 或者 nib 文件中的视图控件触发。

```
   @IBAction func changeText(){
        //...
    }

    @IBAction func changeText(sender:UIButton){
        //...
    }
```

#### (2)、精简视图控制器代码

在 ViewController.swift 文件中，自动生成了一个 `viewDidLoad()` 函数。

该函数表示 storyboard 或者 nib 文件中的视图对象加载完成了。

由于该章中 DEMO 暂时用不到该内容，所以，先删除这个函数。

#### (3)、设计用户界面

##### A: 添加按钮和操作方法

如下图，在搜索框中直接输入 ”uibu“ 或者 ”uibutton“ 时就只会出现 Button 控件，不会出现其他 Button相关的控件。

![添加按钮](https://images.gitee.com/uploads/images/2019/0213/113201_b1e50e09_930142.png)

将按钮放置在左下角，并修改按钮文本（注意，下图中说的双击快速打开属性面板是不对的，双击只是可以快速修改文本内容）

![修改按钮文本](https://images.gitee.com/uploads/images/2019/0213/113201_37356f2a_930142.png)

通过辅助编辑器为按钮添加操作方法

![添加操作方法](https://s2.ax1x.com/2019/02/01/k3wvDI.png)

上一步松开按键之后就会出现下图的弹窗，按照下图配置完成后( Type 选择 UIButton )，点击 connect 或者敲击回车键 完成设置。此时就为该按钮创建并关联了一个对应的点击方法。

![](https://images.gitee.com/uploads/images/2019/0213/113202_7797d8c1_930142.png)

上图弹窗中，

* Connection 表示要关联的类型，取值 outlet（输出接口，对应视图控制器中的属性）, action（操作方法，对应视图控制器中的方法） , outlet collention（属性的集合）
* Name 表示 connection 的名字（方法名 或者 属性名 或者 属性集合的名字）
* Type 表示 视图对象 的类型。
* Event 表示响应该方法的事件类型。Touch Up Inside 表示用户的抬手动作，也就是说，用户抬手时触发该方法。
* Arguments 表示方法暴露的操作


创建完成之后，storyboard 中会同步新增一个方法：

![](https://s2.ax1x.com/2019/02/01/k3BkFK.png)

**按住 option 键不放，然后拖动我们先前添加的按钮，放置到界面的右下角位置。——这样就实现了控件的复制**。然后修改文本内容。

将新增的按钮与现有的方法关联：

![](https://s2.ax1x.com/2019/02/01/k3DKc4.png)

##### B: 添加标签和输出接口

搜索并添加文本标签：

![](https://s2.ax1x.com/2019/02/01/k3DL2F.png)

修改居中对齐，清除文本（点击按钮时才显示文本），显示布局边界

![](https://s2.ax1x.com/2019/02/01/k3rlRS.png)

添加输出接口：

![](https://s2.ax1x.com/2019/02/01/k3rcZR.png)

![](https://s2.ax1x.com/2019/02/01/k3rXJf.png)

添加完成之后， ViewController.swift 中就会新增下面一句代码：

![](https://s2.ax1x.com/2019/02/01/k3rxSS.png)

添加完控件和对应的输出接口和操作方法之后，不要忘了添加约束。添加约束的方法 在第二章第4节(启动界面)中有介绍

##### C: 编写操作方法

按钮被点击之后，取出标题作为 Label 的文本

```
@IBAction func onButtonPressed(_ sender: UIButton) {
        let buttonTitle = sender.title(for: .selected) ?? "空"
        let str = "\(buttonTitle)被点击了"
        mLabel.text = str
    }
```

上述代码中，

sender.title() 表示获取按钮的标题文本。

title() 函数接收的是一个 UIControl.State 的属性。`.selected` 表示被选中。

#### (4)、测试 ButtonFun 应用

运行应用程序即可。

原书中，此处介绍的是如何添加约束，但是我们在第二章已经说过了，此处省略。

#### (5)、布局预览

![](https://images.gitee.com/uploads/images/2019/0201/164809_2d478e6a_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0201/164837_79282708_930142.png "屏幕截图.png")

![](https://images.gitee.com/uploads/images/2019/0201/165206_2252ea02_930142.png "屏幕截图.png")

上面的预览界面支持触控板的捏合缩放。

#### (6)、改变文本样式

`NSAttributedString` 类可以对字符串附加格式信息。比如字体和段落对齐等。

可以在整个字符串上使用这些元数据，也可以对字符串的不同部分采用不同的样式。

大部分 UIKit 控件都允许使用 NSAttributedString(属性字符串)。就像这里我们用到的 UILabel，先创建一个 属性字符串，然后赋值给 attributedText 属性即可。

```swift
class ViewController: UIViewController {

    @IBOutlet weak var mLeftButton : UIButton!

    @IBAction func onButtonPressed(_ sender: UIButton) {
        let buttonTitle = sender.title(for: .selected) ?? "空"
        let str = "\(buttonTitle)被点击了"
        
        //构造 NSAttributedStirng
        let styledStr = NSMutableAttributedString(string: str)
        //构造 attribute
        let attributes = [
            NSAttributedString.Key.font : UIFont.boldSystemFont(ofSize: mLabel.font.pointSize)
        ]
        //获取需要改变样式的文本的区间
        let titleRange = (str as NSString).range(of: buttonTitle)
        
        //设置属性给属性字符串。
        styledStr.setAttributes(attributes, range: titleRange)
        
        mLabel.attributedText = styledStr
    }
    
    @IBOutlet weak var mLabel: UILabel!
}
``` 

### 4、应用程序委托

AppDelegate.swift 实现了应用程序委托（application delegate）

Cocoa Touch 广泛的使用委托（Delegate）, 委托是负责为其他对象处理特定任务的对象。通过应用程序委托，能够在某些预定义时间点为 UIApplication 类做一些工作。

每个 ios 应用都有且仅有一个 UIApplication 实例，它负责应用程序的运行循环，以及处理应用程序级的功能（比如把输入信息分发给恰当的控制器类）。UIApplication 是 UIKit 的标准组成部分，主要在后台处理任务。

AppDelegate.swift 的详细内容如下：

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?

    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        // Override point for customization after application launch.
        return true
    }

    func applicationWillResignActive(_ application: UIApplication) {
        // Sent when the application is about to move from active to inactive state. This can occur for certain types of temporary interruptions (such as an incoming phone call or SMS message) or when the user quits the application and it begins the transition to the background state.
        // Use this method to pause ongoing tasks, disable timers, and invalidate graphics rendering callbacks. Games should use this method to pause the game.
    }

    func applicationDidEnterBackground(_ application: UIApplication) {
        // Use this method to release shared resources, save user data, invalidate timers, and store enough application state information to restore your application to its current state in case it is terminated later.
        // If your application supports background execution, this method is called instead of applicationWillTerminate: when the user quits.
    }

    func applicationWillEnterForeground(_ application: UIApplication) {
        // Called as part of the transition from the background to the active state; here you can undo many of the changes made on entering the background.
    }

    func applicationDidBecomeActive(_ application: UIApplication) {
        // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.
    }

    func applicationWillTerminate(_ application: UIApplication) {
        // Called when the application is about to terminate. Save data if appropriate. See also applicationDidEnterBackground:.
    }
}
```

当应用程序完成所有的初始化操作，并准备好与用户进行交互时，就会触发 `application()` 函数。该方法常用来创建要在应用程序运行的整个生命周期内活动的对象。

---

## 四、更丰富的用户界面

### 1、动态控件、静态控件和被动控件

用户界面控件共有三种基本模式：动态，静态 和 被动。

根据是否会响应用户的操作区分的。

所有 ios 控件都是 UIControl 的子类，所以能触发相应的方法。


### 2、创建 Control Fun 项目

先创建一个名称为 Control Fun 的项目，创建步骤与之前相同。

然后导入需要的图片：先点击 `Assets.xcassets` , 然后全选本地需要导入的图片，然后拖拽到界面中，具体如下图：

![](https://s2.ax1x.com/2019/02/01/k37wHP.png) 

### 3、实现图像视图和文本库

#### (1)、添加图像视图

搜索 iamge , 并添加 UIImageView 到界面：

![](https://s2.ax1x.com/2019/02/01/k37HgJ.png)

通过属性面板中的 image 属性添加图片， 打开属性面板的快捷键是： option + cmd + 4 

![](https://s2.ax1x.com/2019/02/01/k37vE6.png)


#### (2)、调整图像视图的大小

调整图像视图大小

![](https://s2.ax1x.com/2019/02/01/k3HsVx.png)

添加一个水平约束（垂直约束后面再添加）

![](https://s2.ax1x.com/2019/02/01/k3H7If.png)


#### (3)、设置视图属性

![](https://s2.ax1x.com/2019/02/01/k3bSZq.png)

通用属性的含义如下：

##### A: Content Mode

控制内容在视图中的显示方式。

包括对齐方式、以及是否缩放图像以使用视图大小等。

##### B: Semantic

是 ios9 添加的属性，可以让你设置视图在某些语言的地区使用从右到左阅读顺序进行渲染。

默认情况下视图的这个属性是 unspecifited(未指定) 状态。 


##### C: Tag

UIView 的所有子类都会有 tag 属性。

它的取值是一个数值，可以在该界面设置，也可以在代码中设置。是给开发者使用的，系统永远不会设置或者修改它的值。如果为某个空间或者视图设置了一个 tag 值，那么这个值会一直不变

（CnPeng 这个 tag 类似于 Android Fragment 的 tag, 是一个辅助性的唯一标识 ） 


##### D: Interaction

interaction  [ɪntər'ækʃ(ə)n] 互动、交互、相互作用。

![](https://s2.ax1x.com/2019/02/02/k8k5a8.png)

如上图，Interaction 的两个复选框与用户交互有关。

**`User Interaction Enabled`** 指定用户能否与当前视图对象交互。不选中则无法触发视图对应的操作方法。

**`Multiple Touch`** 决定了当前控件能否接收多点触摸事件。

##### E: Alpha

定义了视图内容的透明度。取值 0.0-1.0 ，1.0 表示完全不透明，0.0 表示完全透明。

##### F: Background

视图背景

##### G: Tint 

视图的高光（Tint）颜色。

只有部分视图在绘制自身时会用到

##### H: Drawing 

![](https://s2.ax1x.com/2019/02/02/k8Eh4S.png)

**`Opaque`** [ə(ʊ)'peɪk] 不透明的，迟钝的。默认选中。选中时表示告诉 ios 当前视图背后没有需要绘制的内容，同时允许 iOS 的绘图方法通过一些优化来加速当前视图的绘制。（即便前面已经将 Alpha 设置为 1，必要的时候也需要选中 Opaque ，这样才能确保背后的内容不会被展示出来；如果仅设置了 Alpha 为1，当前景视图不足以覆盖背景视图时，背景视图依旧会有一部分显示出来）

**`Hidden`** 表示是否隐藏当前视图对应。选中表示隐藏，用户将无法看到该视图

**`Clears Graphics Context`** 通常不选中。选中则表示 iOS 会在实际绘制对象之前清空所有区域的内容为透明。

**`Clip to Bounds`** 裁切视图，视图区域外的内容不显示。

**`Autoresize SubViews`** 表示 iOS 在当前视图的大小发生变化的时候自动调整子视图的大小。默认选中。


##### I: Stretching

![](https://s2.ax1x.com/2019/02/02/k8VNvj.png)

表示拉伸。在屏幕上调整矩形视图大小导致重绘视图时，需要使用该选项。该选项用来保持视图的外边缘不变，仅拉伸中间部分，而不是均匀拉伸视图的全部内容。

需要设置4个浮点值，用于指定一个矩形可拉伸区域的左上角坐标以及大小。取值范围 0.0-1.0 ,代表整个视图大小的一部分。

例如，如果希望每个边缘最外边的 10% 是不可拉伸的，那么就将 X 和 Y 都设为 0.1，同时将 Width 和 Height 都设置为 0.8。


#### (4)、添加文本框	

##### A: 添加标签和文本框

搜索并添加 文本框--TextField ，放在 ImageView 的右下方位置。

![](https://images.gitee.com/uploads/images/2019/0202/113746_e8683bc9_930142.png "屏幕截图.png")

搜索并添加 文本标签——label , 放在 ImageView 的左下方位置，并修改描述为 Name: 

![](https://images.gitee.com/uploads/images/2019/0202/114036_407ba596_930142.png "屏幕截图.png")

按住 option 拖动之前添加的文本框到其下方，实现复制：

![](https://s2.ax1x.com/2019/02/02/k8ZseI.png)

按住 option 托送之前添加的文本标签到其下方，实现复制，并修改描述为  Number:

![](https://s2.ax1x.com/2019/02/02/k8ZTwq.png)

调整文本框的左边界

![](https://s2.ax1x.com/2019/02/02/k8Zv6J.png)

将两个标签设置为等宽。

![](https://s2.ax1x.com/2019/02/02/k8eFfO.png)

将两个标签同时设置为右对齐

![](https://s2.ax1x.com/2019/02/02/k8e81g.png)


##### B: 文本框的属性

完整属性：

![](https://s2.ax1x.com/2019/02/02/k83NDS.png)

属性解析:

![](https://s2.ax1x.com/2019/02/02/k83I81.png)

* **`Text`** 上图中，Text 后面的下拉框表示支持的文本类型：plain——普通文本，Attributed——属性文本(带样式的文本)。下面的框表示输入的具体内容
* **`Color`** 设置文本颜色。有预置的颜色，也可以自定义
* **`Font`** 字体样式。可以设置字体、样式、尺寸
* **`Alignment`** 表示对齐方式
* **`Plactholder`** 占位符，提示文本
* **`Background`** 背景图片
* **`Disabled`** 不可以输入时的背景图片
* **`BorderStyle`** 边框的样式
* **`ClearButton`** 设置何时出现清除内容的图标按钮。
* **`Clear when editing begins`** 指定文本框获取焦点时是否主动清除已有的内容。选中则清除
* **`Min Font Size`** 设置文本框中文字的最小字号
* **`Adjust to Fit`** 文本字体是否随着文本框尺寸的变化而变化。如果选中，那么整个文本在视图中都是可见的，即使文本大于所分配的空间。该项与最小字号协同工作，无论文本框的大小如何，文本都不会小于最小字号。

下图中的这些内容用来指定键盘的外观和行为。

![](https://s2.ax1x.com/2019/02/02/k8GpFJ.png)

* **`Content Type`** 指定键盘输入内容的类型（CnPeng 这个有点迷糊，没明白具体是啥意思）
* **`Capitalization`** 大写规则
* **`Correction`** 
* **`Smart Dashes`**
* **`Smart Insert`**
* **`Smart Quotes`**
* **`Spell Checking`** 拼写检查
* **`Keyboard Type`** 键盘类型
* **`Keyboard Look`** 键盘外观风格：Drak / Light

![](https://s2.ax1x.com/2019/02/02/k8NcK1.png)

* **`Return Key`** 指定键盘右下角回车键的样式
* **`Auto-enable Return Key`** 文本框内容为空时是否禁用回车键。选中则禁用
* **`Source Text Entry`** 指定是否在文本框中显示已输入的字符。如果文本框中要输入密码，应该选中。


#### (5)、添加约束



![](https://s2.ax1x.com/2019/02/02/k8dfaV.png)

TODO 

#### (6)、创建并关联输出接口



### 4、关闭键盘
#### (1)、按下Done按钮关闭键盘
#### (2)、触摸背景关闭键盘
#### (3)、添加滑动条和标签
#### (4)、创建并关联操作方法和输出接口
#### (5)、实现操作方法

### 5、实现开关、按钮和分段控件
#### (1)、添加两个带标签的开关
#### (2)、为开关创建并关联输出接口和操作方法
#### (3)、实现开关的操作方法
#### (4)、控件状态
#### (5)、为按钮创建并关联输出接口和操作方法

### 6、实现分段控件的操作方法
#### (1)、
#### (2)、
#### (3)、
#### (4)、

### 7、实现操作表单和警告视图
#### (1)、显示操作表单
#### (2)、显示警告视图
#### (3)、
#### (4)、


---


## 五、自动旋转

### 1、自动旋转机制
#### (1)、点、像素 和 Retina 显示屏
#### (2)、控制旋转
#### (3)、
#### (4)、

### 2、创建Orientations项目
#### (1)、应用级支持的方向
#### (2)、独立控制器的旋转支持
#### (3)、
#### (4)、

### 3、创建布局项目
#### (1)、覆盖默认的约束
#### (2)、与屏幕登录安的标签
#### (3)、
#### (4)、

### 4、创建自适应布局
#### (1)、创建 Resturcture 应用程序
#### (2)、设置 iPhone的横向(hC、wC)配置布局
#### (3)、设置 iPad (以及iPhone Plus横向)的wR hR 配置
#### (4)、

### 5、
#### (1)、
#### (2)、
#### (3)、
#### (4)、

---


## 六、创建多视图应用

### 1、多视图应用的常见类型
#### (1)、
#### (2)、
#### (3)、
#### (4)、

### 2、多视图应用体系结构
#### (1)、根控制器
#### (2)、内容视图剖析
#### (3)、
#### (4)、


### 3、构建 ViewSwitcher 项目
#### (1)、重命名视图控制器
#### (2)、添加内容视图控制器
#### (3)、修改 SwitchingViewController.swift
#### (4)、创建拥有工具栏的视图
#### (5)、连接工具栏按钮和视图控制器
#### (6)、编写根视图控制器
#### (7)、实现内容视图
#### (8)、过渡动画效果


---
## 七、分页栏与选取器

### 1、Pickers应用程序
#### (1)、
#### (2)、
#### (3)、
#### (4)、

### 2、委托和数据源
#### (1)、
#### (2)、
#### (3)、
#### (4)、


### 3、创建 Pickers 应用程序
#### (1)、创建视图控制器
#### (2)、创建分页栏控制器
#### (3)、首次模拟器测试
#### (4)、实现日期选择器


### 4、实现单滚轮选取其
#### (1)、构建视图
#### (2)、将控制器实现为数据源和委托
#### (3)、
#### (4)、


### 5、实现多滚轮选取器
#### (1)、构建视图
#### (2)、实现控制器
#### (3)、滚轮内容根据环境变化
#### (4)、

### 6、使用自定义选取器创建一个简单游戏
#### (1)、编写控制器头文件
#### (2)、构建视图
#### (3)、实现控制器
#### (4)、最后的细节
---

## 八、

### 1、
#### (1)、
#### (2)、
#### (3)、
#### (4)、

### 2、
#### (1)、
#### (2)、
#### (3)、
#### (4)、


### 3、
#### (1)、
#### (2)、
#### (3)、
#### (4)、


### 4、
#### (1)、
#### (2)、
#### (3)、
#### (4)、



### 5、
#### (1)、
#### (2)、
#### (3)、
#### (4)、

---