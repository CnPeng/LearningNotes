在下列代码中：data 中有数据，但 bitmap 为空

```java
String stra = d.getResult().getImage();

try {
    byte[] data=null;
    data = stra.getBytes("UTF-8");
    // 为UTF8编码
    // 把二进制图片转成位图
    Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0,data.length,null);
    image.setImageBitmap(bitmap); 
} catch (UnsupportedEncodingException e) {
    // TODO Auto-generated catch block
    e.printStackTrace();
}
```

解决方案：

字节数据要先转成 YUV 格式的设置好 image 类型才可以调用 `decodeByteArray（）`

```java
//20、20分别是图的宽度与高度
 YuvImage yuvimage=new YuvImage(data, ImageFormat.NV21, 20,20, null); 
 ByteArrayOutputStream baos = new ByteArrayOutputStream();
 //80--JPG图片的质量[0-100],100最高
 yuvimage.compressToJpeg(new Rect(0, 0,20, 20), 80, baos);
 byte[] jdata = baos.toByteArray();
 bitmap = BitmapFactory.decodeByteArray(jdata, 0, jdata.length);
```