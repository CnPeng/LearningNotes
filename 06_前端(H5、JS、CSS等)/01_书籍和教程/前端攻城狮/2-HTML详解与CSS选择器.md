## 2.1 内容介绍

### 2.1.1 内容介绍

* 参考书 《CSS设计指南-第三版》
* 参考网站 [https://developer.mozilla.org/zh-CN/](https://developer.mozilla.org/zh-CN/)
* 参考网站 [https://htmlreference.io/](https://htmlreference.io/)
* 参考网站 [https://cssreference.io/](https://cssreference.io/)

开发工具：Visual studio code

调试工具：Chrome DevTools 

### 2.1.2 Web是如何工作的：基本架构和原理

Web 工作流程示意：

![](pics/2-1-网络工作的基本流程.png)

Web 基本架构和原理：

![](pics/2-2-基本架构.png)

## 2.2 HTML 基本要素（标记、元素、内容、属性）

### 2.2.1 基本要素介绍

第一个简单的 HTML 页面：

```html
<!doctype html>
<html>
<head>
    <meta charset="UTF-8"/>
    <title>第一个HTML页面</title>
</head>
<body>
    <h1>Hello HTML and CSS</h1>
</body>
</html>
```

HTML 文件的基本结构包括：文档类型声明、文档头、文档体、文档元素。

![](pics/2-3-HTML元素的组成.png)

> 在实际工作中，标记和元素不做区别。

### 2.2.2 单标记元素

单标记元素通常不包含内容。部分示例如下：

* `<meta charset="UTF-8"/>`
* `<img src="a.jpg" width="100" height="200"/>`
* `<link rel="stylesheet" type="text/css" href="some.css">`

> HTML 中可以不加末尾的结束标记 `/`，但 XHTML 中要求必须有。通常最好加上。

### 2.2.3 全局属性

即所有 HTML 元素都可以添加的属性。如下：

* accesskey
* class
* contenteditable
* contextmenu
* dir
* draggable
* dropzone
* hidden
* id
* itemid
* itemprop
* itemref
* itemscope
* itemtype
* lang
* spellcheck
* style
* tabindex
* title
* translate

## 2.3 HTML 文档类型、注释、HEAD

### 2.3.1 HTML 文档类型和注释

HTML 文档的第一行必须使用 `<!DOCTYPE>` 来声明该文档的类型，即告知 Web 浏览器该 HTML 文档是依据哪个版本的 HTML 规范来编写的。


HTML 中注释的格式为：`<!-- 这里写注释的具体内容 -->`

### 2.3.2 HTML head 及相关标记

在 HTML 的 `<head></head>` 中可以定义如下内容：

* 定义文档的编码：
	* ` <meta charset="UTF-8" />`
* 定义文档的 title 和小 icon
	* `<title>这是文档标题</title>`
	* `<link rel="shortcut icon" type="image/x-icon" href="/favicon.ico" </head>`
* 为 SEO 定义 keywords 和 description
	* `<meta name="keywords" content="keyword1,keyword2,keyword3"/>`
    * `<meta name="description" content="how to describe the document"/>`
* 定义文档的样式规则
	* `<link rel="stylesheet" type="text/css" href="some.css"/>`
    * `<style type="text/css">这里写我们自定义的样式规则</style>`
* 定义文档的 javascript 代码
	* `<script type="text/javascript" src="some.js"></script>`
    * `<script type="text/javascript">这里写自定义的 javascript 脚本内容</script>`

## 2.4 HTML 标题、段落和文本格式化

* 标题：`<h1>、<h2>、<h3>、<h4>、<h5>、<h6>`
* 段落：`<p>`
* 换行：`<br>`
* 水平线：`<hr>`

文本格式化相关的部分元素：

效果| 代码 | 显示效果
---|---|---
加粗 | `<b>Bold text</b>` | <b>Bold text</b>
斜体 | `<i>Italic text</i>` | <i>Italic text</i>
加粗（重要文本）| `<strong>Important text</strong>` | <strong>Important text</strong>
斜体（强调文本）| `<em>Emphasized text</em>` | <em>Emphasized text</em>
小文本 | `<small>Smaller text</small>` |  <small>Smaller text</small>
带删除线的文本 | `<del>(deleted text)</del>`  | <del>(deleted text)</del>
被插入的文本（带下划线） | `<ins>(inserted text)</ins>` | <ins>(inserted text)</ins>
插入代码块 | ` <code> </code>` | 
引用一句话 | `<q>引用一句话</q>` | <q>引用一句话</q>
引用一段话 | `<blockquote>引用一段话</blockquote>` | <blockquote>引用一段话</blockquote> 


## 2.5 HTML 图片 image 和超链接 Hyperlink


## 2.6 HTML 布局

## 2.7 HTML 表单 Form

## 2.8 文档对象模型 DOM 