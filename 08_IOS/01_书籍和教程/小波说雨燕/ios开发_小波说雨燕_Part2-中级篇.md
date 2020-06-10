[Xcode 中的快捷键](https://blog.csdn.net/tyhdxhl/article/details/44748993?locationNum=6&fps=1)

格式化代码的快捷键： 先全选，然后 `Ctr l+ I`


* IOS 10 教程 —— 小波说雨燕：
	* [iOS 10 开发第一季 ①入门篇](http://xiaoboswift.com/course/39)
	* [iOS 10 开发第一季 ②初级篇](http://xiaoboswift.com/my/course/40)
	* [iOS 10 开发第一季 ③中级篇](http://xiaoboswift.com/course/42)
	* [iOS 10 开发第一季 ④高级篇](http://xiaoboswift.com/course/43)
	* [iOS 10 开发第一季 ⑤上架篇](http://xiaoboswift.com/my/course/44)

以下内容基于 XCode 11.0 及更高版本, 2019-10-09

* [Part1-初级篇-笔记](ios开发_小波说雨燕_Part1-初级篇.md) 
* [Part2-中级篇-笔记](ios开发_小波说雨燕_Part2-中级篇.md)
* [Part3-高级篇-笔记](ios开发_小波说雨燕_Part3-高级篇.md)
* [Part4-上架篇-笔记](ios开发_小波说雨燕_Part4-上架篇.md)

# [③中级篇](http://xiaoboswift.com/course/42)

## 一、面向对象编程——OOP

本章节内容依旧基于上一章节的示例。

### 1、面向对象的概念

### 2、类、对象和实例

`Class (类) ` 在其他语言中是主流地位，但在 Swift 中不是。除了系统裤架已经很成熟的各种累以外，Swift 推荐使用 `结构体（struct）`。

`结构体（struct）`能实现 `类（class）` 全部功能的同时，可以更加模块化，默认实现初始化方法并且无需内存管理。

### 3、FansArea模型重构 (Refactor）

将原先的多个基本类型数组转换成一个对象化数组。

#### (1)、新建一个 `Area.swift` 的文件

新建 swift 文件：

![](pics/140-新建swift文件.png)

编辑 Area 结构体中的内容:

![](pics/141-新建Area结构体.png)

#### (2)、借助 `playground` 将原有的数组打印成对象模式

![](pics/142-新建playground.png)

```swift
import Cocoa

var areas=["闵行区莘庄镇","兰州七里河区","三明市尤溪县","西宁城西区","广州白云区",
           "闽侯县上街镇","哈尔滨市南岗区","临汾市尧都区","成都武侯区","汕头市金平区",
           "长沙市芙蓉区","临沂市地方镇","济南市历下区"]

var pics=["xinzhuang","qilihe","youxi","chengxi","baiyun",
          "shangjie","nangang","yaodu","wuhou","jinping",
          "furong","xining","xining"]

//构建一个元素重复的 Bool 数组，
//等价于 var cellCheckStatus=[false,false,false,false,false,false,false,false,false,false,false,false,false,]
var cellCheckStatus=[Bool](repeatElement(false, count: 13))

for i in 0..<areas.count{
    // 我们想要的是 Area(areaName:"闵行区莘庄镇",imgName:"xinzhuang",isVisted:false) ，所以,需要使用引号的转义模式： \”
    var  areaStr="Area(areaName:\"\(areas[i])\",imgName:\"\(pics[i])\",isVisted:\(cellCheckStatus[i]))"
    // 在使用三目运算时，? 两边需要有空格，否则会报错。
    print(areaStr,i==areas.count-1 ? "":",")
}
```

运行结果如下:

```swift
Area(areaName:"闵行区莘庄镇",imgName:"xinzhuang",isVisted:false) ,
Area(areaName:"兰州七里河区",imgName:"qilihe",isVisted:false) ,
Area(areaName:"三明市尤溪县",imgName:"youxi",isVisted:false) ,
Area(areaName:"西宁城西区",imgName:"chengxi",isVisted:false) ,
Area(areaName:"广州白云区",imgName:"baiyun",isVisted:false) ,
Area(areaName:"闽侯县上街镇",imgName:"shangjie",isVisted:false) ,
Area(areaName:"哈尔滨市南岗区",imgName:"nangang",isVisted:false) ,
Area(areaName:"临汾市尧都区",imgName:"yaodu",isVisted:false) ,
Area(areaName:"成都武侯区",imgName:"wuhou",isVisted:false) ,
Area(areaName:"汕头市金平区",imgName:"jinping",isVisted:false) ,
Area(areaName:"长沙市芙蓉区",imgName:"furong",isVisted:false) ,
Area(areaName:"临沂市地方镇",imgName:"xining",isVisted:false) ,
Area(areaName:"济南市历下区",imgName:"xining",isVisted:false) 
```

#### (3)、修改相关代码

* `AreaTableViewController.swift`

```swift
import UIKit

class AreaTableViewController:UITableViewController {
    
    var areaBeans=[Area(areaName:"闵行区莘庄镇",imgName:"xinzhuang",isVisted:false) ,
                   Area(areaName:"兰州七里河区",imgName:"qilihe",isVisted:false) ,
                   Area(areaName:"三明市尤溪县",imgName:"youxi",isVisted:false) ,
                   Area(areaName:"西宁城西区",imgName:"chengxi",isVisted:false) ,
                   Area(areaName:"广州白云区",imgName:"baiyun",isVisted:false) ,
                   Area(areaName:"闽侯县上街镇",imgName:"shangjie",isVisted:false) ,
                   Area(areaName:"哈尔滨市南岗区",imgName:"nangang",isVisted:false) ,
                   Area(areaName:"临汾市尧都区",imgName:"yaodu",isVisted:false) ,
                   Area(areaName:"成都武侯区",imgName:"wuhou",isVisted:false) ,
                   Area(areaName:"汕头市金平区",imgName:"jinping",isVisted:false) ,
                   Area(areaName:"长沙市芙蓉区",imgName:"furong",isVisted:false) ,
                   Area(areaName:"临沂市地方镇",imgName:"xining",isVisted:false) ,
                   Area(areaName:"济南市历下区",imgName:"xining",isVisted:false)
    ]
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    // MARK: - table view data source
    //初始化单元格视图
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        // 使用 as! 强制转换为自定义的 AreaTableViewCell
        let cell = tableView.dequeueReusableCell(withIdentifier: "cusCell", for: indexPath) as! AreaTableViewCell
        
        /* 将数据填充到自定义的单元格控件中。
         nameLabel 和 thumbImageView 都是我们在 AreaTableViewCell 中声明过的
         */
        cell.nameLabel.text=areaBeans[indexPath.row].areaName
        cell.thumbImageView?.image=UIImage(named: areaBeans[indexPath.row].imgName)
        
        //设置圆角半径
        cell.thumbImageView.layer.cornerRadius=cell.thumbImageView.frame.size.width/2
        //让前一步的圆角生效
        cell.thumbImageView.clipsToBounds=true
        
        //        //如果状态是选中的，则选中；否则，不选中
        //        if(areaBeans[indexPath.row]. isVisted){
        //            cell.accessoryType = .checkmark
        //        }else{
        //            cell.accessoryType = .none
        //        }
        cell.accessoryType=areaBeans[indexPath.row].isVisted ? .checkmark:.none
        
        return cell
    }
    
    // 显示几列，如果删除该函数，则默认为 1.不删除就需要手动赋值
    override func numberOfSections(in tableView: UITableView) -> Int {
        // #warning Incomplete implementation, return the number of sections
        return 1
    }
    
    //列中的行数
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // #warning Incomplete implementation, return the number of rows
        return areaBeans.count
    }
    
    // MARK: - table view delegate
    // 快速打出该方法的方式是，输入与其他函数不一样的参数，即 didSelect 然后就会在提示窗中展示该方法，选择即可。
    /*
     override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
     //通过preferredStyle 的值可以切换 Alert 的样式：.alert 为中间弹窗，.actionSheet 为底部弹窗
     let alert=UIAlertController(title: "提示", message: "您点击了第\(indexPath.section)组,第\(indexPath.row)行", preferredStyle:.actionSheet)
     let optionCancel = UIAlertAction(title: "取消", style: .cancel, handler: nil)
     let option3 = UIAlertAction(title: "我去过", style: .default,handler:((UIAlertAction) -> Void)?{_ in
     //点击了我去过之后，该单元格后面打上对勾
     tableView.cellForRow(at: indexPath)?.accessoryType = .checkmark
     //修改数组中的状态
     self.cellCheckStatus[indexPath.row]=true
     })
     
     alert.addAction(optionCancel)
     alert.addAction(option3)
     self.present(alert, animated: true, completion: nil)
     
     //事件响应完之后清除选中状态
     tableView.deselectRow(at: indexPath, animated: true)
     }*/
    
    // 展示侧滑事件（从右向左滑动）
    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle,forRowAt indexPath: IndexPath) {
        if(editingStyle == .delete){
            //删除数据
            self.areaBeans.remove(at: indexPath.row)
            
            //删除视图
            tableView.deleteRows(at: [indexPath], with: .fade)
            
        }else if(editingStyle == .insert){
            
        }
    }
    
    //自定义侧滑菜单，返回 UITableViewRowAction 数组
    override func tableView(_ tableView: UITableView, editActionsForRowAt indexPath: IndexPath) -> [UITableViewRowAction]? {
        //定义侧滑菜单中的分享按钮
        let rowShareAction=UITableViewRowAction(style: .normal, title: "分享") { (UITableViewRowAction, IndexPath) in
            let shareSheet=UIAlertController(title: "分享到", message: nil, preferredStyle: .actionSheet)
            
            let qqAction=UIAlertAction(title: "QQ", style: .default, handler: nil)
            let weCharAction=UIAlertAction(title: "微信", style: .default, handler: nil)
            let cancleAction=UIAlertAction(title: "取消", style: .cancel, handler: nil)
            
            shareSheet.addAction(qqAction)
            shareSheet.addAction(weCharAction)
            shareSheet.addAction(cancleAction)
            
            self.present(shareSheet, animated: true, completion: nil)
        }
        
        //修改侧滑菜单项的背景颜色方式1：
        //rowShareAction.backgroundColor = UIColor.orange
        //修改侧滑菜单项的背景颜色方式2：构造中的每一个参数取值都是 0~1 之间，所以需要用色值除255
        rowShareAction.backgroundColor=UIColor(red: 245/255, green: 105/255, blue: 10/255, alpha: 1)
        
        //定义侧滑菜单中的删除按钮
        let rowDelAction=UITableViewRowAction(style: .destructive, title: "删除") { (UITableViewRowAction, indexPath) in
            self.areaBeans.remove(at: indexPath.row)
            
            tableView.deleteRows(at: [indexPath], with: .fade)
        }
        
        return [rowShareAction,rowDelAction]
    }
    
    // MARK: - Navigation
    // In a storyboard-based application, you will often want to do a little preparation before navigation
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        // showAreaDetail 是我们为转场添加的 identifer
        if segue.identifier == "showAreaDetail"{
            //强转
            let destController=segue.destination as! AreaDetailViewController
            //传递
            destController.areaBean=areaBeans[tableView.indexPathForSelectedRow!.row]
        }
    }
}
```

* `AreaDetailViewController.swift`

```swift
import UIKit

class AreaDetailViewController: UIViewController {
    
    //一定非空
    var areaBean:Area!
    
    @IBOutlet weak var areaImage: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        //显示图片
        areaImage.image=UIImage(named: areaBean.imgName)
        print("显示图片\(areaBean.imgName)")
    }
}
```

## 二、导航条美化和详情页增强

本章内容基于上一章进行修改。

### 1、重新设计详情页

#### （1）、删除之前的内容

删除之前章节中创建的  `AreaDetailViewController.swift` 、stroyborad 中的 `AreaDetailViewController` 其对应的 转场。


#### (2)、新建 TableViewController 及其转场

然后在 storyboard 中新建一个 TableViewController , 然后将其与单元格通过转场链接， 转场的 id 仍然用 `showAreaDetail `。

添加转场：

![](pics/143-添加转场1.png)

为转场添加 id：

![](pics/144-为转场添加id.png)

#### (3)、 设置原型单元格

为新增的 `Table View Controller` 中的 `Table View Cell` 设置 Identifier 为 `DetailCell`:

![](pics/145-设置单元格id.png)

设置单元格行高:

![](pics/146-设置单元格行高.png)

#### (4)、在单元格中添加 ImageView

注意：ImageView 和 单元格（DetailCell）是平级的

![](pics/147-添加ImageView并设置宽高.png)


####（5）、指定 swift 控制器

新建 `DetailTableViewController.swift` 文件:

![](pics/148-新建DetailTableViewController的swift文件.png)

将 storyboard 中的 controller 与 `DetailTableViewController.swift` 关联：

![](pics/149-关联swift文件中的controller.png)

#### (6)、将 ImageView 与 ViewController 中的变量关联

![](pics/150-将ImageView与变量关联.png)

### 2、图像填充模式

UIImageView 继承自 UIView，它是一个图片容器。

UIImageView 的 `属性栏` 中，有 `填充模式 (Content Mode) ` 选项，其常用值有三种：

* `Scale  to Fill` : 拉伸，这是默认模式，图片适应容器尺寸，显示的图片可能会变形
* `Aspect Fit` : 居中，图片保持原始比例不变，可以完整的展示在容器中间，四周可能会展示空白
* `Aspect Fill` : 平铺，图片原始尺寸不变，如果超出容器尺寸，多处的部分会被裁剪 。使用这种模式时，必须启用 `属性栏` 下 `Drawing`  的 `Clip To Bounds` (否则会延伸出去覆盖其他视图控件)

![](pics/162-图片平铺并裁剪.png)

### 3、定制原型单元格和控制器

#### (1)、新增两个Label并组合为UIStackView

新增两个Label并组合为UIStackView:

![](pics/151-新增两个Label并组合成UIStackView.png)

为StackView添加约束: 左边顶格，右边顶格，垂直居中

![](pics/152-为StackView添加约束.png)

添加完成后，效果如下:

![](pics/153-修改边距为0.png)

#### (2)、Content Hugging Priority

当我们通过上一节添加完约束信息之后，会看到下面的状态

![](pics/154-错误状态.png)

此时就需要用到  `Content Hugging Priority`, 简写 `CHP `。这是 自动布局中的一个子属性，表示自动布局中元素的优先级。值越大优先级越低，也就是说，值越大对应的控件占用的区域越少，值足够大时，就只显示控件本身内容的大小。

![](pics/155-调整CHP.png)

调整完之后，错误消失了，并且字段 Label 的尺寸也变小了。

#### (3)、将字段 Label 的宽度设置为值 Label 宽度的一半

先将两者调整为等宽：

![](pics/156-设置等宽.png)

进过上面设置完等宽之后，界面上可能并不会刷新，但这并不影响，我们继续向下调整——设置前者为后者宽度的一半：

![](pics/157-调整字段的宽度为值的一半.png)


#### (4)、设置单元格的控制器

新建单元格的控制器:

![](pics/158-新建单元格的控制器.png)

将单元格与控制器关联：

![](pics/159-将单元格与控制器关联.png)

将单元格中的 Label 与控制器关联:

![](pics/160-将Label与控制器关联.png)


### 4、更新详情页

#### (1）、新建area对象

新建area对象：

![](pics/161-新建area对象.png)

#### (2）、修改组函数和行函数的返回值

```swift
class DetailTableViewController: UITableViewController {

    @IBOutlet weak var largeImageView: UIImageView!
    var areaBean:Area!
    
   override func numberOfSections(in tableView: UITableView) -> Int {
        //返回1列/ 1组，如果将该函数注释掉，默认就是返回1
        return 1
    }

    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // 返回4行, 依次显示：名称、是否到访过
        return 2
    }
}    
```

#### (3）、创建单元格并填充数据

```swift
class DetailTableViewController: UITableViewController {
    
    @IBOutlet weak var largeImageView: UIImageView!
    var areaBean:Area!
    
     override func viewDidLoad() {
        super.viewDidLoad()
        //显示图片
	largeImageView.image=UIImage(named:areaBean.imgName)
    }
    
    //...其他代码省略
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "DetailCell", for: indexPath) as! DetailTableViewCell
        
        switch indexPath.row {
        case 0:
            cell.fieldTitle.text="地名："
            cell.fieldValue.text=areaBean.areaName
        case 1:
            cell.fieldTitle.text="参观过："
            cell.fieldValue.text=areaBean.isVisted ? "是":"否"
        default:
            break
        }
        
        return cell
    }
}    
```

#### (4）、修改转场传值

修改 `AreaTableViewController.swift` 中的核心代码：

```swift
// MARK: - Navigation
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    // showAreaDetail 是我们为转场添加的 identifer
    if segue.identifier == "showAreaDetail"{
        //强转
        let destController=segue.destination as! DetailTableViewController
        //传递destController.areaBean=areaBeans[tableView.indexPathForSelectedRow!.row]
    }
}
```

运行结果如下:

![](pics/163-运行结果.png)

完整代码：

```swift
import UIKit

class AreaTableViewController:UITableViewController {
    
    var areaBeans=[Area(areaName:"闵行区莘庄镇",imgName:"xinzhuang",isVisted:false) ,
                   Area(areaName:"兰州七里河区",imgName:"qilihe",isVisted:false) ,
                   Area(areaName:"三明市尤溪县",imgName:"youxi",isVisted:false) ,
                   Area(areaName:"西宁城西区",imgName:"chengxi",isVisted:false) ,
                   Area(areaName:"广州白云区",imgName:"baiyun",isVisted:false) ,
                   Area(areaName:"闽侯县上街镇",imgName:"shangjie",isVisted:false) ,
                   Area(areaName:"哈尔滨市南岗区",imgName:"nangang",isVisted:false) ,
                   Area(areaName:"临汾市尧都区",imgName:"yaodu",isVisted:false) ,
                   Area(areaName:"成都武侯区",imgName:"wuhou",isVisted:false) ,
                   Area(areaName:"汕头市金平区",imgName:"jinping",isVisted:false) ,
                   Area(areaName:"长沙市芙蓉区",imgName:"furong",isVisted:false) ,
                   Area(areaName:"临沂市地方镇",imgName:"xining",isVisted:false) ,
                   Area(areaName:"济南市历下区",imgName:"xining",isVisted:false)
    ]
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    // MARK: - table view data source
    //初始化单元格视图
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        
        // 使用 as! 强制转换为自定义的 AreaTableViewCell
        let cell = tableView.dequeueReusableCell(withIdentifier: "cusCell", for: indexPath) as! AreaTableViewCell
        
        /* 将数据填充到自定义的单元格控件中。
         nameLabel 和 thumbImageView 都是我们在 AreaTableViewCell 中声明过的
         */
        cell.nameLabel.text=areaBeans[indexPath.row].areaName
        cell.thumbImageView?.image=UIImage(named: areaBeans[indexPath.row].imgName)
        
        //设置圆角半径
        cell.thumbImageView.layer.cornerRadius=cell.thumbImageView.frame.size.width/2
        //让前一步的圆角生效
        cell.thumbImageView.clipsToBounds=true
        
        //        //如果状态是选中的，则选中；否则，不选中
        //        if(cellCheckStatus[indexPath.row]){
        //            cell.accessoryType = .checkmark
        //        }else{
        //            cell.accessoryType = .none
        //        }
        cell.accessoryType=areaBeans[indexPath.row].isVisted ? .checkmark:.none
        
        return cell
    }
    
    // 显示几列，如果删除该函数，则默认为 1.不删除就需要手动赋值
    override func numberOfSections(in tableView: UITableView) -> Int {
        // #warning Incomplete implementation, return the number of sections
        return 1
    }
    
    //列中的行数
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        // #warning Incomplete implementation, return the number of rows
        return areaBeans.count
    }
    
    // MARK: - table view delegate
    // 快速打出该方法的方式是，输入与其他函数不一样的参数，即 didSelect 然后就会在提示窗中展示该方法，选择即可。
    /*
     override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
     //通过preferredStyle 的值可以切换 Alert 的样式：.alert 为中间弹窗，.actionSheet 为底部弹窗
     let alert=UIAlertController(title: "提示", message: "您点击了第\(indexPath.section)组,第\(indexPath.row)行", preferredStyle:.actionSheet)
     let optionCancel = UIAlertAction(title: "取消", style: .cancel, handler: nil)
     let option3 = UIAlertAction(title: "我去过", style: .default,handler:((UIAlertAction) -> Void)?{_ in
     //点击了我去过之后，该单元格后面打上对勾
     tableView.cellForRow(at: indexPath)?.accessoryType = .checkmark
     //修改数组中的状态
     self.cellCheckStatus[indexPath.row]=true
     })
     
     alert.addAction(optionCancel)
     alert.addAction(option3)
     self.present(alert, animated: true, completion: nil)
     
     //事件响应完之后清除选中状态
     tableView.deselectRow(at: indexPath, animated: true)
     }*/
    
    // 展示侧滑事件（从右向左滑动）
    override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle,forRowAt indexPath: IndexPath) {
        if(editingStyle == .delete){
            //删除数据
            self.areaBeans.remove(at: indexPath.row)
            
            //删除视图
            tableView.deleteRows(at: [indexPath], with: .fade)
            
        }else if(editingStyle == .insert){
            
        }
    }
    
    //自定义侧滑菜单，返回 UITableViewRowAction 数组
    override func tableView(_ tableView: UITableView, editActionsForRowAt indexPath: IndexPath) -> [UITableViewRowAction]? {
        //定义侧滑菜单中的分享按钮
        let rowShareAction=UITableViewRowAction(style: .normal, title: "分享") { (UITableViewRowAction, IndexPath) in
            let shareSheet=UIAlertController(title: "分享到", message: nil, preferredStyle: .actionSheet)
            
            let qqAction=UIAlertAction(title: "QQ", style: .default, handler: nil)
            let weCharAction=UIAlertAction(title: "微信", style: .default, handler: nil)
            let cancleAction=UIAlertAction(title: "取消", style: .cancel, handler: nil)
            
            shareSheet.addAction(qqAction)
            shareSheet.addAction(weCharAction)
            shareSheet.addAction(cancleAction)
            
            self.present(shareSheet, animated: true, completion: nil)
        }
        
        //修改侧滑菜单项的背景颜色方式1：
        //rowShareAction.backgroundColor = UIColor.orange
        //修改侧滑菜单项的背景颜色方式2：构造中的每一个参数取值都是 0~1 之间，所以需要用色值除255
        rowShareAction.backgroundColor=UIColor(red: 245/255, green: 105/255, blue: 10/255, alpha: 1)
        
        //定义侧滑菜单中的删除按钮
        let rowDelAction=UITableViewRowAction(style: .destructive, title: "删除") { (UITableViewRowAction, indexPath) in
            self.areaBeans.remove(at: indexPath.row)
            
            tableView.deleteRows(at: [indexPath], with: .fade)
        }
        
        return [rowShareAction,rowDelAction]
    }
    
    // MARK: - Navigation
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        // showAreaDetail 是我们为转场添加的 identifer
        if segue.identifier == "showAreaDetail"{
            //强转
            let destController=segue.destination as! DetailTableViewController
            //传递
            destController.areaBean=areaBeans[tableView.indexPathForSelectedRow!.row]
        }
    }
}
```

### 5、美化列表外观和导航条

#### (1）、更改详情页背景及详情页单元格背景等

以下内容在 `DetailTableViewController.swift` 中修改。 

核心代码：

```swift
//调整背景颜色
tableView.backgroundColor=UIColor(white: 0.93, alpha: 1)
//去除页脚（即没有内容的单元格）
tableView.tableFooterView=UIView(frame: CGRect.zero)
//修改分割线的颜色
tableView.separatorColor=UIColor(white: 0.48, alpha: 1)

//上面三个在  viewDidLoad() 中修改，下面这个在 tableView(, ) -> UITableViewCell 中修改

//设置单元格背景透明（UIColor.clear 即表示背景透明）
cell.backgroundColor=UIColor.clear
```

#### (2）、使用代码对导航条进行改进

可以使用 Appearance API 来批量定制大多数 iOS 的 UI  组件的外观。

在 APP 的入口——`AppDelegate.swift` 中更改导航条的背景及字体 ([点击查询字体名](http://iosfonts.com/))

```swift
import UIKit

@UIApplicationMain
class AppDelegate: UIResponder, UIApplicationDelegate {
    
    func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
        //导航条颜色
        UINavigationBar.appearance().tintColor=UIColor.white
        UINavigationBar.appearance().barTintColor=UIColor(red: 242/255, green: 116/255, blue: 119/255, alpha: 1)
        
        //导航条字体和颜色
        if let barFont=UIFont(name: "Avenir-Light", size: 24){
            UINavigationBar.appearance().titleTextAttributes=[
                //NSFontAttributeName 已经被重命名为NSAttributedString.Key.font
                NSAttributedString.Key.font:barFont,
                //NSForegroundColorAttributeName 已经被重名为下方的 NSAttributedString.Key.foregroundColor
                NSAttributedString.Key.foregroundColor:UIColor.white
            ]
        }
        return true
    }
	// 其他无关代码省略   
}    
```

![](pics/164-修改导航条字体和颜色.png)

#### (3）、使用 storyboard 修改导航条

先注释掉上一步中的代码内容，然后依次按照如下步骤执行:

修改导航条背景色:

![](pics/165-修改导航条背景色.png)

设置导航条的字体:

![](pics/166-设置导航条的字体.png)

修改标题栏文本色：(TODO 这里不知道为啥不生效啊，设置了白色但是依旧显示黑色)

![](pics/167-修改标题栏文本色.png)

修改子视图中导航文本的字体颜色: (如果不做设置，返回按键及其文本会显示蓝色的——默认值)

![](pics/168-修改子视图中导航文本的字体颜色.png)


#### (4）、更改详情页返回按钮中的标题

```swift
class AreaTableViewController:UITableViewController {
  override func viewDidLoad() {
        super.viewDidLoad()
        
        //取消标题，仅保留返回操作
        self.navigationItem.backBarButtonItem=UIBarButtonItem(title: "", style: .plain, target: nil, action: nil)
    }
    //其他内容省略
}    
```

为详情页指定标题：

```swift
class DetailTableViewController: UITableViewController {
   override func viewDidLoad() {
        super.viewDidLoad()
               
        //设置标题
        self.title=areaBean.areaName
    }
}    
```

运行效果如下:
 
 ![](pics/169-仅显示返回按钮并设置标题.png)
 
 TODO 注意: 由于前一小节中设置标题的字体和颜色未生效，所以，此处依旧显示的是黑色的标题。


### 6、隐藏导航条和更改状态条样式

#### (1）、上下滑动页面时时隐藏导航条

![](pics/170-滑动时隐藏导航条.png)

![](pics/171-显示或隐藏导航条的效果.png)

#### (2）、修改状态栏字体颜色

![](pics/172-修改状态栏字体颜色.png)

![](pics/173-修改状态栏字体颜色2.png)

![](pics/174-设置导航条的样式为black.png)

TODO 注意: 上面这几种方式并未能向我们预期的一样文本变成白色，而是依旧保持黑色。暂时不确定是哪里出错了。

## 三、自适应单元格和动态字体


本章代码基于上一章内容继续。

### 1、单元格动态行高问题

#### (1）、自适应单元格介绍

UITableView 在 iOS 8 之后新增 “自适应单元格” 属性，可以根据内容动态调整单元格高度。而且，“自适应单元格” 支持 “动态字体” ，从而可以实现自定义字体尺寸。

使用概要:

* 对单元格（动态宽度变化的部分）添加自动布局约束
* 指定列表的一个预计行高（估算值，不需要精确）
* 设置列表的实际行高为自动

示例:

```swift
//估算高度
tableView.estimatedRowHeight = 50
//设置行高自动
tableView.rowHeight = UITableView.automaticDimension
```


#### (2）、修改地区数组中的地区名称

修改地区数组中的地区名称为比较长的地名，这样更接近实际项目中的情况。

```swift
class AreaTableViewController:UITableViewController {
    
    var areaBeans=[Area(areaName:"闵行区莘庄镇莘庄镇莘庄镇莘庄镇莘庄镇莘庄镇",imgName:"xinzhuang",isVisted:false) ,
                   Area(areaName:"兰州七里河区里河区里河区里河区里河区",imgName:"qilihe",isVisted:false) ,
                   Area(areaName:"三明市尤溪县三明市尤溪县",imgName:"youxi",isVisted:false) ,
                   Area(areaName:"西宁城西区西宁城西区西宁城西区",imgName:"chengxi",isVisted:false) ,
                   Area(areaName:"广州白云区白云区白云区广州白云区白云区白云区广州白云区白云区白云区",imgName:"baiyun",isVisted:false) ,
                   Area(areaName:"闽侯县上街镇",imgName:"shangjie",isVisted:false) ,
                   Area(areaName:"哈尔滨市南岗区南岗区南岗区南岗区南岗区",imgName:"nangang",isVisted:false) ,
                   Area(areaName:"临汾市尧都区",imgName:"yaodu",isVisted:false) ,
                   Area(areaName:"成都武侯区",imgName:"wuhou",isVisted:false) ,
                   Area(areaName:"汕头市金平区金平区金平区金平区",imgName:"jinping",isVisted:false) ,
                   Area(areaName:"长沙市芙蓉区",imgName:"furong",isVisted:false) ,
                   Area(areaName:"临沂市地方镇",imgName:"xining",isVisted:false) ,
                   Area(areaName:"济南市历下区",imgName:"xining",isVisted:false)
    ]
    
    //其他代码省略
}    
```

此时，运行程序之后我们会看到下面的样子：

![](pics/175-文本显示不开了.png)

如上图，文本比较长的条目末尾显示了省略号，然而实际应用中我们可能想让他们能够完全显示。

### 2、启用自适应及添加约束

#### (1）、修改代码

在 `DetailTableViewController.swift` 的 `viewDidLoad()` 函数中添加代码：

![](pics/176-自适应单元格行高的代码.png)

#### (2）、添加约束

**使用单元格自适应时，必须确保响应的标签设置了顶距和底距**，所以，我们现在需要对 stack view 设置相对其容器视图的顶距和底距（我们设置为0）。

![](pics/177-为自适应单元格设置边距.png)

### 3、标签行数自适应

#### (1）、设置标签行数为0

设置标签行数为0表示自动适应行数：

![](pics/178-设置标签行数为0.png)

运行效果如下:

![](pics/179-单元格自适应效果.png)

上面的步骤仅是修改了详情页的单元格动态适应，外部列表还没有做动态适应。

**注意: 调整外部列表自适应是要注意，ImageView 不能设置约束, 否则界面会错乱**

#### (2）、调整列表的单元格自适应

![](pics/180-为列表单元格添加约束.png)

![](pics/181-修改行数.png)

![](pics/182-代码设置.png)

![](pics/183-列表调整为单元格自适应.png)

TODO 如上图所示，我们在做列表单元格自适应时，多行文本显示无异常，但是当文本只有一行时，左侧的图片显示不全，这个暂时不知道怎么解决啊。


### 4、动态字体

用户在手机的 `设置——辅助功能——显示与文本大小——更大字体` 处开启动态字体支持，如下图。

![](pics/184-大字体支持.png)

我们需要做的就是让我们的 APP 支持动态字体。 

![](pics/185-让APP支持动态字体.png)

接下来，当用户在手机端开启大字体支持，并调整了字体大小之后，我们 APP 中的文本也会随之变化，效果如下：

![](pics/186-大字体支持.png)


### 5、动态字体下的图像显示 BUG

在前一节的运行效果中，我们会发现，列表中的图片并为居中，而且，如果我们将字体调的很小，还可能会出现前面那种图片显示不全的问题，下面就通过为 ImageView 添加约束来解决这个问题。

为 ImageView 添加约束，垂直居中，宽高 60 ，左边距15 

![](pics/187-让图片垂直居中.png)

![](pics/188-设置图片的宽高信息.png)

通过上面的设置，不论我们是将字体调的很大还是很小，图像都可以正常居中显示：

![](pics/189-图像垂直居中显示.png)

TODO  如上图，当字体较大时，省份和乡镇 Label 之间的间距很小甚至会重叠，暂时还不知道怎么解决。


## 四、动画特效和反向转场

本章内容依旧基于上一章的代码效果。

### 1、什么是动画

iOS 系统中，创建动画时需要使用 `UIView.animate(,)` 函数

### 2、添加评价视图

最终实现效果：

* 在详情页的图片右上方添加一个评价按钮
* 点击评价按钮后，弹出一个新的页面，显示好中差评三个按钮（动画出现）

#### (1）、下载图标资源并添加到 assets 目录中

[点击下载视频中的图片资源](http://ese3a8b8c4d6ab.pri.qiqiuyun.net/coursematerial-42/20170330112250-jkuuptoevf48s0sg?attname=emoticons.zip&e=1585211147&token=ExRD5wolmUnwwITVeSEXDQXizfxTRp7vnaMKJbO-:rR-OQQWOipMs2hpAcIIjaRePRmY=)

![](pics/190-添加评价相关的图片资源.png)

#### (2）、添加图片按钮

如果我们直接向详情页图片上方拖拽一个  Button 时，是无法添加的。

这是因为 UITableViewController 有规定的三部分：页眉视图，原型单元格，页脚视图。因为我们之前添加的 ImageView 是作为页眉的，所以，必须将整个页眉视图放入到一个视图容器中，并设置其宽高等于 ImageView 的宽高，然后将 ImageView 放进去，最后再添加我们的 Button。

添加空view:

![](pics/191-添加空view.png)

将ImageView拖拽到View中:

![](pics/192-将ImageView拖拽到View中.png)

将页脚调整为页眉:

![](pics/193-将页脚调整为页眉.png)

添加Button:

![](pics/194-添加Button.png)

为 Button 添加约束: 宽高40，顶距10，右距10，

![](pics/195-为Button添加约束.png)

设置如下属性：Type:System, 图片：rating, View部分的背景：红，前景（tine）: 白

![](pics/196-设置Button属性.png)

设置 Button 为圆角 (添加运行时属性)：

![](pics/197-用运行时属性设置Button为圆角.png)

此时运行，会看到如下样式的按钮:

![](pics/198-详情页Button的运行效果.png)

#### (3）、添加点击按钮时要出现的新视图

点击评价按钮时，弹出一个模态视图让用户评价，操作要点如下

拖一个新的 ViewController ,

![](pics/199-添加ViewController.png)

新加一个 ImageViwe，并设置 ImageView 的约束信息：等宽登高，水平和垂直居中

设置水平居中：

![](pics/202-水平垂直居中.png)

设置等宽等高1：

![](pics/200-设置等宽登高.png)

设置等宽等高2:

![](pics/201-设置等宽等高后调整比率.png)

为该页面添加大标题，内容：“我来过，评价一下吧”，白色， Title1 字体样式，水平居中，顶距50

![](pics/203-添加文本及其约束.png)

![](pics/204-为文本设置顶距.png)

添加差评按钮，并设置相关属性：Type：system，View部分背景色: 红色，前景色：白色 , 图片：dislike ，尺寸：70*70，运行时圆角 :layer.cornerRadius 35

![](pics/205-添加差评按钮.png)

![](pics/206-设置差评按钮宽高信息.png)

![](pics/207-设置运行时圆角.png)

复制两个差评按钮，分别设置图片为中评 (对应图片 good)、好评 (对应图片 great)，然后将三个按钮组成 StackView ,并添加组约束：水平居中，顶距100，内部间距 10 .

![](pics/208-复制并组合.png)

![](pics/209-设置组的水平居中.png)

![](pics/210-设置顶距和内间距.png)

然后替换中评和好评的图片。

右上角再添加一个关闭按钮，图片设置为 close , 背景色为默认，尺寸30*30，顶距和右距10

![](pics/211-添加删除按钮.png)

![](pics/212-关闭按钮设置好之后的效果.png)


### 3、添加转场及返回

#### (1）、建立模态转场

模态转场即从下向上展示一个弹窗，关闭时从上向下消失。

按住 Ctrl ， 拖动评价按钮到 评价场景界面，从菜单中选择 `Present modally` 的转场机制。建立之后，把这个转场的 identifier 命名为 `showReview`

![](pics/213-创建模态转场.png)

![](pics/214-修改转场id.png)

运行效果如下：

![](pics/215-模态转场效果.png)

#### (2）、为评价视图定义出口——反向转场

模态展现的视图系统不提供返回按钮。所以需要我们手动定义一个 `反向转场 ( unwind segue )`，用于让模态视图退场（ dismiss ）。

创建反向转场的步骤：

* 步骤1：在目标控制器（即要返回的控制器，当前是 `DetailTableViewController.swift`）中定义一个方法，该方法只包含一个 `UIStroyboardSegue` 类型的参数，该函数用 `@IBAction` 修饰。

```swift
@IBAction func close ( segue : UIStoryboardSegue ){
	//只做返回使用，不需要任何实现或其他操作
}
```

![](pics/216-定义反向转场.png)

*  步骤2：在 storyboard 中将关闭按钮与反向转场事件关联

按住 ctrl 拖动 关闭按钮 到视图的 Exit （出口处）,选择刚定义的 close 方法、

![](pics/217-将返回按钮与反向转场绑定.png)

添加上述的反向转场后，我们在评价页面点击 返回按钮即可关闭评价页面。

### 4、可视化特效

#### (1）、添加评价 ViewController

操作步骤如下:

* 步骤1:  为评价视图中的 ImageView 添加一张图片

![](pics/218-指定图片.png)

* 步骤2：`NewFile > Cocoa Touch Class` , 将文件命名为 `ReviewViewController` 继承自 `UIViewController` 

![](pics/219-新建ViewController.png)

* 步骤3：先将新建的 ReviewViewController 与评价视图关联；然后按住 Ctrl, 拖动 ImageView 到代码中作为背景图片使用。`@IBOutlet weak var bgImageView:UIImageView!`

![](pics/220-将评价视图与ViewController绑定.png)

![](pics/221-将ImageView与ViewController关联.png)

#### (2）、背景虚化特效

使用 `UIVisualEffectView` 对一个视图应用可视化特效。配合 `UIBlurEffect` 类可以实现背景虚化特效。

在 ReviewViewController 的 `viewDidLoad()` 方法中按如下步骤添加代码：

* 步骤1：创建一个特效视图，指定特效类型（有 暗、亮、超亮）
* 步骤2：设定特效视图的大小
* 步骤3：把特效视图叠加在源视图之上（作为原视图的子视图）

```swift
import UIKit
class ReviewViewController: UIViewController {
    @IBOutlet weak var bgImageView: UIImageView! 
    override func viewDidLoad() {
        super.viewDidLoad()
        //创建虚化特效
        let blurEffect=UIBlurEffect(style: .light)
        //创建带有虚化特效的特效视图
        let effectView=UIVisualEffectView(effect: blurEffect)
        //设置特效视图的大小
        effectView.frame=view.frame
        //为 ImageView 添加子视图
        bgImageView.addSubview(effectView)
    }
}
```

![](pics/222-虚化的显示效果.png)


### 5、让视图动起来

使用 `UIView.animate` 给3个评价按钮的堆叠视图添加一个从无到有的 “放大” 动画

#### (1）、动画步骤

先确定首帧（不可见状态）和尾帧（完全可见状态）。中间的过渡需要使用 `transform` 函数集实现，包括 缩放、旋转和移动视图。

`CGAffineTransform(scaleX:0 , y:0)` 放射变换——范围缩放。

设置一个视图对象的 transform 属性设置为变换后的值。


#### (2）、设置堆叠视图动画

将堆叠视图与控制器关联: 

![](pics/223-将包含评价按钮的StackView与Controller绑定.png)

在 `viewDidLoad()` 中设置变形属性（初始不显示）:

![](pics/224-动画初始不显示StackView中的按钮.png)

在上图每一次显示完毕的时候（即 viewDidAppear 方法 中），设置动画启动时机及时长：

![](pics/225-设置动画时长及结束状态.png)

执行现有代码就可以看到三个评价按钮从无到有的渐变过程。(即缩放动画)

#### (3）、Spring animation

Spring animation 是从 iOS 7 开始添加的一种动画效果。即 震荡效果，或者叫回弹效果。

![](pics/226-震荡效果.png)

#### (4）、冉冉升起动画（位移动画）

使用 `CGAffineTransform(translationX: , y : )` 可以改变视图位置。

```swift
class ReviewViewController: UIViewController {

    @IBOutlet weak var ratingStackView: UIStackView!
    @IBOutlet weak var bgImageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        //创建虚化特效
        let blurEffect=UIBlurEffect(style: .light)
        //创建带有虚化特效的特效视图
        let effectView=UIVisualEffectView(effect: blurEffect)
        //设置特效视图的大小
        effectView.frame=view.frame
        //为 ImageView 添加子视图
        bgImageView.addSubview(effectView)
        
        //设置初始不显示，scaleX 和 y 是一个范围，不是 x 轴和 y 轴
        let startScale=CGAffineTransform(scaleX: 0, y: 0)
        //初始位移量，x坐标0，y坐标500（屏幕左上角为0，0）
        let startPos=CGAffineTransform(translationX: 0, y: 500)
        //将缩放动画和位移动画做组合效果
        ratingStackView.transform=startScale.concatenating(startPos)
    }
    
    override func viewDidAppear(_ animated: Bool) {
        //withDuration 动画时长，秒；delay 延时多久执行；usingSpringWithDamping 震荡时间；initialSpringVelocity 震荡初速度。
        UIView.animate(withDuration: 0.3, delay: 0, usingSpringWithDamping: 0.5, initialSpringVelocity: 0.5, options: [], animations: {
            //结束时的位置
            let endPos=CGAffineTransform(translationX: 0, y: 0)
            //结束时显示为原始大小
            let endScale=CGAffineTransform.identity
            //将结束时缩放效果和结束时的位置组合在一起
            self.ratingStackView.transform=endPos.concatenating(endScale)
        }, completion: nil)
    }
}    
```

上述代码中，使用了 `anim1.concatenating(anim2 )` 将两个动画组合在一起，从而实现了初始时不可见，然后逐渐弹跳变大并可见的效果。 


### 6、反向转场和传值

当用户点击了评价按钮中的一项之后，将结果回传给详情页面，并更新评价图标。

大致步骤如下：

* 在评论视图中增加一个 action 方法， 点击任意评价按钮项时触发该方法。
* 在方法内容检查时哪个评价项被点击了，并保存到属性中
* 点击关闭按钮时，通过转场参数 UIStoryboardSegue 对象获取源控制器，返回评价。
* 更新响应页面的评价按钮图标

#### (1）、修改 `Areas` 类 

```swift
struct Area {
    var areaName:String
    var imgName:String
    var isVisted:Bool
    
    //新增属性 评价等级
    var rating=""
    
    //新增属性后，为了不改变现有代码中该类的构造，所以手动创建一个指定内容的 init 方法
    init(areaName:String,imageName:String,isVisted:Bool) {
        self.areaName=areaName
        self.imgName=imageName
        self.isVisted=isVisted
    }
}
```

#### (2）、把评价按钮项与控制器关联

![](pics/227-将评价按钮与控制器关联.png)

![](pics/228-将方法与其他控件关联.png)

![](pics/229-查看已关联的控件.png)

#### (3）、区分评价按钮 (定义 Tag )

为了在触发点击事件时区分是哪个按钮触发的，需要给 View  设置 Tag , 可以分别设置为 100、101、102。（ CnPeng 这个应该可以理解为 Android 中的 viewId）

![](pics/230-为View添加Tag.png)

#### (4）、定义反向转场 id

将反向转场的 identifier 命名为 `unwindToDetailView`，以便在代码中使用

![](pics/231-为反向转场添加id.png)

#### (5）、区分被点击的按钮并执行反向转场

在 stroryboard 界面中可以通过下图的方式快速进入

![](pics/232-快速进入视图对应的控制器.png)

在评价控制器 `ReviewViewController.swift` 中添加一个评价变量 `var rating:String?` ，用来记录是哪个按钮被点击了，并执行反向转场事件。

![](pics/233-区分被点击按钮并执行反向转场.png)

#### (6）、将详情页面的评价按钮与controller关联

将详情页面的评价按钮与 controller 关联，目的是为了更新其中的图片。

![](pics/234-将详情页面的评价按钮与controller关联.png)


#### (7）、在 close 方法中获取被点击的按钮并更新图片

![](pics/235-在close中获取被点击的评价按钮.png)

运行上述代码，即可实现在评价页面点击某一项之后，图片随即在详情页面更新。


## 五、使用地图

### 1、启用 MapKit 并添加互动   

iOS 中的 MapKit 框架提供地图的显示、导航、地点标注，增加图层等。

利用 MapKit 可以继承一个全功能地图到 app 中，无需任何编码。

本章学习的内容主要包括：

* 如何嵌入地图
* 地址转坐标
* 在地图上标注（图钉）
* 定制图钉

本章将在上一章完成版基础上，点地址，打开地图并做标注 

#### (1）、启用 MapKit 

默认的 Xcode 工程中是没有绑定 MapKit 框架的，需要手动打开。

选择工程的 `TARGETS` , 然后在 `Capabilities` 中打开 Maps 即可。

![](pics/236-启用地图.png)

![](pics/237-添加地图之后的样子.png)

#### (2）、添加地图按钮

在详情页评价按钮下方添加一个  “地图” 按钮，可以打开地图场景来显示地点。

[点击下载视频中的地图图标资源](http://ese3a8b8c4d6ab.pri.qiqiuyun.net/coursematerial-42/20170330112315-d6gnk8tbwygcc4g0?attname=mapicon.zip&e=1587089051&token=ExRD5wolmUnwwITVeSEXDQXizfxTRp7vnaMKJbO-:fQ1ttTF5DSY9-i3nkO1UQQfkPtw=)

![](pics/238-添加地图图标.png)

打开详情页视图，`shift+option` 选中评价按钮，然后向下方拖拽，这样就在下方复制了一个按钮，然后更改图标并添加约束。

![](pics/239-添加地图按钮并更改图标.png)

![](pics/240-添加约束.png)


#### (3）、添加地图视图

拖一个空视图控制器到 storyboard 上，

![](pics/241-添加一个空的视图控制器.png)

再加一个 MapView 在其中:

![](pics/242-添加mapview.png)

为 MapView 添加约束：四周边距皆为0，水平垂直居中。

![](pics/243-设置mapview水平居中.png)

![](pics/244-设置边距为0.png)

![](pics/245-添加完约束之后的效果.png)

#### (4）、建立转场

按住 Ctrl, 将详情页面的地图按钮拖向新建立的地图视图控制器，从而创建一个 show 转场。

![](pics/246-为地图按钮添加转场.png)

接下来我们为转场设置id ：

![](pics/247-为转场设置id.png)

为该转场添加 id 是为了方便与该页面中的其他转场做区分。

此时，我们运行程序，点击地图按钮后就会进入地图界面，点击地图界面的返回按钮就可以返回详情页面。

MapView 提供多种选项，可以实现缩放、滑动定制化。可以将地址从标准显示模式切换到卫星或者两者混合模式。还可以启用 “用户位置” 功能。

### 2、地址与坐标间的相互转换

#### (1）、如何定位地点

定位地点时需要使用包含经纬度的地理坐标。

MapKit 中的 `Geocoder` 类可以用于经纬度与实际地址的转换，转换数据保存在 `Placemark (地标)` 对象中。

实例化一个 `CLGeocoder` , 调用 `geocodeAddressString` 方法即可实现转换。

因实际地址没有固定的格式，所以 `geocodeAddressString` 方法会返回一个联网查询到的地标对象数组，地址越准确，返回的地标越接近，如果地址不够精确，可能会返回多个地标。

通过解析返回的地标对象 （即 `CLPlackmark` 类）, 就可以获得地址的坐标。

#### (2）、测试地址与坐标的转换

新建一个名称为 Map 的 playground 文件，在其中测试转换

 ![](pics/248-新建一个mapplayground.png)
 
 ```swift
 import UIKit
//需要先导入 MapKit 包
import MapKit

let coder = CLGeocoder()
var placeMark:CLPlacemark?

coder.geocodeAddressString("山东省济南市历下区齐鲁软件园") { (placemarks, err) in
    //geocodeAddressString 是异步的，
    if err != nil{
        print(err ?? "出错了")
    }
    
    if let ps = placemarks{
        placeMark=ps.first
        //海拔
        print(placeMark?.location?.altitude ?? "海拔")
        //纬度 ?? "纬度"
        print(placeMark?.location?.coordinate.latitude )
        //经度?? "经度"
        print(placeMark?.location?.coordinate.longitude )
    }
}
 ```
 
 ![](pics/249-地址转换为坐标.png)


### 3、添加地图标注

常见的标注是一个弹框，左侧可以附加一张图片。

开发角度，标注包含2个不同的对象：

* 数据对象（一个 object）: 保存有地标的数据，遵从 MKAnnotation 协议，
* 视图对象（一个 view）: 用于展现地标，默认是图钉📌，如果享用铅笔代替图标，需要自定义此 view

MapKit 默认以上2个对象，无需自己创建。
 


#### (1）、添加地图控制器

步骤如下：

`New File > Cocoa Touch Class` ，然后选择父类 `UIViewController` , 命名为 `MapViewController`，然后在该文件源码的开头添加导包语句： `import MapKit`

然后指定为地图视图的控制器。

![](pics/250-新建地图ViewController.png)

![](pics/251-导入地图包.png)

![](pics/252-将地图与控制器绑定.png)

#### (2）、将地图界面的视图元素与控制器关联

按住 Ctrl 将 storyboard 中的 MapView  拖向 `MapViewController.swift` 中，为它们建立关联。然后在创建一个局部变量 `var area:Area!`

![](pics/253-将view与地图控制器绑定.png)


#### (3）、添加标注代码

```swift
class MapViewController: UIViewController {
    
    @IBOutlet weak var mapView: MKMapView!
    
    var area:Area!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        let geoCoder = CLGeocoder()
        geoCoder.geocodeAddressString(area.areaName) {(placeMarkArr, err) in
            // guard 方法确定 placeArr 有值，无值就会进入 else
            guard  let  placeArr = placeMarkArr else{
                print(err ?? "未知错误")
                return
            }
            
            //地图标注数据
            let annatation = MKPointAnnotation()
            annatation.title = self.area.areaName
            annatation.subtitle = self.area.areaName
            
            //如果地址存在，在地图中用标注显示
            let place = placeArr.first
            if let loc = place?.location{
                annatation.coordinate=loc.coordinate
                self.mapView.showAnnotations([annatation], animated: true)
                self.mapView.selectAnnotation(annatation, animated: true)
            }
        }
    }
}    
```

#### (4）、从详情页传递数据到地图页

在详情页控制器执行转场前（`prepare`）加入如下代码：

```swift
class DetailTableViewController: UITableViewController {
    
    @IBOutlet weak var largeImageView: UIImageView!
    
    @IBOutlet weak var ratingButton: UIButton!
    
    var areaBean:Area!
  // 转场之前的操作
    override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
        if segue.identifier == "showMap" {
            let destVC = segue.destination as! MapViewController
            destVC.area=self.areaBean
        }
    }
 
 // 其他代码省略
}    
```

此时，运行 APP , 从详情页点击地图图标后就会跳转到地图页面，进入地图页面后，就会根据地址在地图上进行坐标定位，如下图：

![](pics/254-地图标注展现.png)


#### (5）、添加标注图片

前面拖，标注视图可以定制。定制时需要遵从 `MKMapViewDelegate` 协议，这个协议可以更新地图中的相关信息。

为了遵从该协议，就需要让地图控制器类实现该协议：

```swift
class MapViewController: UIViewController,MKMapViewDelegate {
	//其他省略
]
```

在 storyboard 中，按住 Ctrl 拖动 MapView 到地图控制器，选中 `delegate` ， 这样地图控制器就成为 `MapView` 的代理，负责实现指定的方法。

![](pics/255-为MapView设置代理.png)

实现标注视图的方法:

```swift

class MapViewController: UIViewController,MKMapViewDelegate {
    
    @IBOutlet weak var mapView: MKMapView!
    
    var area:Area!
 //输入 viewFor 然后 xcode 就会提示该方法
    func mapView(_ mapView: MKMapView, viewFor annotation: MKAnnotation) -> MKAnnotationView? {
        if annotation is MKUserLocation{
            // 如果是 用户(我的)位置, 不执行定制化操作
            return nil
        }
        
        //复用已经存在的标注视图
        let id = "myAnnotation"
        var av = mapView.dequeueReusableAnnotationView(withIdentifier: id) as? MKPinAnnotationView
        
        //如果没有可以复用的标注视图，则新建一个带图钉的标注，并启用气球（气泡）提示
        if av == nil{
            av = MKPinAnnotationView(annotation: annotation, reuseIdentifier: id)
            av?.canShowCallout=true
        }
        
        //添加地区缩略图到标注视图中
        let leftIconView = UIImageView(frame: CGRect(x: 0, y: 0, width: 53, height: 53))
        leftIconView.image = UIImage(named:area.imgName)
        //左侧的附加视图
        av?.leftCalloutAccessoryView = leftIconView
        
        return av
    }
    //其他代码省略
}
```

自定义标注的显示效果如下:

![](pics/256-自定义标注的显示效果.png)

#### (6）、自定义图钉颜色

iOS 9 开始支持更改图钉颜色，使用 `pinTintColor` 属性实现。

```swift
av?.pinTintColor = UIColor.green
``` 

### 4、地图功能定制

MKMapView 类还有很多功能可以定制，比如：

* `showsTraffice` ——显示交通信息
* `showsScale` ——显示比例尺（左上角）
* `showCompass` ——显示指南针按钮（右上角）

```swift
class MapViewController: UIViewController,MKMapViewDelegate {
    
    @IBOutlet weak var mapView: MKMapView!
    
    var area:Area!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        //显示比例尺（左上角）
        mapView.showsScale=true
        //显示指南针（右上角）
        mapView.showsCompass=true
        //显示交通信息
        mapView.showsTraffic=true
        //显示用户位置
        mapView.showsUserLocation=true
        //显示大厦三维图
        mapView.showsBuildings=true
        
        let geoCoder = CLGeocoder()
        geoCoder.geocodeAddressString(area.areaName) {(placeMarkArr, err) in
            // guard 方法确定 placeArr 有值，无值就会进入 else
            guard  let  placeArr = placeMarkArr else{
                print(err ?? "未知错误")
                return
            }
            
            //地图标注数据
            let annatation = MKPointAnnotation()
            annatation.title = self.area.areaName
            annatation.subtitle = self.area.areaName
            
            //如果地址存在，在地图中用标注显示
            let place = placeArr.first
            if let loc = place?.location{
                annatation.coordinate=loc.coordinate
                self.mapView.showAnnotations([annatation], animated: true)
                self.mapView.selectAnnotation(annatation, animated: true)
            }
        }
    }
    
    //其他代码省略
}    
```

更多深入的功能比如：驾车、公交、地铁、步行、骑行、打的、火车、飞机、轮渡等路线信息，就需要借助 `MKDirection` 类实现。


## 六、静态 TableView、相册和代码约束

截至目前，APP 只是用来显示列表，无法让用户手动添加，本章新加一个页面用来收集信息，并可从相册挑选图片。

本章主要知识点包括：

* 创建静态 TableView （静态即单元格内容固定，动态即单元格内容不固定）
* 使用 UIImagePickerController 来从相册中选择图片或拍照
* 使用 MSLayoutConstraint 类来进行代码约束

本章内容基于上一章完成的工程。

### 1、静态Table View

#### (1）、创建静态单元格

从组件库拖一个 TableViewController,然后把 Content 属性修改为 `Static Cells` (默认三个空单元格)。

![](pics/257-添加静态单元格.png)

再添加两行单元格：

![](pics/258-调整单元格的行数.png)

接下来我们就需要自定义每一行的单元格，定义的内容如下：

第一行：照片
第二行：名称 Label 和 文本框
第三行：省 Label 和 文本框
第四行：地区 Label 和 文本框
第五行：我来过 Label 和 是/否 按钮

#### (2）、自定义第一行的单元格

[点击下载本节视频中的图片资源](http://ese3a8b8c4d6ab.pri.qiqiuyun.net/coursematerial-42/20170330112338-e7oc3n9f0c0s048k?attname=photoicons.zip&e=1587382995&token=ExRD5wolmUnwwITVeSEXDQXizfxTRp7vnaMKJbO-:BjB7XeALQ3mjYuHGcMu57el7Cfg=)

添加图片资源：

![](pics/259-添加图片资源.png)

修改单元格属性：

Cell : 行高 250 ，背景色为亮灰色（Light Gray）

![](pics/260-设置行高.png)

![](pics/261-调整背景色.png)

然后添加一个 ImageView ，约束条件为：水平和垂直居中

![](pics/262-设置水平和垂直居中.png)

设置图片为刚才下载并添加的 photoalbum , 设置 Mode 为 Aspect Fit

![](pics/263-设置图片及其填充模式.png)

#### (3）、自定义第二行单元格

注意: 原视频中要求设置行高 72 ，然而在 iPhone 11 Pro Max 预览图中，如果设置为 72，Label 和 TextField 会有部分重叠。所以，需要设置行高为 80

行高： 80，

![](pics/264-设置行高.png)

新增一个 Label ，设置其 title 为“地名”

![](pics/265-新增Label并设置title为地名.png)

拖一个 Text Field 到 Label 下方，与推荐的蓝色虚线对齐，并调整宽度。设置  placeholder 为 “输入地名”

![](pics/266-新增TextField.png)

选中这两个控件，然后设置 `Reset to Suggested Constraints` （CnPeng : 注意，这一步不做也行，做了还可能会导致界面错乱——Label 和 TextField 叠加在一起了）

![](pics/267-ResetToSuggestedConstraints.png)


#### (4）、自定义第三行和第四行单元格

具体内容参照第二行单元格

![](pics/268-自定义第三和第四行单元格.png)

#### (5）、自定义第五行单元格

新增一个 Label , 设置 title 为 “我来过”

在 Label 下方新增一个 Button 按钮，title 为 “是”，background 为红色，tint 为白色，并调整其宽度。

然后按住 `Shift + Option` 拖动刚才添加的按钮，实现复制，设置其 title 为 “否”

选中这三个控件，然后设置 `Reset to Suggested Constraints`

 ![](pics/269-自定义第五行的单元格.png)
 
#### (6）、将新建的TableViewController嵌入到导航控制器中

将新建的TableViewController嵌入到导航控制器中：

![](pics/270-嵌入到导航控制器中.png)

完成嵌入之后，TableViewController 就有了一个标题栏，设置其标题为“新增”：

![](pics/271-修改标题.png)
 
### 2、添加转场

#### (1）、添加新场景的入口

要进入添加页面，可以在地名列表视图的导航条添加一个 +  按钮。

拖一个 `bar button item` 到导航条右侧（此按钮专用于导航栏和工具栏）, 然后设置 `System Item` 为 `Add`:

![](pics/272-添加barButtonItem并设置为Add.png)

#### (2）、建立转场

>present modally 即 模态化转场，从下向上弹出，没有返回按钮

按住 Ctrl , 拖动刚才在导航栏新增的 +  按钮到我们之前新建立的视图导航控制器中,创建一个 `present modally` 转场：

![](pics/273-建立模态转场.png)

 设置 `identifier` 为 `addArea`:

![](pics/274-为转场设置id.png)


#### (3）、提供模态转场的出口（即返回按钮）

由于模态转场不提供默认的返回按钮，所以我们需要手动提供交互出口。

在新建的导航条上增加一个按钮，然后设置 `System Item: Cancel`  

![](pics/275-为模态转场添加取消按钮.png)

在点击取消按钮时需要退回的控制器（`AreaTableViewController.swift`）中新建参数类型为 UIStoryboard，修饰符为 IBAction 的方法:

```
  @IBAction func close(segue:UIStoryboardSegue){
        //新增条目时点击左上角的 cancel 触发该方法，然后回到该界面
    }
```

然后为取消按钮添加反向转场:

![](pics/276-添加反向转场.png)

此时，当用户点击地区列表界面右上角的 + ，就可以从底部弹窗新增界面，点新增界面左上角的取消按钮时就会返回到地区列表界面（滑落效果）

![](pics/277-新增界面效果图)


### 3、调用系统相册

用户点击新增界面的图片区域时，需要调起系统相册让用户选择图片，这就需要用到 `UIImagePickerController`

#### (1）、为新增界面关联控制器

首先，为新增界面增加视图控制器，继承自 `UITableViewController`, 命名为 `AddAreaController.swift`, 并与 View 绑定:

![](pics/278-新建ViewController1.png)

![](pics/279-新建viewController2.png)

![](pics/280-将界面与视图控制器关联.png)

静态 Table View 中不需要处理数据源相关的代码，所以，我们可以删除 `AddAreaController.swift` 中默认实现的 `numberOfSections`  和 `tableView` 方法。

#### (2）、实现单元格选择事件（打开相册）

```swift
import UIKit

class AddAreaController: UITableViewController {
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    //处理单元格的选中事件, 直接输入 didSelectRowAt，XCode 就会提示完整的方法名
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row==0 {
            //先使用 guard 判断相册是否可用（有时用户可能会禁用相册），如果想拍照就将 .photoLibrary 替换为 .camera
            guard UIImagePickerController.isSourceTypeAvailable(.photoLibrary)else {
                print("else 语句表示相册不可用，不执行后续操作")
                return
            }
            
            let picker = UIImagePickerController()
            //是否允许编辑（裁剪）
            picker.allowsEditing = false
            picker.sourceType = .photoLibrary
            
            //将以模态视图从底部弹出
            self.present(picker, animated: true, completion: nil)
        }
        
        //取消选中
        tableView.deselectRow(at: indexPath, animated: true)
    }
}
```

运行程序，点击 UIImageView 控件区域之后，会出现如下视图：

![](pics/281-打开相册.png)

如果程序运行报错，需要在 `Info.plist` 中加入 2 项隐私权限提示设置摄像头和相册使用的描述。添加方式如下：

![](pics/282-添加申请权限时的说明文档.png)

使用下面的方式也可以添加：

![](pics/282-添加申请权限时的说明文档-方式2.png)

#### (3）、将 ImageView 与控制器关联

按住 Ctrl 键，拖到 UIImageView 到 `AddAreaTableViewController.swift`，设置 `outlet` 为 `coverImageView`

![](pics/283-将控件与控制器关联.png)

#### (4）、获取并显示相册中被选择的图片

想要获取相册中被选中的图片，需要实现两个协议：`UIImagePickerControllerDelegate
` 和 `UINavigationControllerDelegate`。（相册中带有导航，所以需要实现 `UINavigationControllerDelegate `）

用户从相册选中一张图片后，会触发下列方法：

```swift
func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
       //此处获取获取被选中的图片内容
}
```    

然后再做如下设置，不设置将无法让图片显示出来：

![](pics/284-设置picker的代理为自己.png)

完整代码如下:

```swift
import UIKit

// 关于读取图库/拍照 并展示的代码，还可以参考：https://blog.csdn.net/weixin_41454168/article/details/103011239
class AddAreaController: UITableViewController,
UIImagePickerControllerDelegate,UINavigationControllerDelegate {
    
    @IBOutlet weak var coverImageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    //处理单元格的选中事件, 直接输入 didSelectRowAt，XCode 就会提示完整的方法名
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row==0 {
            //先使用 guard 判断相册是否可用（有时用户可能会禁用相册）
            guard UIImagePickerController.isSourceTypeAvailable(.photoLibrary)else {
                print("else 语句表示相册不可用，不执行后续操作")
                return
            }
            
            let picker = UIImagePickerController()
            //是否允许编辑（裁剪）
            picker.allowsEditing = false
            picker.sourceType = .photoLibrary
            //如果不做这个设置，在 imagePickerController 获取并设置了图片之后，并不会显示
            picker.delegate = self
            
            //将以模态视图从底部弹出
            self.present(picker, animated: true, completion: nil)
        }
        
        //取消选中
        tableView.deselectRow(at: indexPath, animated: true)
    }
    
    //用户从相册选中一张图片后，会触发该方法
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {

        // 展示图片内容。 info 是一个字典，键为 InfoKey, 值为 Any
        coverImageView.image = info[UIImagePickerController.InfoKey.originalImage] as? UIImage
        coverImageView.contentMode = .scaleAspectFill
        coverImageView.clipsToBounds = true
        
        // 关闭图库选择界面
        dismiss(animated: true, completion: nil)
    }
}
```

运行上述代码，选择并展示图片的效果如下：

![](pics/285-选择并展示被选中的图片的效果.png)

### 4、代码约束(NSLayoutConstraints)

在上一节的运行效果中，界面有点丑，所以我们还需要对约束进行调整，下面就将使用代码来调整约束信息。

#### (1）、代码约束和storyboard约束的对照关系

![](pics/286-代码约束和storyboard约束的对照关系.png)

#### (2）、代码添加约束

核心代码：

```swift
let coverWidthCons = NSLayoutConstraint(item: coverImageView, attribute: .width, relatedBy: .equal, toItem: coverImageView.superview, attribute: .width, multiplier: 1, constant: 0 )
        
let coverHeightCons = NSLayoutConstraint(item: coverImageView, attribute: .height, relatedBy: .equal, toItem: coverImageView.superview, attribute: .height, multiplier: 1, constant: 0)
        
 //让约束生效，等同于 storyboard 中勾选 installed
 coverWidthCons.isActive = true
 coverHeightCons.isActive=true
```

完整代码：

```swift
import UIKit

// 关于读取图库/拍照 并展示的代码，还可以参考：https://blog.csdn.net/weixin_41454168/article/details/103011239
class AddAreaController: UITableViewController,UIImagePickerControllerDelegate,
UINavigationControllerDelegate {
    
    @IBOutlet weak var coverImageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
    }
    
    //处理单元格的选中事件, 直接输入 didSelectRowAt，XCode 就会提示完整的方法名
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        if indexPath.row==0 {
            //先使用 guard 判断相册是否可用（有时用户可能会禁用相册）
            guard UIImagePickerController.isSourceTypeAvailable(.photoLibrary)else {
                print("else 语句表示相册不可用，不执行后续操作")
                return
            }
            
            let picker = UIImagePickerController()
            //是否允许编辑（裁剪）
            picker.allowsEditing = false
            picker.sourceType = .photoLibrary
            //如果不做这个设置，在 imagePickerController 获取并设置了图片之后，并不会显示
            picker.delegate = self
            
            //将以模态视图从底部弹出
            self.present(picker, animated: true, completion: nil)
        }
        
        //取消选中
        tableView.deselectRow(at: indexPath, animated: true)
    }
    
    //用户从相册选中一张图片后，会触发该方法
    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]) {
        
        // 与父布局等宽
        let coverWidthCons = NSLayoutConstraint(item: coverImageView, attribute: .width, relatedBy: .equal, toItem: coverImageView.superview, attribute: .width, multiplier: 1, constant: 0 )
        // 与父布局等高
        let coverHeightCons = NSLayoutConstraint(item: coverImageView, attribute: .height, relatedBy: .equal, toItem: coverImageView.superview, attribute: .height, multiplier: 1, constant: 0)
        
        //让约束生效，等同于 storyboard 中勾选 installed
        coverWidthCons.isActive = true
        coverHeightCons.isActive=true
        
        // 展示图片内容。 info 是一个字典，键为 InfoKey, 值为 Any
        coverImageView.image = info[UIImagePickerController.InfoKey.originalImage] as? UIImage
        coverImageView.contentMode = .scaleAspectFill
        coverImageView.clipsToBounds = true
        
        // 关闭图库选择界面
        dismiss(animated: true, completion: nil)
    }
}
```

![](pics/287-代码约束后的效果.png)

#### (3）、作业

在导航条右侧增加一个 “保存” 按钮，并在点击后判断各文本框字段是否都输入完成，如果有未输入完成的提示用户。（视频中未实现，我也没有实现😢）