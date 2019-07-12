引用的三方库中使用了 DataBinding ，自己的项目中也使用的 DataBinding，

在项目编译过程中报错 xxBindingImpl 无法强转为 三方库中的 xxxBinding，

* 这种情况 **可能** 是因为三方库中的 DataBinding 文件命名不规范导致的，

——比如，自己的 PicSelector 库中，一开始 VideoPreviewActivtiy 对应的 xml 布局文件为 `video_preview.xml`，那么生成的类也就是 VideoPreviewBinding 类，但是，在引用该库的项目中编译的时候，报错 `VideoPreviewBindingImpl can not cast to VideoPreviewBinding`, 然后将 上述 Activity 对应的 xml 文件修改为 `activity_video_preview.xml` 之后，再次编译引用该库的项目时，没有报错。

* 也可能是因为 当前项目中和引用的三方库中存在同名的布局文件。