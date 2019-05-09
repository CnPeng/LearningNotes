快捷键整理系列文章地址：
[AndroidStudio快捷键整理--1](http://www.jianshu.com/p/2dfb57962e33)  
[AndroidStudio快捷键整理--2](http://www.jianshu.com/p/8839f27dfda8)
[AndroidStudio快捷键整理--3](http://www.jianshu.com/p/2fc220b1220d)
[AndroidStudio快捷键整理--4](http://www.jianshu.com/p/095fa5668580)
[AndroidStudio快捷键整理--5](http://www.jianshu.com/p/cd79fc1f399d)
```
文中内容 对应 keymap -- Main menu 中的 File、Edit、Find、View、Navigate、Bookmarks.

主要包含如下内容：
    * AS窗口的操作，
    * 基本的编辑操作，
    * 查找操作，
    * 查看变量/方法等的定义，
    * 代码内容之间的跳转切换，
    * 书签的操作
```
##Main menu 菜单类快捷键

### （1）File 菜单相关
功能键组合|作用
---|---
cmd + , |打开 Preferences 属性界面
cmd + ; | 打开 project Structure 界面
cmd + s | 保存
alt + cmd + y | synchronize同步(菜单栏保存按钮后的蓝色循环符号) 
cmd + q | 退出android studio
cmd + h | 隐藏AS界面
alt + cmd + h | 隐藏非AS的其他界面

### （2）Edit 菜单相关

功能键组合|作用
---|---
cmd + z | 撤销操作
shift + cmd + z | 取消撤销（还原到撤销之前）
cmd + x | 剪切 (同 fn + shift + del )
fn + shift + del | 剪切（同 cmd + x ）
cmd + c | 复制
shift + cmd + c | 复制当前文件在本地的绝对路径
cmd + v | 粘贴
shift + cmd + v | 从粘贴历史中选择粘贴内容
alt + shift + cmd + v | paste simple （这个没明白有什么特殊）
cmd + a | 全选
alt + ↑ | 扩展选中
alt + ↓ | 收缩选中
cmd + d | 复制或选中一行（通常是复制一行）
shift + cmd + u | 转换成大写字母
shift + cmd + backspace | 回到最后一次编辑位置（可以跨文件回到最后一次编辑位置）
ctrl + shift + j | join lines（没看出啥效果）
shift + cmd + 8 | column selection mode（没看出啥效果）
shift + cmd + enter | complete current statement（只在末尾加个分号？）


####1） Find 操作 -- 搜索、查找、替换

功能键组合|作用
---|---
cmd + f | 搜索
cmd + r | 替换
cmd + g | 使用 cmd + f  搜索到结果后，用来切换到下一个
shift + cmd + g | 使用 cmd + f  搜索到结果后，用来切换到上一个
ctrl + cmd + g | 查询全部出现的地方（select all occurrences）
|--注意：ctrl + cmd + g 这是一个模糊查询
ctrl + g | 选中并逐个查找出使用的位置
|（ctrl + g 可用于当前文件内批量修改变量名/方法名）
ctrl + shift + g | 逐个取消选中（ctrl + g 的反向操作）
shift + cmd + f | 全局搜索（ find in path）
shift + cmd + r | 全局替换 （replace in path）
alt + F7 | 查询使用位置并在工具栏的 Find 面板展示（ find usages ）
alt + shift + cmd + F7 | 弹出搜索面板并自定义搜索范围
alt + cmd + F7 | 同 cmd + b ，以popup的形式展示全局使用位置
cmd + F7 | 查看变量的赋值、方法的使用位置（find usages in file）
shift + cmd + F7 | 将当前文件中出现的位置高亮（highlight usages in file ）



### （3）View  菜单相关

功能键组合|作用
---|---
cmd + y | 以面板的形式查看类、变量、方法的完整定义
alt + space | 以面板的形式查看类、变量、方法的完整定义(同上)
F1 | 查看类、变量、方法的定义摘要
ctrl + j | 查看类、变量、方法的定义摘要（同上）
cmd + p | 以popupWindow的形式展示方法的参数信息（同 alt + space）
ctrl + shift + p | 查看方法的返回值类型
ctrl + shift + q | 查看所在的上下文环境
cmd + F1 | 查看错误描述（没啥作用？）
cmd + ↓ | 跳转到源码
F4 | 跳转到源码（同上）
cmd + e | 最近打开的文件
shift + cmd + e | 最近修改的文件
alt + shift + c | 最近在AS中修改内容(不一定是当前项目的)
ctrl + ` | 快速切换模式(颜色、代码、热键等)
ctrl + cmd + f | 全屏AS （toggle full scree mode ）
 
### （4）Navigate  菜单相关
功能键组合|作用
---|---
cmd + o | 搜索类
shift + cmd + o | 搜索文件
alt + cmd + o | 搜索 symbol (没明白啥意思) 
alt + cmd + . | 搜索自定义 foldings( 没明白啥意思)
cmd + L | 查看行号（这里的 L 不区分大小写） 
cmd + [  | 回退光标到上一个位置（回退光标位置）
cmd + ] | 切换到下一个光标位置（与 cmd + [ 配合使用）
shift + cmd + del | 回退到上一次编辑的位置
alt + F1 | 选择从哪里展示当前文件选中状态（比较有用）
cmd + ↑ | 跳转到顶部的文件导航条
cmd + b | 查看 变量/方法 的引用位置
alt + cmd + b | 查看 变量/方法 的具体实现
shift + cmd + b | 查看变量所属类 的类文件 
cmd + u | 搜索某个 类/方法 的父类的实现（查看父类）
shift + cmd + t | 创建单元测试
cmd + F12 | 以popup的形式查看当前类的结构（含全部方法和变量）
ctrl + h | 查看类的继承关系
shift + cmd + h | 查看方法的继承关系
ctrl + alt + h | 查看方法是从哪里继承过来的以及最初的声明位置
F2 | 查看下一个错误点
shift + F2 | 查看上一个错误点
alt + cmd + ↑ | 查看上一处调用 / 跳转到上一次调用位置
alt + cmd + ↓ | 查看下一处调用 / 跳转到下一次调用位置
ctrl + alt + shift + ↑ | 查看当前文件中的上一处修改
ctrl + alt + shift + ↓ | 查看当前文件中的下一处修改
ctrl + ↑ | 方法之间切换-- 上一个方法（和OSX 系统的冲突）
ctrl + ↓ | 方法之间切换-- 下一个方法（和OSX 系统的冲突）
####1) Bookmarks -- 书签操作类
AS自带书签功能，重点的代码和方法可以添加到书签中

功能键组合| 作用
---|---
F3 | 添加到书签
cmd + F3 | 查看书签
alt + F3 | 设置书签编号
