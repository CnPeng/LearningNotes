![](http://upload-images.jianshu.io/upload_images/2551993-a2986a9de54715e1.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



##一、为什么封装？
众所周知，Android 从 6.0开始引入运行时权限机制，将权限分为了[普通权限和危险权限](https://developer.android.com/guide/topics/security/permissions.html?hl=zh-cn#normal-dangerous) ，对于危险权限我们必须在使用的时候动态的去申请。

但是，如果我们在每一个界面中把申请权限的代码全都写一遍，是很费力气的事情，而且不利于代码的维护，所以就需要封装。当然了，目前各位大佬前辈们已经封装了很多优秀的工具类，比如鸿洋大神在 [Android 6.0 运行时权限处理完全解析](http://blog.csdn.net/lmj623565791/article/details/50709663) 中封装的的[MPermission](https://github.com/hongyangAndroid/MPermissions)。

那么，我为什么还要重复造一个轮子呢？首先，虽然运行时权限出了很久了，但是一直没怎么看，不怎么熟悉，所以想仔细看看；其次，当前项目中的工具类无法完全满足需求，一次只能申请一个权限，如果需要申请多个权限只能重复写相关代码。

![](http://upload-images.jianshu.io/upload_images/2551993-9f9312ca63d64a61.gif?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

##二、基本原理和思路
###1、基本原理
啥子基本原理？想了解基本原理还是看[官方文档](https://developer.android.com/training/permissions/index.html?hl=zh-cn)。我说的肯定没有官方文档说的好，所以，我只说一下我们要用到的几个重要方法：
#### （1）、检查权限是否已经被允许
>**ContextCompat.checkSelfPermission(context, permission)**
>
>该方法的含义是检测某个权限是否已经被允许，返回值 为 boolean , true 表示允许，false 表示未被允许

####（2）、申请权限
> **ActivityCompat.requestPermissions(activity, permissions, requestCode);**
>
>向系统请求权限，接收三个参数，第一个是Activity对象，**第二个是 权限数组**，第三个是本地请求码。没有返回值

####（3）、权限请求结果
>**onRequestPermissionsResult(requestCode, permissions,grantResults)**
>
>这是在 activity 中重写的 Activity 的的方法， 该方法中反馈了权限的申请情况，返回的三个参数分别表示：请求码，被请求的权限数组，权限被允许情况的数组

####（4）、某个权限在之前是否被拒绝过？
>**ActivityCompat.shouldShowRequestPermissionRationale(activity, permission)**
>
>该方法用来检测之前某个权限是否被拒绝过，接收两个参数：activity对象，被检测的权限。
>
>按照官方的设计思路是，如果之前被拒绝过，那么当我再次准备去申请权限时必须给用户一个说明，说明我们为什么还要再次申请这个权限。
>
>但是，我个人认为 **在某个权限被拒绝之后，直接给出提示，告知用户可能会影响哪些功能并给出重新配置权限的引导会更好一些**。`比如，点击一个拍照按钮， 弹窗让我允许拍照权限，可是我直接点了个拒绝，然后页面就一直停在这里了，直到我下一次点击按钮时，通过检测是否已经拒绝过拍照权限，才能知道不赋予拍照权限就不能拍照。但是如果我点击拒绝之后，直接给我一个没有权限就不能拍照的提示，我会更容易接受，因为这样减少了我的操作，我也能直接知道我要做啥，我为什么要这么做`

###2、封装思路（需求）
还要啥思路？在介绍上面的四个方法是就已经将思路挑明了，无非就是两种模式，看下图：
![申请运行时权限的两种模式](http://upload-images.jianshu.io/upload_images/2551993-ba2a88cb5d39207f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

需要实现的需求：
* 能够一次申请多个权限
* 能够自由选择上面的两种模式

##三、示例代码：

![](http://upload-images.jianshu.io/upload_images/2551993-0e2dee790fcaf387.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


###1、工具类
代码中的注释已经很清晰了，所以不再做过多的解释了，看注释吧，看不懂得地方可以留言

```java

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/12/5:下午2:11
 * <p>
 * 说明：动态权限申请工具类 < 当前只在Activity中使用过，无异常。FM 中暂未测试 >
 * <p>
 * 注意，处理权限被拒绝有两种方式，
 * （1）一种是在拒绝之后直接给出提示，提示用户拒绝之后将不能使用XX功能，如果是这种方式的话，就使用一个参数的构造，然后直接判断权限是否全都被允许，
 * 如果全都被允许直接执行相关事件；如果有权限未被允许则申请权限。
 * （2）一种是拒绝之后不给提示，然后下次进入时先检测是否有被拒绝的，如果有则展示dialog，这种情况就需要调用两个参数的构造，并调用
 * showRequestReasonOrHandlePermissionEvent()方法，由该方法决定是否需要展示dialog，如果不需要，则执行外部通过PermissionGrantedFactory
 * 传递的事件。
 */

public class DynamicPermissionTool {
    private Context                  context;
    private PermissionGrantedFactory permissionGrantedFactory;

    public String[] permissions = {Manifest.permission.CAMERA, Manifest.permission.WRITE_EXTERNAL_STORAGE};
    public String[] deniedHints = {"没有相机权限将不能拍照", "没有存储设备的读写权限将不能存储照片到本地"};

    /**
     * 如果不需要判断是否之前被拒绝过，调用该构造
     */
    public DynamicPermissionTool(Context context) {
        this.context = context;
    }

    /**
     * 如果需要判断之前是否被拒绝过，调用该构造，
     *
     * @param context                  context
     * @param permissionGrantedFactory permissionGrantedFactory
     */
    public DynamicPermissionTool(Context context, PermissionGrantedFactory permissionGrantedFactory) {
        this.context = context;
        this.permissionGrantedFactory = permissionGrantedFactory;
    }

    /**
     * 检查单个权限是否已经被允许
     *
     * @param permission 要申请的权限
     */
    public boolean checkCurPermissionStatus(String permission) {
        return PackageManager.PERMISSION_GRANTED == ContextCompat.checkSelfPermission(context, permission);
    }

    /**
     * 检查一组权限的授权状态。
     *
     * @param permissions 权限数组
     * @return 权限的状态数组
     */
    public boolean[] checkCurPermissionsStatus(String[] permissions) {
        if (null != permissions && permissions.length > 0) {
            boolean[] permissionsStatus = new boolean[permissions.length];

            for (int i = 0; i < permissions.length; i++) {
                boolean permissionStatus = checkCurPermissionStatus(permissions[i]);
                permissionsStatus[i] = permissionStatus;
            }
            return permissionsStatus;
        } else {
            throw new IllegalArgumentException("参数不能为空，且必须有元素");
        }
    }

    /**
     * 获取被拒绝的权限
     *
     * @param permissions 要申请的全部权限
     */
    public String[] getDeniedPermissions(String[] permissions) {
        if (null != permissions && permissions.length > 0) {
            List<String> deniedPermissionList = new ArrayList<>();

            boolean[] permissionsStatus = checkCurPermissionsStatus(permissions);
            for (int i = 0; i < permissions.length; i++) {
                if (!permissionsStatus[i]) {
                    deniedPermissionList.add(permissions[i]);
                }
            }

            String[] deniedPermissions = new String[deniedPermissionList.size()];
            return deniedPermissionList.toArray(deniedPermissions);
        } else {
            throw new IllegalArgumentException("参数不能为空，且必须有元素");
        }
    }

    /**
     * 获取被拒绝的权限对应的提示文本数组
     *
     * @param permissions 要申请的全部权限
     * @param hints       权限被拒绝时的提示文本
     */
    public String[] getDeniedHint(String[] permissions, String[] hints) {
        if (null == permissions || null == hints || permissions.length == 0 || hints.length == 0 || permissions
                .length != hints.length) {
            throw new IllegalArgumentException("参数不能为空、必须有元素，且两个参数的长度必须一致");
        } else {
            List<String> deniedHintList = new ArrayList<>();

            boolean[] permissionsStatus = checkCurPermissionsStatus(permissions);
            for (int i = 0; i < permissions.length; i++) {
                if (!permissionsStatus[i]) {
                    deniedHintList.add(hints[i]);
                }
            }

            String[] deniedPermissions = new String[deniedHintList.size()];
            return deniedHintList.toArray(deniedPermissions);
        }
    }

    /**
     * 获取被拒绝的权限对应的提示文本字符串
     *
     * @param permissions 要申请的全部权限
     * @param hints       权限被拒绝时的提示文本
     */
    public String getDeniedHintStr(String[] permissions, String[] hints) {
        if (null == permissions || null == hints || permissions.length == 0 || hints.length == 0 || permissions
                .length != hints.length) {
            throw new IllegalArgumentException("参数不能为空、必须有元素，且两个参数的长度必须一致");
        } else {
            StringBuilder hintStr = new StringBuilder();
            boolean[] permissionsStatus = checkCurPermissionsStatus(permissions);
            for (int i = 0; i < permissions.length; i++) {
                if (!permissionsStatus[i]) {
                    hintStr.append(hints[i]).append("\n");
                }
            }
            return hintStr.toString();
        }
    }

    /**
     * 是否所有权限都已经被允许
     *
     * @param permissions 申请的权限
     * @return true 全被允许，false 有没有被允许的权限
     */
    public boolean isAllPermissionGranted(String[] permissions) {
        return getDeniedPermissions(permissions).length == 0;
    }

    /**
     * 是否所有权限都已经被允许
     *
     * @param grantResults 权限申请的结果
     * @return true 全被允许，false 有没有被允许的权限
     */
    public boolean isAllPermissionGranted(int[] grantResults) {
        int isAllGranted = PackageManager.PERMISSION_GRANTED;
        for (int grantResult : grantResults) {
            isAllGranted = isAllGranted | grantResult;
        }
        return isAllGranted == 0;
    }

    /**
     * 请求权限
     *
     * @param activity    Activity
     * @param permissions 权限
     * @param requestCode 请求码
     */
    public void requestNecessaryPermissions(Activity activity, String[] permissions, int requestCode) {
        ActivityCompat.requestPermissions(activity, permissions, requestCode);
    }


    /**
     * 检测之前是否已经拒绝过。如果已经拒绝过，那么再次请求权限的时候就需要给出原因
     *
     * @param activity    activity
     * @param permissions 请求的权限
     */
    public boolean shouldShowRequestReason(Activity activity, String[] permissions) {
        boolean showReason = false;
        for (int i = 0; i < permissions.length; i++) {
            showReason = showReason | ActivityCompat.shouldShowRequestPermissionRationale(activity, permissions[i]);
        }
        return showReason;
    }

    /**
     * 展示被拒绝的弹窗
     */
    public void showDeniedDialog(final Context context, String message) {
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
     * 如果需要判断之前是否被拒绝过，则创建该类的实例，然后将具体的事件处理放在该类的方法中
     */
    public interface PermissionGrantedFactory {
        /**
         * 处理事件或者请求权限：如果权限已经被允许，执行事件，否则请求权限
         */
        void handleEventOrRequestPermission();
    }

    /**
     * 判断是否需要展示为什么二次请求权限，如果不需要执行相应的操作
     * <p>
     * 该方法中首先会检测之前是否被拒绝过，如果已经被拒绝过则展示为什么需要再次申请这个权限，并引导用户去设置中开启权限。
     *
     * @param activity    activity
     * @param permissions 请求的权限
     * @param hints       权限被拒绝时的提示
     */
    public void showRequestReasonOrHandlePermissionEvent(Activity activity, String[] permissions, String[] hints) {
        boolean hadBeanDenied = shouldShowRequestReason(activity, permissions);
        if (hadBeanDenied) {
            showDeniedDialog(activity, getDeniedHintStr(permissions, hints));
        } else {
            if (null != permissionGrantedFactory) {
                permissionGrantedFactory.handleEventOrRequestPermission();
            }
        }
    }
}
```

###2、代码中实际使用：

下面的代码就是 [android: API24 及以上版本调用系统相机时报：FileUriExposedException 的解决](http://www.jianshu.com/p/4e959699b124) 的拍照代码，但是申请权限时已经换成了动态权限工具类

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
 * 如果么有指定照片存储路径，则会存储在 DCIM/Camera 目录下，此时，即便不调用 刷新相册的方法，也会执行刷新操作。V3
 * 
 * 171205 封装动态权限请求工具类
 */
public class TakePhotoActivity extends AppCompatActivity {

    private ActivityTakephotoBinding binding;
    private File                     file;
    private       String takePhotoMode          = "";
    private final String MODE_TAKE_AND_SAVE     = "takePhotoAndSaveToLocal";
    private final String MODE_TAKE_AND_NOT_SAVE = "takePhotoAndNotSaveToLocal";
    private final int    REQUEST_CODE1          = 0;    //申请权限时的请求码
    private DynamicPermissionTool                          permissionTool;
    private DynamicPermissionTool.PermissionGrantedFactory factory;

    @Override
    protected void onCreate(
            @Nullable
                    Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        binding = DataBindingUtil.setContentView(this, R.layout.activity_takephoto);

        //permissionTool = new DynamicPermissionTool(TakePhotoActivity.this);

        factory = new DynamicPermissionTool.PermissionGrantedFactory() {
            @Override
            public void handleEventOrRequestPermission() {
                takePhotoOrRequestPermission();
            }
        };
        permissionTool = new DynamicPermissionTool(TakePhotoActivity.this, factory);

        initTakePhotoBtEvent();
    }

    private void initTakePhotoBtEvent() {

        binding.btTakePhoto.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                takePhotoMode = MODE_TAKE_AND_SAVE;
                //handlePermissonEvent();
                permissionTool.showRequestReasonOrHandlePermissionEvent(TakePhotoActivity.this, permissionTool
                        .permissions, permissionTool.deniedHints);
            }
        });
        binding.btTakePhoto2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                takePhotoMode = MODE_TAKE_AND_NOT_SAVE;
                //handlePermissonEvent();
                permissionTool.showRequestReasonOrHandlePermissionEvent(TakePhotoActivity.this, permissionTool
                        .permissions, permissionTool.deniedHints);
            }
        });
    }

    private void takePhotoOrRequestPermission() {
        boolean isAllGranted = permissionTool.isAllPermissionGranted(permissionTool.permissions);

        if (isAllGranted) {
            openSysCameraView();
        } else {
            String[] deniedPermissions = permissionTool.getDeniedPermissions(permissionTool.permissions);
            permissionTool.requestNecessaryPermissions(TakePhotoActivity.this, deniedPermissions, REQUEST_CODE1);
        }
    }

    @Override
    public void onRequestPermissionsResult(int requestCode,
                                           @NonNull
                                                   String[] permissions,
                                           @NonNull
                                                   int[] grantResults) {
        switch (requestCode) {
            case REQUEST_CODE1:
                boolean isAllGranted = permissionTool.isAllPermissionGranted(grantResults);
                if (isAllGranted) {
                    openSysCameraView();
                } else {
                    //factory 不为空表示在执行点击事件时会先检测是否有权限被拒绝了，如果有则dialog的展示就放在检测完之后处理，所以此处不用重复展示
                    if (null == factory) {
                        String hint = permissionTool.getDeniedHintStr(permissionTool.permissions, permissionTool
                                .deniedHints);
                        permissionTool.showDeniedDialog(TakePhotoActivity.this, hint);
                    }
                }
                break;
            default:
                break;
        }
        super.onRequestPermissionsResult(requestCode, permissions, grantResults);
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

##四、附录

###1、代码地址：
文中对应代码已经上传到 GitHub ，项目地址：https://github.com/CnPeng。
文中 DynamicPermissionTool 在 utils 包中，TakePhotoActivity 在 b_26_TakePhotoWithSysMethod 包中 


###2、官方参考资料：
[系统权限的使用](https://developer.android.com/training/permissions/index.html?hl=zh-cn)
[正常权限与危险权限](https://developer.android.com/guide/topics/security/permissions.html?hl=zh-cn#normal-dangerous)


本文到此结束，谢谢观看！
**如有不足，敬请指正！**





