##一、开始挖坑
项目中有一个点击查看大图的需求，并且在大图模式下支持手势缩放，所以，我们必然会用到 chrisbanes 大神的 [PhotoView](https://github.com/chrisbanes/PhotoView/tree/master/photoview/src/main/java/com/github/chrisbanes/photoview)，主要使用的是其中的PhotoView 和 PhotoViewAttacher 两个类。

使用PhotoView加载《清明上河图》这种巨图时，图片会展示为一个小长条，并且极易产生OOM。为了解决这个问题，先是参考 鸿洋大神的[Android 高清加载巨图方案 拒绝压缩图片](https://blog.csdn.net/lmj623565791/article/details/49300989)这篇文章，但是实现的效果特别卡。所以，就直接引入了现有的轮子——[subsampling-scale-image-view]( https://github.com/davemorrissey/subsampling-scale-image-view)。该组件底层是使用分段加载的模式实现，能够支持巨图加载，内部也封装了手势缩放。但是，**该组件不支持 GIF动图！**

基于以上两个控件，**在加载网络图片时，会先判断是否是GIF动图，是动图则使用PhotoView展示；如果是静态图，不区分是巨图还是普通图，统一使用SubsamplingScaleImageView加载**。

那么，我们该怎么判断图片是否为GIF动图呢？请继续往下看！

##二、坑来了！
###1、判断是否动图的方式
实际编码时，我们判断一个图片是否GIF图，无非三种方式，分别如下：
####（1）根据后缀名判断
通常情况下，服务端在给我们返回图片地址时，后面都会带有后缀名。所以，我们就可以直接根据这个后缀名判断是否以 `.gif`或 `.GIF` 结尾来判定这是否一个动图。

#### (2) 根据头信息判断
大家都晓得，计算机在存储数据时是以二级制字节码存储，其中， 字节码的前几位标识了文件的后缀名类型，这几位标识就被称为头信息。

所以，我们要想通过头信息获取文件类型，就需要先将文件转为流，然后取出其中的前几位（不同文件类型取的位数不一样），然后再判断是否是动图。`具体代码省略，后期补充——其实网上的示例代码有很多`

####（3）使用BitmapFactory.Options
在BitmapFactory.Options中有一个属性 `outMimeType`, 这个属性就标识了文件的具体mimeType类型，它的值是这种格式的 `”image/png”、”image/jpeg”、”image/gif”`。我们根据这个mimeType就可以获取文件类型。

假设我们获取到了某个文件在本地的完整路径，那么，我们就可以根据下面的示例代码获知该文件的具体类型。
示例代码如下：
```
//文件在本地的路径
String filePath = file.getPath();
BitmapFactory.Options options = new BitmapFactory.Options();
options.inJustDecodeBounds = true;
BitmapFactory.decodeFile(filePath, options);

String mimeType = options.outMimeType;
LogUtils.d(TAG, "图片类型1：" + mimeType);
```
###2、快来看，TA掉坑里了！
看完上面三种方式的介绍，你是不是有下面的感觉？
* 第一种最简单最直接；
* 第二种需要操作流，较第一种略微复杂。
* 第三种比第一种代码也多。

所以，你的选择是哪一种呢？你是不是也选了第一种？但是，我必须告诉你！！
**这种方式是极其不准确的！！这种方式是极其不准确的！！这种方式是极其不准确的！！**

就比如下面这张图：
![gif_end_with_jpg2.jpg](https://upload-images.jianshu.io/upload_images/2551993-b92fd8298ba882e0.jpg?imageMogr2/auto-orient/strip)

上面这个小姐姐的图，就是一个以 `.jpg`结尾的 `.gif`图！你不信？下图是MAC 预览工具显示的图片信息。
![image.png](https://upload-images.jianshu.io/upload_images/2551993-75cc75c40f6b4574.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这就是坑点所在！这张图充分体现了用户输入的不可靠性，**用户在保存gif图片时，手动修改后缀名为.jpg**，然后就有了这种会动的 .jpg !

那么，既然这种直接判断后缀名的方式不精确我们该选用哪种方式呢？——墙裂推荐第三种！代码简单，信息精确！！

##三、呦，爬出来了啊！
前面我们已经确定了要使用BitmapFactory.Options的方式来获取精确的类型，那么，我们就需要先将网络图片转换成本地图片，然后获取它在本地的完整路径。

关于如何将网络图片下载到本地并获取完整路径，我在实现这个功能点的时候，直接**使用了 `Glide` 中的 `downloadOnly( )`，就像它的名字一样，只是将文件下载的本地，然后在回调中会给我们暴露一个`File` 对象，然后，我们 getPath()就可以得到完整路径了！**然后，事情就变的很简单了，直接上代码：

```
LogUtils.d(TAG, "图片地址:" + imgUrl);

        //2018/6/7 上午9:18  由于 RequestBuilder 的 downloadOnly 已被废弃，所以根据建议使用 RequestManager中的downloadOnly
        Glide.with(contentView.getContext())
                .downloadOnly()
                .load(imgUrl)
                .listener(getFileRequestListener(loadingProgress, longImg))
                .into(new SimpleTarget<File>() {
                    @Override
                    public void onResourceReady(@NonNull File file, @Nullable Transition<? super File> transition) {
                        String filePath = file.getPath();
                        BitmapFactory.Options options = new BitmapFactory.Options();
                        options.inJustDecodeBounds = true;
                        BitmapFactory.decodeFile(filePath, options);
                        int bmpWidth = options.outWidth;
                        int bmpHeight = options.outHeight;

                        //outMimeType是以--”image/png”、”image/jpeg”、”image/gif”…….这样的方式返回的
                        String mimeType = options.outMimeType;
                        LogUtils.d(TAG, "图片类型1：" + mimeType);

                        //自定义的判断是否为GIF的工具类
                        boolean isGif = PictureMimeType.isGif(mimeType);

                        if (isGif) {
                            //  2018/6/5 下午4:18  如果是动图，隐藏长图控件，否则，点击和长按事件不生效
                            longImg.setVisibility(View.GONE);
                            imageView.setVisibility(View.VISIBLE);

                            RequestOptions gifOptions = new RequestOptions()
                                    .override(480, 800)
                                    .priority(Priority.HIGH)
                                    .diskCacheStrategy(DiskCacheStrategy.NONE);
                            Glide.with(contentView.getContext())
                                    .asGif()
                                    .load(file)
                                    //监听器是RequestListener<GifDrawable>类型的，其中控制了加载进度条——loadingProgress的隐藏
                                    .listener(getGifRequestListener(loadingProgress))
                                    .apply(gifOptions)
                                    //imageView即 PhotoView对象
                                    .into(imageView);
                        } else {
                            longImg.setVisibility(View.VISIBLE);
                            imageView.setVisibility(View.GONE);

                            //longImg即巨图加载控件对象，这一段主要是设置手势动作
                            longImg.setDoubleTapZoomDuration(100);
                            //这一行和setMinScale() 配合使用，可以解决双击缩小视图时直接缩到最小的问题             
                            longImg.setMinimumScaleType(SubsamplingScaleImageView.SCALE_TYPE_CUSTOM);
                            float scale = PictureMimeType.getScaleRate(bmpWidth, bmpHeight);
                            longImg.setMinScale(scale);
                            float maxScale = PictureMimeType.getMaxScaleRate(bmpWidth, bmpHeight);
                            longImg.setMaxScale(maxScale);
                            float tapScale = PictureMimeType.getDoubleTapZoomScale(bmpWidth, bmpHeight);
                            longImg.setDoubleTapZoomScale(tapScale);

                            LogUtils.i(TAG, "init" + scale + "/max" + maxScale + "/tap" + tapScale);
                            //加载静态图，ImageSource、ImageViewState都是巨图控件包中的
                            longImg.setImage(ImageSource.uri(Uri.fromFile(file)), new ImageViewState(scale, new PointF(0, 0), 0));
                        }
                    }
                });
```

##四、爬坑总结
* 简单的并不一定是最好的！
* 用户输入是不可靠的！
* 办法总比困难多，静下心来想一想（其实是Google 一下），总会找到解决办法！


---
本文到此结束，谢谢观看！
**如有不足，敬请指正！**
