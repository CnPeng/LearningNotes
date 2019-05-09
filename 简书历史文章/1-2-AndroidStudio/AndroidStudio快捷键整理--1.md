快捷键整理系列文章地址：
[AndroidStudio快捷键整理--1](http://www.jianshu.com/p/2dfb57962e33)  
[AndroidStudio快捷键整理--2](http://www.jianshu.com/p/8839f27dfda8)
[AndroidStudio快捷键整理--3](http://www.jianshu.com/p/2fc220b1220d)
[AndroidStudio快捷键整理--4](http://www.jianshu.com/p/095fa5668580)
[AndroidStudio快捷键整理--5](http://www.jianshu.com/p/cd79fc1f399d)

```
该系列文章中的内容都是经过实践之后整理出来的.

有一些我也不知道怎么用或者没有达到预期效果的，就直接使用全英文描述或者标注 '我也不知道咋用' 之类的文字。

如果有哪些是我不知道咋用，但是你知道的，请一定要告诉我，O(∩_∩)O谢谢
```


## 1 、 写在前面：
AndroidStudio快捷键整理系列文章主要基于 AndroidStudio--preferences--keymap。

该系列文章中的快捷键基于keymaps选项中的： Mac OSX 10.5+   。具体如下图 

![keymaps配置页面](http://upload-images.jianshu.io/upload_images/2551993-13eadad484325dcb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

该系列文章中主要摘录了keymaps中 Editor Actions , Main menu , Other 三个文件夹的快捷键。（其他文件夹下虽然也有一些快捷键，但是要么就是在前面三个文件夹中已有定义，要么就是日常使用较少，故不再摘录。）

![image.png](http://upload-images.jianshu.io/upload_images/2551993-8e351f8252905b26.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## 2 、 MAC 键盘上的功能键以及对应的符号
对于 MAC 新手来说，在查看 AndroidStudio -- preferences -- keymap 中的快捷键时往往弄不清里面的 各种符号分别对应键盘上的哪个按键，所以，在文章开头，先列出键盘上的几大功能键及其对应的符号。内容摘自 [Apple 支持 ](https://support.apple.com/zh-cn/HT201236), 关于 MAC 本身的操作快捷键这个链接中也一一列出，这里不再赘述。

功能键|对应的符号
---|---
Command |⌘
Shift       | ⇧
Option / alt |⌥
Control |⌃
Caps Lock| ⇪
Fn|没有对应的符号


##3、AndroidStudio 快捷键整理（1）
**注意：以下快捷键均是 MAC OSX 10.5 + 环境下的。 **

```
以下内容对应keymaps 中的 Editor Actions 文件夹，是一些常用的编辑操作
```

功能键组合|作用
---|---
alt + cmd + ]  |  将光标移到当前代码所在 { } 的末尾，并高亮 { }
alt + cmd + [  |  将光标移到当前代码所在 { } 的开始，并高亮 { }
alt + cmd + shift + ]  |  选中光标到当前代码块的  }  之间的内容
alt + cmd + shift + [ |  选中光标到当前代码块的  {  之间的内容
ctrl + k | 剪切光标到当前行尾的内容
cmd + del | 删除一行
cmd + x | 剪切一行
alt + del | 删除光标到光标所在变量名起始位置的内容
fn + alt +del | 删除光标到光标所在变量名结束位置的内容
ctrl + n | 将光标下移 （作用与 向下箭头↓  一致）
shift + ↓  | 选中光标到光标在下一行对应位置的内容，
|再按则选中下一行的整行，重复按的话，效果累计
shift + ↑  | 选中光标到光标在上一行对应位置的内容，
|再按则选中上一行的整行，重复按的话，效果累计
shift + ← | 从光标起逐个字母的向左选中内容
shift + → | 从光标起逐个字母的向右选中内容
cmd + d | 复制一行
ctrl + shift + j | 将光标所在行的下一行内容上移到光标所在行
fn + → | 移动光标到行尾
cmd + → | 移动光标到行尾（功能同上）
ctrl + e | 移动光标到行尾 （功能同上）
shift + cmd + → | 选中光标到当前行尾的内容
shift + fn + → | 选中光标到当前行尾的内容（功能同上）
fn + ← | 移动光标到行首位置
cmd + ← | 移动光标到行首位置（功能同上）
ctrl + a | 移动光标到行首位置 （功能同上）
shift + cmd + ← | 选中光标到当前行首的内容
shift + fn + ← | 选中光标到当前行首的内容（功能同上）
fn + cmd + ↑ | 将光标移动窗口最顶部一行对应的位置
shift + fn + cmd + ↑ | 将光标移动窗口最顶部一行对应的位置并选中内容
fn + cmd + ↓ | 将光标移动窗口最底部一行对应的位置
shift + fn + cmd + ↓ | 将光标移动窗口最底部一行对应的位置并选中内容
alt + → | 移到光标到下一个单词(move caret to next word)
shift + alt + → | 从光标位置向右逐个单词选中内容
alt + ← | 移到光标到上一个单词 (to previous word)
shift + alt + ← | 从光标位置向左逐个单词选中内容
fn + ↓ | 内容过多一屏展示不开时，实现向下滚屏
fn + ↑ | 内容过多一屏展示不开时，实现向上滚屏
shift + fn + ↓ | 内容过多一屏展示不开时，向下滚屏并选中内容
shift + fn + ↑ | 内容过多一屏展示不开时，向上滚屏并选中内容
alt + ↑ | 从光标位置向外扩展式的选中内容
alt + ↓ | 执行 alt + ↑ 之后，向内逐层收缩选中
shift + enter | 在当前行下方新建一个空行并将光标下移
alt + cmd +enter | 在当前行上方新建一个空行并将光标上移
shift + cmd + u | 切换变量名的大小写（整个变量名全部切换）
tab | 在行首增加 tab 空位
shift + tab |  移除行首的 tab 空位
ctrl + p | 上移光标 （作用同向上箭头 ↑）
ctrl + b | 左移光标  （作用同向左箭头 ←）
ctrl + f | 右移光标 （作用同向右箭头 →）
