# 1. 1-registerActivityForResult

作用是替代传统的 startActivityForResult()，

**必须在 activity 的 onCreate() 方法或 fragment 的 onCreate()、onAttach() 里先注册，然后在需要调用的地方调用 launch 方法。**

## 1.1. 基础使用

在 Activity 的 onCreate() 方法或 fragment 的 onCreate()、onAttach() 里先调用如下函数：

```java
    /**
     * CnPeng:2021/7/8 11:50 registerForActivityResult 的基本使用方式
     */
    private void initBaseLauncher() {
        mBaseUseLauncher = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {
                if (result == null) {
                    return;
                }
                Intent data = result.getData();
                if (data == null) {
                    return;
                }
                String resultStr = data.getStringExtra("resultStr");
                mBinding.tvResult.setText(resultStr);
            }
        });
    }
```

然后在点击事件（或其他事件）中调用其 launch 方法，launch() 方法的参数不是固定的，该参数的类型跟 registerForActivityResult 第一个参数 `ActivityResultContract<I,O>` 中的 I 有关。

```java
mBaseUseLauncher.launch(new Intent(v.getContext(), ActResult2Activity.class));
```

## 1.2. ActivityResultContracts

Android 本身提供了常用的几个 Contracts , 如：PickContact、OpenDocument、TakePicture、RequestPermission 等。具体作用和使用方式参考下面的 完整示例。

我们可以直接使用这几个 Contracts , 也可以根据实际需要自行定义 Contract。



## 1.3. 完整示例：

```java

/**
 * CnPeng:2021/7/8 08:55 StartActivityForResult 的新方式
 */
public class ActResultActivity extends AppCompatActivity implements View.OnClickListener {
    private final String TAG = getClass().getSimpleName();

    private ActivityActResultBinding mBinding;
    private ActResultActivity        mActivity;

    private ActivityResultLauncher<Intent> mBaseUseLauncher;

    private ActivityResultLauncher<String[]> mSingleImgLauncher;
    private ActivityResultLauncher<String[]> mSingleMultiTypeDocLauncher;
    private ActivityResultLauncher<String[]> mSingleFreeTypeLauncher;
    private ActivityResultLauncher<String[]> mOpenMultiDocsLauncher;
    private ActivityResultLauncher<String>   mGetContentLauncher;
    private ActivityResultLauncher<Uri>      mOpenDocTreeLauncher;

    private ActivityResultLauncher<Void> mPickContactLauncher;

    private ActivityResultLauncher<String>   mRequestSinglePermissionLauncher;
    private ActivityResultLauncher<String[]> mRequestMultiPermissionLauncher;
    private ActivityResultLauncher<Uri>      mTakePicLauncher;
    private Uri                              mTargetPicUri;
    private ActivityResultLauncher<Void>     mTakePicLauncher2;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        mBinding = DataBindingUtil.setContentView(this, R.layout.activity_act_result);
        mActivity = this;

        initClickListener();
        initLauncher();
    }

    private void initClickListener() {

        mBinding.titleLayout.setClickListener(new CpTitleLayout.ClickListener() {
            @Override
            protected void onLeftBtnClick(View view) {
                finish();
            }
        });

        mBinding.btnBaseUse.setOnClickListener(this);

        // getDocument
        mBinding.btnSingleImg.setOnClickListener(this);
        mBinding.btnMultiTypeDoc.setOnClickListener(this);
        mBinding.btnMultiFreeDoc.setOnClickListener(this);

        // getDocuments
        mBinding.btnMultiDoc.setOnClickListener(this);

        // getContent
        mBinding.btnGetContent1.setOnClickListener(this);

        mBinding.btnOpenDocumentTree.setOnClickListener(this);

        mBinding.btnRequestPermission.setOnClickListener(this);
        mBinding.btnRequestPermissions.setOnClickListener(this);

        mBinding.btnGetContract.setOnClickListener(this);

        mBinding.btnTakePic.setOnClickListener(this);

        mBinding.btnTakePicPreview.setOnClickListener(this);
    }

    private void initLauncher() {

        initBaseLauncher();

        initOpenDocumentLauncher();

        initGetContentLauncher();

        initOpenDocTreeLauncher();

        getContractLauncher();

        getPermissionRequestLauncher();

        getPermissionsRequestLauncher();

        getMultiDocLauncher();

        getTakePicLauncher();

        getTakePicLauncher2();
    }

    private void getTakePicLauncher2() {
        mTakePicLauncher2 = registerForActivityResult(new ActivityResultContracts.TakePicturePreview(), new ActivityResultCallback<Bitmap>() {
            @Override
            public void onActivityResult(Bitmap result) {
                if (null != result) {
                    // 这应该是一个缩略图
                    mBinding.ivResult.setImageBitmap(result);
                }
            }
        });
    }

    /**
     * CnPeng:2021/7/8 18:08 拍照
     */
    private void getTakePicLauncher() {
        mTargetPicUri = getTargetPicUri();
        mTakePicLauncher = registerForActivityResult(new ActivityResultContracts.TakePicture(), new ActivityResultCallback<Boolean>() {
            @Override
            public void onActivityResult(Boolean result) {
                Log.d(TAG, "onActivityResult: " + result);
                if (result) {
                    mBinding.ivResult.setImageURI(mTargetPicUri);
                }
            }
        });
    }

    /**
     * CnPeng:2021/7/8 17:07 选择多个文件
     * 选择文件时，如果直接单击还是选择单个文件；长按则可以实现多选。
     * 而且，没法对数量进行限制
     */
    private void getMultiDocLauncher() {
        mOpenMultiDocsLauncher = registerForActivityResult(new ActivityResultContracts.OpenMultipleDocuments(), new ActivityResultCallback<List<Uri>>() {
            @Override
            public void onActivityResult(List<Uri> result) {
                if (result == null) {
                    return;
                }
                StringBuffer sb = new StringBuffer("选择的文件 Uri 为：");
                for (Uri uri : result) {
                    sb.append("\n\n");
                    sb.append(uri);
                }
                mBinding.tvResult.setText(sb);
            }
        });
    }

    /**
     * CnPeng:2021/7/8 16:46 请求多个权限
     */
    private void getPermissionsRequestLauncher() {
        mRequestMultiPermissionLauncher = registerForActivityResult(new ActivityResultContracts.RequestMultiplePermissions(), new ActivityResultCallback<Map<String, Boolean>>() {
            @Override
            public void onActivityResult(Map<String, Boolean> result) {
                Set<String> strings = result.keySet();

                List<String> deniedList = new ArrayList<>();
                for (String key : strings) {
                    Boolean allowed = result.get(key);
                    if (!allowed) {
                        deniedList.add(key);
                    }
                }

                StringBuffer sb = new StringBuffer("被拒绝的权限：");
                for (String s : deniedList) {
                    sb.append(s);
                }
                mBinding.tvResult.setText(sb);
            }
        });
    }

    /**
     * CnPeng:2021/7/8 16:37 请求单个权限的 Launcher
     */
    private void getPermissionRequestLauncher() {
        mRequestSinglePermissionLauncher = registerForActivityResult(new ActivityResultContracts.RequestPermission(), new ActivityResultCallback<Boolean>() {
            @Override
            public void onActivityResult(Boolean result) {
                Toast.makeText(mActivity, result ? "权限被允许了" : "权限被拒绝了", Toast.LENGTH_SHORT).show();
            }
        });
    }

    /**
     * CnPeng:2021/7/8 16:37 读取联系人信息的 Launcher（包含读取联系人信息和获取手机号的操作）
     */
    private void getContractLauncher() {
        mPickContactLauncher = registerForActivityResult(new ActivityResultContracts.PickContact(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }

                // API 26 以后 ContentResoler 才增加 query 方法
                if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {

                    Log.d(TAG, "onActivityResult: 获取联系人Result-" + result);
                    Cursor cursor = getContentResolver().query(result, null, null, null);
                    if (cursor.moveToFirst()) {
                        int nameIndex = cursor.getColumnIndex(ContactsContract.Contacts.DISPLAY_NAME);
                        String name = cursor.getString(nameIndex);
                        Log.d(TAG, "onActivityResult: 联系人姓名-" + name);

                        int idIndex = cursor.getColumnIndex(ContactsContract.Contacts._ID);
                        String id = cursor.getString(idIndex);
                        Log.d(TAG, "onActivityResult: 联系人ID-" + id);

                        int hasPhoneIndex = cursor.getColumnIndex(ContactsContract.Contacts.HAS_PHONE_NUMBER);
                        String hasPhoneStr = cursor.getString(hasPhoneIndex);
                        // 值为 "1" 表示有手机号，"0" 表示没有手机号
                        if (hasPhoneStr.equals("1")) {
                            Log.d(TAG, "onActivityResult: 读取手机号URI-" + ContactsContract.CommonDataKinds.Phone.CONTENT_URI);
                            // 读联系人电话之前，需要先请求 ReadContacts 的权限
                            Cursor phonesCusor = getContentResolver().query(
                                    ContactsContract.CommonDataKinds.Phone.CONTENT_URI,
                                    new String[]{ContactsContract.CommonDataKinds.Phone.NUMBER},
                                    ContactsContract.CommonDataKinds.Phone.CONTACT_ID + "=" + id,
                                    null,
                                    null);

                            if (phonesCusor == null) {
                                return;
                            }

                            //因为每个联系人可能有多个电话号码，所以需要遍历
                            StringBuilder numStr = new StringBuilder("手机号码：");
                            if (phonesCusor.moveToFirst()) {
                                do {
                                    String num = phonesCusor.getString(0);
                                    Log.d(TAG, "onActivityResult: 电话号码：" + num);
                                    numStr.append("\n");
                                    numStr.append(num);

                                } while (phonesCusor.moveToNext());

                                mBinding.tvResult.setText(numStr);
                            }
                            phonesCusor.close();
                        }
                    }
                    cursor.close();
                }
            }
        });
    }

    /**
     * CnPeng:2021/7/8 12:02 选择目录
     */
    private void initOpenDocTreeLauncher() {
        mOpenDocTreeLauncher = registerForActivityResult(new ActivityResultContracts.OpenDocumentTree(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }
                Log.d(TAG, "onActivityResult: " + result);
                // 考虑如何将 URI 转换为文件对象
                String path = result.getPath();
                mBinding.tvResult.setText(path);
            }
        });
    }

    /**
     * CnPeng:2021/7/8 11:50 registerForActivityResult 的基本使用方式
     */
    private void initBaseLauncher() {
        mBaseUseLauncher = registerForActivityResult(new ActivityResultContracts.StartActivityForResult(), new ActivityResultCallback<ActivityResult>() {
            @Override
            public void onActivityResult(ActivityResult result) {
                if (result == null) {
                    return;
                }
                Intent data = result.getData();
                if (data == null) {
                    return;
                }
                String resultStr = data.getStringExtra("resultStr");
                mBinding.tvResult.setText(resultStr);
            }
        });
    }

    /**
     * CnPeng:2021/7/8 11:45 GetContent 可以选择指定类型的单个文件
     */
    private void initGetContentLauncher() {
        mGetContentLauncher = registerForActivityResult(new ActivityResultContracts.GetContent(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }
                Log.d(TAG, "onActivityResult: " + result);
                // 考虑如何将 URI 转换为文件对象
                String path = result.getPath();
                mBinding.tvResult.setText(path);
            }
        });
    }

    /**
     * CnPeng:2021/7/8 11:34 GetDoucument 可以选择指定类型的单个文件（也可以是多类型的单个文件）
     */
    private void initOpenDocumentLauncher() {
        mSingleImgLauncher = registerForActivityResult(new ActivityResultContracts.OpenDocument(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }
                Log.d(TAG, "onActivityResult: " + result);
                mBinding.tvResult.setText(result.toString());
                mBinding.ivResult.setImageURI(result);
            }
        });

        mSingleMultiTypeDocLauncher = registerForActivityResult(new ActivityResultContracts.OpenDocument(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }
                Log.d(TAG, "onActivityResult: " + result);
                // 考虑如何将 URI 转换为文件对象
                String path = result.getPath();
                mBinding.tvResult.setText(path);
            }
        });

        mSingleFreeTypeLauncher = registerForActivityResult(new ActivityResultContracts.OpenDocument(), new ActivityResultCallback<Uri>() {
            @Override
            public void onActivityResult(Uri result) {
                if (result == null) {
                    return;
                }
                Log.d(TAG, "onActivityResult: " + result);
                // 考虑如何将 URI 转换为文件对象
                String path = result.getPath();
                mBinding.tvResult.setText(path);
            }
        });
    }


    @Override
    public void onClick(View v) {
        int viewId = v.getId();
        if (viewId == R.id.btn_single_img) {
            // 只选择单图
            mSingleImgLauncher.launch(new String[]{"image/*"});
        } else if (viewId == R.id.btn_multi_type_doc) {
            // 选择单个图片或者txt文档
            mSingleMultiTypeDocLauncher.launch(new String[]{"image/*", "text/plain"});
        } else if (viewId == R.id.btn_multi_free_doc) {
            // 选择单个任意类型的文件
            mSingleFreeTypeLauncher.launch(new String[]{"*/*"});
        } else if (viewId == R.id.btn_base_use) {
            mBaseUseLauncher.launch(new Intent(v.getContext(), ActResult2Activity.class));
        } else if (viewId == R.id.btn_getContent1) {
            // 选 pdf 文档
            // mGetContentLauncher.launch("application/pdf");
            // 选图片
            mGetContentLauncher.launch("image/*");
        } else if (viewId == R.id.btn_openDocumentTree) {
            // 参数为 Uri。传递 null 时，初次打开展示系统根目录，之后默认打开上次选择的目录，卸载重装时也是会默认打开上次的目录
            mOpenDocTreeLauncher.launch(null);
        } else if (viewId == R.id.btn_getContract) {
            mPickContactLauncher.launch(null);
        } else if (viewId == R.id.btn_requestPermission) {
            mRequestSinglePermissionLauncher.launch(Manifest.permission.READ_CONTACTS);
        } else if (viewId == R.id.btn_requestPermissions) {
            // 请求多个权限
            mRequestMultiPermissionLauncher.launch(new String[]{Manifest.permission.WRITE_EXTERNAL_STORAGE, Manifest.permission.READ_CONTACTS, Manifest.permission.CAMERA});
        } else if (viewId == R.id.btn_multi_doc) {
            // 选择多个文件
            mOpenMultiDocsLauncher.launch(new String[]{"image/*", "application/pdf"});
        } else if (viewId == R.id.btn_takePic) {
            mTakePicLauncher.launch(mTargetPicUri);
        } else if (viewId == R.id.btn_takePicPreview) {
            mTakePicLauncher2.launch(null);
        }
    }

    private Uri getTargetPicUri() {
        Uri uri;
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.Q) {
            ContentValues values = new ContentValues();
            values.put(MediaStore.MediaColumns.DISPLAY_NAME, "图片名称.jpg");
            values.put(MediaStore.MediaColumns.RELATIVE_PATH, Environment.DIRECTORY_PICTURES);
            uri = getContentResolver().insert(MediaStore.Images.Media.EXTERNAL_CONTENT_URI, values);
        } else {
            uri = FileProvider.getUriForFile(this, "cnpeng.android", new File(getExternalCacheDir().getAbsolutePath() + "图片名称.jpg"));
        }
        return uri;
    }
}
```

xml 布局文件：

```xml
<?xml version="1.0" encoding="utf-8"?>

<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

    </data>

    <androidx.constraintlayout.widget.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        tools:context="com.cnpeng.newapi.activityresult.ActResultActivity">

        <com.cnpeng.custom.titlelayout.CpTitleLayout
            android:id="@+id/titleLayout"
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:title="registerActivityForResult" />

        <androidx.core.widget.NestedScrollView
            android:id="@+id/scrollView"
            android:layout_width="150dp"
            android:layout_height="0dp"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toBottomOf="@id/titleLayout">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">


                <Button
                    android:id="@+id/btn_base_use"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="基本使用方法" />

                <Button
                    android:id="@+id/btn_single_img"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择单张图片" />

                <Button
                    android:id="@+id/btn_multi_type_doc"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择单个图片或txt" />

                <Button
                    android:id="@+id/btn_multi_free_doc"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择单个任意类型文件" />

                <Button
                    android:id="@+id/btn_multi_doc"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择指定类型的多个文件" />

                <Button
                    android:id="@+id/btn_getContent1"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择单个文件-getContent" />

                <Button
                    android:id="@+id/btn_openDocumentTree"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="选择目录-openDocumentTree" />

                <Button
                    android:id="@+id/btn_requestPermission"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="请求读通讯录的权限" />

                <Button
                    android:id="@+id/btn_requestPermissions"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="请求多个权限" />

                <Button
                    android:id="@+id/btn_getContract"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="读取联系人信息-getContract" />

                <Button
                    android:id="@+id/btn_takePic"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="takePicture" />

                <Button
                    android:id="@+id/btn_takePicPreview"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="TakePicturePreview" />


            </LinearLayout>
        </androidx.core.widget.NestedScrollView>

        <LinearLayout
            android:layout_width="0dp"
            android:layout_height="0dp"
            android:background="#ffc"
            android:orientation="vertical"
            app:layout_constraintBottom_toBottomOf="parent"
            app:layout_constraintLeft_toRightOf="@id/scrollView"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toTopOf="@id/scrollView">

            <TextView
                android:id="@+id/tv_result"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/dp10"
                tools:text="这是结果" />

            <ImageView
                android:id="@+id/iv_result"
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_marginTop="@dimen/dp10" />
        </LinearLayout>
    </androidx.constraintlayout.widget.ConstraintLayout>
</layout>
```