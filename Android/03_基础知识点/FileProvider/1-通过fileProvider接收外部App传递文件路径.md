# 1. 1-通过fileProvider接收外部App传递文件路径

## 1.1. 原文内容

[原文地址](https://xiaozhuanlan.com/topic/0319872645)

### 1.1.1. 问题

由于Google的作死，现如今线版本的Android系统阻止了应用之间通过intent传递路径的行为，而通过此方法传递过来的路径会非常奇怪，直接获取会以类似如下形式表现：

```
content://com.example.app.provider/storage/emulated/0/xxx...
```

如果将它丢到 `new File(path)` 里则会导致出错，那么如何解决这样的奇葩路径呢？

按照以往的方法，我们要读取到需要打开的文件的方法为：

```java
Intent intent = getIntent();
if (intent != null) {
    String action = intent.getAction();
    if (action != null) {
        if (intent.ACTION_VIEW.equals(action)) {
            String uriPath = intent.getDataString();    //获取要打开文件的路径
            if (!isNull(uriPath)) {
                //执行打开操作...
            } else {
                //显示错误提示
            }
        }else if (intent.ACTION_SEND.equals(action)) {
            Uri uri = intent.getParcelableExtra(Intent.EXTRA_STREAM);
            if (uri != null) {
                String file = uri.getPath();
                if (!isNull(file)) {
                    //执行打开操作...
                } else {
                   //显示错误提示
                }
            } else {
                //显示错误提示
            }
        }
    }
}
```

但是显然，**fileProvider 传递过来并非通常意义的路径，我们无法直接进行打开操作**，

### 1.1.2. 解决方案

那么此时正确的方法为首先我们需要在接收“打开”操作的 Activity 中接收到系统传递过来的 Intent，从 intent 中获取数据：

```java
Uri data = getIntent().getData();
```

接下来直接通过 ContentResolver 获取读取流进行读取文件内容：

```java
if (data != null) {
    InputStream inputStream = null;
    try {
        inputStream = getContentResolver().openInputStream(data);
        String content = readStreamToString(inputStream);
        //content 就是读取到的内容了，请直接食用
    } catch (Exception e) {
        e.printStackTrace();
    } finally {
        if (inputStream != null) {
            try {
                inputStream.close();
            } catch (IOException ignored) {
            }
        }
    }
}
```

此时通过 ContentResolver 获取到读取流就可以轻松读取到外部App传递过来的文档内容了。

上述代码中的 content 即读取到的文件所有内容，当然了，如果是图片也可以通过 inputStream 读取到，请按具体情况进行修改。

### 1.1.3. 注意

但是注意，如果遇到低版本系统中的 App，依然会传递以前方式正确路径过来，此时通过 fileProvider 法又无法读取内容，那么在这里我们就要做到新旧兼容，请参阅下述的完整代码：

完整代码

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);

    //尝试使用fileProvider方式读取
    Uri data = getIntent().getData();
    if (data != null) {
        InputStream inputStream = null;
        try {
            inputStream = getContentResolver().openInputStream(data);
            String content = readStreamToString(inputStream);

            //请对读取到的内容content进行处理...

        } catch (Exception e) {
            //如果不支持，尝试老方法
            doTryOldReader(getIntent());
        } finally {
            if (inputStream != null) {
                try {
                    inputStream.close();
                } catch (IOException ignored) {
                }
            }
        }
    }else{
        //如果不支持，尝试老方法
        doTryOldReader(getIntent());
    }
}

//部分旧文件浏览器可以直接将路径发过来的情况执行下边的方法
private void doTryOldReader(Intent intent) {
    if (intent != null) {
        String action = intent.getAction();
        if (action != null) {
            if (intent.ACTION_VIEW.equals(action)) {
                String uriPath = intent.getDataString();

                if (!isNull(uriPath)) {
                    //在此处读取uriPath路径的文件即可
                } else {
                    //错误提示
                }
            }
            if (intent.ACTION_SEND.equals(action)) {
                Uri uri = intent.getParcelableExtra(Intent.EXTRA_STREAM);
                if (uri != null) {
                    String file = uri.getPath();

                    if (!isNull(file)) {
                        //在此处读取uriPath路径的文件即可
                    } else {
                        //错误提示
                    }
                } else {
                    //错误提示
                }
            }
        } else {
            //错误提示
        }
    } else {
        //错误提示
    }
    finish();
}
```

## 1.2. 我的最终实现


* 读取外部 app 传递进来的数据，然后将其复制到当前 app 沙盒中的 encrypt 目录中。

```java
  /**
     * CnPeng:2021/6/17 15:44 当 app 被选择为某种文件的打开方式时，从此处获取传递进来的数据
     */
    private void getDataFromActionView() {
        Intent intent = getIntent();
        String action = intent.getAction();
        if (Intent.ACTION_VIEW.equals(action)) {
            Uri uri = intent.getData();
            String str = Uri.decode(uri.getEncodedPath());
            Log.d(TAG, "文件uri-" + str + "||" + uri.getPath());

            if (TextUtils.isEmpty(str)) {
                return;
            }
            String path = GetRealPathBasedOnUri.getPathByUri(this, uri);
            Log.d(TAG, "文件path-" + path);

            if (!TextUtils.isEmpty(path)) {
                new LoginSp(this).saveFilePathToOpen(path);
                return;
            }

            // 如果无法通过前面的方式获取 path ，则尝试直接读取数据内容。参考：https://xiaozhuanlan.com/topic/0319872645
            // 校验或创建目录
            String targetDir = CpFileUtil.getInstance().getSandboxDir(this) + "/encrypt";
            File dirFile = new File(targetDir);
            if (!dirFile.exists()) {
                boolean mkdir = dirFile.mkdir();
            }

            // 创建或校验文件
            File destFile = new File(targetDir, new File(str).getName());
            if (destFile.exists()) {
                new LoginSp(this).saveFilePathToOpen(destFile.getPath());
                return;
            }

            // 复制内容
            InputStream fis = null;
            BufferedInputStream bis = null;
            BufferedOutputStream bos = null;
            try {
                fis = getContentResolver().openInputStream(uri);
                // 存储目标
                bis = new BufferedInputStream(fis);

                // 构造输出流
                bos = new BufferedOutputStream(new FileOutputStream(destFile));
                int b;
                while ((b = bis.read()) != -1) {
                    bos.write(b);
                }

                new LoginSp(this).saveFilePathToOpen(destFile.getPath());
                Log.d(TAG, "文件path2-" + destFile.getPath());
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
                if (null != bis) {
                    try {
                        bis.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }

                if (null != bos) {
                    try {
                        bos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
    }
```

* 获取沙盒目录的函数如下：

```java
  /**
     * CnPeng:2021/6/15 21:35 返回沙盒目录
     */
    public String getSandboxDir(Context ctx) {
        return ctx.getFilesDir().getPath();
    }
```

* 根据 uri 读取真实路径的 getPathByUri(,) 如下：

```java
public class GetRealPathBasedOnUri {

    public static String getPathByUri(final Context context, final Uri uri) {
        // DocumentProvider
        if (DocumentsContract.isDocumentUri(context, uri)) {
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
            // Return the remote address
            if (isGooglePhotosUri(uri)) {
                return uri.getLastPathSegment();
            }

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
        } catch (Exception e) {
            e.printStackTrace();
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

    /**
     * @param uri The Uri to check.
     * @return Whether the Uri authority is Google Photos.
     */
    public static boolean isGooglePhotosUri(Uri uri) {
        return "com.google.android.apps.photos.content".equals(uri.getAuthority());
    }
}
```

## 1.3. 补充：注册 APP 让其能够接收其他 app 的数据

### 1.3.1. 注册到打开方式

直白一点，就是把我们的 app 注册到 "打开方式" 的弹窗中，当用户选择打开文件的方式时，可以选择我们的 app.

其核心逻辑是在清单文件中，对指定的 Activity 配置一个 action 为 `android.intent.action.VIEW` 的 `intent-filter`。

下面的代码中将其注册到了启动页，也可以注册到其他页面（参考后面的注册分享方式）。具体代码如下：

```xml
   <activity
            android:name=".WelcomeActivity"
            android:label="@string/app_name"
            android:theme="@style/Theme.WelcomeActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>

            <!-- 将 app 添加到打开方式的选择项中。此处会报红，先不要管-->
            <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <data android:mimeType="*/*" />
            </intent-filter>

        </activity>
```

### 1.3.2. 注册到分享方式

将页面注册为分享方式，即用户在长按某个文件选择分享到时，能够选择我们的 app 。

核心是为我们的  activity 配置 `android.intent.action.SEND` 或 `android.intent.action.SEND_MULTIPLE` ，前者表示仅接收单一类型文件，后者表示接收多类型的图片文件。

```java
 <activity
            android:name=".square.activity.CreateNewTopicActivity"
            android:screenOrientation="portrait"
            android:windowSoftInputMode="adjustResize">
            <intent-filter>
                <action android:name="android.intent.action.SEND" />
                <action android:name="android.intent.action.SEND_MULTIPLE" />

                <category android:name="android.intent.category.DEFAULT" />
                <!-- <data android:mimeType="application/*"/> -->
                <!-- <data android:mimeType="audio/*"/> -->
                <data android:mimeType="image/*" />
                <!-- <data android:mimeType="text/plain"/> -->
                <!-- <data android:mimeType="video/*"/> -->
                <!-- <data android:mimeType="multipart/*"/> -->
            </intent-filter>
        </activity> 
```

注册为分享方式时，可以使用如下方式读取数据：

(**注意，下面的代码时间比较久了，不确定在新的手机中能否正常运行。**)

```java

    /**
     * 170516
     * 获取外部分享到集结号的数据并存储到本地
     */
    private void getAndSaveSysetemSharedInDatas() {
        //区分是SEND 还是 SEND_MULITPLE
        String intentAction = mInIntent.getAction();
        String mimeType = mInIntent.getType();
        mShareInSpHelper.saveSharedInAction(intentAction);
        mShareInSpHelper.saveSharedInMimeType(mimeType);

        if (Intent.ACTION_SEND.equals(intentAction) && !TextUtils.isEmpty(mimeType)) {
            BuriedPointManager.getInstance().savePoint(R.integer.systemShare);

            //如果是单一类型的普通分享
            if (("text/plain").equals(mimeType)) {
                //文本类型
                //内容或链接
                CharSequence extraText = mInIntent.getCharSequenceExtra(Intent.EXTRA_TEXT);
                //标题,主题
                CharSequence extraSubject = mInIntent.getCharSequenceExtra(Intent.EXTRA_SUBJECT);

                String subject = "";
                String text = "";
                if (!TextUtils.isEmpty(extraSubject)) {
                    //如果不做判断，直接将extraSubject转成字符串的话，会将null转成 "null"
                    subject = String.valueOf(extraSubject);
                }

                if (!TextUtils.isEmpty(extraText)) {
                    text = String.valueOf(extraText);
                }
                mShareInSpHelper.saveSharedInText(subject, text);
            } else if (mimeType.startsWith("image/")) {
                //单张图片分享
                Uri shareUri = mInIntent.getParcelableExtra(Intent.EXTRA_STREAM);
                if (null!=shareUri) {
                    ArrayList<String> realPaths = new ArrayList<>();
                    String realPath = GetRealPathBasedOnUri.getPathByUri(this, shareUri);
                    realPaths.add(realPath);
                    mShareInSpHelper.saveSharedInImages(realPaths);
                    mSelectedMediaType = 0;
                }
            }
        } else if (Intent.ACTION_SEND_MULTIPLE.equals(intentAction) && !TextUtils.isEmpty(mimeType)) {
            BuriedPointManager.getInstance().savePoint(R.integer.systemShare);

            //如果是复合类型的分享。—— 暂时不支持图片以外的其他类型，所以只判断图片类型
            if (mimeType.startsWith("image/")) {
                //多图分享
                ArrayList<String> realPaths = new ArrayList<>();
                List<Uri> uris = mInIntent.getParcelableArrayListExtra(Intent.EXTRA_STREAM);

                if (uris != null && uris.size() > 0) {
                    for (int i = uris.size() - 1; i > -1; i--) {
                        //先移除非图片内容。虽然我们在清单文件中只加入了 image/* 的分享，但是很多手机上还是支持图片和视频同时混选分享，所以，此处加个过滤
                        //但是，用户单纯的选择视频进行分享是无法调起集结号的。只是混选的时候会调起集结号
                        boolean isVideo = AntMediaUtil.isVideo(uris.get(i).toString());

                        if (isVideo) {
                            uris.remove(i);
                            CpToastUtil.toastShort("暂不支持从系统相册分享视频到集结号");
                        }
                        // TODO: 2018/6/4 下午4:21 如果文件大小超出限制怎么办？是否需要移除并且给出提醒？

                    }

                    if (uris.size() > 9) {
                        //多于9张则截取前9张
                        uris = uris.subList(0, 9);
                        CpToastUtil.toastShort("最多可添加9张图片");
                    }
                    for (Uri uri : uris) {
                        if (null==uri) {
                            continue;
                        }
                        String realPath = GetRealPathBasedOnUri.getPathByUri(this, uri);
                        realPaths.add(realPath);
                    }
                }
                mShareInSpHelper.saveSharedInImages(realPaths);
                mSelectedMediaType = 0;
            } else {
                //  CnPeng 2018/6/11 上午10:13 华为荣耀10手机中，8.0.1系统，当图片和视频混选时，返回的类型是 */* ,所以需要做个判断
                CpToastUtil.toastShort("不支持的分享类型");
                finish();
            }
        }
    }
```