今天用到一个知识点,就是修改radioButton颜色,

一般更改radioButton都是将他的button属性设置为null,然后写一个selector,然后正常状态和选中状态分别给出一张图片, 在将这个selector设置为RadioButton的background.但我现在只是需要更改radioButton 的颜色,就没有必要再去写一个selector,可以直接使用ThemeEditor去修改

前提要求:AndroidStudio 必须是1.4及以上版本
操作如下:

####1. 打开ThemeEditor
![01.jpg](http://upload-images.jianshu.io/upload_images/2551993-d65221d47bdd5036.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


####2. 编辑颜色
![2.jpg](http://upload-images.jianshu.io/upload_images/2551993-d4b1d732b5f22b59.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
上图中,左侧是预览,右侧是具体的颜色信息,如果不知道右侧的颜色对应左侧的哪个项目,那么不用管,直接在右侧修改,然后看左侧哪个有变化就知道相应的对应关系了

####注意:
如果左侧不显示预览图,说明当前AndroidStudio关联的jdk版本过低,需要重新给AS关联一个最新版本的jdk
