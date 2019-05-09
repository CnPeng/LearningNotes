##一、问题现象：
我司应用内部具有文件上传和下载功能，A手机（任意品牌手机）上传 gif 动图文件到APP后，B手机（Vivo手机）下载到本地，在B手机打开的时候会提示 “ gif 过大，只显示第一帧 ”。然后，实际上这个gif 文件可能只有几十KB。

在其他品牌手机中下载gif 到本地后并无类似情况。在Vivo手机上通过路径找到下载的这个 GIF 文件，手动打开时依旧提示上述错误。but，将该 GIF文件改名之后就能正常打开了。

##二、问题原因及解决方案
###（1）、问题原因
问题原因是，将文件下载到本地之后，未通知系统图库去刷新，所以无法直接使用Vivo的图库去打开该文件。（其他品牌手机无类似情况）

###（2）、解决方案
将文件下载到本地之后，手动发送广播刷新图库：

```
 /**
     * 发送广播刷新相册/图库，从而让用户能看到新增的图片（不刷新看不到）
     *
     * @param context 上下文，用来发送广播
     * @param file    新增的文件，用来获取其URI，该URI会作为广播携带的intent 的Data
     */
    public static void updateGallery(Context context, File file) {
        Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
        Uri uri = Uri.fromFile(file);
        intent.setData(uri);
        context.sendBroadcast(intent);
    }
```

>另，应用内实现截屏操作时也是需要通过发送该广播去通知图库刷新，否则虽然截图成功，但是图库中可能会看不到截图。具体代码参考：[Android:应用内部截屏的实现](http://www.jianshu.com/p/8475c17be72f)
