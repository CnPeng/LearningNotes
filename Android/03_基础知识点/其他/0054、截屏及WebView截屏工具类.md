该工具类的新版在 https://gitee.com/CnPeng_1/LearningNotes/wikis/56%E3%80%81%E5%88%B7%E6%96%B0%E5%9B%BE%E5%BA%93?sort_id=721277 中


```java
package com.xxx.xxx;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.graphics.Bitmap;
import android.graphics.Canvas;
import android.graphics.Picture;
import android.net.Uri;
import android.support.annotation.NonNull;
import android.view.View;
import android.webkit.WebView;

import java.io.File;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;

/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/6/27:上午9:14
 * <p>
 * 说明：通用的图像处理工具
 */

public class CommonPicTools {

    /**
     * 170627 截屏（不包含状态栏）
     *
     * @param activity 要截取的view所处的activity界面-- 用来获取 docorView
     * @return 返回不包含状态栏的bitmap图像
     */
    public static Bitmap getScreenShotWithoutStatusBar(Activity activity) {
        View decorView = activity.getWindow().getDecorView();
        //允许进行绘图缓存
        decorView.setDrawingCacheEnabled(true);

        //慎用！！会增加内存消耗，可能会导致OOM。强制构建绘图缓存（防止上面的 setDrawingCachtEnabled 不生效）
        //decorView.buildDrawingCache();

        //获取绘图缓存（如果直接返回这个bitmap 可能会报错，提示使用已经被回收的bitmap）
        Bitmap bitmap = decorView.getDrawingCache(true);

        int statusBarHeight = CommonUtils.getStatusBarHeight(activity);
        if (null != bitmap) {
            //decorView.getDrawingCache可能会返回null
            bitmap = Bitmap.createBitmap(bitmap, 0, statusBarHeight, bitmap.getWidth(), bitmap.getHeight() - statusBarHeight);
        }
        //createBitmap完成后置为false，否则短时间内再次截图时如果view变化了，但是截取的内容不会发生变化
        decorView.setDrawingCacheEnabled(false);
        return bitmap;
    }

    /**
     * 170627 保存截屏图片到本地SD
     *
     * @param activity        被截取的view所在的activity（用来调用截图方法）
     * @param path            保存路径
     * @param picNameWithType 要保存的名称并携带扩展名 格式如： 集结号.png
     * @return 是否保存成功
     */
    public static boolean saveScreenShotToSDCard(Activity activity, String path, String picNameWithType) {
        return saveScreenShotToSDCard2(activity, path, picNameWithType, null, 90);
    }

    /**
     * CnPeng 2018/9/6 上午10:34  保存截屏图片到本地SD
     *
     * @param activity        被截取的view所在的activity（用来调用截图方法）
     * @param path            保存路径
     * @param picNameWithType 要保存的名称并携带扩展名 格式如： 集结号.png
     * @param bitmap          要保存的bitmap对象，默认当前屏幕截图
     * @param quality         保存的质量 默认80
     * @return 是否保存成功
     */
    public static boolean saveScreenShotToSDCard2(Activity activity, String path, String picNameWithType, Bitmap bitmap, int quality) {
        if (null == bitmap) {
            bitmap = getScreenShotWithoutStatusBar(activity);
        }

        if (quality <= 0) {
            quality = 80;
        }

        if (bitmap != null) {
            File dir = new File(path);
            if (!dir.exists()) {
                boolean mkDirSuccess = dir.mkdir();
                if (!mkDirSuccess) {
                    ToastUtil.toastShort("保存失败");
                    return false;
                }
            }

            File picFile = new File(dir, picNameWithType);
            FileOutputStream fos = null;
            try {
                fos = new FileOutputStream(picFile);
                //将bitmap对象按指定格式指定质量存储到本地
                boolean saveSuccess = bitmap.compress(Bitmap.CompressFormat.PNG, quality, fos);
                if (saveSuccess) {
                    updateGallery(activity, picFile);

                    if (!bitmap.isRecycled()) {
                        //170809 释放bitmap防止oom
                        bitmap.recycle();
                    }
                    return true;
                } else {
                    return false;
                }
            } catch (FileNotFoundException e) {
                e.printStackTrace();
                return false;
            } finally {
                if (null != fos) {
                    try {
                        fos.close();
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
            }
        }
        return false;
    }


    /**
     * 作者：CnPeng
     * 时间：2018/9/6 上午11:17
     * 功用：截取WebView中的内容作为图片——Bitmap
     * 说明：
     */
    public static Bitmap getWebViewSnapShot(@NonNull WebView webView) {
        // 这个地方很有意思，capturePicture方法提示被废弃了，建议使用onDraw,但是，onDraw确是一个Protected的
        Picture snapShot = webView.capturePicture();
        Bitmap bitmap = Bitmap.createBitmap(snapShot.getWidth(), snapShot.getHeight(), Bitmap.Config.ARGB_8888);
        Canvas canvas = new Canvas(bitmap);
        snapShot.draw(canvas);
        return bitmap;
    }

    /**
     * CnPeng 2018/9/6 上午10:34  截取WebView的内容为图片，并存储到本地SD
     *
     * @param activity        被截取的view所在的activity（用来调用截图方法）
     * @param pathToSave      保存路径
     * @param picNameWithType 要保存的名称并携带扩展名 格式如： 集结号.png
     * @param quality         保存的质量 默认80
     * @param webView         要截取内容为图片的webView
     * @return 是否保存成功
     */
    public static boolean saveWebViewSnapShot(@NonNull Activity activity, @NonNull String pathToSave,
                                              @NonNull String picNameWithType, int quality, @NonNull WebView webView) {

        Bitmap bitmap = getWebViewSnapShot(webView);

        return null != bitmap && saveScreenShotToSDCard2(activity, pathToSave, picNameWithType, bitmap, quality);
    }

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
}

```