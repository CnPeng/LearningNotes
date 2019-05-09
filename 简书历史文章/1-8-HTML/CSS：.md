>文中的元素 即 标签

## 一、CSS初识
CSS（Cascading Style Sheets），通常称为 CSS样式表 或 层叠样式表（级联样式表）。主要用于设置ＨＴＭＬ页面中的文本内容（字体、大小、对齐方式等）、图片的外形（宽高、边框样式、边距等）以及版面的布局等外观显示样式。

## 二、CSS样式规则
使用HTML时需要遵从一定的格式规范。CSS也是有相应的格式，如下：

![](https://upload-images.jianshu.io/upload_images/2551993-e7beece6dff028eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上述规则中：
* 选择器用于指定CSS样式作用的HTML标签，或括号是对该标签的具体样式设置
* 属性和属性值以键值对的形式出现
* 属性是指目标标签的属性，如 字体大小、文本样色等
* 属性和属性值之间使用英文的`:`连接
* 多个键值对之间用英文的`;` 来间隔

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Css01</title>

	<!--指定标签的样式时，需要写在 style 标签中，style 标签又位于 head 标签中-->
	<style>
		p {
			color: deeppink;
		}
	</style>

</head>
<body>
	<p>在段落标签中随便写点东西，然后在head标签中设置p的样式 </p>

</body>
</html>
```
* 效果图如下：

![](https://upload-images.jianshu.io/upload_images/2551993-aceec3549e7cf65e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 三、CSS字体样式属性
### 1、font-size 字号大小
* font-size 用来设置字号，取值可以是相对的长度单位也可以是绝对的长度单位
* 常用的是相对长度单位中的 px

相对长度单位表：
长度单位|含义
---|---
em|相对于当前对象内文本的字体尺寸
px | 像素值，最常用

绝对长度单位表：
长度单位|含义
---|---
in|英尺
cm|厘米
mm|毫米
pt|点

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>font-size</title>
	<style>
		p{
			font-size: 24px;
		}
	</style>
</head>
<body>
	<p>通过 font-size 调整字号为24px</p>
	<div>默认字号大小</div>
</body>
</html>
```

### 2、font-family 字体
* font-family 用来设置字体，网页中常用的字体有 **微软雅黑、宋体、黑体** 等
* 可以同时指定多个字体，中间以逗号间隔，表示如果浏览器不支持第一个字体，则顺位使用后面的字体。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>font-style</title>
	<style>
		p{
			font-size: 16px;
			font-family: "宋体",sans-serif;
		}
		span{
			font-size: 16px;
			font-family: "黑体",serif;
		}
		div{
			font-size: 16px;
		}
	</style>
</head>
<body>
	<p>设置字体为宋体</p>
	<span>设置字体为黑体</span>
	<div>默认字体为微软雅黑</div>
</body>
</html>
```

> **font-size、font-family使用技巧**
>* 目前网页中普片使用 14px+
>* 尽量使用偶数字号，因为部分浏览器中对奇数的支持有BUG
>* 各种字体之间必须使用英文模式下的逗号进行分割
>* 设置字体名称时，如果使用中文则必须添加英文引号，如果使用英文则可以省略引号；如果同时使用英文和中文，则英文必须位于中文之前。
>* 如果字体名称中包含 空格、#、$等符号，则该字体必须加英文状态下的单引号或双引号。如：`font-family:"Time New Roman ";`
>* 尽量使用默认字体，保证在任何用户的浏览器中都能正常显示


### 3、CSS Unicode 字体
在CSS中设置字体名称时，可以直接写中文，但是，部分系统/浏览器中可能不支持中文，解决这个问题有两种方案：
* 直接使用英文字体名称，如 `font-family:"Microsoft YaHei";`
* 使用中文字体名称对应的Unicode编码，Unicode编码在各系统/浏览器中是可以被正常解析的。如：`font-family:"\5B8B\4F53";` ，对应的就是宋体

**字体中文/英文、Unicode名称对照表**
中文名称|英文名称|Unicode
---|---|---
宋体|SimSun|\5B8B\4F53
新宋体|NSimSun|\65B0\5B8B\4F53
黑体|SimHei|\9ED1\4F53
微软雅黑|Micrisoft YaHei|\5FAE\8F6F\96C5\9ED1
楷体_GB2312|KaiTi_GB2312|\6977\4F53_GB2312
隶书|LiSu|\96B6\4E66
幼圆|YouYuan|\5E7C\5706
华文细黑|STXihei|\534E\6587\7EC6\9ED1
细明体|MingLiu|\7EC6\660E\4F53
新细明体|PMingLiu|\65B0\7EC6\660E\4F53

>* 使用的时候考虑到兼容性问题，尽量使用微软雅黑和宋体，其他字体也可能会存在兼容问题所以，
>* 另外，尽量使用 Unicode 来定义字体名称；

### 4、font-weight 字体粗细
字体加粗除了 <b> <strong> 之外，也可以使用CSS中的` font-weight` 来实现，但是CSS是没有语义的
* font-weight 用来定义字体的粗细，取值有：normal、bold、bolder、lighter、100-900（100的整数倍）
* 数字 400 等价于 normal , 700 等价于 bold

### 5、font-style 字体样式
字体倾斜除了用 i 、em 标签外也可以使用CSS实现，CSS实现时是没有语义的。

* font-style 用来设置字体样式，如：斜体、倾斜、正常
* font-style 的取值有：normal——正常字体，italic——斜体，oblique——倾斜
* italic 是应用字体中自带的倾斜效果；oblique 是字体中不自带倾斜效果，人为的设置为斜体，oblique 基本用不到。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>font-style</title>
	<style>
		span{
			font-style: italic;
		}
		div{
			font-style: oblique;
		}
	</style>
</head>
<body>
	<p>font-style 为normal——正常(默认)</p>
	<span>font-style 为italic——斜体</span>
	<div>font-style 为 oblique——倾斜</div>
</body>
</html>
```

### 6、font 综合设置字体样式（重点） 
* font 属性用于对字体样式进行综合设置，其基本语法格式如下：
`选择器 {font : font-style  font-weight  font-size/line-height font-family;}`
* 使用 font 属性时，必须按照上面语法格式中的顺序书写，**不能更换顺序**，各属性之间以空格隔开
* 不需要设置的属性可以直接省略，但**必须保留 font-size 和 font-family 属性**，否则，font 属性将不生效

 ```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>font</title>
	<style>
		p{
			/* 使用 font 属性：倾斜，加粗，字号，字体*/
			font:italic 700 16px \5B8B\4F53;
		}
	</style>
</head>
<body>
	<p>font的使用：倾斜，加粗，* 字号16px，* 字体-黑体</p>
</body>
</html>
```
上述代码效果图如下：

![](https://upload-images.jianshu.io/upload_images/2551993-38b7b138e7e80fb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 7 、CSS注释
/* 注释内容 */ 同Java中的单行注释
使用方法参考 `6、font` 中的示例代码。

## 四、开发者工具（Chrome）

![](https://upload-images.jianshu.io/upload_images/2551993-f2a6553fe1f0858f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 五、选择器（重点）
要想将CSS样式应用于特定的HTML元素，首先需要先找到该元素。在CSS中，执行这一任务的样式规则被称为 **选择器（选择符）**

### 1、标签选择器（元素选择器）
* 标签选择器是指用HTML标签名称作为选择器，按标签名称进行分类，为页面中某一类标签指定统一的CSS样式。
* 标签选择器最大的优点是能快速为页面汇总同类型的标签统一样式，但这也是它的确定，在同一个页面中无法差异化样式。
* 其基本语法格式如下：
`标签名{属性1 : 属性值1; 属性2 : 属性值2 ; }`

示例代码省略，前面的示例都是这种。

### 2、类选择器
* 类选择器使用"`.`" （英文点号）进行标识，后面紧跟类名，其基本格式如下：
`.类名{属性1: 属性值1; 属性2: 属性值2; }`
* 在标签中调用的时候，使用 `class="类名"` 即可
* 类选择器最大的优势是可以为标签/元素对象定义单独或相同的样式


```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>class</title>
	<style>
		.pink{
			color: deeppink;
		}

		.green{
			color: green;
		}
	</style>
</head>
<body>
	<p class="pink">应用类型选择器——pink</p>
	<p class="green">应用类型选择器——green</p>
</body>
</html>
```
![](https://upload-images.jianshu.io/upload_images/2551993-33cca2de456df132.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>**CSS 中类选择器命名规范**
> * 在定义类名时，优先使用中划线`-` 连接多个单词；尽量避免使用下划线`_`连接(因为JavaScript使用了_,后期使用js后可以直接根据这个中划线区分是js还是css中的变量)
> * 不要使用纯数字命名，不识别！
> * 虽然中文命名能被识别，但是，也尽量不要使用。推荐使用英文字母命名


### 3、多类名选择器
* 多类名选择器，即 class 后面跟多个类名，多类名之间使用空格间隔，格式如下
`class="类名1  类名2"`

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>multiClass</title>
	<style>
		.cus-font{
			font-style: italic; font-size: 20px; font-family: \5B8B\4F53,serif;
		}

		.color-pink{
			color: deeppink;
		}
	</style>
</head>
<body>
	<span class="cus-font color-pink">class后面跟多个类名： class="类名1  类名2"</span><br/>
	<span class="color-pink ">使用一个class修改颜色</span>
</body>
</html>
```
![](https://upload-images.jianshu.io/upload_images/2551993-cc5f3968c2df1d72.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 4、id 选择器
* id 选择器使用 `# ` 进行标识，后面紧跟 id名称，其基本格式如下：
`#id名称 {属性1:属性值1; 属性2: 属性值2 ; }`
* id名称即为 HTMLb标签/元素的 id属性值，大多数HTML标签/元素都可以定义id属性，标签/元素的 id 值是唯一的,  id只能对应一个标签/元素。
* 用法基本和类选择器相同
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>idSelector</title>
	<style>
		#id-one{
			color: deeppink;
			font-weight: 800;
		}

		.cus-font{
			font: 16px \5B8B\4F53;
		}

		.cus-font2{
			font-style: italic;
		}
	</style>
</head>
<body>
	<div id="id-one" class="cus-font cus-font2">一个标签只能有一个id，一个id只能被一个标签使用</div>
	
	<!--下面这一段会报错，因为id不能被重复使用-->
	<!--<div id="id-one">一个标签只能有一个id，一个id只能被一个标签使用</div>-->
</body>
</html>
```
![](https://upload-images.jianshu.io/upload_images/2551993-490eb94eb65d188a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5、id选择器和类选择器的区别
* class 可以被重复使用，一个标签/元素中可以使用多个 class
* id 不能被重复使用，且一个标签/元素 仅能有一个id

### 6、通配符选择器
* 通配符选择器使用`*` 表示，是所有选择器中范围最广的，能匹配页面中所有的元素/标签，其基本语法格式如下：
`* {属性1: 属性值1; 属性2: 属性值2;}`

```
<style>
    * {
        margin : 0 ;
        padding: 0 ;
    }
</style>
```

### 7、伪类选择器
* 伪类选择器用于向某些选择器添加特殊的效果。比如，鼠标悬停到某个选项卡之后该选项卡变色。
* 伪类选择器以**`:`**开头。

>个人理解：所谓伪类选择器，虽然是选择器，但只能应用于个别标签；而类选择器，可应用于所有标签

#### （1）、链接伪类选择器
主要针对超链接标签——<a></a>
名称|含义
---|---
:link | 未访问过的连接（即 默认状态）
:visited | 已经访问过的标签
:hover | 鼠标悬停
:active | 按住不松

>使用的时候顺序尽量不要颠倒，按照 lvha 的顺序。有两种记忆方法：`love hate  `或 `lv 包包 非常 hao`

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>linkSelector</title>
	<style>
		a:link{
			/*初始的链接状态*/
			color: black;
		}

		a:visited{
			/*被访问过的链接状态*/
			color: lightgray;
		}

		a:hover{
			/*鼠标悬停时的链接状态*/
			color: green;
		}

		a:active{
			/*长按不松时链接的状态*/
			color: red;
		}
	</style>
</head>
<body>
	<a href="#">应用于&lt;a&gt;&lt;/a&gt;的伪类选择器</a>
</body>
</html>
```
![](https://upload-images.jianshu.io/upload_images/2551993-f41d248537cece19.gif?imageMogr2/auto-orient/strip)


#### （2）、结构（位置）伪类选择器（CSS3）
* 结构伪类选择器是CSS3的新特性，可能会存在兼容性问题
名称|含义
---|---
:first-child | 选取其父类标签/元素中首个子标签/元素
:last-child | 选取其父类标签/元素中最后一个子标签/元素
:nth-child(n) | 选取其父类标签/元素中第N个子元素/标签，不区分元素/标签类型
:nth-last-child(n) | 选取其父类标签中倒数第N个子标签，不区分标签类型

>* n可以是数字、关键词、公式
>* 直接写 n 表示应用到全部的子标签中
>* 写 odd 表示应用到序号为奇数的子标签；even 表示应用到序号为偶数的子标签
>* 3n 表示应用到序号能被3整除的子标签，n 从0开始计算。 

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>Title</title>
	<style>
		/*li:first-child{*/
			/*color: red;*/
		/*}*/

		/*li:last-child{*/
			/*color: #06e529;*/
		/*}*/

		/*li:nth-child(odd){*/
			/*font-style: italic;*/
			/*color: blue;*/
		/*}*/

		/*li:nth-last-child(3){*/
			/*color: deeppink;*/
		/*}*/

		/*li:nth-child(even){*/
			/*color: #9e2fff;*/
		/*}*/

		li:nth-child(3n){
			color: yellow;
		}

	</style>
</head>
<body>

	<ul>
		<li>无序列表项1</li>
		<li>无序列表项2</li>
		<li>无序列表项3</li>
		<li>无序列表项4</li>
		<li>无序列表项5</li>
		<li>无序列表项6</li>
		<li>无序列表项7</li>
	</ul>

</body>
</html>
```

![](https://upload-images.jianshu.io/upload_images/2551993-a9d58026b1604a3f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### （3）、目标伪类选择器
* `:target` 目标伪类选择器，为被选中的目标标签设置个性化样式。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>targetSelector</title>
	<style>
		:target{
			color: deeppink;
		}
	</style>
</head>
<body>
	<a href="#temp">点击跳转到目标位置</a>
	<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
	<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
	<p>加一段文本，下面有内容的</p>
	<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>
	<br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/><br/>

	<!--因为设置了 target目标选择器，所以，跳转到这里之后，这里的文字会变色-->
	<p id="temp">下面真的有内容</p>
</body>
</html>
```

## 六、CSS外观属性
### 1、color 文本颜色
color 属性属于定义文本的颜色，其取值有如下3种：
* 预定义的颜色值，如 red、green、blue 等
* 十六进制，如 #FF0000等，这种方式比较常用
* RGB 形式，如：`rgb(255,0,0) `、`rgb(100%,0%,0%)`

> 注意：使用RGB中的百分比形式时，取值为0时也不能省略百分号，必须写成 %0

### 2、line-height 行间距
行间距也被叫做行高，取值有如下三种：
* 像素 px，比较常用
* 相对值 em
* 百分比 %

### 3、text-align 水平对齐方式
用于设置文本内容的水平对齐方式，相当于html中的 align 对齐属性，取值为：
* left 左对齐（默认）
* right 右对齐
* center 居中对齐

### 4、text-indent 首行缩进
* 用于设置首行文本的缩进，
* 取值可以使不同单位的数值、em字符宽度的倍数、或相对于浏览器窗口宽度的百分比%。
* 允许使用负值
* 通常使用 em 作为设置单位

> em 表示一个字的宽度。如果是汉字，则表示一个汉字的宽度。所以，对于中文段落通常设置 `text-indent:2em`

### 5、letter-spacing 字间距
* 取值可以是不同单位的数值，允许使用负值，默认为 normal 

### 6、word-spacing 单词间距
* 只对英文单词有效，对中文字符无效
* 取值 同 letter-spacing

### 7、颜色半透明（CSS3）
在CSS3中文字颜色可以带有透明度，基本格式如下：
`color:rgba(r,g,b,a)` 

```
/*透明度为百分之30的黑色*/
color:rgba(0,0,0,0.3)
```

### 8、文字阴影（CSS3）
* `text-shadow` 可以给文字设置阴影效果，基本格式为：
` text-shadow: 水平位置 垂直位置 模糊距离 阴影颜色; `

属性|含义
---|---
h-shadow | 必选，水平阴影的位置，可以为负值
v-shadow | 必选，垂直阴影的位置，可以为负值
blur | 可选，模糊的距离
color | 可选，阴影的颜色

### 9、综合案例

>下面代码中 `.类名:hover` 作为选择器使用时，`.类名` 就类似于对应标签的唯一id，其他标签中最好就不要再使用该类名，否则，对应的效果会应用于这些相同类名的标签中。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>综合案例</title>

	<style>
		h2{
			text-align: center;
		}

		div{
			font-size: 14px;
			text-align: center;
		}

		.author{
			color: pink;
		}

		.viewer{
			color: deeppink;
		}

		.bookmark:hover{
			/*这里直接使用 类名作为伪链接选择器的 标记。在之前的代码中，都是直接以标签名作为标记，但那样会改变页面中的全部*/
			color: #e225dc;
		}
		.bookmark:active{
			color: red;
		}

		p{
			color: #3e3e3e;
			font-size: 14px;
			font-weight: normal;
			line-height: 22px;
			text-indent: 2em;
			letter-spacing: 1px;
		}

		.daminghu{
			font-style: normal;
			font-weight: bolder;
		}

		.jinan{
			color: blue;
		}

	</style>
</head>
<body>

	<h2>大明湖</h2>
	<div>
		2018年8月24日 星期五
		<span class="author">CnPeng</span>
		（ <a href="#" class="viewer">XX人</a>已查看）
		<a href="#" class="bookmark">收藏本文</a>
	</div>
	<hr/>
	<p>

	<em class="daminghu">大明湖</em>，位于 <a href="#" class="jinan"> 山东省济南市</a>市中心偏东北处、旧城区北部，是由济南众多泉水汇流而成，湖面58公顷，公园面积103.4公顷，平均水深2米左右，最深处达4.5米，是繁华都市中一处难得的天然湖泊，与趵突泉、千佛山并称为济南三大名胜，也是泉城济南重要的风景名胜、开放窗口和闻名中外的旅游胜地，素有“泉城明珠”的美誉。
	</p>

	<p class="bookmark">使用了与超链接重名的类名，所以悬停时 与 "收藏文本" 具有相同效果。但初始状态依旧是默认的黑色。另外，p 标签本身不具有点击事件，所以，不响应 active 伪链接选择器</p>

</body>
</html>
```
![](https://upload-images.jianshu.io/upload_images/2551993-589088421659dde7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##七、sublime/webstorm快捷键
经过实践，下列快捷键同时适用于 sublime 和 webstorm
快捷键|含义
---|---
输入标签名，然后按TAB  | 快速生成标签
标签名 * 3 ，然后按TAB  | 同时生成三个该标签
父标签 > 子标签 , 然后按TAB | 同时生成父标签和子标签
A标签 + B 标签，然后按TAB | 同时生成具有平级关系的A标签和B标签
.类名, 然后按TAB | 生成 class="类名" 的 div 标签
#id名称，然后按TAB | 生成 id="id名称" 的 div 标签

> * 大于号 &gt; ，用来连接具有父子关系的标签
> * 加号 + ，用来连接具有平级关系的标签
> * 英文字符 **`.`**,  用来表示类名
> * 英文字符`#` , 用来表示id

```
/*生成类名分别为 nav、header、main 的 三个div ,其中，main 中又包含 两个类名分别为 left 、right 的div*/
.nav+.header+.main>.left+.right
``` 

## 八、引入CSS样式表（即CSS的书写位置）
CSS样式并不一定非要写到html文件中。

### 1、内部样式（内嵌式）
* 内嵌式是将CSS代码集中写在HTML文档的 head 标签中，并且用<style></style> 标签包裹，

基本格式如下：

```
<head>
	<meta charset="UTF-8">
	<style>
		选择器{
			属性1:属性值1; 属性2:属性值2;
		}
	</style>
</head>
```
前面的示例都是这一种！

> 实际使用的时候，可以将 <style></style> 放置在html文件的任何地方，甚至可以和<html></html>平级，但是，通常都会放到 <head></head>内

### 2、行内式（内联样式）
* 内联样式，又称为 行内样式、行间样式。
* 是通过标签的 style 属性来设置元素的样式
* 行内样式只对其所在的标签及其子标签有效

基本语法格式：
```
<标签名 style="属性1: 属性值1;  属性2: 属性值2; "> 内容 </标签名>
```
示例：
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
</head>
<body>
	<div style="color: red"> 这是行内样式</div>
</body>
</html>
```

### 3、外部样式（外链式）
* 外链式是将所有的样式放在一个或多个以 `.css` 为扩展名的文件中，然后通过<link /> 标签将 .css 文件与 .html 相关联。
* <link /> 标签需要写在<head></head>标签中

格式如下：

```
<head>
        <link href="CSS文件的路径"  type="text/CSS" rel=“stylesheet”/>
</head>
```

属性|含义
---|---
href | 被关联的文件的路径，可以是相对路径/绝对路径
type | 被关联的文件的类型，此处是用 text/CSS
rel | 与被关联文件的关系，此处指定为 `stylesheet`，表示被链接的文档是一个样式表文件

### 4、三种样式表总结
样式表 | 优点 | 缺点 | 使用情况 | 控制范围
---|---|---|---|---
行内样式 | 书写方便，权重高 | 没有实现样式和结构的分离 | 使用较少 | 控制一个标签及其子标签
内嵌样式 | 实现了样式和结构的部分分离 | 没有彻底分离 | 较多 | 控制一个页面
外部样式 | 实现了样式和结构的完全分离 | 需要通过 link 引入 | 最多，并推荐使用 | 可以控制任意被关联的html文件

> 个人理解：第三种外部样式，体现了 MVP 模式。

三种样式的综合示例代码：
```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<link href="../01.commonCss.css" rel="stylesheet" type="text/css"/>
	<style>
		p{
			color: blue;
		}
	</style>
</head>
<body>
	<div style="color: red"> 这是行内样式</div>
	<p>这是内嵌样式</p>
    <span>这里使用了外部样式</span>
</body>
</html>
```
01.commonCss.css中的内容：
```
/* 在.css 中编写样式的时候，直接按照下面的样式编写就好了*/
span{
    color: deeppink;
}
```

## 九、标签显示模式（display）
html标签通常分为：块标签、行内标签
### 1、块级元素（block-level）

#### （1）、块级元素有哪些？
* 每个 **块级元素通常都会单独占据一行或多行，可以为其设置：宽高、对齐等属性**，常用于网页布局和网页结构的搭建。

* 常用的块级元素有：`h1~h6、p、div、ul、ol、li `等，其中，div 是最典型的块元素

#### (2)、块级元素的特点
块级元素的特点：
* 总是从新的一行开始
* 高度、行高、内边距、外边距都可以调整
* 宽度默认是容器的百分百
* 可以容纳内联元素和其他块元素

### 2、行内元素（inline-level）
#### （1）、行内元素有哪些？
* 行内元素又叫内联元素，不占有独立的区域，仅靠自身的字体大小和图像尺寸来支撑结构，一般不可以设置宽高、对齐方式等内容。常用于控制页面中的文本样式。
* 常见的行内元素有：`a、strong、b、em、i、del、s、ins、u、span`等，其中，span 是最典型的行内元素。

#### (2)、行内元素的特点
* 自身不具有换行效果，和相邻的行内元素在同一行
* 不能设置宽高，设置也无效。可以设置水平方向上的padding 、margin，垂直方向的无效
* 默认宽度就是它包裹内容的宽度
* 行内元素只能容纳文本或其他行内元素（a 比较特殊）

>**注意**
>* 只有文字才能组成段落，所以 p 标签中不能放块级元素，同理，h1~h6、dt 也都是文字类块级元素，内部不能再嵌套其他块级元素。
>* 链接里面不能嵌套链接


#### (3)、块级元素和行内元素的区别
参考上面两者的特点即可。

### 3、行内块元素（inline-block）
在行内元素中有几个特殊的标签 `img、input、td`，可以给它们设置宽高和对齐属性，这几个元素就被称作 行内块元素。

####（1）、行内块元素的特点
* 和相邻行内元素（行内块）在一行，但是之间会有空白缝隙
* 默认宽度就是他本身内容的宽度
* 高度、行高、外边距、内边距都可以控制。

### 4、标签显示模式转换（display）
属性:取值|含义
---|---
display:inline | 块转行内
display:block | 行内转块
display:inlin-block | 块/行内元素转换为行内块

还有更多属性，后续再学习。

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>styleConvert3</title>
	<style>
		a{
			font-size: 14px;
			background-color: pink;
			width: 300px;
			height: 60px;

			/*只有带有 block 才可以设置宽高。行内块的特点是：不单独占据一行，可以设置宽度*/
			display: inline-block;
			/*display: block;*/
		}

		p{
			background-color: deeppink;
			/*块级元素转换为行内元素*/
			display: inline;
		}

		span{
			background-color: aquamarine;
			display: block;
		}
	</style>
</head>
<body>
	<a>超链接a 是一个行内元素,此处转为 行内块元素</a>
	<a>超链接a 是一个行内元素,此处转为 行内块元素</a>
	<a>超链接a 是一个行内元素,此处转为 行内块元素</a>

	<p>p 是块级元素，默认占据一行，此处转为行内元素</p>
	<p>p 是块级元素，默认占据一行，此处转为行内元素</p>
	<p>p 是块级元素，默认占据一行，此处转为行内元素</p>

	<span>span是行内元素，默认包裹内容，此处转为块元素</span>
	<span>span是行内元素，默认包裹内容，此处转为块元素</span>
	<span>span是行内元素，默认包裹内容，此处转为块元素</span>
</body>
</html>
```

![](https://upload-images.jianshu.io/upload_images/2551993-6e516bdaa70105a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 十、CSS复合选择器
复合选择器是两个或多个基础选择器，通过不同的方式组合而成的。目的是为了可以选择更准确更精细的目标元素标签。

### 1、交集选择器
* 交集选择器是由两个选择器构成，其中第一个为标签选择器，第二个为 class 选择器，两个选择器之间不能有空格。如：`h1.name`

基本格式为：
`标签名.类名 {属性1：属性值1；属性2：属性值2}`
如：
`p.one {color : red}`
含义：
将类名为one的P标签内容设置为red颜色

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>17、交集选择器</title>
	<style>
		p.firstP{
			color: red;
			/*font 中包含：倾斜，加粗，字号，字体。中间用空格间隔，并且必须有字号和字体*/
			font: 14px \5B8B\4F53;
		}
	</style>
</head>
<body>
	<p class="firstP">
		这是第一行，应用了交集选择器样式。
	</p>
	<p>
		这是第二行
	</p>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/2551993-a5b067b8b7d7e2ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2、并集选择器

* 并集选择器（CSS选择器分组）是将各个选择器通过 **逗号** 链接而成的。
* 任何形式的选择器（包括标签选择器、class类选择器、id选择器等）都可以作为并集选择器的一部分。
* 如果某些选择器定义的样式完全一致或部分一致，就可以利用并集选择器为他们定义相同的CSS样式

基本格式：
`选择器A , 选择器B {属性1：属性值1；属性2：属性值2}`
如：
`.one , p , #test { color : red } `
含义：
将类名为one的标签、P标签、id为test的标签设置颜色为red

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>18.并集选择器</title>
	<style>
		p.firstP,span,#firstDiv{
			background-color: cornsilk;
			color: red;
		}
	</style>
</head>
<body>
	<p class="firstP">
		第一行是一个P标签
	</p>
	<p>
		第二行也是一个P标签
	</p>
	<div id="firstDiv">
		第二行是一个div标签
	</div>
	<span>
		第三行是一个span标签
	</span>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/2551993-453b660e83bfce5d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3、后代选择器

* 后代选择器又称为包含选择器，用来选择元素或元素组的后代。
* 其写法是把 **外层标签写在前面，内层标签写在后面，中间用空格分隔**。
* 当标签发生嵌套时，内层标签就称为外层标签的后代；内层标签可以是儿子级标签，也可以是孙子级标签

基本格式：
`外层标签  内层标签 { 属性1：属性值1；属性2：属性值2}`
如：
`div h3 {color : red}`
含义：
将div中的h3标签内容设置为红色

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>19.后代选择器</title>
	<style>
		div h3{
			/* 后代选择器——外层标签 内层标签之间用空格间隔。可应用到子、孙、重孙等无限后代*/
			color: red;
		}
	</style>
</head>
<body>
	<div>
		<h3>这是div内部的三号标题</h3>
		<div>
			<h3>这是嵌套了两层div的三号标题</h3>
		</div>
	</div>
	<h3>这是div外部的三号标题</h3>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/2551993-a764a6f7cc5fa171.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


### 4、子元素选择器

* 只能选择作为某元素子元素的元素
* 其写法是把父级标签写在前面，子级标签卸载后面，中间跟一个 **>** 链接
* 注意：**> 左右两端需要各保留一个空格**
* 注意：这里的子标签，指被父级标签包含的第一级标签——即 儿子标签，孙子类标签不算。

基本格式：
`父标签 > 子标签 {属性1：属性值1；属性2：属性值2}`
如：
`.demo > h3 { color : red}`
含义：
为类名为demo的儿子级标签h3设置颜色为red

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>20.子元素选择器</title>
	<style>
		ul > li {
			color: red;
		}

		ul > li > ol > li {
			color: blue;
		}
	</style>
</head>
<body>
	<ul>
		<li>无序列表第一行</li>
		<li>无序列表第二行</li>
		<li>
			<ol>
				<li>无序列表第三行嵌套的有序列表第一行</li>
				<li>无序列表第三行嵌套的有序列表第二行</li>
			</ol>
		</li>
	</ul>
</body>
</html>
```
![image.png](https://upload-images.jianshu.io/upload_images/2551993-19a3c38fb13618dc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 5、测试题

```
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>21.选择器练习题</title>
</head>
<body>
	<!--主导航栏-->
	<div class="nav">
		<ul>
			<li><a href="#">公司首页</a> </li>
			<li><a href="#">公司简介</a> </li>
			<li><a href="#">公司产品</a> </li>
			<li>
				<a href="#">联系我们</a>
				<ul>
					<li>
						<a href="#">公司邮箱</a>
						<a href="#">公司电话</a>
					</li>
				</ul>
			</li>
		</ul>
	</div>

	<!--侧导航栏-->
	<div class="siteNav">
		<div class="siteL">左侧导航栏</div>
		<div class="siteR">
			<a href="#">登录</a>
		</div>
	</div>

</body>
</html>
```
要求：
* 不修改body中代码
* 登录链接的颜色变为红色，同时主导航栏中所有的连接改为蓝色
* 主导航栏和侧导航栏里面文字都是14px并且样式为微软雅黑
* 主导航栏中一级菜单链接文字颜色为绿色

```
<style>
		.siteR > a{
			/*使用子类选择器实现登录变红色。也可以用后代选择器*/
			color: red;
		}

		.nav ul li a {
			color: aqua;
		}
		.nav,.siteNav{
			/*主导航栏和侧导航栏都改为14px 且为微软雅黑字体*/
			font: 14px \5B8B\4F53;
		}

		.nav > ul > li > a {
			color: green;
		}

	</style>
```

### 6、属性选择器
* 用来选取带有某些指定属性的标签
* 属性选择器用 `[ ]` 表示

选择器格式|含义|示例
---|---|---
E[attr] | 选取带有attr属性的E标签|a[title]
E[attr=val] | 选取带有attr属性，且属性值为val的E标签 | a[title=哈哈哈]
E[attr*=val] | 选取带有attr属性，且属性值中包含val的E标签| a[title*=哈]
E[attr^=val] | 选取带有attr属性，且属性值以val开头的E标签 | a[title^=哈]
E[attr$=val] | 选取带有attr属性，且属性值以val结尾的E标签 | a[title$=哈]

```
<style>
    a[title]{
        color:red;
    }

    a[title=hahha]{
        color:red;
    }
</style>
```

### 7、伪元素选择器（Css3）

格式|含义
---|---
E::first-letter | 文本的第一个单词或字（英文是单词，中文/韩文等是字）
E::first-line  | 文本第一行
E::selection  | 可以改变选中文本的样式
E::before | 在E元素内部的开始位置创建一个元素，该元素为行内元素，且必须结合content属性使用
E::after | 在E元素内部的结束位置创建一个元素，该元素为行内元素，且必须结合content属性使用

>E:after、E:before 在旧版本里是伪元素。Css3的规范中 **`:`** 用来表示伪类，**`::`** 用来表示伪元素。但是，在高版本浏览器下，E:after、E:before 会被自动识别为 E::before、E::after , 这样做的目的是为了做兼容。
```
<style>
    p::first-letter{
        /*改变文本的第一个单词/字的样式*/
        font-size:20px;
        color:hotpink;
    }

    p::first-line{
        /*改变第一行文本的样式*/
        color:skyblue;
    }

    p::selection{
        /*改变被选中文本的样式*/
        color:orange;
    }

    div::before{
        /*在div包裹的内容前面插入文本"哈哈"*/
        content:"哈哈";
    }

    div::after{
        /*在div包裹的内容末尾追加文本"结束"*/
        after:"结束";
    }
</style>
```

## 十一、CSS书写规范

### 1、空格规范
V18

### 2、选择器规范

### 3、属性规范



## 十二、CSS背景（background）
### 1、背景图片（image）

### 2、背景平铺（repeat）

### 3、背景位置（position）

### 4、背景附着

### 5、背景简写

### 6、背景透明（CSS3）

### 7、背景缩放（CSS3）

### 8、多背景（CSS3）

### 9、凹凸文字


## 十三、CSS三大特性

### 1、CSS层叠性

### 2、CSS继承性

### 3、CSS优先级

### 4、CSS特殊性（Specificity）


## 十四、盒子模型(CSS 重点)

### 1、网页布局的本质

### 2、盒子模型（Box Model）

### 3、盒子边框（border）

