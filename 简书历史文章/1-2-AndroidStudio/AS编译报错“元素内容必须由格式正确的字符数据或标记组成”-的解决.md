## 1 事件背景
我将自己每个月写的demo都放在一个project中，然后提交到了gitHub上，按照正常的操作流程，本地修改后，先commit 到本地，然后pull 拉取gitHub 的代码，merge 冲突，然后push 将本地修改推送到gitHub。

but ，昨天临下班的时候push完后再次 编译，报错 “元素内容必须由格式正确的字符数据或标记组成”。由于昨晚公司年会，所以就想着年会结束回家之后再研究吧，然而，作为红包绝缘体的我，成功的避开了一二三等奖以及各种大额红包，所以，回家之后就睡了。。。

## 2 问题解决过程
今天早上来到公司，打开电脑，Bug 还是静静的躺在那里。就像下图中标注的一样，左侧的 project 并没有红色下划线提示，“Message ” 标签中只有一句没头脑的错误提示。由于是编译期报错，所以AndroidMonitor中肯定是啥提示也没有的。有点蒙圈，该从哪里下手呢？
>![准确的错误点在哪里？？](http://upload-images.jianshu.io/upload_images/2551993-265e72ab5e2eaf15.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

先回退版本试一下，先  git  log  查看提交历史，然后记录需要会退到的提交历史的 SHA-1 值 ，然后 git reset XXX 回退 , 这里的XXX 就是 SHA-1 值 。（**使用git reset XXX 命令之后，XXX对应的提交历史还在，但它之后提交的内容就没有了。**）然后，回退了许多版本之后再次编译还是一样的错误。

接下来开始google ，开始百度，然后都没有找到合适的答案？那我该咋整呢？要不还是请教同事吧 --   but ,

就在这时候，我忽然想起代码编辑区顶部的那个黄色的提示，也就是下图中圈出来的内容，以前从来没关注过它, 要是在最左侧有 X 的，顺手就 X掉了，但这次一直停靠在那里。
>![黄黄的提示](http://upload-images.jianshu.io/upload_images/2551993-53d0adb34cec0827.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

看看这黄黄的提示，“Gradle 项目同步失败，基本功能（Debug功能）将不会。。。”，也是没啥具体错误信息。然后再看后面三个超链接 “Try Again ” ——再试一次 ，那就点它再试一次，还是报这个错；“Open Messages View” ——打开Messages 标签，里面除了那行错误提示，并没有其他的东西；“Show Log In Finder” ——在Finder 中展示log, 点击之后就跳转到了 AndroidStudio 错误日志文件夹，如下图：
>![错误日志：idea.log](http://upload-images.jianshu.io/upload_images/2551993-d1266c61b1f8a547.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

哎吆，有错误日志，点开看看。吧啦吧啦的一大堆英文，看的脑仁儿疼，直接拉到最后，从后往前看，找 “Casued by ..” 字样，找到了下图中的内容：
>![Sax 解析异常.png](http://upload-images.jianshu.io/upload_images/2551993-315ab25b9a09185a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

从上面的图中，我们知道，之所以报错，是因为Sax解析的时候出错了，错误在 66 行第 2 列，但不知道是哪个文件的 66行第二列，那就继续往上看日志。结果找到下图中的内容：
>![清单文件出错](http://upload-images.jianshu.io/upload_images/2551993-8eb9f0bcf18c1bb8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

上图说，清单文件出错了。。。那就打开看下，直接查看 66行第二列。如下图：
>![错误根源](http://upload-images.jianshu.io/upload_images/2551993-666f07145c1e3e9b.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

打开清单文件后，该出红线的地方就都有了，再看66 行 ，这是啥？这是与gitHub 同步代码的时候产生的冲突，但没有解决！！！然后，果断处理完该冲突，再次编译的时候，就开始提示其他地方还有冲突，然后挨个解决，全部冲突处理完毕，再次编译安装，妥妥的通过了。。。

## 3 总结：
> **A** 
平时项目中，合并完代码之后如果有冲突没解决，都会有提示，直到冲突解决完才可以提交。**但是没明白为什么这次有了冲突之后，在我提交的时候却没有给出提示？如果哪位同学晓得，请留言告知，将感激不尽**

>**B**
代码编写过程中碰到问题的时候，先不要着急问别人，还是先自己尝试各种方式，加上google 加上百度，实在没思路，那就随便点点看看吧。实在实在没有法子没有思路了，再去请教。

>**C**
当出现bug时，静下心来，不要慌，捋捋思路，不要放过任何一个正常情况下不会弹出的提示框。就像 代码编辑器上面那个 黄黄的提示条！！





