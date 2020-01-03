2020-01-03

文中内容基于：[黑马/传智播客的《Web前端入门教程》](http://yun.itheima.com/course/267.html)中的 CSS 部分。

* [WebStorm使用指南](https://www.jetbrains.com/help/webstorm/getting-started-with-webstorm.html)
* [WebStorm激活参考](https://www.cnblogs.com/zzguan/p/11669895.html)

## 一、CSS基本介绍

###(1)、概念介绍

原 H5 中结构和样式混乱，所以需要 CSS 样式。

基本原则：结构和样式分离，即H5 只写结构，CSS 写样式

**CSS （Cascading Sytle Sheets）层叠样式表，也叫 级联样式表**。主要用于设置 HTML  页面中文本内容（字体、大小、对齐方式等）、图片的外形（宽高、边框、边距等）以及其他版面布局和外观系那是样式。

### (2)、基本格式

CSS 的基本格式如下

![](pics/1_css基本格式.png)

在上图中，

* `选择器`即 H5 标签对象，
* `{ }` 花括号内是需要修改的属性和值
* 属性和值以 `键值对` 的形式展现，二者之间使用 `：` 冒号分隔
* 多个 `键值对` 之间，使用英文 `;` 分号分隔


###(3)、示例

* 页面结构体放在 `<body></body>` 之间
* 页面样式（CSS）根标签为 `<style> </style>`, 放在 `<head></head>` 之间，

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>V3 体会 CSS 样式 </title>
    <style>
        h1 {
            color: orange;
        }

        p {
            color: #f0f;
        }
    </style>
</head>
<body>
	<h1>这是一号标题</h1>
	<p>这是 p 段落内容</p>
</body>
</html>
```

上述代码的显示结果如下：

![](pics/2_CSS样式初体验.png)

## 二、字体样式

* `font-size` 字号大小
* `font-family` 字体
* `CSS Unicode` 字体
* `font-weight` 字体粗细
* `font-style` 字体风格
* `font` 综合设置字体样式（重要）

### (1)、`font-size` 字号大小

* 相对的长度单位

单位|说明
---|---
em | 相对于当前对象内文本的字体尺寸
px | 像素，最常用，并且也推荐使用

* 绝对长度单位

单位|说明
---|---
in | 英寸
cm | 厘米
mm | 毫米
pt | 点

示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>V5-字体大小</title>
    <style>
        h2 {
            font-size: 20px;
            color: pink;
        }

        h4 {
            font-size: 20px;
            color: orange;
        }

        p {
            font-size: 14px;
        }
    </style>
</head>
<body>
<h2>企业简介</h2>
<h4>公司由来</h4>
<p>公司由来的一堆介绍文本</p>

</body>
</html>
```
代码显示结果如下：

![](pics/3_字体中的字号.png)
 


