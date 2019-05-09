快捷键整理系列文章地址：
[AndroidStudio快捷键整理--1](http://www.jianshu.com/p/2dfb57962e33)  
[AndroidStudio快捷键整理--2](http://www.jianshu.com/p/8839f27dfda8)
[AndroidStudio快捷键整理--3](http://www.jianshu.com/p/2fc220b1220d)
[AndroidStudio快捷键整理--4](http://www.jianshu.com/p/095fa5668580)
[AndroidStudio快捷键整理--5](http://www.jianshu.com/p/cd79fc1f399d)
```
文中内容 对应 keymap -- Main menu 中的 Buid、Run、Tools、VCS、window、Active Tool Window、Resize、Editor Tabs

主要包含如下内容：
    * Debug 操作
    * AS窗口操作，（部分窗口操作在 快捷键整理2 中）
    * VCS版本控制
    * 对工具栏窗口的展开收起
    * 对工具栏窗口大小的自定义
    * 工具栏窗口内部的Tab 切换
```

##Main menu 菜单类快捷键
### （8）Build 菜单相关
```
这里面的俩快捷键真不知道怎么用/(ㄒoㄒ)/~~，哪位小伙伴要是知道一定告诉我
```

功能键组合|作用
---|---
cmd + F9 | compileDirty
shift + cmd + F9 | make module(s)
### （9）Run 菜单相关 -- Debug 调试用 
功能键组合|作用
---|---
ctrl + r | 运行程序  (run)
ctrl + d | 运行debug (debug)
ctrl + alt + r | 运行程序（run...）
ctrl + alt + d | 运行debug (debug...)
cmd + F10 | apply Changes
cmd + F2 | 停止运行（stop）
F8 | 下一步（step over）
alt + shift + F8 | 强制运行下一步（force step over）
F7 | 进入方法内部（step into）
alt + shift + F7 | 强制进入方法内（force step into）
shift + F7 | 智能进入（smart step into）
shift + F8 | 跳出 （step out ）
alt + F9 | run to cursor
alt + cmd + F9 | force run to cursor 
alt + cmd + r | resume program
alt + F8 | evaluate expression
alt + cmd + F8 | quick evaluate expression
alt + F10 | show execution point 
cmd + F8 | 为某一行打上断点
alt + shift + cmd + F8 | 为某一行打上执行一次的临时断点
shift + cmd + F8 |  查看断点列表 view breakpoints 
 
### （10）Tools 菜单相关

```
这俩快捷键也是不知道怎么用/(ㄒoㄒ)/~~，哪位小伙伴要是知道一定告诉我
```

功能键组合|作用
---|---
shift + cmd + n | new scratch file 
alt + cmd + g | synchronize griffon settings

### （11）VCS 菜单相关 -- 版本控制
功能键组合|作用
---|---
ctrl + v | 打开vcs 面板（vcs operation popup...)
cmd + k | 提交更改到本地（check in project ）
cmd + t | 刷新项目 （update project） 
alt + cmd + z | 还原到上一次提交(revert)
shift + cmd + k | 推送到vcs远端服务器  (push)

### （12）Window 菜单相关
功能键组合|作用
---|---
cmd + m | 最小化窗体 （minimize） 
ctrl + cmd + = | 缩放窗体
shift + F12 | 还原为默认布局 （restore default layout）
cmd + ` | 切换到下一个项目窗口 （next project window）
shift + cmd + ` | 切换到上一个项目窗口

###1）Active Tool Window
功能键组合|作用
---|---
shift + cmd + F12 | 隐藏全部工具窗口--编辑区最大化(hide all tool windows)
ctrl + shift + F4 | 关闭正在展开的工具窗口（close active tab）
F12 | 跳转到最后一次展开的工具窗口
shift + cmd + ' | 最大化工具窗口（注意：是单引号）
ctrl + ↓ | 展示 tabs 列表

###2）Resize -- 定制工具窗口大小
注意：
* 左右拉伸 只针对 AS 左侧 和 右侧的工具边栏生效！
* 上下拉伸 只针对AS 底部的工具栏生效

功能键组合|作用
---|---
shift + cmd + ← | 向左拉伸
shift + cmd +  → | 向右拉伸
shift + cmd + ↑ | 向上拉伸
shift + cmd + ↓ | 向下拉伸

###3）Editor Tabs 
注意：
* 只针对包含多个 tab 选项卡的工具栏生效，如 TODO， Android Monitor , Version Control 
* ctrl + → 和 ctrl + ← 可能会和OSX系统的切换屏幕冲突，最终会响应的是OSX系统的操作

功能键组合|作用
---|---
shift + cmd + ] | 选择下一个Tab选项卡
ctrl + → | 选择下一个Tab选项卡（同上）
shift + cmd + [ | 选择上一个Tab选项卡
ctrl + ← | 选择下一个Tab选项卡（同上）
cmd + w | 关闭

### （13）Help 菜单相关
功能键组合|作用
---|---
shift + cmd + a | find action ...
