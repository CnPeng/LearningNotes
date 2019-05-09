在 ListView 的适配器中使用数据绑定时，有以下特别需要注意的地方：
 ### 1. DataBindingUtils.inflate ( ) 和 binding.getRoot( )
>adapter 的 getView( ) 方法中，如果converView 为null 时，使用 **DataBindingUtils.inflate ( )** 方法填充布局。然后 获取binding 对应的root的布局，作为ConvertView 的值。同时，在绑定完bean类数据，返回view之前，必须加入** binding.executePendingBinding( )**方法，：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-91b231d1a7ee023c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 2.  BaseObservable
>为了实现当bean类数据发生变化时，及时通知适配器去更新视图，需要让bean类继承BaseObservable ，在bean类成员变量的setXXX( )方法中加入 notifyPropertyChanged(BR.XXX); ,在getXXX( )方法上加 @Bindable 注解
 
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-5d8471bb18dafa40.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

### 3 示例代码： 
> DataBinding 在LV中使用的详细示例可参考：https://github.com/CnPeng/MyDemosFrom2016_12 中的 a_12_GetLocalFiles_VP_FM

### 4 补充：
>在实际使用的时候，出现了这么一个情况，Lv 下面跟着一个EditText , 在展示LV的内容时，ET上面莫名其妙的多出了一片空白，如下图
![LV 和 ET 之间多了一片空白](http://upload-images.jianshu.io/upload_images/2551993-c6c2fac75e8f568e.png)
然后，我尝试着给LV 和ET分别设置背景，然后就发现是这片空白是被LV控制的，如下图：
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-41a88fd50d3c8ab7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
然后，仔细排查代码，发现是因为在Adapter中使用了数据绑定，但是在绑定bean类之后，直接就return convertView, 忘记了 binding.executePendingBindings( ) , 加入这行代码后，一切就正常了。
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-ce02c81d230947c6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



