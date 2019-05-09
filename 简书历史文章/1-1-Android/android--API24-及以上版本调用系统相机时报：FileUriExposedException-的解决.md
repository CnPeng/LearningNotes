>171205 更新文章：
>* 更新照片在本地的存储路径，解决刷新相册不生效的问题。
>* 增加部分注释，明确 getExternalCacheDir()、getExternalFilesDir()与Environment.getExternalStoragePublicDirectory()的区别


##一、问题现象
某一天我厂的测试小妹说，在我们APP中拍照的时候崩溃了，然后我去问清了测试的手机型号，系统版本，还有当时的操作步骤，再然后就果然崩溃了。具体如下：

![FileUriExposedException 错误截图](http://upload-images.jianshu.io/upload_images/2551993-1468133090bd4c1a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、问题原因
为啥会出现这个崩溃信息呢？
那是因为，在调用系统相机的时候我们使用 Uri.fromUri(file) 来获取 URI 并传递给 调用系统相机的intent，代码如下：

```
Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
File file = new File(getExternalCacheDir(), System.currentTimeMillis() + "temp.jpg");
Uri photoURI = Uri.fromUri(file);
intent.putExtra(MediaStore.EXTRA_OUTPUT, photoURI);
startActivityForResult(intent, Activity.DEFAULT_KEYS_DIALER);
```
这种方式在 7.0 以前的版本中并没有任何异常，but , 从 7.0 (API 24) 之后这种方式就有问题了 ，**因为 Uri.fromUri(file) 得到的是 以“ `file://` ” 开头的文件在本地的真实路径，Android 认为直接将这个真是地址对外暴露是有很多的风险的**，所以，就会抛出上面截图中的异常信息。 

为了解决这种风险，从 6.0 ( API 22 ) 开始，Android 提供了 **FileProvider** 类，我们通过调用 FileProvider 中的 **getUriFromFile( )** 方法就可以得到一个 封装过之后的 URI，这样就可以有效的避免相关风险。 
>getUriFromFile( ) 方法接收三个参数，第一个参数是 Context 对象，第二个参数是 唯一标识字符串，也就是清单文件中在provider 节点中声明的 `android:authorities` ，第三个参数是 File 对象。

我们使用 getUriFromFile( ) 获取Uri 之后，在 7.0 及以后的版本中调用系统相机时将不会在崩溃，示例代码如下： 

##三、示例代码：

###（1）、在清单文件中注册 FileProvider

```
  <!--解决API24 及以上版本调用系统相机时报：FileUriExposedException 的情况-->
<application>
        <provider
            android:name="android.support.v4.content.FileProvider"
            android:authorities="${applicationId}.fileprovider"
            android:exported="false"
            android:grantUriPermissions="true">
            <meta-data
                android:name="android.support.FILE_PROVIDER_PATHS"
                android:resource="@xml/provider_paths"/>
        </provider>
</application>
```
**上面的 authorities 可以自定义，但是，在getUriFromFile( ) 中传递的唯一标识必须与它一致**

###（2）、provider_paths.xml文件
```
<?xml version="1.0" encoding="utf-8"?>
<paths>
    <external-path name="external_files" path="."/>
</paths>
```
###（3）、调用系统相机的方法片段
```java
 //171129 兼容API24 之前和之后，FileProvider 是API22推出的，所以在API22及以后的版本中就可以支持FileProvider，22、23 既可以使用原有的Uri.from(file) 方式也可以使用 FileProvider, 24 及以后必须使用 FileParovider
Intent takePhotoIntent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
File file = new File(getExternalCacheDir(), System.currentTimeMillis()+"temp.jpg");
Uri photoURI;
if (Build.VERSION.SDK_INT > 21) {
    photoURI = FileProvider.getUriForFile(getApplicationContext(), com.zjelite.antlinkercampus
                                .BuildConfig.APPLICATION_ID + ".fileprovider", file);
} else {
    photoURI = Uri.fromFile(file);
}

//  Uri photoURI = FileProvider.getUriForFile(context,getPackageName()+ ".fileprovider", file);     //这种方式也可以获取URI

takePhotoIntent.putExtra(MediaStore.EXTRA_OUTPUT, photoURI);
startActivityForResult(takePhotoIntent, CAMERA_WITH_DATA);
```

###（4）、调用系统执行拍照的完整示例代码

```java

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/12/1:上午10:36
 * <p>
 * 说明：调用系统相机执行拍照操作
 * 主要知识点：
 * 1、调用系统相机执行拍照
 * 2、动态权限申请
 * 3、刷新相册
 * 4、开启APP对应的设置界面
 * 5、使用 FileProvider 解决7.0及以后系统中使用 Uri.fromUri() 获取URI之后调用相机崩溃的情况
 * 6、getExternalCacheDir()获取当前APP对应的缓存目录，使用该方式不用申请读写SD的权限
 * <p>
 * 注意：
 * 不同品牌的手机对拍照处理不一样，个别手机中即便我们没有指定存储路径，也会存储照片到默认的地址中。如：Galaxy Note4 调用系统相机执行拍照时，
 * 如果么有指定照片存储路径，则会存储在 DCIM/Camera 目录下，此时，即便不调用 刷新相册的方法，也会执行刷新操作。
 */
public class TakePhotoActivity extends AppCompatActivity {

    private ActivityTakephotoBinding binding;
    private File                     file;
    private       String   takePhotoMode          = "";
    private final String   MODE_TAKE_AND_SAVE     = "takePhotoAndSaveToLocal";
    private final String   MODE_TAKE_AND_NOT_SAVE = "takePhotoAndNotSaveToLocal";
    private final int      REQUEST_CODE1          = 0;    //申请权限时的请求码
    private final int      REQUEST_CODE2          = 1;    //申请权限时的请求码
    private final int      REQUEST_CODE3          = 2;    //申请权限时的请求码
    private final String[] DENIED_DESC            = {"没有拍照权限将不能使用拍照功能", "没有存储权限将不能存储照片到本地"};  //权限被拒绝的描述文本

    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_takephoto);

        initTakePhotoBtEvent();
    }

    private void initTakePhotoBtEvent() {

        binding.btTakePhoto.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                takePhotoMode = MODE_TAKE_AND_SAVE;
                takePhotoOrRequestPermission();
            }
        });
        binding.btTakePhoto2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                takePhotoMode = MODE_TAKE_AND_NOT_SAVE;
                takePhotoOrRequestPermission();
            }
        });
    }

    private void takePhotoOrRequestPermission() {
        String PERMISSION_CAMERA = Manifest.permission.CAMERA;
        boolean isCamearPermissionGranted = isPermissionGranted(TakePhotoActivity.this, PERMISSION_CAMERA);
        String PERMISSION_EXTERNAL_STORAGE = Manifest.permission.WRITE_EXTERNAL_STORAGE;
        boolean isExternalStoragePermissionGranted = isPermissionGranted(TakePhotoActivity.this, 
                PERMISSION_EXTERNAL_STORAGE);

        if (isCamearPermissionGranted && isExternalStoragePermissionGranted) {
            //如果权限已经被允许
            openSysCameraView();
        } else if (isExternalStoragePermissionGranted && !isCamearPermissionGranted) {
            //如果拍照权限未被允许，申请拍照权限
            requestNecessaryPermission(TakePhotoActivity.this, new String[]{PERMISSION_CAMERA}, REQUEST_CODE1);
        } else if (isCamearPermissionGranted && !isExternalStoragePermissionGranted) {
            //存储权限未被允许
            requestNecessaryPermission(TakePhotoActivity.this, new String[]{PERMISSION_EXTERNAL_STORAGE}, 
                    REQUEST_CODE2);
        } else {
            //啥子权限都没有
            requestNecessaryPermission(TakePhotoActivity.this, new String[]{PERMISSION_EXTERNAL_STORAGE, 
                    PERMISSION_CAMERA}, REQUEST_CODE3);
        }
    }

    private void requestNecessaryPermission(Activity activity, String[] permissions, int requestCode) {
        ActivityCompat.requestPermissions(activity, permissions, requestCode);
    }


    @Override
    public void onRequestPermissionsResult(int requestCode,
                                           @NonNull
                                                   String[] permissions,
                                           @NonNull
                                                   int[] grantResults) {
        switch (requestCode) {
            case REQUEST_CODE1:
                //如果有权限被允许了——因为此处值申请了一个拍照权限，所以只要被允许了就可以了
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    openSysCameraView();
                } else {
                    showDeniedDialog(TakePhotoActivity.this, DENIED_DESC[0]);
                }
                break;
            case REQUEST_CODE2:
                //如果有权限被允许了——因为此处值申请了一个拍照权限，所以只要被允许了就可以了
                if (grantResults.length > 0 && grantResults[0] == PackageManager.PERMISSION_GRANTED) {
                    openSysCameraView();
                } else {
                    showDeniedDialog(TakePhotoActivity.this, DENIED_DESC[1]);
                }
                break;
            case REQUEST_CODE3:
                if (grantResults.length > 0) {
                    boolean isAllGranted = true;
                    StringBuilder deniedHitDesc = new StringBuilder();    //权限被拒绝是的提示文本
                    for (int i = 0; i < grantResults.length; i++) {
                        boolean isCurGranted = grantResults[i] == PackageManager.PERMISSION_GRANTED;
                        if (!isCurGranted) {
                            deniedHitDesc.append(DENIED_DESC[i]).append("\n");
                        }
                        isAllGranted = isAllGranted && isCurGranted;
                    }

                    if (isAllGranted) {
                        openSysCameraView();
                    } else {
                        showDeniedDialog(TakePhotoActivity.this, deniedHitDesc.toString());
                    }
                }
                break;
            default:
                break;
        }

        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
    }

    /**
     * 展示被拒绝的弹窗
     */
    private void showDeniedDialog(final Context context, String message) {
        AlertDialog.Builder builder = new AlertDialog.Builder(context);
        builder.setTitle("提示");
        builder.setMessage(message);
        builder.setNegativeButton("就不给你权限", null);
        builder.setPositiveButton("我要重新开启权限", new DialogInterface.OnClickListener() {
            @Override
            public void onClick(DialogInterface dialog, int which) {
                openSysSettingPage(context);
            }
        });
        builder.setCancelable(false);
        builder.show();
    }

    /**
     * 打开系统设置界面
     */
    private void openSysSettingPage(Context context) {
        Intent intent = new Intent();
        intent.setAction(Settings.ACTION_APPLICATION_DETAILS_SETTINGS);
        Uri uri = Uri.fromParts("package", context.getPackageName(), null);
        intent.setData(uri);
        context.startActivity(intent);
    }

    /**
     * 检查权限是否已经通过
     *
     * @param context    上下文
     * @param permission 申请的权限
     */
    private boolean isPermissionGranted(Context context, String permission) {
        return PackageManager.PERMISSION_GRANTED == ContextCompat.checkSelfPermission(context, permission);
    }

    /**
     * 调用系统相机执行拍照
     * <p>
     * 这里使用的 getExternalCacheDir() 是系统为每个APP单独分配的缓存空间，返回一个绝对路径，API19以后使用该路径不需要申请权限，API19之前需
     * 要申请。该路径只对当前APP可用，其他APP不可访问，也就是说，如果我们将照片存储在这个路径，系统的媒体扫描器也无法检测到该路径的内容，也就无法
     * 实现 相册/图库 内容的刷新。APP卸载则该目录清空。getExternalFilesDir() 与此相同
     * <p>
     * If you saved your photo to the directory provided by getExternalFilesDir(), the media scanner cannot access the
     * files because they are private to your app.
     */
    private void openSysCameraView() {
        Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);

        if (MODE_TAKE_AND_SAVE.equals(takePhotoMode)) {
            //如果是拍照并存储，则需要指定在本地的存储路径，并需要获取拍照之后的结果
            //file = new File(getExternalCacheDir(), System.currentTimeMillis() + "temp.jpg");
            // file = new File(getExternalFilesDir(Environment.DIRECTORY_PICTURES), System.currentTimeMillis() + "temp" 
            //         + ".jpg");

            file = new File(Environment.getExternalStoragePublicDirectory(DIRECTORY_PICTURES), System
                    .currentTimeMillis() + "temp.jpg");
            Uri photoURI;
            photoURI = getPhotoUri(file);

            intent.putExtra(MediaStore.EXTRA_OUTPUT, photoURI);
        }
        startActivityForResult(intent, Activity.DEFAULT_KEYS_DIALER);
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        super.onActivityResult(requestCode, resultCode, data);
        if (Activity.RESULT_OK == resultCode) {
            //读取存储在本地的图片作为背景，这个图的清晰度会比较高
            if (MODE_TAKE_AND_SAVE.equals(takePhotoMode)) {
                Uri photoUri = getPhotoUri(file);
                try {
                    Drawable drawable = Drawable.createFromStream(getContentResolver().openInputStream(photoUri), "");
                    binding.parentLayout.setBackground(drawable);
                } catch (FileNotFoundException e) {
                    e.printStackTrace();
                }
                updateGallery(photoUri);
            } else {
                //相机拍照后会返回一个intent给onActivityResult。 intent的extra部分包含一个编码过的Bitmap缩略图,但这个Bitmap会比较模糊
                Bundle bundle = data.getExtras();
                if (null != bundle) {
                    Bitmap bitmap = (Bitmap) bundle.get("data");
                    Drawable drawable = new BitmapDrawable(bitmap);
                    binding.parentLayout.setBackground(drawable);
                }
            }
        }
        //模式使用完之后记得要重置
        takePhotoMode = "";
    }


    /**
     * 发送广播更新相册，不更新的话，在相册中将无法查看到截取的图片
     * If you saved your photo to the directory provided by getExternalFilesDir(), the media scanner cannot access the
     * files because they are private to your app.
     */
    private void updateGallery(Uri photoURI) {
        Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE, photoURI);
        sendBroadcast(intent);
    }

    private Uri getPhotoUri(File file) {
        Uri photoURI;
        if (Build.VERSION.SDK_INT > 21) {
            photoURI = FileProvider.getUriForFile(getApplicationContext(), BuildConfig.APPLICATION_ID + "" + "" + "" 
                    + ".fileprovider", file);
            //  Uri photoURI = FileProvider.getUriForFile(TakePhotoActivity.this,getPackageName()+ 
            // "" + ".fileprovider", file);     //这种方式也可以获取URI
        } else {
            photoURI = Uri.fromFile(file);
        }
        return photoURI;
    }
}
```
###（5）、权限申请
对于6.0之后需要在代码中动态申请权限，但是，对于6.0之前的版本还是必须要在 清单文件中声明拍照和本地存储设备的写入权限。
```java
     <!--拍照的权限，以及拍照的属性-自动聚焦-->
    <uses-permission android:name="android.permission.CAMERA"/>
    <uses-feature android:name="android.hardware.camera"/>
    <uses-feature android:name="android.hardware.camera.autofocus"/>
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"/>
```
###（6）、activity_takephoto.xml 
```java
<?xml version="1.0" encoding="utf-8"?>
<layout>

    <android.support.constraint.ConstraintLayout
        android:id="@+id/parentLayout"
        xmlns:android="http://schemas.android.com/apk/res/android"
        xmlns:app="http://schemas.android.com/apk/res-auto"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <Button
            android:id="@+id/bt_takePhoto"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="点击拍照--存储图片到本地"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_bias="0.327"/>

        <Button
            android:id="@+id/bt_takePhoto2"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="点击拍照--不存储到本地"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintEnd_toEndOf="parent"
            app:layout_constraintHorizontal_bias="0.497"
            app:layout_constraintStart_toStartOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:layout_constraintVertical_bias="0.569"/>
    </android.support.constraint.ConstraintLayout>
</layout>

```

##四、参考内容

###（1）、主要参考
重点看这篇官方文档：
[https://developer.android.com/training/camera/photobasics.html#TaskManifest](https://developer.android.com/training/camera/photobasics.html#TaskManifest)

###（2）、其他参考
下面这两篇原理透彻：==》但获取URI时使用 .provider 是会报错的
https://inthecheesefactory.com/blog/how-to-share-access-to-file-with-fileprovider-on-android-nougat/en

http://gelitenight.github.io/android/2017/01/29/solve-FileUriExposedException-caused-by-file-uri-with-FileProvider.html


这一篇是正解：==》获取URI的时候使用的是 .fileprovider
https://stackoverflow.com/questions/42251634/android-os-fileuriexposedexception-file-jpg-exposed-beyond-app-through-clipdata

后面这两篇没仔细看：
https://medium.com/@ali.muzaffar/what-is-android-os-fileuriexposedexception-and-what-you-can-do-about-it-70b9eb17c6d0

https://proandroiddev.com/sharing-files-though-intents-are-you-ready-for-nougat-70f7e9294a0b

##五、附录

* [完整代码已经上传到 GitHub ，链接为：https://github.com/CnPeng/CrazyAndroid。
文中的内容对应其中的 b_26_TakePhotoWithSysMethod](https://github.com/CnPeng/CrazyAndroid)

* [安卓 API版本 与 Version 的对应关系 https://source.android.com/setup/build-numbers](https://source.android.com/setup/build-numbers)


本文到此结束，谢谢观看！

---

**CnPeng 微信公众号上线了！！**

**我们可以聊聊软件开发，说说计算机硬件维护，侃侃历史知识，谈谈人生感悟。当然，我们还可以聊点其他的——具体你懂的。**

**欢迎扫描下方二维码关注!**

![](http://upload-images.jianshu.io/upload_images/2551993-d4bb113b7a0bf81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




