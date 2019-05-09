**文中内容主要是介绍如何通过拖拽模式实现view界面，所以相关属性请自行打开text模式查看**

本文基于[https://developer.android.google.cn/training/constraint-layout/index.html](https://developer.android.google.cn/training/constraint-layout/index.html)总结而成。

##一、ConstraintLayout及其特性
- 和 LinearLayout、RelativeLayout等一样，都是继承自ViewGroup,是view容器
- 能够用较少的视图层级创建出复杂的视图。
- 与RelativeLayout 类似，view 的摆放位置取决于  view 之间 或者 view 与 parent 之间的相对位置
- 可以完全的通过拖拽实现某个页面 (很方便，但是也需要了解如何手动编写xml)
- ConstraintLayout 最低兼容到 API 9 (即Android 2.3)
>关于 ConstraintLayout 的性能优势，请参考：[解析ConstraintLayout的性能优势](https://mp.weixin.qq.com/s?__biz=MzAwODY4OTk2Mg==&mid=2652044589&idx=1&sn=36f09ada2b279b0c56fcd91085ebe93a&chksm=808d5d68b7fad47e4de2704b24e51fd57799d19f1f7b334aaa9bfa2671c34ca8cc6bcd493882&scene=38#wechat_redirect)

##二、ConstraintLayout引入和ConstraintLayout布局文件的生成

###1、如何引入ConstraintLayout
#### 方法 （1）:
最简单，最直接的方式是，切换到 xml 的 design 视图模式下，然后在左上角的 Layouts 中直接 双击 ConstraintLayout , 然后就会弹窗提示是否添加到依赖，点击 ok，然后坐等依赖成功即可。  
![最简单最直接的添加依赖方式](http://upload-images.jianshu.io/upload_images/2551993-263b3da9220e58ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### 方法 （2）：
在当前Modle 的 build.gradle 中添加如下内容：
>dependencies {
    compile 'com.android.support.constraint:constraint-layout:1.0.2'
}

###2、将已有的布局文件转换为ConstraintLayout
直接看图就好了
![image.png](http://upload-images.jianshu.io/upload_images/2551993-d6b008ca8ccacca4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

###3、新建根节点为 ConstraintLayout 的xml文件
这个就不说了，直接新建一个xml 文件，将其根节点手动指定 为 ConstraintLayout即可。

## 三、ConstraintLayout 的基本使用
###1、添加约束

**约束就是用来控制view的相对位置的参数信息，其含义类似于RelativeLayout中的 layout_below 、layout_up等**

![拖拽view到编辑区并添加约束](http://upload-images.jianshu.io/upload_images/2551993-2599e21f5eb2d035.gif?imageMogr2/auto-orient/strip)

 
在上图中，我们将某个view从 Palette 面板中拖到编辑区域之后，会在该 view 周边生成一个边线，四个角上是白色填充的小方块，四条边线中间是空心圆圈，底部还有两个按钮。各自的作用分别如下：

名称/图标|功能
--|--
小方块|用来调整view的大小
小圆圈|是用来添加约束的锚点(或者叫 handle 把手，如果是**被连接称锚点**，如果是**发出连接称把手**)，还没有添加约束时光标放上去会变绿，已经有了约束后光标放上去会变红色
![](http://upload-images.jianshu.io/upload_images/2551993-ab262919faaaee88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|删除约束条件
![](http://upload-images.jianshu.io/upload_images/2551993-4bb54575309bffe5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|添加基线（BaseLine）的约束条件

点击该view，让view周边显示出小方块和小圆圈，点击小圆圈按住不松手（此时该小圆圈被称为`把手`），然后拖拽到另一个锚点位置后松手, 该锚点可以是另一个view的小圆圈，也可以是parent 的布局边界，也可以是 guideLine ( guideLine 后面会有介绍 )。松手之后，就创建好了一个约束条件，该约束条件会有一个默认的8dp 的margin 值。

###2、使用须知 (注意事项) :
####（1）:
将 view 添加到 ConstraintLayout 之后，**至少需要给该view分别在 X 轴和Y轴上各定义一个约束条件**。
>这些约束条件可以是相对于其他view的，也可以是相对于parent的，甚至是相对于其他 invisiable 的view。这些约束条件用来确定该view在X轴和Y轴的位置，所以至少需要分别在 X 轴 和 Y 轴定义一个约束条件，当然，实际使用的时候，可能会定义许多个约束条件。

#### （2）:
我们将view拖到 LayoutEditor(布局编辑器)之后，**如果未指定约束条件**，那么在编辑器预览界面中该view会处于我们放置的位置，但是，**实际部署运行之后会居于界面左上角**,也就是从 [0,0] 坐标点开始绘制。

#### （3）：
相连接的锚点和把手必须在一个平面中，垂直平面的锚点只能和垂直平面的把手互连
>也就是说，A 的 左边界或者右边界中的锚点只能连接到 B 的左边界或者右边界的锚点，不能连接到B的上边界或下边界锚点；BaseLine 的锚点也只能连接到另一个View的BaseLine 锚点。原文如下：
`You can create constraints only between a constraint handle and an anchor point that share the same plane. So a vertical plane (the left and right sides) of a view can be constrained only to another vertical plane; and baselines can constrain only to other baselines.`

#### （4）：
每个把手只能用来创建一个约束条件，但是一个锚点却可以连接到不同view的把手上。

>原文如下：
`Each constraint handle can be used for just one constraint, but you can create multiple constraints (from different views) to the same anchor point.`

### 3、移除约束

![singleRefresh.gif](http://upload-images.jianshu.io/upload_images/2551993-454ae2d7edacd98f.gif?imageMogr2/auto-orient/strip)

通过上图我们可以总结出移除约束的三种方式：

方式|作用
--|--
点击把手(发起连接的小圆圈)|移除该把手创建的约束（光标放上去之后会变红，然后单击即可）
![](http://upload-images.jianshu.io/upload_images/2551993-ab262919faaaee88.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|点击该图标，删除该view的全部删除约束条件
![](http://upload-images.jianshu.io/upload_images/2551993-392c2ba5dcfdb1ef.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)|点击工具栏中的该图标，删除当前整个布局中的全部view的全部约束条件（右击view，在右键菜单中也有该图标）

###4、约束条件的类型
####（1）、相对于父布局的约束（Parent position）

把手与父布局的边界相连接，父布局的边界作为锚点
![Parent position](http://upload-images.jianshu.io/upload_images/2551993-848625cc7cf170bf.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）、序列式约束（Order Position）
view 与 view 之间的位置约束，把手与其他view相连接，其他view的把手作为锚点。
![Order Position](http://upload-images.jianshu.io/upload_images/2551993-a0273f3afc114a38.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（3）、对齐式约束（Alignment）
A 的边线或中间线与 B的边线或中间线对齐（遵守上面注意事项中的3）
如下图，分为两种，一种是不带margin的对齐，对应下图中的左半部分；一种是待margin的对齐，对应下图中的有半部分
![Alignment](http://upload-images.jianshu.io/upload_images/2551993-20ed9f9f74177495.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
>如果按照边线区分的话，可以分为上边线对齐，下边线对齐，左边线对齐，右边线对齐，中间线对齐

####（4）、基线对齐式约束(BaseLine Alignment)
基线与基线连接形成的约束。
![BaseLine Alignment](http://upload-images.jianshu.io/upload_images/2551993-57f6e6b31249288a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（5）、辅助线约束（Constrain to a guideline）
基于辅助线的约束条件，把手连接到辅助线
![Constrain to a guideline](http://upload-images.jianshu.io/upload_images/2551993-dbc82b250c55ae94.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

######1）：辅助线及辅助线约束的创建
![guideLine的创建方式及部分按钮的作用](http://upload-images.jianshu.io/upload_images/2551993-a0e7f208530b6bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

GuideLine 是编辑布局时的一条辅助线，可以是水平的，也可以是垂直的。该辅助线只在代码中对程序猿可见，app部署之后在页面中是看不到该线的。

如上图，我们点击工具栏中的图标之后，就可以选择创建一个水平或者垂直的 guideLine。

![guideLine的添加和使用.gif](http://upload-images.jianshu.io/upload_images/2551993-25662bd392c01815.gif?imageMogr2/auto-orient/strip)

上图中，我们创建了一个垂直的guideLine ,然后以它为锚点为两个view添加了约束。 

######2）：辅助线的摆放模式及摆放模式的更改
在上一个图中，我们会看到创建完 guideLine后， 顶部出现一个小圆圈包含一个向左的小箭头，表示guideLine 的位置是相对于父布局的左边界多少 dp；点击该小圆点，箭头变成向右的，表示此时guideLine 的位置是相对于父布局右边界多少dp;再次点击小圆圈，箭头换成了 % ，表示 guideLine 的 X 轴坐标占父布局的百分比。这三种情况分别对应 guideLine 的三个属性值，具体如下：

> * **layout_constraintGuide_begin**     `相对于起始位置定位，取值 dp`
指定 guideLine 到父布局的左边界或上边界的具体距离
>
>* **layout_constraintGuide_end**  `相对于终止位置定位，取值 dp`
指定 guideLine 到父布局的右边界或下边界的具体距离
>
>* **layout_constraintGuide_percent**  `百分比位置定位 取值 float （0，1）`
>指定 guideLine 占宽度或高度的百分比距离


**注意**
创建了 guideLine 之后，拖拽的时候，如果左侧或者顶部并没有那个小圆圈，那么我们该怎么去切换 guideLine 的摆放模式呢？

其实很简单，我们上面已经知道了三种摆放模式 对应 guideLine 的三个属性，所以，我们直接从 xml 代码中手动修改模式，然后再回到 design 模式中拖拽到期望的位置即可。
代码如下：
``` 
<android.support.constraint.Guideline
        android:id="@+id/guideline"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:orientation="vertical"
        app:layout_constraintGuide_percent="0.56"/>
```
` 这里切换 guideLine 摆放模式的时候有点坑啊，昨天写到这里的时候，在MAC上死活没有顶部的小圆圈，各种百度谷歌都没找到解决方案，然后查看文档的时候发现有那么三个属性，然后机智如我的手动改代码。今天想着再看看，MAC上还是没有，然后动图也只好在 windows 上去做。but ,我刚做完动图， 就在现在，在此刻，now , MAC 上顶端的小圆圈又有了。。。。 `

####（6）、链条式约束（chain）

请看下面的第七大知识点。





##四、调整约束偏移率（Adjust the constraint bias）
所谓的偏移率可以理解成距离父布局左边框的距离占父布局宽度的百分比，约束的偏移率可以用来调整view的位置。

**如果我们给某个view的左边框和右边框同时添加了约束，那么在properties面板中就会出现 调整水平bias的拖动条（垂直方向上同理） ，bias 调整条的使用有如下两种情况：**

>下面所说的view的大小指的是view的 width 或者 height 的取值。
>在 ConstraintLayout 中，view 的width 、height 的取值有三种，分别是：
>* 具体数值，如 99dp
>* wrap_content , 包裹内容
>* match_constraint，填满约束区域，**0dp等价于match_constraint**
>
> **这里需要注意：官方文档中指出被ConstraintLayout 包裹的控件不支持 match_parent 的取值，但实际在AS中使用的时候，并没有match_constraint ，所以实际使用时 使用0dp 表示 match_constraint 即可。**
[原文Widgets dimension constraints节点中描述如下：](https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout.html)`Important: MATCH_PARENT is not supported for widgets contained in a ConstraintLayout, though similar behavior can be defined by using MATCH_CONSTRAINT with the corresponding left/right or top/bottom constraints being set to "parent".`



###1、view的大小是 指定大小或者wrapContent的时候

![调整约束偏移率1](http://upload-images.jianshu.io/upload_images/2551993-7c6fd9ba75c63b07.gif?imageMogr2/auto-orient/strip)

如上图，当我们给某个 view 的 **左边和右边（或者上边和下边）都添加了约束**，并且该View 的**大小是具体的值或者 wrapContent**的时候，那么该 view 默认会居中，也就是说 偏移率 bias 是 50%。而且在最右侧的 properties 面板中会显示出 偏移率调整条。我们通过这个调整条就可以调整 view 的偏移率，当然，直接拖动view也可以调整偏移率。

![调整约束偏移率2--注意：bias 的计算是将margin排除之后计算的结果](http://upload-images.jianshu.io/upload_images/2551993-e67b3917a887a2b6.gif?imageMogr2/auto-orient/strip)
在上面这个图中，我们发现，给View 左右两侧都添加约束之后，虽然右侧 properties 面板中偏移率调整条中显示是 50% ，但是view 并没有居中。

这是因为，我们在将view添加到编辑区并添加了左侧和上侧的约束之后，又手动调整了view的位置，让view距离左侧和上侧有一定的距离，这个距离是margin 。而在计算 bias 的时候，被除数（也就是view可用的摆放区域）并不包含margin 值，所以，我们看到虽然bias 是 50 % ，但 view 没有居于父布局的center位置。

###2、view的大小是0dp (match_constraint） 时

![bias_0dp.gif](http://upload-images.jianshu.io/upload_images/2551993-60910a8715b66e1b.gif?imageMogr2/auto-orient/strip)

上图中，我们给button的上边框和下边框都添加了约束，此时，出现了 垂直方向的bias 调整条，然后我们将height 手动设置为0dp，然后我们拖动bias 调整条，我们会发现，此时调整条无效。这是因为：**我们将height 设置为 0dp 之后，view就会填满约束区，也就是说，该view 的父布局在垂直方向上已经没有额外的空间供 该view在垂直方向上移动**

>填满约束区的意思其实就是，填满除margin 之外的区域



## 五、调整view的尺寸大小（Adjust the view size）

### 1、方式一：拖拽调整
![调整view 大小 + 展开和收起properties面板 ](http://upload-images.jianshu.io/upload_images/2551993-cfb9a4a3dec06985.gif?imageMogr2/auto-orient/strip)

我们可以在布局编辑界面中拖动 view四个角上的白色小方块来调整其大小，这种方式属于硬编码模式，不会导致其他view或者屏幕的重新测量。如果我们想使用更多的方式去调整 view 的大小，那么就需要使用 properties 面板了。


### 2、方式二：使用约束调整view的大小

![0dp](http://upload-images.jianshu.io/upload_images/2551993-47324374ffd37cd7.gif?imageMogr2/auto-orient/strip)

使用约束调整view的大小时，只能实现填满约束的情况。如上图，如果我们想让button的宽度填满约束区，那么我们就先给该button的左边和右边都加上约束，然后设置其大小为 0dp 即可。（高度填满约束区同理）

>**注意：**
>* 在上面的动图中，我们看到在button的四周其实还会有一个白色边距，这个边距是创建约束时默认的margin 值-- 8dp ,我们前面已经说过，约束区不包括margin值。（关于如何修改这个margin值，后面会有介绍）
>* 在上面的动图中，拖拽第二个button的时候，设置完约束之后，我们选择了 match_parent ，看上去效果和 0dp 没啥区别，但是官方文档不止一次的强调不能使用 match_parent , 而且也有部分网友反馈使用match_parent 时出错，所以，保守起见，在设置被 constraintLayout 包裹的view的大小时，我们还是用 0dp吧。（具体为啥暂时不清楚，后面有时间了再去源码中翻看一下）

properties面板中包含调整尺寸和约束的工具，以及部分常用属性。

### 3、方式三：使用properties面板中的工具调整尺寸
我们先来看一张图：
![properties 面板中尺寸调整工具图标](http://upload-images.jianshu.io/upload_images/2551993-c32eea49f0bd2492.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图中，各个标识对应的功能分别如下：

标号|功能
--|--
 1 |宽和高的比率（size ratio）
 2 |删除约束（delete constraint）
 3 |宽高模式（height/width mode）：fixed、wrapcontent、0dp
 4 |控件与边界的margin（margins）
 5 | 偏移率（constraint bias）有垂直偏移率和水平偏移率

图中所示 3 宽高模式共有三种，对应的图标与模式分别如下：

图标|对应的宽高模式
--|--
![](http://upload-images.jianshu.io/upload_images/2551993-de9903ad5c34dc54.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |包裹内容（**Wrap Content**）
![](http://upload-images.jianshu.io/upload_images/2551993-f1513bbfa72a2496.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |填充约束区域（**即 0dp、Match Constraints**）填充约束区域的时候会将margin值排除在外。另外，**只有在该模式下才能调整 宽高比率**。
![](http://upload-images.jianshu.io/upload_images/2551993-dfd92e04c11756f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) |指定大小（**Fixed**）

点击上面的几个图标，就可以在三种模式之间任意切换 , 从而实现view尺寸的调整。

>**注意**:  在设置被 ConstraintLayout 包裹的view 的 width 或者 height 的时候千万不要使用 match_parent , 而必须使用 match constraints (也就是 0dp).

### 4、方式四：以比率的形式调整尺寸（Set size as a ratio）
某些时候，我们需要控制某个view的宽是高的 百分之多少，或者 高是宽的百分值多少，之前我们可能会使用 LinearLayout 或者 FrameLayout , 然后使用weight 属性去控制，或者直接使用 固定的dp 值，在或者直接在代码中手动的获取并计算宽高，实际上用这三种方式实现的时候一方面可能会增加布局嵌套，另一方面可能会手写较多的代码，而且，对设计稿的还原度也没那么高。but , 如果使用constraintLayout ，我们只需要轻轻的拖拽几下，然后输入比率值，然后回车，搞定。。。具体请看下面的例子

>**注意：**
**如果我们想使用 ratio 的形式去设置宽高, 那么必须保证 宽高 中 至少有一项使用了 0dp（即  match_constraint）** 
`由于0dp 就表示 match_constraint ,所以之后的内容中不在引用 match_parent字样，统一用 0dp 表示`

####（1）、宽度是0dp（宽度是 match_constraint）

![宽度是0dp](http://upload-images.jianshu.io/upload_images/2551993-e2d6703c57c07dcd.gif?imageMogr2/auto-orient/strip)

在上图中,我们拖动一个 button 到编辑区，先添加约束，此时默认的宽高都是 wrap_content , 我们会看到 properties 面板中的方形区域 左上角 并没有三角符号，然后我们点击 方形区域 中的 宽高模式图标，手动将宽度切换为 0dp(即 match_constraint) 。 此时，我们会发现，方形区域右上角出现一个 空心的三角区域，然后我们点击一下，就变成了实心的三角，并且方形区域右下方会出现 ratio 字样 和 一个 输入框，我们直接在输入框中 输入 `宽：高 ` 的比率并回车，这样就实现了  宽 和 高 的比率设置。

此外，在上图中我们发现，当点击空心三角，启用 比率设置之后，不但空心三角变成了实线三角，而且properties面板中方形区域的左边框和有边框的两个小圆圈（把手）直接使用 直线连通了，**这个连通的直线表示：当前宽度是0dp,宽度的具体尺寸以高度为基准**

####（2）、高度是0dp

![高度是0dp.gif](http://upload-images.jianshu.io/upload_images/2551993-ffad3ae3f97d4f4c.gif?imageMogr2/auto-orient/strip)

在上图中,我们拖动一个 button 到编辑区，添加完约束，手动将宽度切换为 0dp(即 match_constraint) 。 点击 空心的三角区域，启用ratio 设置，并设置了宽高比率。

在上图中，我们发现，而且properties面板中方形区域的垂直方向的两个小圆圈（把手）被直线连通了，表示：当前高度是0dp，高度的具体尺寸以宽度为基准。

>**注意：**
>* **ratio 下方的输入框中，格式是固定的即—— 宽度：高度**

####（3）、宽高都是0dp（必看此处！！！）

![singleRefresh.gif](http://upload-images.jianshu.io/upload_images/2551993-3e2f73bbafa8d887.gif?imageMogr2/auto-orient/strip)

如上图，当宽高都是0dp 的时候，我们点击空心三角启用ratio模式后，先采用的是 高度为0dp的模式（即，高度的具体值以宽度为基准）；接着，我们再点击实心三角，此时会切换到 宽度为 0dp 的模式（即，宽度的具体值以高度为基准）；然后，我们再点实心三角，此时已经退出了比率模式，但是ratio和输入框还在，但是输入内容也不会起作用；然后，我们再点实心三角，完全退出比率模式，三角标消失。


##六、调整view的margin值（Adjust the view margins）

###1、批量设置 margin 值

![margin设置工具](http://upload-images.jianshu.io/upload_images/2551993-f6f1b3edf471ba8e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果我们需要给多个view、或者单个view的多个侧边设置一样的margin值，那么我们就可以使用编辑界面工具栏中的 margin 设置工具--也就是上图中被红色框圈出来的数字 8 ，这里的 8 表示：**我们给某个view添加约束之后，默认就会添加一个 8dp 的margin值。**

那么，具体如何添加margin并修改默认margin值呢？请看下面的动图。

![使用工具栏中的工具批量设置margin](http://upload-images.jianshu.io/upload_images/2551993-964d5337f543c30a.gif?imageMogr2/auto-orient/strip)

在上面的动图中，我们可以看到，我们添加约束之后，就会直接带有一个8dp的margin值，并且对应的在properties 面板中也会有margin值的展示。


![修改默认margin值](http://upload-images.jianshu.io/upload_images/2551993-7ce02c4c77e79f37.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

如上图，如果我们需要修改这个默认的margin值，那么就 单击工具栏中的 “8” ，此时会弹出面板，在弹出的面板中有系统预设 的 0、8、16、24 可供选择，也可以直接输入你需要的margin 值。需要注意，单位是dp。而且，修改之后的margin值只对之后添加的view生效，对修改之前已经添加的view不生效。

>补充：为什么预设的margin值都是8的倍数？
预设8dp是基于Material Design 设计理念而设置的，具体参考 [8dp square grid recommendations](https://material.google.com/layout/metrics-keylines.html).

###2、个性化margin值
在上一节中我们知道了如何去批量的设置margin值，那么，如果我们需要调整某个侧边的margin值该怎么做呢？看下图：
![margin.gif](http://upload-images.jianshu.io/upload_images/2551993-5d18eb41616c821e.gif?imageMogr2/auto-orient/strip)

如上图，当我们将光标挪到 properties 面板中方形区域的表示margin的数字上时，数字就变成了一个输入框和一个下拉按钮，点击下拉按钮会有预设的margin值，都是8 的倍数；也可以直接从输入框中输入我们想要的margin值，然后回车，就是这么简单。


##七、链条的使用（Control linear groups with a chain）

###1、链条及其分类
所谓链条，就是一组相互连接的view，这些view之间具有双向的位置约束，如下图：
![水平方向的链条](http://upload-images.jianshu.io/upload_images/2551993-eb7951d77260ca39.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在上图中，A的右边框位置取决于B的左边框位置（A 右边框的把手连接到B左边框的锚点），B的左边框位置也取决于A的右边框（B 左边框的把手连接到A右边框的锚点），这样，A和B就组合成了一个水平的链条。

链条可分为垂直链条和水平链条。

###2、如何创建链条
快速创建链条的方法是，选中需要添加到链条中的view，然后右击，选择
 **Center Horizontally** 或 **Center Vertically**，这样就完成了一个水平或垂直链条的创建。具体如下图：

![水平链条的创建](http://upload-images.jianshu.io/upload_images/2551993-a0f2141e60f2e8de.gif?imageMogr2/auto-orient/strip)

>注意事项：
>* 只有水平排列的view才能通过 center horizontally 创建水平链条（垂直链条同理）
>* 在创建水平链条式，多个view之间的位置可以有高低偏差，但是偏差不能过大，通常是 **后一个view的顶部边框不能低于前一个view的底部边框**，如果超出这个范围将无法创建链条（垂直链条同理）



###3、链条模式
水平链条中view的排列方式有如下几种：
**（垂直链条中模式一致，只不过view的排列变成了垂直的）**
![](http://upload-images.jianshu.io/upload_images/2551993-e65b3596cfeb94aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

**以下模式依次对应上图中的 1、2、3、4**

链条模式|view排列的特点
--|--
**Spread:**| 平均分布view的位置,将空余部分平均分配到view的两侧作为view之间以及view与父布局的间距（**默认模式**）
**Spread inside:**| 第一个view和最后一个view贴边，空余部分被分配给其他view，作为view之间的间距
**Weighted:**| 如果链条模式是 spread 或者 spread inside ,我们也可以设置一个或多个view的大小为 0dp，从而让view占满余下的间距部分。默认情况下，如果有一个view的大小设置了0dp,那么该view会填满空余部分；如果多个view设置了0dp, 那么这几个view就会平均分配空余部分（空余部分会被囊括到View中），但是，我们可以通`layout_constraintHorizontal_weight`和 `layout_constraintVertical_weight` 属性调整这几个view的权重（也叫比重），就像在 LinearLayout中使用 layout_weight 一样。设置权重之后，权重大的所占的区域就多，权重小的所占区域就小。
**Packed:** |多个view被次序排列到一起。在packed模式下，我们可以通过调整链条头的bias来调整链条的bias位置（**链条头也就是链条中的起始位置的view**，链条尾也就是链条中结束位置的view）

在水平链条中，链条头就是最左侧的view；垂直链条中，链条头是最顶端的view。

###4、链条模式的切换
![链条模式的切换](http://upload-images.jianshu.io/upload_images/2551993-8359b0a06032d5b8.gif?imageMogr2/auto-orient/strip)

如上图，创建链条之后，默认spread 模式。如果想切换模式，则选中链条中的任一view， 然后点击view下方的 链条图标，即可实现 **spread**, **spread inside**, 和 **packed** 模式之间的切换。链条图标如下： ![链条图标](http://upload-images.jianshu.io/upload_images/2551993-391ab86eccab68bb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 


###4、链条使用的注意事项

* 一个view在同一时刻既可以是水平链条中的一部分，也可以是垂直链条中的一部分，这个属性能让我们更灵活的创建出一个Grid栅格界面
* 多个view的位置大致在同一个水平轴或者垂直轴的时候，才可以创建出水平或者垂直链条（这一点在如何创建链条中有说明）
* 虽然有水平链条也有垂直链条，但是链条本身并不会对齐它所包含的view，所以必要的时候需要借助 对齐约束（alignment）或者 辅助线约束（guideLine）等实现view对齐



##八、自动创建约束（Automatically create constraints）

###1、推断式约束（Infer constraints)
![推断式约束--Infer constraints](http://upload-images.jianshu.io/upload_images/2551993-401e60896423fe31.gif?imageMogr2/auto-orient/strip)

如上图，除了手动的为每一个view添加约束之外，我们也可以先将view移动到我们期望的位置，然后点击工具栏中的 **Infer Constraints** 图标让AndroidStudio自动帮我们添加约束条件。图标如下： ![Infer Constraints 图标](http://upload-images.jianshu.io/upload_images/2551993-492dff2b719a02fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

推断式约束的大致原理是：检测所有view的摆放位置，然后根据当前的位置为这些view确定出最高效的约束条件。  但是在实际使用的时候，可能还需要我们自己手动的根据不同的屏幕朝向或者屏幕尺寸做出适当的调整。

###2、自动连接式约束（Autoconnect）
Autoconnect 在默认情况下是处于关闭状态的，我们可以通过点击工具栏中的 **Turn on Autoconnect** 图标手动的开启或关闭。图标如下：
 ![Autoconnect图标](http://upload-images.jianshu.io/upload_images/2551993-0dd7da80d610924b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240) 

![创建Autoconnect 自动连接式约束](http://upload-images.jianshu.io/upload_images/2551993-1a8bade86262d948.gif?imageMogr2/auto-orient/strip)

在上图中，Autoconnect 可以手动的开启或者关闭，开启之后会自动为之后添加到 constraintLayout 中的view添加两个或多个约束条件，而对开启之前添加的view不生效。

>此处根据上图我们也发现：
>* **开启 AutoConnect 之后, 只有将view拖到某些位置才会自动创建约束，也就是说，并不一定能为每一个添加进来的view创建约束。**
在上图中，第三个被添加的button虽然是在开启 AutoConnect之后添加的，但是并没有创建任何约束
>* **使用AutoConnect为View创建约束的时候，并不能保证同时在水平和垂直方向上都创建约束**
在上图中，我们第二个被添加的button，是在开启 AutoConnect之后添加的，虽然自动给添加约束，但是我们也看到了，只添加了水平方向的约束，而没有添加垂直方向的约束，此时垂直方向的约束就需要我们手动的去添加。

###3、两种自动创建约束的对比
经过前面的介绍，两者的对比其实很明显了：
>* Infer Constraints 会为所有Constraint 中所包裹的还没有约束的view创建约束；而AutoConnect 只会为开启该功能之后的、在特定位置的view创建约束
>* Infer Constraints 能同时创建垂直和水平的约束；而AutoConnect 可能只会创建单一坐标轴方向上的约束。

所以，我觉的还是用 InferConstraints 更为方便一些。


##九、其他补充内容

###1、工具栏中的图标及其各自含义
![工具栏中的图标及其含义](http://upload-images.jianshu.io/upload_images/2551993-51be023c921268b8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在文中之前部分的介绍中已经介绍过上图中的多个功能按钮了，这里再做一次统一的介绍。

编号|功能
--|--
1|非编辑状态下是否展示约束线。默认非编辑状态不展示，只有选中view要编辑该view时才会展示；点击开启之后，无论是否编辑状态都会展示约束线
2|是否开启 AutoConnect (自动添加约束)，默认关闭
3|删除所有view的全部约束条件
4|点击之后会为ConstraintLayout中未添加约束条件的所有view添加适当的约束条件
5|批量调整view的margin值
6|调整view（或链条）的居中位置，详细的分类参考下面的2。
7|调整view的对齐方式（适用于批量调整多个view的对齐方式），详细分类参考下面的3
8|创建水平或者垂直的 guideLine
9|缩小编辑区的预览界面和蓝本界面
10|放大编辑区的预览界面和蓝本界面
11|错误（或警告）提醒。点击之后会展示出当前布局文件中的有错误或者警告的地方并给出修改建议

###2、居中位置的几种类型及其特点
![view居中位置的几种类型](http://upload-images.jianshu.io/upload_images/2551993-e4e73e6d8590e31b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

类型及特点的对应关系如下：

类型|特点
--|--
Center Horizontally|水平居中；作用于多个被选中的水平排列的view时是创建水平链条
Center Vertically|垂直居中；作用于多个被选中的垂直排列的view时是创建垂直链条
Center Horizontally in Parent|相对于父布局水平居中；作用于多个被选中的水平排列的view时会导致view的居中重叠，并且具有RelativeLayout的效果，后添加的会覆盖先添加的。
Center Vertically in Parent|相对于父布局垂直居中；作用于多个被选中的垂直排列的view时会导致view的居中重叠，并且具有RelativeLayout的效果，后添加的会覆盖先添加的

###3、对齐方式的类型及其特点

![对齐方式的几种类型](http://upload-images.jianshu.io/upload_images/2551993-ea12207c5e1979b4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

类型|特点
--|--
Align Left Edges|左边框对齐
Align Horizontal Centers|水平居中对齐
Align Right Edges|有边框对齐
Align Top Edges|顶部边框对齐
Align Vertical Centers|垂直居中对齐
Align Bottom Edges|底部边框对齐
Align BaseLines| 基线对齐

**注意：只有选中多个view时，才能设置对齐方式**

###4、打包和拉伸（Pack 和 Expand）

![打包和伸展](http://upload-images.jianshu.io/upload_images/2551993-aa8d9d50b49eba16.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

类型|特点
--|--
Pack Horizontally|水平方向打包（应用之后并没有看到有和变化）
Pack Vertically|垂直方向打包（应用之后并没有看到有什么变化）
Expand Horizontally|水平伸展。在伸展的时候，会根据你当前在工具栏中选择的 “Device in Editor ”得到一个绝对的数值并作为该view的宽度
Expand Vertically|垂直伸展。在伸展的时候，会根据你当前在工具栏中选择的“Device in Editor ”得到一个绝对的数据并作为该view的高度。

>* **Pack 实在是没看出有啥效果，如果你有发现请告知，谢谢**
>* Expand Horizontally 会根据你当前view的约束条件的不同，以及当前在水平方向是否有其他view 等得到不同的效果（Expand Vertically同理）

***
下图中圈出来的这个就是 “Device in Editor”

![Device in Editor](http://upload-images.jianshu.io/upload_images/2551993-5ed4b4969d5f715a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



## 十、附录：参考资料
###（一）、官方参考资料
类定义：
>1、[https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout.html](https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout.html)

基本使用：
>1、[https://codelabs.developers.google.com/codelabs/constraint-layout/#0](https://codelabs.developers.google.com/codelabs/constraint-layout/#0)
2、[https://developer.android.google.cn/training/constraint-layout/index.html](https://developer.android.google.cn/training/constraint-layout/index.html)
3、[https://developer.android.google.cn/studio/write/layout-editor.html](https://developer.android.google.cn/studio/write/layout-editor.html)

进阶：
>1、[https://medium.com/google-developers/building-interfaces-with-constraintlayout-3958fa38a9f7](https://medium.com/google-developers/building-interfaces-with-constraintlayout-3958fa38a9f7)

官方示例：
>1、[https://github.com/googlesamples/android-ConstraintLayoutExamples](https://github.com/googlesamples/android-ConstraintLayoutExamples)

### （二）、中文参考资料
>1、[郭霖： Android新特性介绍，ConstraintLayout完全解析](http://blog.csdn.net/guolin_blog/article/details/53122387)
 2、[张旭童：ConstraintLayout 属性详解 和Chain的使用](http://blog.csdn.net/zxt0601/article/details/72683379)
3、 [张旭童：ConstraintLayout 属性详解 和Chain的使用](http://blog.csdn.net/zxt0601/article/details/72683379)
