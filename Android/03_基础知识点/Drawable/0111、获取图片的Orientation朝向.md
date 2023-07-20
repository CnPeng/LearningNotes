## 一、问题现象

使用 ImageView 显示图片的时候发现图片显示不正，方向偏了或者倒过来 , 需要先获知图片的原始方向，然后修正显示。 

## 二、解决方案：

获取图片方向的核心代码：

```java
/**
 * CnPeng:2020/3/20 14:03 获取 JPEG 图片的方向
 */
private int getJpegBmpOrientation(String filePath) {
    int bmpOrientation = 0;

    try {
        ExifInterface exif = new ExifInterface(filePath);
        // 读取图片中相机方向信息
        int ori = exif.getAttributeInt(ExifInterface.TAG_ORIENTATION,
                ExifInterface.ORIENTATION_UNDEFINED);
        // 计算旋转角度
        switch (ori) {
            case ExifInterface.ORIENTATION_ROTATE_90:
                bmpOrientation = 90;
                break;
            case ExifInterface.ORIENTATION_ROTATE_180:
                bmpOrientation = 180;
                break;
            case ExifInterface.ORIENTATION_ROTATE_270:
                bmpOrientation = 270;
                break;
            default:
                break;
        }
    } catch (IOException e) {
        e.printStackTrace();
    }
    return bmpOrientation;
}
```

如果加载图片时使用了 `SubsamplingScaleImageView` 作为要展示图片的 ImageView，则可以按照如下代码实现 ( 其中的 `longImg` 是 `SubsamplingScaleImageView` 的对象)：

```java
// CnPeng 2020/3/20 14:11 获取图片朝向，修正部分手机上传的图片在查看大图时方向不正的问题
int bmpOrientation = getJpegBmpOrientation(imgUrl);
longImg.setImage(ImageSource.uri(imgUrl), new ImageViewState(scale, new PointF(0, 0), bmpOrientation));
```

上述解决方案主要参考了[Android图片处理：识别图像方向并显示](https://blog.csdn.net/tantion/article/details/51274432)。

另外，在 [Android，获取相册图片的方向](https://www.jianshu.com/p/4c0c7d327a43) 中有提到可以使用 MediaStore 去查询图片方向，但这种方式对网络图无效。所以，在获取图片方向时，还是采用了 Exif 的方式。


## 三、附录

### 1、[Android图片处理：识别图像方向并显示](https://blog.csdn.net/tantion/article/details/51274432) 原文：


在Android中使用ImageView显示图片的时候发现图片显示不正，方向偏了或者倒过来了。


解决这个问题很自然想到的分两步走：

* 1、自动识别图像方向，计算旋转角度；

* 2、对图像进行旋转并显示。



#### (一)、识别图像方向

首先在这里提一个概念 `EXIF(Exchangeable Image File Format，可交换图像文件)`，具体解释参见Wiki。

简而言之，Exif是一个标准，用于电子照相机（也包括手机、扫描器等）上，用来规范图片、声音、视屏以及它们的一些辅助标记格式。

Exif支持的格式如下：

媒体类型 | 支持格式 | 
|---|---
图像 | 压缩图像文件：JPEG、DCT  <br>非压缩图像文件：TIFF|不支持：JPEG 2000、PNG、GIF  
音频 | RIFF、WAV


Android 提供了对 JPEG 格式图像 Exif 接口支持，可以读取 JPEG 文件 metadata 信息，参见[ExifInterface](https://developer.android.google.cn/reference/android/media/ExifInterface?hl=en).

这些Metadata信息总的来说大致分为三类：**日期时间、空间信息（经纬度、高度）、Camera信息（孔径、焦距、旋转角、曝光量等等）**。



#### (二)、图像旋转

Android 中提供了对 Bitmap 进行矩阵旋转的操作，参见 Bitmap 提供的静态 createBitmap 方法.


`public static Bitmap createBitmap (Bitmap source, int x, int y, int width, int height, Matrix m, boolean filter)`

Returns an immutable bitmap from subset of the source bitmap, transformed by the optional matrix. The new bitmap may be the same object as source, or a copy may have been made. It is initialized with the same density as the original bitmap. If the source bitmap is immutable and the requested subset is the same as the source bitmap itself, then the source bitmap is returned and no new bitmap is created.

Parameters：

参数|含义
---|---
source	|The bitmap we are subsetting
x	|The x coordinate of the first pixel in source
y	|The y coordinate of the first pixel in source
width|	The number of pixels in each row
height|	The number of rows
m	|Optional matrix to be applied to the pixels
filter	|true if the source should be filtered. Only applies if the matrix contains more than just translation.

Returns：

A bitmap that represents the specified subset of source

Throws：

IllegalArgumentException	if the x, y, width, height values are outside of the dimensions of the source bitmap.


到此这两个问题理论上都解决了，开始实际操作一下吧，参照以下代码。


```java
public class IOHelper {  
      
    ......  
      
    /** 从给定路径加载图片*/  
    public static Bitmap loadBitmap(String imgpath) {  
        return BitmapFactory.decodeFile(imgpath);  
    }  
  
      
    /** 从给定的路径加载图片，并指定是否自动旋转方向*/  
    public static Bitmap loadBitmap(String imgpath, boolean adjustOritation) {  
        if (!adjustOritation) {  
            return loadBitmap(imgpath);  
        } else {  
            Bitmap bm = loadBitmap(imgpath);  
            int digree = 0;  
            ExifInterface exif = null;  
            try {  
                exif = new ExifInterface(imgpath);  
            } catch (IOException e) {  
                e.printStackTrace();  
                exif = null;  
            }  
            if (exif != null) {  
                // 读取图片中相机方向信息  
                int ori = exif.getAttributeInt(ExifInterface.TAG_ORIENTATION,  
                        ExifInterface.ORIENTATION_UNDEFINED);  
                // 计算旋转角度  
                switch (ori) {  
                case ExifInterface.ORIENTATION_ROTATE_90:  
                    digree = 90;  
                    break;  
                case ExifInterface.ORIENTATION_ROTATE_180:  
                    digree = 180;  
                    break;  
                case ExifInterface.ORIENTATION_ROTATE_270:  
                    digree = 270;  
                    break;  
                default:  
                    digree = 0;  
                    break;  
                }  
            }  
            if (digree != 0) {  
                // 旋转图片  
                Matrix m = new Matrix();  
                m.postRotate(digree);  
                bm = Bitmap.createBitmap(bm, 0, 0, bm.getWidth(),  
                        bm.getHeight(), m, true);  
            }  
            return bm;  
        }  
    }  
          
    ......  
}  
```

### 2、 [Android，获取相册图片的方向](https://www.jianshu.com/p/4c0c7d327a43) 原文

我们在开发获取图片功能的时候，总会发现从相册获取的图片，图片的方向和预览时候的方向是不一样的，这是因为系统在给我们预览图片的时候，自动旋转了图片，而旋转的角度就是根据你拍摄照片的手机方向。

####（一）、获取图片

首先，我们先从相册获取到图片

```java
private void takePictureFromGallery() {
    Intent intent = new Intent(Intent.ACTION_PICK, MediaStore.Images.Media.EXTERNAL_CONTENT_URI);
    intent.setType("image/*");
    startActivityForResult(intent, REQUEST_ALBUM_PICTURE);
}
@Override
public void onActivityResult(int requestCode, int resultCode, Intent data) {
    super.onActivityResult(requestCode, resultCode, data);
    if (request == REQUEST_ALBUM_PICTURE && result == RESULT_OK) {
        Uri uri = data.getData();
        if (uri != null) {
            // 这里我们即将处理图片的方向
            int degrees = getOrientation(this, uri);
            // 旋转你的图片
            // 这里的假设的bmp 是你从uri里面获取的Bitmap对象，通常你需要经过压缩图片等操作，具体参照官网给出的例子
            bmp = rotateImage(bmp, degrees);
        }
    }
}
```

#### (二)、获取图片的方向

一般的思路都是从 Bitmap 或者 Exif 信息的方向入手，可是这些都不太完美，因为 Exif 信息可以抹除，实际上我们在拍照的时候，方向的信息已经被应用记录在数据库里面了，我们只需要从数据库获取即可。可能你会问，如果是别人发给你的呢？这里不必担心，因为别人发给你的，肯定是他希望你所看的形式所展现的，如果是以文件发给你也没关系，因为有 Exif 的信息情况下，能够被系统应用保存起来的图片，那么它的方向也会被写入到数据库。

```java
public static int getOrientation(Context context, Uri photoUri) {
    int orientation = 0;
    Cursor cursor = context.getContentResolver().query(photoUri,
                new String[] { MediaStore.Images.ImageColumns.ORIENTATION }, null, null, null);
    if (cursor != null) {
        if (cursor.getCount() != 1) {
            return -1;
        }
        cursor.moveToFirst();
        orientation = cursor.getInt(0);
        cursor.close();
    }
    return orientation;
}
```

旋转你的图片
因为旋转图片会生成 Bitmap，所以建议处理大图之前先压缩

```java
public static Bitmap rotateImage(Bitmap bmp, int degrees) {
    if (degrees != 0) {
        Matrix matrix = new Matrix();
        matrix.postRotate(degrees);
        return Bitmap.createBitmap(bmp, 0, 0, bmp.getWidth(), bmp.getHeight(), matrix, true);
    }
    return bmp;
}
```