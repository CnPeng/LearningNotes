```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/6/27:下午3:06
 * <p>
 * 说明：应用程序内部截屏的实现（不截状态栏，兼容5.0 以上及以下版本）
 * -- 申请写入SD的权限
 * -- 实现应用内截取屏幕的功能（不截状态栏）
 * -- 计算状态栏的高度（两种方式）
 * -- 发送广播通知相册/图库刷新数据
 */

public class ScreenShotActivity extends AppCompatActivity {

    private Button    bt_screenShot;
    private ImageView iv_showScreenShot;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_screen_shot);

        initView();
        initEvent();
    }

    private void initView() {
        bt_screenShot = (Button) findViewById(R.id.bt_clickToScreenShot);
        iv_showScreenShot = (ImageView) findViewById(R.id.iv_showScreenShot);
    }

    private void initEvent() {
        bt_screenShot.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {       //点击之后，保存截图，更新相册
                Bitmap bitmap = getScreenShotBmp();
                boolean saveSuccess = saveScreenShotToSD(bitmap);
                String hint = saveSuccess ? "保存成功" : "保存失败";
                Toast.makeText(ScreenShotActivity.this, hint, Toast.LENGTH_SHORT).show();

                if (saveSuccess) {
                    iv_showScreenShot.setImageBitmap(bitmap);
                }
            }
        });
    }

    /**
     * 发送广播更新相册，不更新的话，在相册中将无法查看到截取的图片
     */
    private void updateGallery(File file) {
        Intent intent = new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE);
        Uri uri = Uri.fromFile(file);
        intent.setData(uri);
        sendBroadcast(intent);
    }

    /**
     * 保存截图到本地
     *
     * @param bitmap 截取到的图片
     */
    private boolean saveScreenShotToSD(Bitmap bitmap) {
        if (bitmap != null) {
            if (Environment.getExternalStorageState().equals(MEDIA_MOUNTED)) {  //如果SD存储设备可用
                String path = Environment.getExternalStorageDirectory().getPath() + File.separator + "校园集结号";
                File dir = new File(path);
                if (!dir.exists()) {
                    dir.mkdir();   //创建目录
                }

                File file = new File(dir, "截图成功.png");
                FileOutputStream fos = null;
                try {
                    fos = new FileOutputStream(file);

                    //根据指定的格式、质量、输出流 将bitmap保存到本地，并返回是否保存成功
                    boolean saveSuccess = bitmap.compress(Bitmap.CompressFormat.PNG, 100, fos);

                    if (saveSuccess) {
                        updateGallery(file);    //刷新相册
                    }

                    return saveSuccess;
                } catch (FileNotFoundException e1) {
                    e1.printStackTrace();
                    return false;
                } finally {
                    if (fos != null) {
                        try {
                            fos.close();    //关闭流防止溢出
                        } catch (IOException e1) {
                            e1.printStackTrace();
                        }
                    }
                }
            } else {
                Toast.makeText(this, "本地存储空间不可用", Toast.LENGTH_SHORT).show();
            }
        }
        return false;
    }

    /**
     * 获取屏幕截图
     */
    private Bitmap getScreenShotBmp() {
        View decorView = getWindow().getDecorView();    //获取当前activity所在的最顶层的view--DecorView
        decorView.setDrawingCacheEnabled(true);         //启用绘图缓存
        decorView.buildDrawingCache();                  //强制构建绘图缓存（防止上面启用绘图缓存的操作失败）
        Bitmap bitmap = decorView.getDrawingCache();     //获取绘图缓存中的 bitmap

        //        int statusBarHeight = getStatusBarHeight();
        int statusBarHeight = getStatusBarHeight(decorView);

        int newBmpHeight = bitmap.getHeight() - statusBarHeight;    //最终截取的图片的高度（取出状态栏之后的高度）

        bitmap = Bitmap.createBitmap(bitmap, 0, statusBarHeight, bitmap.getWidth(), newBmpHeight);

        decorView.setDrawingCacheEnabled(false);    //createBitmap完成之后一定要置为false，否则短时间内多次截图时内容不会变化！
        return bitmap;
    }

    /**
     * 获取状态栏高度方式2
     *
     * @param decorView 要获取状态栏高度的页面所在的顶层布局
     */
    private int getStatusBarHeight(View decorView) {
        Rect rect = new Rect();
        decorView.getWindowVisibleDisplayFrame(rect);
        return rect.top;
    }

    /**
     * 获取状态栏的高度--方式1
     */
    public int getStatusBarHeight() {
        int statusBarHeight = 0;
        int resourceID = getResources().getIdentifier("status_bar_height", "dimen", "android");
        if (resourceID != 0) {
            statusBarHeight = getResources().getDimensionPixelSize(resourceID);
        }
        return statusBarHeight;
    }

}

```
