
## 1、导入module之后，在 Project Structure 中看不到导入的module
问题现象：

![](https://gitee.com/uploads/images/2018/0419/140647_ec2ffd7d_930142.png "屏幕截图.png")

解决方案：

* 将所需要的module通过 File--new--import Module 导入（或者直将module直接粘贴到项目根目录下）
* 打开当前项目的 settings.gradle ,将新导入的module追加到末尾，比如：
`include  ':opencv', ":tess-two", ':cwac-richedit', ':picture_library', ':ucrop'
`
追加时，命名格式为：‘:module名称’

---


## 2、Manifest Merger failed with multiple errors 

参考：
https://stackoverflow.com/questions/35842955/manifest-merger-failed-with-multiple-errors-in-android-studio

https://www.zhihu.com/question/36645628

我的问题所在：

将 [PictureSelector](https://github.com/LuckSiege/PictureSelector)作为module导入到项目时，实际导入了两个module 一个是 picture_library （图片/视频选择的核心）,一个时 ucrop (图片裁剪)。

picture_library 的清单文件不为空，并且在 application 节点中定义了 theme， 而我自己项目的清单文件中，也在 application节点中定义了项目，所以，二者冲突了，将pictrue_library 中的 theme 删除后，问题解决。

![](https://gitee.com/uploads/images/2018/0419/161044_2c68df92_930142.png "屏幕截图.png")

---

## 3、Error:Unable to resolve dependency 
https://www.jianshu.com/p/c4bbd66d842a

---

## 4、'com.android.support:appcompat-v7' has different version for the compile (26.1.0) and runtime (27.0.1) classpath

参考链接：https://github.com/flutter/flutter/issues/14020

**具体原因：**

自己项目和导入的module在各自的清单文件的dependencies 节点内依赖了相同的组件，但是两者版本不一致！！（通常是自己项目的版本低，才会出现这个问题，）

在我们的项目中，引用了下面两个support库，但是这两个版本一开始是低于 新导入的 picture_library 中的版本，所以，修改成一致的之后，正常编译了
![](https://gitee.com/uploads/images/2018/0419/164851_865e656f_930142.png "屏幕截图.png")

**整理:**

网络资料中一般都是说，编译版本和目标版本的差异可能会出现这个问题。但是，根据我的解决过程发现，依赖包的版本低于导入module中的版本也不行（还是导入 PictrueSelector 的 picture_library时，我们自己的项目和这个module中都依赖了 glide ,但是，我们项目中的版本高于导入module的，所以，没有报错）

---

