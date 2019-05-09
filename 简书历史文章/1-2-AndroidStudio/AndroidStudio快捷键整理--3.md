快捷键整理系列文章地址：
[AndroidStudio快捷键整理--1](http://www.jianshu.com/p/2dfb57962e33)  
[AndroidStudio快捷键整理--2](http://www.jianshu.com/p/8839f27dfda8)
[AndroidStudio快捷键整理--3](http://www.jianshu.com/p/2fc220b1220d)
[AndroidStudio快捷键整理--4](http://www.jianshu.com/p/095fa5668580)
[AndroidStudio快捷键整理--5](http://www.jianshu.com/p/cd79fc1f399d)
```
文中内容 对应 keymap -- Main menu 中的 Code、Analyze、Refactor

主要包含如下内容：
    * 方法的重写实现、用快捷键生成setget等、用快捷键生成if / for / try...catch 等
    * 代码补全类操作，
    * 代码块的展开收起操作，
    * 导包、注释、格式化
    * 代码位移，
    * 重构（移动当前文件或代码、复制当前文件、重命名等）
    * 抽取变量、常量、方法
```
##Main menu 菜单类快捷键
### （5）Code 菜单相关（重点记忆）
功能键组合|作用
---|---
ctrl + o | 重写父类方法
ctrl + i | 实现父接口的方法
shift + cmd + d | 生成 definition（没看出啥效果呀）
cmd + n | 弹出 Generate 弹窗 
ctrl + enter | 弹出 Generate 弹窗 （同上）
alt + cmd + t | 弹出 surround with.弹窗
Fn + shift + cmd +del | 移除包裹在代码外层的 if 、while、try...catch语句
|有些机器可能不需要加 Fn 键

附： Generate 弹窗和 Surround with 弹窗

![cmd + n 或 ctrl + enter 弹出的Generate 弹窗](http://upload-images.jianshu.io/upload_images/2551993-c4f06f6be6cbf8d7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![alt + cmd + t 弹出的 surround with 弹窗](http://upload-images.jianshu.io/upload_images/2551993-0f596365a8e55762.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


#### 1) 代码补全类
功能键组合| 作用
---|---
ctrl + space | 补全代码(与系统OSX 快捷键冲突)
ctrl +shift + space | 智能补全
alt + / | cyclic expand word 
alt +shift + / | cyclic expand word (backword)

#### 2) 文件夹/代码块 展开收起类
功能键组合| 作用
---|---
cmd + + | 展开
cmd + = | 展开（同上）
cmd + - | 收缩
alt + cmd + + | 递归展开         recursively [ri'kəsivli] 递归的
alt + cmd + - | 递归收缩
shift + cmd + + | 展开全部
shift + cmd + = | 展开全部（同上）
shift + cmd + - | 收缩全部

#### 3) 导包、注释、格式化代码
功能键组合| 作用
---|---
cmd + j | 导入模板
alt + cmd +j | 用模板包裹
cmd + / |单行注释
alt + cmd + / | 多行注释
ctrl +shift + / | 多行注释
shift + cmd + / | 多行注释
ctrl + cmd + / | 文档注释（是我自定义的，后面会讲自定义）
alt + cmd + L | 格式化代码
alt + shift + cmd + L | 展示格式化代码的提示框
ctrl + alt + i | auto-indent line (自动换行？)
ctrl + alt + o | 优化导包

#### 4) 代码位移
功能键组合| 作用
---|---
shift + cmd + ↓ | 代码语句下移 (光标在方法上就是移动整个方法的位置)
shift + cmd + ↑ | 代码语句上移（光标在方法上就是移动整个方法的位置)
alt + shift + cmd + ← | 元素左移
alt + shift + cmd + → | 元素右移
alt + shift + ↓ | 行下移
alt +Shift + ↑ | 行上移

### （6）Analyze 菜单相关
功能键组合|作用
---|---
alt + shift + cmd + i | 运行 inspection (不知道是干啥的。。)
alt + shift + cmd + h | 生成当前文件的 Analysis (也不知道是干啥的)
alt + cmd + F6 | show coverage data 显示覆盖数据 ，不知道咋用


### （7）Refactor 菜单相关
>refactor   [ri'fæktə] 重构
>signature  ['sɪgnətʃə] 签名
>migration

功能键组合|作用
---|---
ctrl + t | 重构当前文件（含移动，复制 等多种操作）
shift + F6 | 重命名 类 /方法
cmd + F6 | 作用于方法或者类，作用于方法是更改方法参数，
|作用于类是更改类的泛型  (change signature)
shift + cmd + F6 | type migration (不知道咋用)
F6  | 移动到指定目录/位置
F5 | 创建当前文件的副本
cmd + del | 安全删除
alt + cmd + n | inline

#### 1) Extract 抽取操作
功能键组合| 作用
---|---
alt + cmd + e | 抽取成属性  property
alt + cmd + d | 抽取成define 
alt + cmd + k | 抽取成 typedef
alt + cmd + v | 抽取成局部变量 Variable
alt + cmd + c | 抽取成常量constant
alt + cmd + f  | 抽取成 filed 成员变量
alt + cmd + p | 抽取成 parameter
alt + shift + cmd + p | funcitonal Parameter
alt + cmd + m | 抽取成方法
