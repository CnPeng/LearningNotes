加载 drawable目录下的图片：


```
"drawable://" + R.drawable.ic_launcher, // Image from drawables


String uri = "drawable://"+mImageIds[position];
mImageLoader.displayImage(uri.toString(), image, mImageLoaderOptions);

```