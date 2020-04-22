* IOS 10 教程 —— 小波说雨燕：
	* [iOS 10 开发第一季 ①入门篇](http://xiaoboswift.com/course/39)
	* [iOS 10 开发第一季 ②初级篇](http://xiaoboswift.com/my/course/40)
	* [iOS 10 开发第一季 ③中级篇](http://xiaoboswift.com/course/42)
	* [iOS 10 开发第一季 ④高级篇](http://xiaoboswift.com/course/43)
	* [iOS 10 开发第一季 ⑤上架篇](http://xiaoboswift.com/my/course/44)

**以下内容基于 XCode 11.0 及更高版本**, 2019-10-09

 
# [④高级篇](http://xiaoboswift.com/course/43)
	
## 一、Core Data

本章工程基于上一章完成版。

### 1、什么是 Core Data

#### (1）、数据的保存

把数据保存到硬盘称为数据持久化（Persistence），iOS中持久化有如下几种方式：

存储方式|特点|示例
---|---|---
文件 | 存储更新频率低、离散、非结构化的文本类型数据 | 工程目录下的 `Info.plist` （用于保存工程配置）、 `UserDefault` 
数据库 | 更新频繁、数据量大、包含大文件（图片、视频等） | SQLite、Realm、CoreData（不是数据库，但基于数据库实现） 
 
 
SQLite 是一种轻型数据库，占内存小、速度快，非常实用移动设备、嵌入式设备实用。 
 
#### (2）、Core Data 介绍

Core Data 是 iOS/macOS 平台的官方**数据库框架**，封装了 SQLite 的基础操作，增加了面向对象的特性，增加了比如同 TableView 互动等高级特性。

iOS 其他核心库还有：Core Location、Core Graphics、Core Animation


>[摘自：认识CoreData—初识CoreData](http://www.cocoachina.com/articles/17245)
>
>CoreData 出现在 iOS 3 中，是苹果推出的一个**数据存储框架**。CoreData 提供了一种**对象关系映射(ORM)的存储关系**，类似于 Java 的 hibernate 框架。CoreData 可以将数据对象存储到数据库中，也可以将数据库中的数据转化为对象，在这个过程中不需要手动编写任何SQL语句，由系统帮我们完成。
>
>CoreData 最大的优势就是使用过程中不需要编写任何 SQL 语句，CoreData 封装了数据库的操作过程，以及数据库中数据和对象的转换过程。所以在使用 CoreData 的过程中，很多操作就像是对数据库进行操作一样，也有过滤条件、排序等操作。
>
>这就相当于 CoreData 完成了 Model 层的大量工作，例如 Model 层的表示和持久化，有效的减少了开发的工作量，使 Model 层的设计更加面向对象。


#### (3）、Core Data 组件

![](pics/288-coredata组件.png)

### 2、启用 CoreData

#### (1）、方式一：新建项目时启用

![](pics/289-新建项目时启用coredata.png)

这种方式启用之后，就会在 `AppDelegate.swift`中生成默认代码。


#### (2）、方式二：现有项目启用 CoreData

先使用方式一新建一个项目，然后将 `AppDelegate.swift` 中的模板代码拷贝到我们现有的项目中，不要忘了导包——`import CoreData`

下图是新建的临时项目，我们需要拷贝其中生成的与 CoreData 相关的代码——变量`persistentContainer` 和 `saveContext()` 方法：

![](pics/290-启用CoreData并查看实现.png)

拷贝之后，我们现有项目的 `AppDelegate.swfit` 中的代码如下：

```swift
import UIKit
import CoreData

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
//        //导航条颜色
//        UINavigationBar.appearance().tintColor=UIColor.white
//        UINavigationBar.appearance().barTintColor=UIColor(red: 242/255, green: 116/255, blue: 119/255, alpha: 1)
//
//        //导航条字体和颜色
//        if let barFont=UIFont(name: "Avenir-Light", size: 24){
//            UINavigationBar.appearance().titleTextAttributes=[
//                //NSFontAttributeName 已经被重命名为NSAttributedString.Key.font
//                NSAttributedString.Key.font:barFont,
//                //NSForegroundColorAttributeName 已经被重名为下方的 NSAttributedString.Key.foregroundColor
//                NSAttributedString.Key.foregroundColor:UIColor.white
//            ]
//        }
        
        return true
    }
    
    // MARK: UISceneSession Lifecycle
    
    func application(_ application: UIApplication, configurationForConnecting connectingSceneSession: UISceneSession, options: UIScene.ConnectionOptions) -> UISceneConfiguration {
        // Called when a new scene session is being created.
        // Use this method to select a configuration to create the new scene with.
        return UISceneConfiguration(name: "Default Configuration", sessionRole: connectingSceneSession.role)
    }
    
    func application(_ application: UIApplication, didDiscardSceneSessions sceneSessions: Set<UISceneSession>) {
        // Called when the user discards a scene session.
        // If any sessions were discarded while the application was not running, this will be called shortly after application:didFinishLaunchingWithOptions.
        // Use this method to release any resources that were specific to the discarded scenes, as they will not return.
    }
    
    // 后面的两段内容是拷贝进来的。
    //拷贝后，注意两个地方：导包，构建 NSPersistentContainer 对象时要改成自己的项目名称
    
    // MARK: - Core Data stack 
    lazy var persistentContainer: NSPersistentContainer = {
        //CnPeng 拷贝后，注意修改这里的name,改成自己项目的名称
        let container = NSPersistentContainer(name: "FansArea")
        container.loadPersistentStores(completionHandler: { (storeDescription, error) in
            if let error = error as NSError? {
                fatalError("Unresolved error \(error), \(error.userInfo)")
            }
        })
        return container
    }()

    // MARK: - Core Data Saving support
    func saveContext () {
        let context = persistentContainer.viewContext
        if context.hasChanges {
            do {
                try context.save()
            } catch {
                let nserror = error as NSError
                fatalError("Unresolved error \(nserror), \(nserror.userInfo)")
            }
        }
    }
}
```

### 3、可视化建模

启用 CoreData 后，可以实现可视化建模（即可视化构建对象）

#### (1）、新建CoreData模型文件

`New File` > `Core Data`  > `Data Model`  命名为 `FansArea`

![](pics/291-新建coredata模型.png)

创建之后，将看到如下界面：

![](pics/292-CoreDataModel的可视化界面.png)

#### (2）、对象与实体的映射

即对象（Object）与 数据库的实体（Entity）的对应

先点击 `Add Entity` 添加名称为 `Area` 的 Entity 实体：

![](pics/293-添加实体.png)

参照 `Area.swift` 编辑 `Area` 实体的属性（下图中的内容完整按照视频编辑的，与我本地的项目有差异）：

![](pics/294-添加实体属性.png)

注意：`image` 属性对应的属性值是 `Binary Data` 。这是因为，我们直接引用 Assets  资源目录中的文件时可以使用文件名，但是，当我们需要获取系统相册/摄像头的图片时，必须存储实际的图片文件。

通过下图中的方式，可以调整属性信息：

![](pics/295-调整属性.png)


#### (3）、创建托管对象

启用 Core Data 后，必须把对象置于 Core Data 框架中托管。

 先选中 Core Data 可视化界面中的 `Area` ，然后在属性栏中调整 `class` 的 `Name` 为 `AreaMo` (即添加后缀 MO)。通过菜单栏的 `Product` > `Build`  工程后，会自动创建 `AreaMo` 这个托管对象的类定义，但该文件是隐藏的，在左侧的文件浏览器中是看不到的。
 
 ![](pics/296-构建对象托管MO.png)

#### (4）、更新模型

接下来要做的事情是，将所有使用 Area 类的地方都替换成 AreaMO。

首先清空 `AreaTableViewController.swift` 文件中 `areaBeans` 数组中的内容（我们不再使用固定数据，而是通过前面的新增页面动态的添加内容），并指定其元素类型为 `AreaMO`。

```swift
class AreaTableViewController:UITableViewController {
	var areaBeans : [AreaMO] = []
	//其他内容省略
}
```

做完上一步之后，会提示 `Use of undeclared type 'AreaMO'`, 先不管它，我们可以通过菜单栏中的 `Product` > `Clean Build Folder` 清理一下工程，然后再通过 `Product` > `Build` 重新构建，此时，可以看到下图中的错误提示：

![](pics/297-查看构建过程中的错误信息.png)

然后点击上图 3 中的内容，挨个将 `Area` 修改为 `AreaMO`。

然后修改构建 UIImage 的方法，将原先的 ` UIImage(named: )` 改为  `UIImage(data: )`，如 ` largeImageView.image=UIImage(data:areaBean.image)`。之所以这样修改是因为，我们之前在 Area 中 image 是 String 类型，也就是图片的名字；而 AreaMO  中 image 是 Binary Data 类型，所以需要使用  `UIImage(data: )`

在替换过程中，每次修改完上图左侧列表中提示的错误后，就重新 Build 一次，直到提示 `Build Success`。

注意，在 Build 成功之后，依旧会提示 `Use of undeclared type 'AreaMO'` , 此时，关闭 XCode , 然后重新打开（在重启 Xcode 之前可以先尝试 Clean, 然后 Build，如果依旧无效，则重启 Xcode）

重启后，将不会再出现上述错误，此时，我们按住 CMD 键，单击 `var areaBeans : [AreaMO] = []` 中的 AreaMO 即可看见其定义，如下：

![](pics/298-AreaMO的内部定义.png)



#### (5）、

### 4、保存数据
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 5、取回数据
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 6、删除和更新数据
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
## 二、搜索条

### 1、UISearchController
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 2、筛选内容

#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
### 3、定制搜索条外观
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

 
## 三、用 Page View 创建引导页

### 1、UIPageViewController
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 2、添加翻页控制器

#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
### 3、实现翻页控制器
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 4、添加页码
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 5、用 UserDefaults 保存参数
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
## 四、Tab Bar 和 Storyboard 引用

### 1、创建一个 Tab Bar 控制器
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 2、添加新 Tab 页
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 3、定制 Tab 外观
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 4、Storyboard 引用
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、


## 五、WebView、WKWebView 和 SFSafariViewController

### 1、关于 Tab 的页面设计
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 2、在 Safari 中打开网页
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 3、使用 UIWebView

#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
### 4、使用 WKWebView
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 5、使用 SFSafariViewController

#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

## 六、LeanCloud

### 1、LeanCloud 云存储
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 2、保存数据到云端
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 3、取回数据
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

### 4、性能提升

#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、
### 5、下拉刷新
#### (1）、

#### (2）、

#### (3）、

#### (4）、

#### (5）、

