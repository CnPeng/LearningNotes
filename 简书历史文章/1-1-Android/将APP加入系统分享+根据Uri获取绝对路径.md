##1 需求分析
当前项目中需要将我司的APP加入系统分享中，然后接收外部分享过来的数据。单纯看需求貌似是很简单，然后认真整理了一下思路，呵呵。。。具体看下图吧。


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-a5ccd85458dc9aae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##2 实现需求时的主要知识点
实现这个需求的时候，主要的知识点如下。
###（1）将app加入系统分享
假设我们用一个EditActivity 来接收并展示分享过来的数据。

那么为了能够让 EditActivity 响应系统分享事件，需要在清单文件中给 该 Activity 配置 intent-filter , 通过 intent-filter 中配置的 action  节点指定该页面能接受的分享类型----**android.intent.action.SEND** 或者 **android.intent.action.SEND_MULTIPLE**。通过配置 data 节点 指定页面能接收的具体数据类型。

```
 <!--CnPeng 从广场创建分享 windowSoftInputMode 控制emoji与系统键盘同显示或不显示-->
        <activity
            android:name="com.zjelite.square.activity.CreateNewTopicActivity"
            android:screenOrientation="portrait"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.SEND"/>
                <action android:name="android.intent.action.SEND_MULTIPLE"/>
                <category android:name="android.intent.category.DEFAULT"/>
                <!--<data android:mimeType="application/*"/>-->
                <!--<data android:mimeType="audio/*"/>-->
                <data android:mimeType="image/*"/>
                <data android:mimeType="text/plain"/>
                <!--<data android:mimeType="video/*"/>-->
                <!--<data android:mimeType="multipart/*"/>-->
            </intent-filter>
        </activity>
```
>android.intent.action.SEND 表示单文件分享android.intent.action.SEND_MULTIPLE 表示多文件分享

###（2）获取系统分享过来的数据
系统在分享数据的时候，也是通过Intent来传递数据，所以我们在EditActivity 中通过 getIntent() 可以获取系统分享过来的具体数据。

>* getIntent().getAction( ) 可以获取当前页面所响应的 android.intent.action
* getIntent().getType( ) 可以获取当前页面所接收到数据的 MimeType 类型
* 如果 MimeType 类型是 text/plain 表示分享过来的是纯文本或者网址。
>>* 可以通过如下方式获取分享过来的网址标题/主题
        CharSequence extraSubject=getIntent().getCharSequenceExtra(Intent.EXTRA_SUBJECT); 
>> * 可以通过如下方式获取分享过来的内容或链接
        CharSequence extraText=getIntent().getCharSequenceExtra(Intent.EXTRA_TEXT); 
>>
A:
**如果标题为空，表示分享过来的是纯文本，也就是 extraText是纯文本；如果标题不为空，则表示分享过来的是一个网址，也就是说extraText是一个网址链接**
>>
B:
**之所以使用CharSquence来接收，是因为有些系统分享在组织数据时用的数据类型是CharSquence**

>* 如果 MimeType 类型是 以** image/ **开头， 表示分享过来的是图片数据，可能是单图，也可能是多图。
>> * 如果action 的类型是 ACTION_SEND ，则表示分享的单张图片。可以通过如下方式获取该图片的Uri:
        Uri shareUri = inIntent.getParcelableExtra(Intent.EXTRA_STREAM);
>> * 如果action 的类型是 ACTION_SEND_MULTIPLE ，则表示分享的是多张图片。可以通过如下方式获取该组图片的Uri集合:
        List<Uri> uris = inIntent.getParcelableArrayListExtra(Intent.EXTRA_STREAM);

在下面的示例代码中 ``SharedPreferenceSharedInHelper sharedInSPHelper;``   是自己定义的用来存储接收到的分享数据的SP。后面会有详细的介绍 ``GetRealPathBasedOnUri.getPathByUri4kitkat（）``是自定义的根据Uri获取绝对路径的工具类。后面会有详细介绍
```
 /**
     * 170516
     * 获取外部分享到集结号的数据并存储到本地
     */
    private void getAndSavaSharedInDatas() {
        String intentAction = inIntent.getAction();   //区分是SEND 还是 SEND_MULITPLE
        String mimeType = inIntent.getType();
        sharedInSPHelper.saveSharedInAction(intentAction);
        sharedInSPHelper.saveSharedInMimeType(mimeType);

        if (Intent.ACTION_SEND.equals(intentAction) && !TextUtils.isEmpty(mimeType)) {   //如果是单一类型的普通分享
            if (("text/plain").equals(mimeType)) {   //文本类型
                CharSequence extraText = inIntent.getCharSequenceExtra(Intent.EXTRA_TEXT);  //内容或链接
                CharSequence extraSubject = inIntent.getCharSequenceExtra(Intent.EXTRA_SUBJECT);  //标题,主题

                String subject = "";
                String text = "";
                if (!TextUtils.isEmpty(extraSubject)) {
                    subject = String.valueOf(extraSubject); //如果不做判断，直接将extraSubject转成字符串的话，会将null转成 "null"
                }

                if (!TextUtils.isEmpty(extraText)) {
                    text = String.valueOf(extraText);
                }
                sharedInSPHelper.saveSharedInText(subject, text);
            } else if (mimeType.startsWith("image/")) { //单张图片分享
                Uri shareUri = inIntent.getParcelableExtra(Intent.EXTRA_STREAM);
                ArrayList<String> realPaths = new ArrayList<>();
                String realPath = GetRealPathBasedOnUri.getPathByUri4kitkat(this, shareUri);
                realPaths.add(realPath);
                sharedInSPHelper.saveSharedInImages(realPaths);
            }
        } else if (Intent.ACTION_SEND_MULTIPLE.equals(intentAction) && !TextUtils.isEmpty(mimeType)) {
            //如果是复合类型的分享
            if (mimeType.startsWith("image/")) {     //多图分享
                ArrayList<String> realPaths = new ArrayList<>();
                List<Uri> uris = inIntent.getParcelableArrayListExtra(Intent.EXTRA_STREAM);
                if (uris != null && uris.size() > 0) {
                    if (uris.size() > 9) {      //多于9张则截取前9张
                        uris = uris.subList(0, 9);
                    }
                    for (Uri uri : uris) {
                        String realPath = GetRealPathBasedOnUri.getPathByUri4kitkat(this, uri);
                        realPaths.add(realPath);
                    }
                }
                sharedInSPHelper.saveSharedInImages(realPaths);
            }
        }
    }
```
###（3）根据Uri 获取图片在本地的绝对路径
在我司的项目中，需要拿到图片的绝对路径，然后根据绝对路径进行压缩，并获取一个压缩后的图以及路径，最后再根据压缩后的路径将压缩后的图片上传到服务器。所以，这就需要根据Uri 获取其绝对路径。

关于如何根据Uri 获取绝对路径，主要代码如下：
```
 
public class GetRealPathBasedOnUri {
    @SuppressLint( "NewApi" )
    public static String getPathByUri4kitkat(final Context context, final Uri uri) {
        final boolean isKitKat = Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT;
        // DocumentProvider  
        if (isKitKat && DocumentsContract.isDocumentUri(context, uri)) {
            if (isExternalStorageDocument(uri)) {// ExternalStorageProvider  
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];
                if ("primary".equalsIgnoreCase(type)) {
                    return Environment.getExternalStorageDirectory() + "/" + split[1];
                }
            } else if (isDownloadsDocument(uri)) {// DownloadsProvider  
                final String id = DocumentsContract.getDocumentId(uri);
                final Uri contentUri = ContentUris.withAppendedId(Uri.parse("content://downloads/public_downloads"), 
                        Long.valueOf(id));
                return getDataColumn(context, contentUri, null, null);
            } else if (isMediaDocument(uri)) {// MediaProvider  
                final String docId = DocumentsContract.getDocumentId(uri);
                final String[] split = docId.split(":");
                final String type = split[0];
                Uri contentUri = null;
                if ("image".equals(type)) {
                    contentUri = MediaStore.Images.Media.EXTERNAL_CONTENT_URI;
                } else if ("video".equals(type)) {
                    contentUri = MediaStore.Video.Media.EXTERNAL_CONTENT_URI;
                } else if ("audio".equals(type)) {
                    contentUri = MediaStore.Audio.Media.EXTERNAL_CONTENT_URI;
                }
                final String selection = "_id=?";
                final String[] selectionArgs = new String[]{split[1]};
                return getDataColumn(context, contentUri, selection, selectionArgs);
            }
        } else if ("content".equalsIgnoreCase(uri.getScheme())) {// MediaStore  
            // (and  
            // general)  
            return getDataColumn(context, uri, null, null);
        } else if ("file".equalsIgnoreCase(uri.getScheme())) {// File  
            return uri.getPath();
        }
        return null;
    }

    /**
     * Get the value of the data column for this Uri. This is useful for
     * MediaStore Uris, and other file-based ContentProviders.
     *
     * @param context       The context.
     * @param uri           The Uri to query.
     * @param selection     (Optional) Filter used in the query.
     * @param selectionArgs (Optional) Selection arguments used in the query.
     * @return The value of the _data column, which is typically a file path.
     */
    public static String getDataColumn(Context context, Uri uri, String selection, String[] selectionArgs) {
        Cursor cursor = null;
        final String column = "_data";
        final String[] projection = {column};
        try {
            cursor = context.getContentResolver().query(uri, projection, selection, selectionArgs, null);
            if (cursor != null && cursor.moveToFirst()) {
                final int column_index = cursor.getColumnIndexOrThrow(column);
                return cursor.getString(column_index);
            }
        } finally {
            if (cursor != null) {
                cursor.close();
            }
        }
        return null;
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is ExternalStorageProvider.
     */
    public static boolean isExternalStorageDocument(Uri uri) {
        return "com.android.externalstorage.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is DownloadsProvider.
     */
    public static boolean isDownloadsDocument(Uri uri) {
        return "com.android.providers.downloads.documents".equals(uri.getAuthority());
    }

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is MediaProvider.
     */
    public static boolean isMediaDocument(Uri uri) {
        return "com.android.providers.media.documents".equals(uri.getAuthority());
    }
}
```

>A:
** 注意：上面这段根据 Uri 获取绝对地址的代码的原始出处是：http://ch-kexin.iteye.com/blog/2297846**

>B:
上述代码我在 4.3   / 5.0   /  6.0  / 7.0 上都做过测试，均能正确的取到绝对路径。

>C:
另外，在http://www.jianshu.com/p/b168cbe50066 中也有提及如何根据 Uri 获取绝对路径，但是非常遗憾，我在测试的时候没能调通

###（4）将List 集合数据存储到SP
为了避免数据在各个界面之间传来传去，我的思路是在 EditActivity 界面获取到数据之后直接存储到本地，如果用户未登录就先跳转到登录界面，登录成功之后再跳转回来，然后直接从本地取出先前存储的数据展示出来。

个人比较习惯用SP存储数据，但是用SP存储的时候只能存储 基本数据类型和 Set ，然而，如果系统分享过来的是多张图片的时候，获取到的将是 List<Uri>。为了保证List 中数据的顺序性，我们不能使用 putStringSet( ) , 因为set 是无序的。所以，需要考虑将 List 转成基本数据类型。这里需要借助 Gson,代码如下：

```
 /**
     * 存储外部分享进来的图片uri
     */
    public void saveSharedInImages(ArrayList<String> sharedInImages) {
        Gson gson = new Gson();
        String gsonString = gson.toJson(sharedInImages);
        LogUtils.e("转成Gson串的图片集合：", gsonString);
        Editor editor = sp.edit();
        editor.putString("SharedInImages", gsonString);
        editor.commit();
    }

    /**
     * 获取外部分享进来的图片 URI,
     */
    public ArrayList<String> getSharedInImages() {
        String gsonString = sp.getString("SharedInImages", "");
        Gson gson = new Gson();
        ArrayList<String> realPaths = gson.fromJson(gsonString, new TypeToken<ArrayList<String>>() {
        }.getType());

        LogUtils.e("将Gson串转成集合时的类型", new TypeToken<ArrayList<String>>() {
        }.getType().toString());
        
        return realPaths;
    }
```
>在上面的代码中
>A:
将List 转成json 字符串时使用的是 Gson的 toJson(object) 方法，该方法可以将任意类型的对象转成 json 串。

>B:
将 json 串转成 List 的时候，使用的是Gson 的 fromJson(String json, Type typeOfT) ,第一个参数是 源数据 json 串 ，第二个参数表示需要转成什么类型的数据，在获取 这个type 的时候 使用 固定写法  ** new TypeToken<T> **, 其中， T 是要转换成的数据类型。
比如，在上面的代码中，我声明了 realPaths 的类型是 ArrayList<String> , 那么 T 就需要写 ArrayList<String>



###（5）List 集合的截取
由于我司APP一次最多支持 9 张图片的上传，所以，获取到系统分享过来的数据时需要作出判断，如果图片数量大于9张，那么就截取前9张。在截取的时候使用了  List  的 subList(int start, int end) 方法。该方法的含义是，从 start 位置开始截取集合的数据，截取到end 位置，但不包含end位置的数据，也即是说，截取list中的数据，含头不含尾。截取集合前9个数据的代码如下：

```
 if (uris.size() > 9) {      //多于9张则截取前9张
      uris = uris.subList(0, 9);
 }
```

##3 相关参考：
A:
将APP加到系统分享的参考：
http://www.jianshu.com/p/00464708f8c4
http://blog.csdn.net/xiaanming/article/details/9428613
http://www.imooc.com/article/9197

B:
根据URI 获取 绝对路径的参考：
http://ch-kexin.iteye.com/blog/2297846
