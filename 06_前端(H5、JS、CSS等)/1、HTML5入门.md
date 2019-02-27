内容发布在简书，具体地址如下：

简书地址：https://www.jianshu.com/p/da18c2fd033a


文中内容基于：[黑马/传智播客的《Web前端入门教程》](http://yun.itheima.com/course/267.html)中的前两节。

相关网站：
* [w3schools英文网站]( https://www.w3schools.com/html/default.asp)
* [w3school 中文网站](http://w3school.com.cn/)

## 一、浏览器内核分类

* Trident——IE/Edge浏览器使用
* Gecko——火狐浏览器使用
* Webkit——safari浏览器使用
* Chromium/Blink——Chrome浏览器使用
* Presto——Opera浏览器使用（新版Opera已经使用Blink内核）

## 二、Web标准构成

Web标准是由W3C及其他标准化资质制定的标准集合。包括：结构（Structure）、表现（Presentation）、行为（Behavior）

* 结构标准：用于对网页元素进行整理和分类，包括 xml、xhtml 两部分
* 样式标准：用于设置网页元素的版式、颜色、大小等外观，主要指 Css
* 行为标准：网页模型的定义及交互的编写。包括 DOM和ECMAScript两部分

所以，理想状态下，一个网页的源码中需要包含：.html、.css、.js 


## 三、HTML开发工具

* Dreamweaver、Sublime、WebStorm、HBuilder、Visual Studio Code
![](https://images.gitee.com/uploads/images/2019/0201/085018_d136e116_930142.png)

## 四、html基本骨架

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>

</body>
</html>
```
### 1、<!DOCTYPE> 

<!DOCTYPE>位于文档的最前面，用于向浏览器说明当前.html文件使用的是哪种HTML或者XHTML标准规范。浏览器会按照此处指定的规范对html文件进行解析。

HTML5可以向下兼容，所以，现在直接指定为`<!DOCTYPE html>`即可。

### 2、charset（字符编码集）

* GB2312：简体中文字符集，含6763个常用汉字
* BIG5：繁体中文，港澳台地区使用
* GBK：含全部中文字符，是对GB2312的扩展，支持繁体字
* UTF-8：支持中文和英文等，是最常用的字符集

## 五、HTML标签

>HTML大量使用语义化标签，所谓语义化就是见名知意，

### 1、排版标签

#### （1）、标题标签

* h 即 head 的简写
* 有 `<h1>、<h2>、<h3>、<h4>、<h5>、<h6>` 6种，从左到右字号依次变小。
* 基本格式`<h1></h1>`
* 像<h7>这种错误的标签在展示时不起作用

#### （2）、段落标签

* p 即 paragraph 的简写
* 基本格式`<p>段落内容</p>`
* 段落中的文本内容超出浏览器宽度之后会执行自动换行

#### （3）、水平线标签
* hr 即 horizontal  的缩写
* 其作用是在页面中插入一条水平线
* 基本格式:`<hr />`
* 这是一个自闭合标签。（普通标签成对出现；自闭合标签不需要包裹内容，自己就执行了开始和结束的操作）

#### （4）、容器标签 

* div 即 division 的缩写，分割、区分的意思
* span 即 span ，跨度、范围的意思
* 这俩本质上是一个容器，类似于 Android 中的ViewGroup
* 基本格式`<div>这是div标签中的内容</div>
<span>这是span标签中的内容</span>`

### 2、文本格式化标签

|标签|效果
---|---
`<b></b>、<strong></strong>`| 加粗，XHTML推荐使用<strong>
`<i></i>、<em></em>`|斜体，XHTML推荐使用<em>
`<s></s>、<del></del>`|删除线，XHTML推荐使用<del>
`<u></u>、<ins></ins>`|下划线，XHTML推荐使用<ins>

> 关于 HTML 和 XHTML的区别，可以参考
> * [http://www.w3school.com.cn/xhtml/xhtml_html.asp](http://www.w3school.com.cn/xhtml/xhtml_html.asp)、
> * [https://www.zhihu.com/question/19783105](https://www.zhihu.com/question/19783105)

### 3、标签的属性

* 基本格式：`<标签名 属性1=”属性值1“  属性2=”属性值2“></标签名>`
* 标签可以拥有多个属性
* 属性必须写在开始标签中，位于标签名后面
* 属性之间不区分顺序
* 标签名与属性、属性与属性之间使用空格隔开
* 任何属性都有默认值，省略该属性表示使用默认值

### 4、图像标签 

* img 即 image 的缩写
* 基本格式`<img src="图片URI/URL" />`
* `<img />`常用属性如下：

属性|属性值|属性含义
---|---|---
src|URI/URL|图像的路径
alt|文本|图像无法正常显示时的提示文本
title|文本|鼠标悬停于图像时显示的文本
width|像素（XHTML 不支持按页面百分比显示）|图像的宽度
height|像素（XHTML 不支持按页面百分比显示）|图像的高度
border|数字|设置图像边框的宽度

* 设置图像的宽高时，如果想等比缩放，则只设置其中一个即可

### 5、超链接标签

#### (1)、超链接标签<a></a>
* anchor 的缩写
* 基本格式 `<a href="跳转目标url" target="目标窗口的弹出方式">超链接文本或图像</a>`
    * href 即 HyperText Reference , 指定要跳转的URL地址
    * target , 指定目标窗口的打开方式。取值为 self / blank , self 为默认值，blank 表示新窗口打开

>注意：
> * 外链需要添加 http:// 或 https:// 前缀
> * 内部链接 直接链接内部页面名称即可，如 `<a href="index.html">首页</a>` 
> * 如果当时没有确定链接目标时，可以为 href 赋值 为 “#” ,即 `href="#"`,表示一个空连接
> * 各种网页元素，如 文本、图像、表格、音频、视频等都可以作为超链接的载体

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>超链接文本示例</title>
</head>
<body>
	<a href="https://www.jianshu.com/u/414acf7abc2b" target="_blank">CnPeng简书</a>
	<br/>
	<a href="https://blog.csdn.net/north1989">CnPeng CSDN</a>
	<br/>
	<a href="aTag.html" target="_blank">内部链接--再打开一个aTag.html</a>
	<br/>
	<a href="#">空的超链接</a>
</body>
</html>
```

#### （2）、锚点

* 通过创建锚点，可以快速定位到目标内容区域
* 创建锚点分为两步
     * 为目标内容（即锚点）创建id 并赋值
     * 将超链接文本与锚点的id 关联，`<a href="#id名称"> 超链接文本 </a>`

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
</head>
<body>
	<a href="#createAnchor">点击跳转到锚点位置</a>
	<br/>
	通过创建锚点，
	可以快速定位到目标内容区域

	<!--加这一堆br是为了增加页面高度，不然显示不出锚点效果-->
	<br><br><br><br><br><br><br><br><br><br><br><br><br><br>
	<br><br><br><br><br><br><br><br><br><br><br><br><br><br>
	<br><br><br><br><br><br><br><br><br><br><br>

	<h3 id="createAnchor">创建锚点分为两步</h3>
	<hr/>
		为目标内容（即锚点）创建id 并赋值
	<br/>
		将超链接文本与锚点的id 关联，&lt;a href="#id名称"&gt; 超链接文本 &lt;/a&gt;
</body>
</html>
```

### 6、<base> 标签
* <base> 标签可以限定同一页面内所有 超链接 的打开方式。`<base target="_blank">`
*  设置 <base> 之后依旧可以为某个单独的超链接设置打开方式。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>base 标签</title>

	<!--指定页面中所有超链接的默认打开方式为 blank -->
	<base target="_blank">
</head>
<body>
	<a href="https://www.jianshu.com/u/414acf7abc2b" >CnPeng简书</a>
	<br/>

	<!--虽然 head 中设置了base,但此处依旧可以单独指定打开方式为 self,实现重载-->
	<a href="https://blog.csdn.net/north1989" target="_self">CnPeng CSDN</a>

	<br/>
	<a href="aTag.html">内部链接--再打开一个aTag.html</a>
	<br/>
	<a href="#">空的超链接</a>

</body>
</html>
```

`<img />  、 < br/>、 <hr />、<base/> `都是自闭合标签

### 7、转义字符

![](https://images.gitee.com/uploads/images/2019/0201/085018_567ac099_930142.png)

### 8、注释标签
* 格式为 `< !-- 注释内容 -- >`

### 9、路径
#### （1）、相对路径
* 图像文件和HTML文件位于同一文件夹中，只需要输入图像文件的名称即可，`<img src="logo.gif"/>`
* 图像文件位于Html文件的下一级文件夹，输入文件夹名和文件名，二者之间用“/” 隔开，`<img src="image/logo.gif"/>`
* 图像文件位于HTML文件的上一级，在文件名之前加 `../ `, 上两级则使用 `../../`,依次类推。`<img src="../image/logo.gif"/>`

#### (2）、绝对路径
* 本地绝对路径：    `D:\web\img\logo.gif`
* 网络绝对路径：`https://upload-images.jianshu.io/upload_images/2551993-7b4cba4929e08d7c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700`

### 10、列表标签
#### （1）、无序列表 `<ul>`
* 所谓无序列表就是以小圆点或者小方块作为行首标志的列表
* 无序列表的各项之间是并列的，没有顺序级别的区分

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>无序列表 ul</title>
</head>
<body>
	<ul>
		<li>无序列表1</li>
		<li>无序列表2</li>
		<li>无序列表3</li>
		<li>无序列表4</li>
	</ul>

</body>
</html>
```

>注意：
>* `<ul></ul>` 之间只能嵌套 `<li></li>`,不允许嵌套其他标签
>* `<li></li>` 之间相当于一个容器，可以嵌套任意标签

#### （2）、有序列表 `<ol></ol>`

* 内部也是嵌套`<li></li>`
* 默认以 1、2、3...作为行首排序标志

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>有序列表ol</title>
</head>
<body>
	<ol >
		<li>有序列表1</li>
		<li>有序列表2</li>
		<li>有序列表3</li>
		<li>有序列表4</li>
	</ol>
</body>
</html>
```

#### （3）、自定义列表 `<dl></dl>`

* `<dl></dl>`为外层标签、`<dt></dt>`为内层标签、`<dt>` 下还可以嵌套 `<dd></dd>`
* 自定义列表项前不具有任何项目符号，既没有小圆点也没有1234

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>自定义列表</title>
</head>
<body>
	<dl>
		<dt>自定义列表项1</dt>
			<dd>自定义列表项1 的内容解释1</dd>
			<dd>自定义列表项1 的内容解释2</dd>
		<dt>自定义列表项2</dt>
			<dd>自定义列表项2 的内容解释1</dd>
			<dd>自定义列表项2 的内容解释2</dd>
	</dl>
</body>
</html>
```
效果图如下：

![](https://images.gitee.com/uploads/images/2019/0201/085019_e07320b9_930142.png)

### 11、表格标签 table
* `<table></table>` 用来定义表格，
* `<tr></tr>` 用来定义行，嵌套在 `<table></table>` 中
* `<td></td>` 用来定义行中的单元格，嵌套在 `<tr></tr>` 中
* **`<tr></tr>` 中只能嵌套<td></td> , 但 `<td></td>` 相当于一个容器，可以嵌套任意元素**

表格相关的属性如下：

属性名称|含义|属性取值
---|---|---
border | 表格的边框。默认 border="0",即无边框| 像素值
cellspacing | 单元格与单元格边框之间的间距。<br> 默认 cellspacing="2"| 像素值
cellpadding | 单元格内容与单元格边框的间距。<br> 默认 cellpadding="1" | 像素值
width | 表格的宽度 | 像素值
height | 表格的高度 | 像素值
align | 表格在页面中的水平对齐方式| left 、center 、right 
caption| 标题| 文本
colspan | 从当前列向后 横跨几列, 应用于td、th| 数字
rowspan | 从当前行向下 竖跨几行, 应用于td、th | 数字 

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>表格</title>
</head>
<body>
<table cellspacing="3" cellpadding="2" border="1" align="left">
	<tr>
		<td>第一行第1列</td>
		<td>第一行第2列</td>
		<td>第一行第3列</td>
	</tr>
	<tr>
		<td>第二行第1列</td>
		<td>第二行第2列</td>
		<td>第二行第3列</td>
	</tr>
</table>
</body>
</html>
```

### 12、表头标签

* 表头一般位于表格的第一行或者第一列。
* 表头标签为 `<th></th>`，在显示的时候默认会显示为加粗的效果
* 增加表头时，使用 `<th></th>` 替代对应位置的 `<td></td>`即可

下图即是设置了表头的表格。

![](https://images.gitee.com/uploads/images/2019/0201/085019_08e00a0a_930142.png)

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>表头</title>
</head>
<body>
<table border="1" cellspacing="1" cellpadding="10" align="center">
	<caption>caption标签是啥？标题？</caption>
	<tr>
		<th>属性</th>
		<th>含义</th>
		<th colspan="2">取值</th>
	</tr>
	<tr>
		<th>border</th>
		<td>单元格边框</td>
		<td>像素值，默认0</td>
		<td rowspan="3">rowspan从当前单元格向下跨三行</td>
	</tr>
	<tr>
		<th>cellspacing</th>
		<td>单元格与单元格边框的间距</td>
		<td>像素值，默认2</td>
	</tr>
	<tr>
		<th>cellpadding</th>
		<td>单元格内容与单元格边框的间距</td>
		<td>像素值，默认1</td>
	</tr>
</table>
</body>
</html>
```

### 13、表格结构(了解)

使用表格时，可以将表格分为头部、主体、页脚（页脚有兼容问题）

* `<thead></thead>` 用来定义头部。必须位于 `<table></table>` 中，一般包含网页的logo和导航等头部信息。
* `<tbody></tbody>` 用来定义表格的主体，一般包含网页中除头部和底部之外的其他内容。

![](https://images.gitee.com/uploads/images/2019/0201/085019_36bda9e3_930142.png)

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>thead+tbody</title>
</head>
<body>
<table cellspacing="2" cellpadding="10" align="center" border="1">
	<thead>
	<tr>
		<th>属性名称</th>
		<th>含义</th>
		<th>取值</th>
	</tr>
	</thead>
	<tbody>
	<tr>
		<td>colspan</td>
		<td>向右横跨几列</td>
		<td>数值</td>
	</tr>
	<tr>
		<td>rowspan</td>
		<td>向下竖跨几行</td>
		<td>数值</td>
	</tr>

	</tbody>
</table>
</body>
</html>
```

### 14、网页元素检查（开发者模式）

* 基于 chrome 浏览器，在页面中右击，然后选择 “检查”

![](https://images.gitee.com/uploads/images/2019/0201/085020_79a0c4ca_930142.png)

### 15、表格标题标签 caption
* `<caption></caption>` 标签用来定义标题的标签
* 必须写在 `<table></table>` 中，和 `<thead></thead>`平级

相关代码可以参考  `表头标签` 的代码。

### 16、单元格合并
* 适用于 `<td></td>、<th></th>`
* colspan 跨列合并（水平合并）
* rowspan 跨行合并（垂直合并）

相关代码可以参考  `表头标签` 的代码。

### 17、表单标签

html 中一个完整的表单通常由 表单元素、提示信息、表单域（即多个表单的父容器）三部分组成。

![](https://images.gitee.com/uploads/images/2019/0201/085019_bfac118a_930142.png)

#### （1）、input 输入标签
* `<input/>` 为单标签（自闭合标签）
* type 是其基本属性，用来控制输入的类型

> input 、br、hr 、img、 base 都是单标签


属性|取值|含义
---|---|---
type | text | **单行**文本输入框（不换行的）
type |  password | 密码输入框
type | radio | 单选框（配合name 可以实现单选效果）
type | checkbox | 复选框
type | button | 普通按钮
type | submit | 提交按钮
type | reset | 重置按钮
type | image | 图像形式的提交按钮
type | file | 文件域, 点击之后打开文件选择器
name | 任意文本 | 控件名称 , name 相同则表示是同一组数据 
value | 任意文本 | 默认文本值
size | 正整数 | 显示大小
checked | checked | 默认是否被选中
maxlength | 正整数 | 控制输入的最大字符数量

>多个 radio 使用相同的 name ，则表示这是一组数据，这样可以实现单选效果。如果不加 name 多个 radio 可同时被选中

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>input标签</title>
</head>
<body>
	用户名：<input type="text" maxlength="15"/>
	<br/>

	<!--次数密码中间的空格使用了是全角输入法，全角输入法中，空格占一个汉字的大小-->
	密　码：<input type="password"/>
	<br/>

	<!--使用name 限定了一组内容，从而实现单选-->
	性　别：
	<input type="radio" name="sex" checked="checked"/> 男
	<input type="radio" name="sex"/> 女
	<br/>

	爱　好：
	<input type="checkbox" name="hobby"/> 看电影
	<input type="checkbox" name="hobby"/> 读书
	<br/>

	<input type="button" value="普通按钮"/>
	<br/>
	<input type="submit" value="提交按钮"/>
	<br/>
	<input type="reset" value="重置按钮"/>
	<br/>
	<input type="image" src="../image/imgButton.png"/>
	<br/>

	请选择文件：<input type="file"/>

</body>
</html>
```
效果图如下：

![](https://images.gitee.com/uploads/images/2019/0201/085019_bd460f22_930142.png)

#### (2)、label 标签(理解)
* label 标签为 input 标签定义标注/标签
* 用来绑定一个表单元素，当点击 label 标签的时候，被绑定的 表单元素就会获取焦点
* 通过 for 属性，可以绑定 label 和 input ; 或者直接用lable 标签将input 包裹起来

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>label的使用</title>
</head>
<body>
	<!--label 中直接包裹 input,可以实现绑定-->
	<label>点击此处文本，用户名输入框会获取焦点 <br> 用户名：<input type="text"/></label>
	<br/>

	<hr/>
	<!--使用 label 的 for 属性绑定input-->
	<label for="#two">点击此处文本，密码输入框会获取焦点</label>
	<br/>
	用户名：<input type="text"/>
	<br/>
	密　码：<input type="text" id="#two"/>
</body>
</html>
```

#### (3)、textarea 文本域标签
* `<textarea></textarea>`用来做大量文本的输入，支持多行
* 有 cols 、rows 属性。cols 限制每行中所输入的文本字数，rows 限制最大行数。（这两个属性通常不被使用，更多使用会使用CSS样式做相关控制）

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>textarea标签</title>
</head>
<body>
	请输入评论内容：
	<br/>
	<textarea ></textarea>
</body>
</html>
```
效果图如下：

![](https://images.gitee.com/uploads/images/2019/0201/085019_36b2b23b_930142.png)

#### （4）、下拉菜单 `<select></select>`
* `<select></select>` 用来定义下拉菜单
* `<option></option>` 用来定义下拉菜单选项
* `<select></select>` 中至少包含一对 `<option></option>`
* 在 option 中定义了属性 selected="selected" 之后，表示该项被默认选中

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>select标签</title>
</head>
<body>
	设置家乡
	<select >
		<option>选择省份</option>
		<option>山东</option>
		<option>内蒙古</option>
		<option>黑龙江</option>
		<option>山西</option>
	</select>
	<select>
		<option>济南</option>
		<option selected="selected">临沂</option>
		<option>聊城</option>
		<option>莱芜</option>
		<option>青岛</option>
	</select>
</body>
</html>
```
效果图如下：

![](https://images.gitee.com/uploads/images/2019/0201/085019_3cdf617d_930142.png)


#### (5)、表单域 `<form></form>`

该标签用来定义表单域，以实现用户信息的收集和传递，form 中的内容通常都会被提交到服务器。

基本语法格式：

   ```
    <form action="url地址" method="提交方式" name="表单名称">
        ...各种表单控件...
    </form>
   ```

常用属性有action、method、name

* action : 指定接收并处理表单信息的服务器url地址
* method : 表单数据的提交方式。分为 post / get 
* name : 指定表单名称，用来区分页面中的多个表单

每个表单都应该有自己的表单域

**使用form 包裹之后点击提交按钮才有提交的动作**

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>form表单域</title>
</head>
<body>
	<!--使用 form 包裹之后，提交按钮和图片按钮点击时就有效果了-->
	<form action="https://www.baidu.com" method="post" name="userInfo">
		用户名：<input type="text" maxlength="15"/>
		<br/>

		<!--次数密码中间的空格使用了是全角输入法，全角输入法中，空格占一个汉字的大小-->
		密　码：<input type="password"/>
		<br/>

		<!--使用name 限定了一组内容，从而实现单选-->
		性　别：
		<input type="radio" name="sex" checked="checked"/> 男
		<input type="radio" name="sex"/> 女
		<br/>

		爱　好：
		<input type="checkbox" name="hobby"/> 看电影
		<input type="checkbox" name="hobby"/> 读书
		<br/>

		<input type="button" value="普通按钮"/>
		<br/>
		<input type="submit" value="提交按钮"/>
		<br/>
		<input type="reset" value="重置按钮"/>
		<br/>
		<input type="image" src="../image/imgButton.png"/>
		<br/>

		请选择文件：<input type="file"/>

	</form>
</body>
</html>
```

## 六、HTML5新标签及新特性

![HTML演变](https://images.gitee.com/uploads/images/2019/0201/085019_50e8f7f7_930142.png)

### 1、文档类型设定
即 <!Doctype > 对应的属性值。
* HTML , 对应早期的 HTML4.0.1, 其基本结构如下：

   ```
    <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01//EN" "http://www.w3.org/TR/html4/strict.dtd">
    <html lang="en">
        <head>
	        <meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
	        <title>Document</title>
        </head>
        <body>
        </body>
    </html>
   ```
* XHTML ，其基本结构如下：

  ```
	<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN"
			"http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
	<html xmlns="http://www.w3.org/1999/xhtml" xml:lang="en">
	<head>
		<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">
		<title>Document</title>
	</head>
	<body>
	
	</body>
	</html>
  ```

* HTML5 ，其基本格式如下

  ```
  	<!doctype html>
	<html lang="en">
	<head>
		<meta charset="UTF-8">
		<meta name="viewport"
		      content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">
		<meta http-equiv="X-UA-Compatible" content="ie=edge">
		<title>Document</title>
	</head>
	<body>
	
	</body>
	</html>
  ```

>* 在webStorm 中，如果想查看上述三种类型的基本格式，可以按如下步骤：new > file > 输入文件名为 xxx.html > 分别输入 html:4s  / html:xt / html:5 然后回车即可
>* 如果想查看某个页面使用了两种 HTML，可以 右击，然后选择`查看网页源码` ，然后查看<!Doctype > 中的信息即可

### 2、字符设定

* XHTML、HTML中设置字符集时使用：
`<meta http-equiv="Content-Type" content="text/html;charset=UTF-8">`

* HTML5 中设置字符集时使用：
`<meta charset="UTF-8">`

### 3、常用新标签
[w3school 中文网站](http://w3school.com.cn/)

标签|作用
---|---
header  |定义文档的页眉
nav |定义导航链接部分
footer | 定义文档或者节的页脚/底部
article | 定义文章
section | 定义文档中的节（section/段落）
aside | 定义其所处内容之外的内容/侧边
datalist | 定义选项列表，与input 配合使用该标签，两者通过id关联
fieldset | 可将表单内的相关元素打包/分组, 与legend 搭配使用

#### (1)、datalist 示例
* datalist 中包裹 option
* datalist 与 input 关联后，input 就具备的 select 的效果，同时具有了输入联想功能。 

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>inputlist</title>
</head>
<body>
	<input type="text" value="请输入姓名" list="names">
	<datalist id="names">
		<option>张三</option>
		<option>李四</option>
		<option>王五</option>
	</datalist>

</body>
</html>
```

#### （2）、fieldset 示例
* fieldset 默认宽度满屏

效果图如下：

![](https://images.gitee.com/uploads/images/2019/0201/085020_46648229_930142.png)

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>fieldset</title>
</head>
<body>
	<fieldset>
		<legend>用户登录</legend>
		用户名:<input type="text"/>
		<br/>
		密　码:<input type="password"/>
	</fieldset>
</body>
</html>
```

### 4、新增的input type属性值
* 这些新增的类型，更加细化的限定了输入内容，如果输入格式不对，在提交的时候会自动给出相应提示
* 更多新增type 参考 w3school

类型|示例|含义
---|---|---
email | `<input type="email">` | 输入邮箱格式
tel|`<input type="tel">` | 输入手机号
url |`<input type="url">` | 输入url
number | `<input type="number">`| 输入数字
search | `<input type="search">`| 搜索框（体现语义化）
range |`<input type="range">` | 自由拖动的滑块
time |`<input type="time">` | 输入小时 分钟
date|`<input type="date">` | 输入年 月 日
datetime | `<input type="datetime">`|输入 日期 时间
month  |` <input type="month">` |  输入月年
week |`<input type="week">` | 输入星期 年   
color| `<input type="color">`| 调出调色板

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>newInputType</title>
</head>
<body>
	<form action="http://www.baidu.com">
		email：<input type="email"/>
		<br/>
		tel：<input type="tel"/>
		<br/>
		url：<input type="url"/>
		<br/>
		number：<input type="number"/>
		<br/>
		search：<input type="search"/>
		<br/>
		range：<input type="range"/>
		<br/>
		time：<input type="time"/>
		<br/>
		date：<input type="date"/>
		<br/>
		datetime：<input type="datetime-local"/>
		<br/>
		month：<input type="month"/>
		<br/>
		week：<input type="week"/>
		<br/>
		color：<input type="color"/>
		<br/>
		<input type="submit"/>
	</form>
</body>
</html>
```
![](https://images.gitee.com/uploads/images/2019/0201/085020_17685171_930142.png)

### 5、新增的input 属性

属性| 示例|含义
---|---|---
placeholder | `<input type="text" placeholder="请输入用户名"/>` | 提示文本，参考 android TextView 的 hintText
autofocus | `<input type="text" autofocus>` | 自动获取焦点
multiple | `<input type="file" multiple>` |  多文件上传
autocomplete | `<input type="text">`| 自动完成，取值 on、 off。<br>on表示记录已经输入的值供下次自动完成。<br>此外，必须有提交按钮，必须设置name 属性，然后该属性才会生效
required | `<input type="text" required>` | 必填项
accesskey | `<input type="text" accesskey="s">`| 定义让控件获取焦点的快捷键，快捷键采用` alt + 字母`的形式  

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>newInputAttr</title>
</head>
<body>
	<form action="form.html">

		<input type="text" placeholder="请输入用户名"/>
		<br/>
		<br/>
		<!--可以简化为 <input type="text" autofocus/>  -->
		<input type="text" autofocus="autofocus" placeholder="自动获取焦点"/>
		<br/>
		<br/>
		<input type="file" multiple/>
		<br/>
		<br/>
		<input type="text" autocomplete name="identify" placeholder="下次自动补足输入内容"/>
		<br/>
		<br/>
		<!--在火狐浏览器中，如果没有输入内容，点击输入框外部区域，边框会变红-->
		<input type="text" required placeholder="这是必填项"/>
		<br/>
		<br/>
		<input type="text" accesskey="s" placeholder="获取焦点的快捷键为 alt+s"/>
		<br/>
		<br/>
		<input type="submit"/>
	</form>
</body>
</html>
```

![](https://images.gitee.com/uploads/images/2019/0201/085020_c8f76fea_930142.png)

> MAC 中，使用 WebStorm 编辑完上述代码然后部署到浏览器之后，不知道为什么 accesskey 一直不生效！！暂时没找到原因。

### 6、综合案例
* 效果图

![](https://images.gitee.com/uploads/images/2019/0201/085020_adea2ea7_930142.png)

* 实现代码

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>test1</title>
</head>
<body>
	<form action="">
		<fieldset>
			<legend>学生档案</legend>
			<label>姓　　名：<input type="text" placeholder="请输入学生姓名"/></label> <br/>
			<label>手 机 号：<input type="tel" placeholder="请输入学生手机号"/></label><br/>
			<label>邮　　箱：<input type="email"/></label><br/>
			<label>所属学院：<input type="text" list="academy"/></label>
			<datalist id="academy">
				<option >JAVA学院</option>
				<option >前端学院</option>
				<option >PHP学院</option>
			</datalist><br/>
			<label>出生日期：<input type="date"/></label><br/>
			<label>语文成绩：<input type="number" max="100" min="0" value="0"/></label><br/>
			<label>数学成绩: <meter max="100" min="0" low="59" value="10"></meter></label><br/>
			<label>英语成绩: <meter max="100" min="0" low="59" value="90"></meter></label><br/>
			<input type="submit"><br/>
			<input type="reset">

		</fieldset>
	</form>
</body>
</html>
```

### 7、多媒体标签
* embed : 定义嵌入的内容
* audio : 播放音频
* video : 播放视频

#### （1）、embed
* 用来插入各种多媒体，格式可以是 Midi、Wav、 AIFF 、AU 、Mp3等

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>embed</title>
</head>
<body>
<embed src="http://player.video.iqiyi.com/44cb2ab93ef163fea5a206e52da7c390/0/0/v_19rqyv6lfo.swf-albumId=1268727400-tvId=1268727400-isPurchase=0-cnId=3"
       allowFullScreen="true" quality="high" width="480" height="350" align="middle"
       allowScriptAccess="always" type="application/x-shockwave-flash"></embed>
</body>
</html>
```
> 上面示例代码中，embed 节点中的内容是直接从 爱奇艺 网站下复制的。做法是：`找到一个视频 > 分享 > 点击向下的箭头（即 更多）> 复制 html 代码` 即可

#### [(2)、audio](http://www.w3school.com.cn/html5/html_5_audio.asp)
* html5 通过 `<audio></audio>` 标签实现音频播放
* **audio 开始和结束标签之间可以嵌入文本，当浏览器不支持该标签时，该文本可以作为提示语。**
* audio 在不同浏览器中的兼容情况如下：

  ![](https://images.gitee.com/uploads/images/2019/0201/085020_935f181f_930142.png)


常用属性如下：

属性|含义
---|---
src | 定义音频文件的路径
autoplay | 自动播放
controls | 显示默认播放控件
loop | 循环播放
preload | 页面初始化时就加载音频，autoplay会覆盖该属性
 
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>audio</title>
</head>
<body>
	<!--使用方式1-->
	<audio src="../assets/audio/皇后大道东.mp3" autoplay="autoplay" controls="controls" loop="loop">
		提示语：您的浏览器不支持audio标签
	</audio>

	<br/>

	<!--使用方式2: 通过 source 定义三种音频格式，从而达到不同浏览器上都能播放的情况-->
	<audio loop controls preload="auto">
		<source src="../assets/audio/皇后大道东.mp3">
		<source src="../assets/audio/皇后大道东.ogg">
		<source src="../assets/audio/皇后大道东.wav">
		提示语：您的浏览器不支持audio标签
	</audio>
</body>
</html>
```
![](https://images.gitee.com/uploads/images/2019/0201/085020_f7847ef9_930142.png)

> 注意，如果 歌曲比较大，那么加载的过程会比较长！！！但是，只要设置了 autoplay ，加载完成后必然会自动播放

#### (3)、video

* html5中使用`<video></video>` 来实现视频的播放
* video 目前支持三种视频格式：ogg、mp4、webM
* video 在各浏览器上的兼容情况如下：

![](https://images.gitee.com/uploads/images/2019/0201/085020_de5f28ef_930142.png)


常用属性如下：

属性 | 含义
---|---
autoplay | 自动播放
controls | 显示默认播放控制
loop | 循环播放
width | 设置播放器宽度
height | 设置播放器高度
 

