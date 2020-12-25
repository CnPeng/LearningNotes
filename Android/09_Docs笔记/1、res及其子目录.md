[原文](https://developer.android.com/guide/topics/resources/providing-resources?hl=zh-CN)

## 一、res子目录说明

* res目录中的子目录及各自能存储的文件说明：

子目录名称|放置的文件类型
---|---
animator/	|用于定义属性动画的 XML 文件。
anim/		|定义渐变动画的 XML 文件。（属性动画也可以保存在此目录中，但是为了区分这两种类型，属性动画首选 animator/ 目录。）
drawable/	|位图文件（.png、.9.png、.jpg、.gif）或可用 XML 表示的可绘制对象文件，如 shape
mipmap/	|适用于不同启动器图标密度的可绘制对象文件。
raw/		|要以原始形式保存的任意文件。要使用原始 `InputStream` 打开这些资源，请使用资源 ID（即 R.raw.filename）调用 `Resources.openRawResource()`。
assets/	|如需访问原始文件名和文件层次结构，则可以考虑将某些资源保存在 assets/ 目录下（而不是 res/raw/）。assets/ 中的文件没有资源 ID，因此您只能使用 `AssetManager` 读取这些文件。
color/	 	|用于定义颜色状态列表的 XML 文件。
layout/	|用于定义用户界面布局的 XML 文件
menu/		|用于定义应用菜单（如选项菜单、上下文菜单或子菜单）的 XML 文件。
xml/		|可以在运行时通过调用 `Resources.getXML()` 读取的任意 XML 文件。各种 XML 配置文件（如可搜索配置）都必须保存在此处。
values/ 	|包含字符串、整型数和颜色等简单值的 XML 文件。

* values 目录中的文件如下：

文件名|内容说明
---|---
arrays.xml  | 用于资源数组（类型化数组）。
colors.xml  | 颜色值。
dimens.xml  | 尺寸值。
strings.xml | 字符串值。
styles.xml  | 样式。

## 二、目录和文件的命名

在适配不同设备和不同区域时，目录和文件的命名规则如下为 `目录名-限定符` 或 `文件名-限定符`。

比如：`drawable-xhpi` 中 原始目录名为 drawable ，限定符为 xhdpi , 他们中间使用中划线 `-` 连接。

> [点击查看原文中对于命名规则的更多介绍](https://developer.android.com/guide/topics/resources/providing-resources?hl=zh-CN)