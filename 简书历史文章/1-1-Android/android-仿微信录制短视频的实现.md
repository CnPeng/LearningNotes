##一、需求
仿微信短视频录制，点击拍照，长按录视频

##二、实现代码

###1、RecordVideoActivity
```
/**
 * Created by yxj on 17/4/17.
 * 
 * 录制视频的界面
 */

public class RecordVideoActivity extends TitleActivity {
    private JCameraView mJCameraView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        requestWindowFeature(Window.FEATURE_NO_TITLE);//没有标题
        this.getWindow().setFlags(WindowManager.LayoutParams.FLAG_FULLSCREEN, WindowManager.LayoutParams.FLAG_FULLSCREEN);//设置全屏
        this.getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
        setRequestedOrientation(ActivityInfo.SCREEN_ORIENTATION_NOSENSOR);//设置竖屏
        setContentView(R.layout.record_video);

        mJCameraView = (JCameraView) findViewById(R.id.cameraView);
        mJCameraView.setActivity(this);
        //设置视频保存路径（如果不设置默认为Environment.getExternalStorageDirectory().getPath()）
        mJCameraView.setAutoFocus(false);
//        mJCameraView.setSaveVideoPath(Environment.getExternalStorageDirectory().getPath());
        mJCameraView.setCameraViewListener(new JCameraView.CameraViewListener() {
            @Override
            public void quit() {
                RecordVideoActivity.this.finish();
            }

            @Override
            public void captureSuccess(Bitmap bitmap) {//拍照成功
                String url = FileUtil.saveBitmapToSDCard(bitmap);//拍照保存路径
                Intent intent = new Intent();
                //把返回数据存入Intent 0是照片,1是视频
                intent.putExtra("url", url);
                intent.putExtra("type", 0);
                //设置返回数据
                setResult(RESULT_OK, intent);
                finish();
            }

            @Override
            public void recordSuccess(String url) {//录视频成功
                Intent intent = new Intent();
                //把返回数据存入Intent 0是照片,1是视频
                intent.putExtra("url", url);
                intent.putExtra("type", 1);
                //设置返回数据
                setResult(RESULT_OK, intent);
                finish();
            }
        });
    }

    @Override
    protected void onResume() {
        if (Build.VERSION.SDK_INT < 23) {
            if (!PermissionUtils.PermissionToolBefore23()){
                AlertDialog.Builder builder = new AlertDialog.Builder(this);
                builder.setTitle("提示")
                        .setMessage("没有照相机权限,请赋予本权限再开始拍照吧~").setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface arg0, int arg1) {
                        finish();
                    }
                }).show();
                return;
            }
        }
        mJCameraView.onResume();
        super.onResume();
    }

    @Override
    protected void onPause() {
        mJCameraView.onPause();
        super.onPause();
    }

    public PermissionUtils.PermissionGrant mPermissionGrant = new PermissionUtils.PermissionGrant() {
        @Override
        public void onPermissionGranted(int requestCode) {
            switch (requestCode) {
                case PermissionUtils.CODE_CAMERA:
                    PermissionUtils.requestPermission(RecordVideoActivity.this, PermissionUtils.CODE_RECORD_AUDIO,
                            mPermissionGrant, false);
                    break;
                case PermissionUtils.CODE_RECORD_AUDIO:
                    BuriedPointManager.getInstance().savePoint(R.integer.share_create_photograph_click);
                    break;
                default:
                    break;
            }
        }
    };
}
```
###2、record_video.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

    <com.zjelite.commonview.JCameraView
        android:id="@+id/cameraView"
        android:layout_width="match_parent"
        android:layout_height="match_parent"/>
</LinearLayout>
```
###3、CaptureButton
```

/**
 * 录制视频的按钮,长按录制,点击拍照
 */
public class CaptureButton extends View {
    public final String TAG = "CaptureButtom";

    private Paint               mPaint;
    private Context             mContext;
    private RecordVideoActivity activity;
    
    private float btn_center_Y;
    private float btn_center_X;

    private float btn_inside_radius;
    private float btn_outside_radius;
    //before radius
    private float btn_before_inside_radius;
    private float btn_before_outside_radius;
    //after radius
    private float btn_after_inside_radius;
    private float btn_after_outside_radius;

    private float btn_return_length;
    private float btn_return_X;
    private float btn_return_Y;

    private float btn_left_X, btn_right_X, btn_result_radius;

    //state
    private int STATE_SELECTED;
    private final int STATE_LESSNESS       = 0;     //空闲状态
    private final int STATE_KEY_DOWN       = 1;
    private final int STATE_CAPTURED       = 2;
    private final int STATE_RECORD         = 3;
    private final int STATE_PICTURE_BROWSE = 4;     //拍照完成后的预览状态
    private final int STATE_RECORD_BROWSE  = 5;     //录像完成后的预览状态
    private final int STATE_READYQUIT      = 6;     //退出
    private final int STATE_RECORDED       = 7;

    private float key_down_Y;

    private RectF rectF;
    private float             progress          = 0;
    private LongPressRunnable longPressRunnable = new LongPressRunnable();
    private RecordRunnable    recordRunnable    = new RecordRunnable();
    private ValueAnimator     record_anim       = ValueAnimator.ofFloat(0, 360);
    private CaptureListener mCaptureListener;

    public CaptureButton(Context context) {
        this(context, null);
    }

    public CaptureButton(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public CaptureButton(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        mContext = context;
        mPaint = new Paint();
        mPaint.setAntiAlias(true);
        STATE_SELECTED = STATE_LESSNESS;
    }

    public void setActivity(RecordVideoActivity activity) {
        this.activity = activity;
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
//        int widthMode = MeasureSpec.getMode(widthMeasureSpec);
        int widthSize = MeasureSpec.getSize(widthMeasureSpec);
//        int heightMode = MeasureSpec.getMode(heightMeasureSpec);
//        int heightSize = MeasureSpec.getSize(heightMeasureSpec);
        int width = widthSize;
        Log.i(TAG, "measureWidth = " + width);
        int height = (width / 9) * 4;
        setMeasuredDimension(width, height);
    }

    @Override
    protected void onSizeChanged(int w, int h, int oldw, int oldh) {
        super.onSizeChanged(w, h, oldw, oldh);

        btn_center_X = getWidth() / 2f;
        btn_center_Y = getHeight() / 2f;

        btn_outside_radius = (float) (getWidth() / 9);
        btn_inside_radius = (float) (btn_outside_radius * 0.75);

        btn_before_outside_radius = (float) (getWidth() / 9);
        btn_before_inside_radius = (float) (btn_outside_radius * 0.75);
        btn_after_outside_radius = (float) (getWidth() / 6);
        btn_after_inside_radius = (float) (btn_outside_radius * 0.6);

        btn_return_length = (float) (btn_outside_radius * 0.35);
        //        btn_result_radius = 80;
        btn_result_radius = (float) (getWidth() / 9);
        btn_left_X = getWidth() / 2;
        btn_right_X = getWidth() / 2;
    }

    @Override
    protected void onDraw(Canvas canvas) {
        super.onDraw(canvas);
        if (STATE_SELECTED == STATE_LESSNESS || STATE_SELECTED == STATE_RECORD) {
            //draw capture button
            mPaint.setColor(0xFFEEEEEE);
            canvas.drawCircle(btn_center_X, btn_center_Y, btn_outside_radius, mPaint);
            mPaint.setColor(Color.WHITE);
            canvas.drawCircle(btn_center_X, btn_center_Y, btn_inside_radius, mPaint);

            //draw Progress bar
            Paint paintArc = new Paint();
            paintArc.setAntiAlias(true);
            paintArc.setColor(0xFF00CC00);
            paintArc.setStyle(Paint.Style.STROKE);
            paintArc.setStrokeWidth(10);

            rectF = new RectF(btn_center_X - (btn_after_outside_radius - 5), btn_center_Y - (btn_after_outside_radius
                    - 5), btn_center_X + (btn_after_outside_radius - 5), btn_center_Y + (btn_after_outside_radius - 5));
            canvas.drawArc(rectF, -90, progress, false, paintArc);

            //draw return button
            Paint paint = new Paint();
            paint.setAntiAlias(true);
            paint.setColor(Color.WHITE);
            paint.setStyle(Paint.Style.STROKE);
            paint.setStrokeWidth(4);
            Path path = new Path();

            btn_return_X = ((getWidth() / 2) - btn_outside_radius) / 2;
            btn_return_Y = (getHeight() / 2 + 10);

            path.moveTo(btn_return_X - btn_return_length, btn_return_Y - btn_return_length);
            path.lineTo(btn_return_X, btn_return_Y);
            path.lineTo(btn_return_X + btn_return_length, btn_return_Y - btn_return_length);
            canvas.drawPath(path, paint);
        } else if (STATE_SELECTED == STATE_RECORD_BROWSE || STATE_SELECTED == STATE_PICTURE_BROWSE) {

            mPaint.setColor(0xFFEEEEEE);
            canvas.drawCircle(btn_left_X, btn_center_Y, btn_result_radius, mPaint);
            mPaint.setColor(Color.WHITE);
            canvas.drawCircle(btn_right_X, btn_center_Y, btn_result_radius, mPaint);

            //left button
            Paint paint = new Paint();
            paint.setAntiAlias(true);
            paint.setColor(Color.BLACK);
            paint.setStyle(Paint.Style.STROKE);
            paint.setStrokeWidth(3);
            Path path = new Path();

            path.moveTo(btn_left_X - 2, btn_center_Y + 14);
            path.lineTo(btn_left_X + 14, btn_center_Y + 14);
            path.arcTo(new RectF(btn_left_X, btn_center_Y - 14, btn_left_X + 28, btn_center_Y + 14), 90, -180);
            path.lineTo(btn_left_X - 14, btn_center_Y - 14);
            canvas.drawPath(path, paint);

            paint.setStyle(Paint.Style.FILL);
            path.reset();
            path.moveTo(btn_left_X - 14, btn_center_Y - 22);
            path.lineTo(btn_left_X - 14, btn_center_Y - 6);
            path.lineTo(btn_left_X - 23, btn_center_Y - 14);
            path.close();
            canvas.drawPath(path, paint);

            paint.setStyle(Paint.Style.STROKE);
            paint.setColor(0xFF00CC00);
            paint.setStrokeWidth(4);
            path.reset();
            path.moveTo(btn_right_X - 28, btn_center_Y);
            path.lineTo(btn_right_X - 8, btn_center_Y + 22);
            path.lineTo(btn_right_X + 30, btn_center_Y - 20);
            path.lineTo(btn_right_X - 8, btn_center_Y + 18);
            path.close();
            canvas.drawPath(path, paint);
        }
    }

    @Override
    public boolean onTouchEvent(MotionEvent event) {
//        try {
            switch (event.getAction()) {
                case MotionEvent.ACTION_DOWN:
                    if (STATE_SELECTED == STATE_LESSNESS) {
                        if (event.getY() > btn_return_Y - 37 && event.getY() < btn_return_Y + 10 && event.getX() > 
                                btn_return_X - 37 && event.getX() < btn_return_X + 37) {
                            STATE_SELECTED = STATE_READYQUIT;
                        } else if (event.getY() > btn_center_Y - btn_outside_radius && event.getY() < btn_center_Y + 
                                btn_outside_radius && event.getX() > btn_center_X - btn_outside_radius && event.getX
                                () < btn_center_X + btn_outside_radius && event.getPointerCount() == 1) {
                            key_down_Y = event.getY();
                            STATE_SELECTED = STATE_KEY_DOWN;
                            postCheckForLongTouch();
                        }
                    } else if (STATE_SELECTED == STATE_RECORD_BROWSE || STATE_SELECTED == STATE_PICTURE_BROWSE) {
                        if (event.getY() > btn_center_Y - btn_result_radius && event.getY() < btn_center_Y + 
                                btn_result_radius && event.getX() > btn_left_X - btn_result_radius && event.getX() < 
                                btn_left_X + btn_result_radius && event.getPointerCount() == 1) {
                            if (mCaptureListener != null) {
                                if (STATE_SELECTED == STATE_RECORD_BROWSE) {
                                    mCaptureListener.deleteRecordResult();
                                } else if (STATE_SELECTED == STATE_PICTURE_BROWSE) {
                                    mCaptureListener.cancel();
                                }
                            }
                            STATE_SELECTED = STATE_LESSNESS;
                            btn_left_X = btn_center_X;
                            btn_right_X = btn_center_X;
                            invalidate();
                        } else if (event.getY() > btn_center_Y - btn_result_radius && event.getY() < btn_center_Y + 
                                btn_result_radius && event.getX() > btn_right_X - btn_result_radius && event.getX() <
                                btn_right_X + btn_result_radius && event.getPointerCount() == 1) {
                            if (mCaptureListener != null) {
                                if (STATE_SELECTED == STATE_RECORD_BROWSE) {
                                    mCaptureListener.getRecordResult();
                                } else if (STATE_SELECTED == STATE_PICTURE_BROWSE) {
                                    mCaptureListener.determine();
                                }
                            }
                            STATE_SELECTED = STATE_LESSNESS;
                            btn_left_X = btn_center_X;
                            btn_right_X = btn_center_X;
                            invalidate();
                        }
                    }
                    break;
                case MotionEvent.ACTION_MOVE:
                    //                    if (event.getY() > btn_center_Y - btn_outside_radius &&
                    //                            event.getY() < btn_center_Y + btn_outside_radius &&
                    //                            event.getX() > btn_center_X - btn_outside_radius &&
                    //                            event.getX() < btn_center_X + btn_outside_radius) {
                    //                    }
                    //                    if (mCaptureListener != null) {
                    //                        mCaptureListener.scale(key_down_Y - event.getY());
                    //                    }
                    break;
                case MotionEvent.ACTION_UP:
                    removeCallbacks(longPressRunnable);
                    if (STATE_SELECTED == STATE_READYQUIT) {
                        if (event.getY() > btn_return_Y - 37 && event.getY() < btn_return_Y + 10 && event.getX() > 
                                btn_return_X - 37 && event.getX() < btn_return_X + 37) {
                            STATE_SELECTED = STATE_LESSNESS;
                            if (mCaptureListener != null) {    // 录制完成不使用直接返回键时，这个监听器null了，导致再次点击下箭头无法返回
                                mCaptureListener.quit();
                            }
                        }
                    } else if (STATE_SELECTED == STATE_KEY_DOWN) {
                        if (event.getY() > btn_center_Y - btn_outside_radius && event.getY() < btn_center_Y + 
                                btn_outside_radius && event.getX() > btn_center_X - btn_outside_radius && event.getX
                                () < btn_center_X + btn_outside_radius) {
                            if (mCaptureListener != null) {
                                mCaptureListener.capture();
                            }
                            STATE_SELECTED = STATE_PICTURE_BROWSE;
                        }
                    } else if (STATE_SELECTED == STATE_RECORD) {
                        LogUtils.e("CaptureButton", "record_anim.getCurrentPlayTime==" + record_anim
                                .getCurrentPlayTime());
                        if (record_anim.getCurrentPlayTime() < 800) {
                            ToastUtil.toastShort("视频过短，请重新录制");
                            STATE_SELECTED = STATE_LESSNESS;
                            //                        Toast.makeText(mContext, "Under time", Toast.LENGTH_SHORT).show();
                            progress = 0;
                            invalidate();
                            record_anim.cancel();
                            if (mCaptureListener != null) {
                                mCaptureListener.cancel();
                            }
                        } else {
                            STATE_SELECTED = STATE_RECORD_BROWSE;
                            removeCallbacks(recordRunnable);
                            //                        Toast.makeText(mContext, "Time length " + record_anim
                            // .getCurrentPlayTime(), Toast.LENGTH_SHORT).show();
                            captureAnimation(getWidth() / 5, (getWidth() / 5) * 4);
                            record_anim.cancel();
                            progress = 0;
                            invalidate();
                            if (mCaptureListener != null) {
                                mCaptureListener.recordEnd();
                            }
                        }
                        if (btn_outside_radius == btn_after_outside_radius && btn_inside_radius == 
                                btn_after_inside_radius) {
                            //                            startAnimation(btn_outside_radius, btn_outside_radius - 40,
                            // btn_inside_radius, btn_inside_radius + 20);
                            startAnimation(btn_after_outside_radius, btn_before_outside_radius, 
                                    btn_after_inside_radius, btn_before_inside_radius);
                        } else {
                            startAnimation(btn_after_outside_radius, btn_before_outside_radius, 
                                    btn_after_inside_radius, btn_before_inside_radius);
                        }
                    }
                    break;
                default:
                    break;
            }
//        } catch (Exception e) {
//            return false;
//        }
        return true;
    }

    public void initButton() {
        STATE_SELECTED = STATE_LESSNESS;
        invalidate();
    }

    public void captureSuccess() {
        captureAnimation(getWidth() / 5, (getWidth() / 5) * 4);
    }

    private void postCheckForLongTouch() {
        postDelayed(longPressRunnable, 200);
    }

    private class LongPressRunnable implements Runnable {
        @Override
        public void run() {
            startAnimation(btn_before_outside_radius, btn_after_outside_radius, btn_before_inside_radius, 
                    btn_after_inside_radius);
            STATE_SELECTED = STATE_RECORD;
        }
    }

    private class RecordRunnable implements Runnable {
        @Override
        public void run() {
            if (mCaptureListener != null) {
                mCaptureListener.record();
            }
            record_anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                @Override
                public void onAnimationUpdate(ValueAnimator animation) {
                    if (STATE_SELECTED == STATE_RECORD) {
                        progress = (float) animation.getAnimatedValue();
                    }
                    invalidate();
                }
            });
            record_anim.addListener(new AnimatorListenerAdapter() {
                @Override
                public void onAnimationEnd(Animator animation) {
                    super.onAnimationEnd(animation);
                    if (STATE_SELECTED == STATE_RECORD) {
                        STATE_SELECTED = STATE_RECORD_BROWSE;
                        progress = 0;
                        invalidate();
                        captureAnimation(getWidth() / 5, (getWidth() / 5) * 4);
                        if (btn_outside_radius == btn_after_outside_radius && btn_inside_radius == 
                                btn_after_inside_radius) {
                            startAnimation(btn_after_outside_radius, btn_before_outside_radius, 
                                    btn_after_inside_radius, btn_before_inside_radius);
                        } else {
                            startAnimation(btn_after_outside_radius, btn_before_outside_radius, 
                                    btn_after_inside_radius, btn_before_inside_radius);
                        }
                        if (mCaptureListener != null) {
                            mCaptureListener.recordEnd();
                        }
                    }
                }
            });
            record_anim.setInterpolator(new LinearInterpolator());
            record_anim.setDuration(10000);
            record_anim.start();
        }
    }

    private void startAnimation(float outside_start, float outside_end, float inside_start, float inside_end) {

        ValueAnimator outside_anim = ValueAnimator.ofFloat(outside_start, outside_end);
        ValueAnimator inside_anim = ValueAnimator.ofFloat(inside_start, inside_end);
        outside_anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                btn_outside_radius = (float) animation.getAnimatedValue();
                invalidate();
            }
        });
        outside_anim.addListener(new AnimatorListenerAdapter() {
            @Override
            public void onAnimationEnd(Animator animation) {
                super.onAnimationEnd(animation);
                if (STATE_SELECTED == STATE_RECORD) {
                    postDelayed(recordRunnable, 100);
                }
            }
        });
        inside_anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                btn_inside_radius = (float) animation.getAnimatedValue();
                invalidate();
            }
        });
        outside_anim.setDuration(100);
        inside_anim.setDuration(100);
        outside_anim.start();
        inside_anim.start();
    }

    private void captureAnimation(float left, float right) {
        //        Toast.makeText(mContext,left+ " = "+right,Toast.LENGTH_SHORT).show();
        ValueAnimator left_anim = ValueAnimator.ofFloat(btn_left_X, left);
        ValueAnimator right_anim = ValueAnimator.ofFloat(btn_right_X, right);
        left_anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                btn_left_X = (float) animation.getAnimatedValue();
                invalidate();
            }

        });
        right_anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                btn_right_X = (float) animation.getAnimatedValue();
                invalidate();
            }
        });
        left_anim.setDuration(200);
        right_anim.setDuration(200);
        left_anim.start();
        right_anim.start();
    }

    public void setCaptureListener(CaptureListener mCaptureListener) {
        this.mCaptureListener = mCaptureListener;
    }

    public interface CaptureListener {
        void capture();

        void cancel();//拍照点击返回

        void determine();//拍照的确定

        void quit();//关闭界面

        void record();

        void recordEnd();

        void getRecordResult();//录像点击确定

        void deleteRecordResult();//录像点击返回

        void scale(float scaleValue);
    }
}
```

###4、JCwebView

```
/**
 * Created by yxj on 17/4/17.
 * 录制视频的自定义控件
 */

public class JCameraView extends RelativeLayout implements SurfaceHolder.Callback, Camera.AutoFocusCallback,
        CameraFocusListener {

    private Context             mContext;
    private VideoView           mVideoView;
    private FocusView           mFocusView;     //对焦的按钮
    private CaptureButton       mCaptureButton; //录制视频/拍照的按钮
    private ImageView           mImageView;     //右上角摄像头切换的图标
    private ImageView           picPreImageView;//拍照完成的预览的图片
    private float               screenProp;
    private MediaRecorder       mediaRecorder;
    private Camera              mCamera;
    private Camera.Parameters   mParam;
    private int                 previewWidth;
    private int                 previewHeight;
    private int                 pictureWidth;
    private int                 pictureHeight;
    private boolean             autoFocus;
    private String              fileName;
    private Bitmap              pictureBitmap;
    private CameraViewListener  cameraViewListener;
    private RecordVideoActivity activity;
    private boolean             needSetVisible;

    public final String                TAG                   = "JCameraView";
    private      PowerManager          powerManager          = null;
    private      PowerManager.WakeLock wakeLock              = null;
    private      int                   iconMargin            = 0;
    private      int                   iconSrc               = 0;
    private      SurfaceHolder         mHolder               = null;
    private      String                videoFileName         = "";
    private      boolean               isPlay                = false;
    private      boolean               isRecorder            = false;
    private      boolean               isPre                 = false;//是不是在拍照的预览
    private      int                   SELECTED_CAMERA       = -1;
    private      int                   CAMERA_POST_POSITION  = -1;
    private      int                   CAMERA_FRONT_POSITION = -1;


    public void setCameraViewListener(CameraViewListener cameraViewListener) {
        this.cameraViewListener = cameraViewListener;
    }

    public JCameraView(Context context) {
        this(context, null);
    }

    public JCameraView(Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public JCameraView(Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
        mContext = context;
        powerManager = (PowerManager) mContext.getSystemService(mContext.POWER_SERVICE);
        wakeLock = this.powerManager.newWakeLock(PowerManager.FULL_WAKE_LOCK, "My Lock");
        findAvailableCameras();
        SELECTED_CAMERA = CAMERA_POST_POSITION;

        TypedArray a = context.getTheme().obtainStyledAttributes(attrs, R.styleable.JCameraView, defStyleAttr, 0);
        iconMargin = a.getDimensionPixelSize(R.styleable.JCameraView_iconMargin, (int) TypedValue.applyDimension
                (TypedValue.COMPLEX_UNIT_SP, 15, getResources().getDisplayMetrics()));
        iconSrc = a.getResourceId(R.styleable.JCameraView_iconSrc, R.drawable.capture);

        initView();
    }

    public void setActivity(RecordVideoActivity activity) {
        this.activity = activity;
        mCaptureButton.setActivity(activity);
    }

    /**
     * 初始化View界面，含控件的初始化以及控件监听器的初始化
     */
    private void initView() {
        setWillNotDraw(false);
        this.setBackgroundColor(Color.BLACK);

        initVideoView();            //视频播放控件
        initPicPreImageView();      //预览
        initCaptureButton();        //录像按钮
        initCameraChangeButton();   //切换摄像头
        initFocusView();            //对焦视图
        initSurfaceHolder();
    }

    private void initSurfaceHolder() {
        mHolder = mVideoView.getHolder();
        mHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
        mHolder.addCallback(this);
    }

    private void initFocusView() {
        //        对焦的图标
        mFocusView = new FocusView(mContext, 120);
        mFocusView.setVisibility(INVISIBLE);

        this.addView(mFocusView);       //对焦
    }

    private void initCameraChangeButton() {
        //右上角切换摄像头的按钮       
        mImageView = new ImageView(mContext);
        LayoutParams imageViewParam = new LayoutParams(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
        imageViewParam.addRule(RelativeLayout.ALIGN_PARENT_RIGHT, RelativeLayout.TRUE);
        imageViewParam.setMargins(0, iconMargin, iconMargin, 0);
        mImageView.setLayoutParams(imageViewParam);
        mImageView.setImageResource(iconSrc);
        mImageView.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                if (!isRecorder && mCamera != null) {
                    releaseCamera();

                    //切换摄像头
                    SELECTED_CAMERA = SELECTED_CAMERA == CAMERA_POST_POSITION ? CAMERA_FRONT_POSITION :
                            CAMERA_POST_POSITION;

                    getCamera(SELECTED_CAMERA);
                    previewWidth = previewHeight = 0;
                    pictureWidth = pictureHeight = 0;
                    setStartPreview(mCamera, mHolder);
                }
            }
        });

        this.addView(mImageView);       //右上角摄像头切换
    }

    private void initCaptureButton() {
        //底部居中的录像/拍照按钮 
        LayoutParams btnParams = new LayoutParams(LayoutParams.WRAP_CONTENT, LayoutParams.WRAP_CONTENT);
        btnParams.addRule(RelativeLayout.CENTER_HORIZONTAL, RelativeLayout.TRUE);
        btnParams.addRule(RelativeLayout.ALIGN_PARENT_BOTTOM, RelativeLayout.TRUE);
        mCaptureButton = new CaptureButton(mContext);
        mCaptureButton.setLayoutParams(btnParams);

        //初始化为自动对焦
        autoFocus = true;
        initCaptureButtonListener();

        this.addView(mCaptureButton);   //录视频
    }

    private void initPicPreImageView() {
        LayoutParams picPreImageViewParam = new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT);
        picPreImageViewParam.addRule(RelativeLayout.CENTER_IN_PARENT, RelativeLayout.TRUE);
        picPreImageView = new ImageView(mContext);
        picPreImageView.setLayoutParams(picPreImageViewParam);
        picPreImageView.setVisibility(INVISIBLE);

        this.addView(picPreImageView);  //预览
    }

    private void initVideoView() {
    /*VideoView 播放视频的界面*/
        mVideoView = new VideoView(mContext);
        LayoutParams videoViewParam = new LayoutParams(LayoutParams.MATCH_PARENT, LayoutParams.MATCH_PARENT);
        videoViewParam.addRule(RelativeLayout.CENTER_IN_PARENT, RelativeLayout.TRUE);
        mVideoView.setLayoutParams(videoViewParam);
        this.addView(mVideoView);       //viedoView

        mVideoView.setOnClickListener(new OnClickListener() {
            @Override
            public void onClick(View v) {
                mCamera.autoFocus(JCameraView.this);
            }
        });
    }

    /**
     * 初始化拍照/录像按钮的监听器
     */
    private void initCaptureButtonListener() {
        mCaptureButton.setCaptureListener(new CaptureButton.CaptureListener() {
            @Override
            public void capture() {
                JCameraView.this.capture();
            }

            @Override
            public void cancel() {
                isRecorder = false;
                releaseCamera();
                getCamera(SELECTED_CAMERA);
                isPre = false;
                setStartPreview(mCamera, mHolder);
                picPreImageView.setVisibility(INVISIBLE);
            }

            @Override
            public void determine() {
                if (cameraViewListener != null) {
                    cameraViewListener.captureSuccess(pictureBitmap);
                }
                releaseCamera();
                isPre = false;
            }

            @Override
            public void quit() {
                if (cameraViewListener != null) {
                    cameraViewListener.quit();
                }
            }

            @Override
            public void record() {
                startRecord();
            }

            @Override
            public void recordEnd() {
                stopRecord();
            }

            @Override
            public void getRecordResult() {
                if (cameraViewListener != null) {
                    cameraViewListener.recordSuccess(fileName);
                }
                mVideoView.stopPlayback();
                releaseCamera();
                isPlay = false;
            }

            @Override
            public void deleteRecordResult() {
                File file = new File(fileName);
                if (file.exists()) {
                    file.delete();
                }
                fileName = null;
                releaseCamera();

                mVideoView.pause();
                mVideoView.stopPlayback();  //整个界面销毁，各种按钮不存在了，依附于按钮的监听器也就不存在了

                isPlay = false;
                getCamera(SELECTED_CAMERA);
                setStartPreview(mCamera, mHolder);
            }

            @Override
            public void scale(float scaleValue) {
                if (scaleValue >= 0) {
                    int scaleRate = (int) (scaleValue / 50);

                    if (scaleRate < 10 && scaleRate >= 0 && mParam != null && mCamera != null && mParam
                            .isSmoothZoomSupported()) {
                        mParam = mCamera.getParameters();
                        mParam.setZoom(scaleRate);
                        mCamera.setParameters(mParam);
                    }
                }
            }
        });
    }

    @Override
    protected void onLayout(boolean changed, int l, int t, int r, int b) {
        super.onLayout(changed, l, t, r, b);
    }

    //获取Camera
    private void getCamera(int position) {
        try {
            if (null == mCamera) {
                Log.d(JCameraView.class.getName(), "position:" + position);
                mCamera = Camera.open(position);
                LogUtils.d("guo_zjin:", "create camera success");
            }
        } catch (RuntimeException e) {
            LogUtils.d("guo_zjin:", "create camera Runtime Exception");
            AlertDialog.Builder builder = new AlertDialog.Builder(activity);
            builder.setTitle("提示").setMessage("没有照相机权限,请赋予本权限再开始拍照吧~").setPositiveButton("确定", new DialogInterface
                    .OnClickListener() {
                @Override
                public void onClick(DialogInterface arg0, int arg1) {
                    activity.finish();
                }
            }).show();
        } catch (Exception e) {
            LogUtils.d("guo_zjin:", "create camera Exception");
            mCamera = null;
            e.printStackTrace();
        }
    }

    private void setStartPreview(Camera camera, SurfaceHolder holder) {
        if (camera == null) {
            return;
        }
        if (mImageView.getVisibility() != View.VISIBLE && !isPre && !isPlay && !isRecorder) {
            mImageView.setVisibility(View.VISIBLE);
            this.removeView(mImageView);
            this.addView(mImageView);
        }
        this.invalidate();

        mParam = camera.getParameters();

        if (screenProp != 0) {
            Camera.Size previewSize = CameraParamUtil.getInstance().getPreviewSize(mParam.getSupportedPreviewSizes(),
                    1000, screenProp);
            Camera.Size pictureSize = CameraParamUtil.getInstance().getPictureSize(mParam.getSupportedPictureSizes(),
                    1200, screenProp);
            mParam.setPreviewSize(previewSize.width, previewSize.height);
            mParam.setPictureSize(pictureSize.width, pictureSize.height);
        }
        if (CameraParamUtil.getInstance().isSupportedFocusMode(mParam.getSupportedFocusModes(), Camera.Parameters
                .FOCUS_MODE_CONTINUOUS_VIDEO)) {
            mParam.setFocusMode(Camera.Parameters.FOCUS_MODE_CONTINUOUS_VIDEO);
        } else if (CameraParamUtil.getInstance().isSupportedFocusMode(mParam.getSupportedFocusModes(), Camera
                .Parameters.FOCUS_MODE_AUTO)) {
            mParam.setFocusMode(Camera.Parameters.FOCUS_MODE_AUTO);
        }
        if (CameraParamUtil.getInstance().isSupportedPictureFormats(mParam.getSupportedPictureFormats(), ImageFormat
                .JPEG)) {
            mParam.setPictureFormat(ImageFormat.JPEG);
            mParam.setJpegQuality(100);
        }
        camera.setParameters(mParam);
        try {
            LogUtils.d("guo_zjin:", "发生错误的开始。。。。");
            camera.setPreviewDisplay(holder);
            LogUtils.d("guo_zjin:", "发生错误的开始。。。。01");
            camera.setDisplayOrientation(CameraParamUtil.getInstance().setCameraDisplayOrientation(activity,
                    SELECTED_CAMERA));
            LogUtils.d("guo_zjin:", "发生错误的开始。。。。02");

            camera.startPreview();
            LogUtils.d("guo_zjin:", "发生错误的开始。。。。03");
        } catch (FileNotFoundException e) {
            LogUtils.d("guo_zjin:", "在setStartPreview里发生文件不存在,被release");
            releasePre();
        } catch (IOException e) {
            LogUtils.d("guo_zjin: ", "在这里发生了一个该死的异常,但是我不得不在这里处理掉。。。。。00" + e.getMessage());
        } catch (Exception e) {
            LogUtils.d("guo_zjin:", "在这里发生了一个该死的异常,但是我不得不在这里处理掉。。。。。01" + e.getMessage());
        }
    }

    private void releaseMediaRecorder() {
        if (mVideoView != null) {
            mVideoView.stopPlayback();
            mHolder = mVideoView.getHolder();
        }
        if (mediaRecorder != null) {
            mediaRecorder.reset();   // clear recorder configuration
            mediaRecorder.release(); // release the recorder object
            mediaRecorder = null;
            mCamera.lock();           // lock camera for later use
        }
    }

    private void releaseCamera() {
        LogUtils.d("guo_zjin: ", "release Camera here.......");
        if (mCamera != null) {
            mCamera.stopPreview();
            try {
                mCamera.setPreviewDisplay(null);
            } catch (IOException e) {
                e.printStackTrace();
            }
            mCamera.setPreviewCallback(null);
            mCamera.release();
            mCamera = null;
        }
    }

    public void capture() {
        if (autoFocus) {
            mCamera.autoFocus(this);
        } else {
            isPre = true;
            if (SELECTED_CAMERA == CAMERA_POST_POSITION) {
                mCamera.takePicture(null, null, new Camera.PictureCallback() {
                    @Override
                    public void onPictureTaken(byte[] data, Camera camera) {
                        mCamera.stopPreview();
                        Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
                        Matrix matrix = new Matrix();
                        matrix.setRotate(90);
                        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
                        pictureBitmap = bitmap;
                        mImageView.setVisibility(INVISIBLE);
                        mCaptureButton.captureSuccess();
                        picPreImageView.setVisibility(VISIBLE);
                        picPreImageView.setImageBitmap(pictureBitmap);
                    }
                });
            } else if (SELECTED_CAMERA == CAMERA_FRONT_POSITION) {
                mCamera.takePicture(null, null, new Camera.PictureCallback() {
                    @Override
                    public void onPictureTaken(byte[] data, Camera camera) {
                        mCamera.stopPreview();
                        Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
                        Matrix matrix = new Matrix();
                        matrix.setRotate(270);
                        matrix.postScale(-1, 1);
                        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
                        pictureBitmap = bitmap;
                        mImageView.setVisibility(INVISIBLE);
                        mCaptureButton.captureSuccess();
                        picPreImageView.setVisibility(VISIBLE);
                        picPreImageView.setImageBitmap(pictureBitmap);
                    }
                });
            }
        }
    }

    //自动对焦
    @Override
    public void onAutoFocus(boolean success, Camera camera) {
        if (autoFocus) {
            isPre = true;
            if (SELECTED_CAMERA == CAMERA_POST_POSITION && success) {
                mCamera.takePicture(null, null, new Camera.PictureCallback() {
                    @Override
                    public void onPictureTaken(byte[] data, Camera camera) {
                        mCamera.stopPreview();
                        Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
                        Matrix matrix = new Matrix();
                        matrix.setRotate(90);
                        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
                        pictureBitmap = bitmap;
                        mImageView.setVisibility(INVISIBLE);
                        mCaptureButton.captureSuccess();
                        picPreImageView.setVisibility(VISIBLE);
                        picPreImageView.setImageBitmap(pictureBitmap);
                    }
                });
            } else if (SELECTED_CAMERA == CAMERA_FRONT_POSITION) {
                mCamera.takePicture(null, null, new Camera.PictureCallback() {
                    @Override
                    public void onPictureTaken(byte[] data, Camera camera) {
                        mCamera.stopPreview();
                        Bitmap bitmap = BitmapFactory.decodeByteArray(data, 0, data.length);
                        Matrix matrix = new Matrix();
                        matrix.setRotate(270);
                        matrix.postScale(-1, 1);
                        bitmap = Bitmap.createBitmap(bitmap, 0, 0, bitmap.getWidth(), bitmap.getHeight(), matrix, true);
                        pictureBitmap = bitmap;
                        mImageView.setVisibility(INVISIBLE);
                        mCaptureButton.captureSuccess();
                        picPreImageView.setVisibility(VISIBLE);
                        picPreImageView.setImageBitmap(pictureBitmap);
                    }
                });
            }
        }
    }

    @Override
    protected void onMeasure(int widthMeasureSpec, int heightMeasureSpec) {
        super.onMeasure(widthMeasureSpec, heightMeasureSpec);
        float widthSize = MeasureSpec.getSize(widthMeasureSpec);
        float heightSize = MeasureSpec.getSize(heightMeasureSpec);
        screenProp = heightSize / widthSize;
    }

    //    @Override
    //    public void surfaceCreated(SurfaceHolder holder) {
    //        LogUtils.d("拍摄视频", "surfaceCreated");
    //        setStartPreview(mCamera, holder);
    //        mHolder = holder;
    //        LogUtils.d("guo_zjin:surfaceCreated ", mHolder.getSurface().hashCode() + "");
    //    }
    @Override
    public void surfaceCreated(SurfaceHolder holder) {
        try {
            if (mCamera != null) {
                mCamera.setPreviewDisplay(holder);
                mHolder = holder;
            }
            LogUtils.d("guo_zjin:surfaceCreated ", mHolder.getSurface().hashCode() + "");
        } catch (IOException exception) {
            Log.e(TAG, "IOException caused by setPreviewDisplay()", exception);
        }
    }

    @Override
    public void surfaceChanged(SurfaceHolder holder, int format, int width, int height) {
        LogUtils.d("拍摄视频", "surfaceChanged");
        if (mHolder.getSurface() == null) {
            return;
        }
        mHolder = holder;
        // stop preview before making changes
        try {
            LogUtils.d("guo_zjin:surfaceChanged before stopPreview ", mHolder.hashCode() + "");

            if (null == mCamera) {
                getCamera(SELECTED_CAMERA);
            }
            mCamera.stopPreview();
            LogUtils.d("guo_zjin:surfaceChanged ", mHolder.hashCode() + "");
            setStartPreview(mCamera, holder);
        } catch (Exception e) {
            LogUtils.d("guo_zjin:", "surfaceChanged Exception " + e.getMessage());
            Log.d(TAG, "Error starting camera preview: " + e.getMessage());
        }
    }

    @Override
    public void surfaceDestroyed(SurfaceHolder holder) {
        releaseCamera();
    }

    public void onResume() {
        SystemClock.sleep(500);
        if (mCamera == null) {
            getCamera(SELECTED_CAMERA);
            if (needSetVisible && mVideoView != null) {
                mVideoView.setVisibility(View.VISIBLE);
            } else {
                needSetVisible = true;
            }
            if (mHolder != null && !isPre) {
                setStartPreview(mCamera, mHolder);
            }
        }
        wakeLock.acquire();
    }

    public void onPause() {
        if (wakeLock.isHeld()) {
            wakeLock.release();
        }
        releaseMediaRecorder();
        releaseCamera();
        if (needSetVisible && mVideoView != null) {
            mVideoView.setVisibility(View.INVISIBLE);
        }
    }

    private void startRecord() {
        if (isRecorder) {
            mediaRecorder.stop();
            mediaRecorder.release();
            mediaRecorder = null;
        }
        if (mCamera == null) {
            stopRecord();
            return;
        }
        mCamera.unlock();
        if (mediaRecorder == null) {
            mediaRecorder = new MediaRecorder();
        }
        mediaRecorder.reset();
        mediaRecorder.setCamera(mCamera);
        mediaRecorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);
        mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC);
        mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.MPEG_4);

        if (android.os.Build.MODEL.contains("vivo X5Pro")) {
            mediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.DEFAULT);
        } else {
            mediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.H264);
        }
        mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.AAC);
        //在1加手机上,如果设置15,直接挂掉,好像是能接受的最大值是30,所以屏蔽掉
        //        mediaRecorder.setVideoFrameRate(15);//帧率

        if (mParam == null) {
            mParam = mCamera.getParameters();
        }
        Camera.Size videoSize = CameraParamUtil.getInstance().getPictureSize(CameraParamUtil.getSupportedVideoSizes
                (mParam), 1000, screenProp);

        mediaRecorder.setVideoSize(videoSize.width, videoSize.height);
        int rotationRecord = CameraParamUtil.getInstance().setCameraDisplayOrientation(activity, SELECTED_CAMERA);
        android.hardware.Camera.CameraInfo info = new android.hardware.Camera.CameraInfo();
        android.hardware.Camera.getCameraInfo(SELECTED_CAMERA, info);
        int frontRotation;
        if (rotationRecord == 180) {
            //反向横屏的前置角度
            frontRotation = 180;
        } else {
            //竖屏和正向横屏的前置角度
            //录制下来的视屏选择角度，此处为前置
            frontRotation = (rotationRecord == 0) ? 270 - info.orientation : info.orientation;
        }
        mediaRecorder.setOrientationHint((SELECTED_CAMERA == 1) ? frontRotation : rotationRecord);
        mediaRecorder.setMaxDuration(10000);
        mediaRecorder.setVideoEncodingBitRate(1024 * 1024);
        mediaRecorder.setPreviewDisplay(mHolder.getSurface());

        videoFileName = "/myimage/video_" + System.currentTimeMillis() + ".mp4";
        File file = new File(PathUtil.getPathFile(), videoFileName);
        FileUtil.checkFilePath(file, false);
        mediaRecorder.setOutputFile(file.getPath());
        try {
            mediaRecorder.prepare();
            mediaRecorder.start();
            isRecorder = true;
            mImageView.setVisibility(INVISIBLE);
        } catch (IOException e) {
            e.printStackTrace();
        } catch (Exception e) {
            LogUtils.d("错误", e.getMessage());
        }
    }

    private void stopRecord() {
        if (mediaRecorder != null) {
            mediaRecorder.setOnErrorListener(new MediaRecorder.OnErrorListener() {
                @Override
                public void onError(MediaRecorder mr, int what, int extra) {
                    ToastUtil.toastShort("视频过短，请重新录制");
                    if (mr != null) {
                        mr.reset();
                    }
                    mediaRecorder.release();
                    mediaRecorder = null;
                    isRecorder = false;
                    fileName = PathUtil.getPathFile() + videoFileName;
                    File file = new File(fileName);
                    if (file.exists()) {
                        file.delete();
                    }
                    releaseCamera();
                    getCamera(SELECTED_CAMERA);
                    isPlay = false;
                    setStartPreview(mCamera, mHolder);
                    mCaptureButton.initButton();
                }
            });

            mediaRecorder.setOnInfoListener(null);
            mediaRecorder.setPreviewDisplay(null);
            try {
                mediaRecorder.stop();
                mediaRecorder.release();
                mediaRecorder = null;
                isRecorder = false;
            } catch (IllegalStateException e) {
                e.printStackTrace();
            }

            releaseCamera();

            mImageView.setVisibility(INVISIBLE);

            fileName = PathUtil.getPathFile() + videoFileName;
            File file = new File(fileName);
            if (file.exists() && file.length() > 0) {
                mVideoView.setVideoPath(fileName);
                mVideoView.start();

                mVideoView.setOnPreparedListener(new MediaPlayer.OnPreparedListener() {
                    @Override
                    public void onPrepared(MediaPlayer mp) {
                        isPlay = true;
                        mp.start();
                        mp.setLooping(true);
                    }
                });
                mVideoView.setOnCompletionListener(new MediaPlayer.OnCompletionListener() {
                    @Override
                    public void onCompletion(MediaPlayer mp) {
                        if (fileName != null) {
                            mVideoView.setVideoPath(fileName);
                            mVideoView.start();
                        }
                    }
                });

                mVideoView.setOnErrorListener(new MediaPlayer.OnErrorListener() {
                    @Override
                    public boolean onError(MediaPlayer mp, int what, int extra) {
                        LogUtils.d("guo_zjin:", "mVideoView.setOnErrorListener,被release");
                        if (!isPre) {
                            releasePre();
                        }
                        return true;
                    }
                });
            } else {
                ToastUtil.toastShort("视频不存在，赶紧重拍一个吧~");
                LogUtils.d("guo_zjin:", "stopRecorder,被release");
                releasePre();
            }
        }
    }

    //关闭预览
    private void releasePre() {
        if (mVideoView != null && mVideoView.isPlaying()) {
            mVideoView.stopPlayback();
        }
        if (mHolder != null) {
            mHolder.getSurface().release();
        }
        mCaptureButton.initButton();
        picPreImageView.setVisibility(INVISIBLE);
        // 区分一下是照片还是视频
        releaseCamera();
        isPre = false;
        isPlay = false;
        getCamera(SELECTED_CAMERA);
        setStartPreview(mCamera, mHolder);
    }

    /**
     * 获得可用的相机，并设置前后摄像机的ID
     */
    private void findAvailableCameras() {
        Camera.CameraInfo info = new Camera.CameraInfo();
        int numCamera = Camera.getNumberOfCameras();
        for (int i = 0; i < numCamera; i++) {
            Camera.getCameraInfo(i, info);
            // 找到了前置摄像头
            if (info.facing == Camera.CameraInfo.CAMERA_FACING_FRONT) {
                CAMERA_FRONT_POSITION = info.facing;
            }
            // 找到了后置摄像头
            if (info.facing == Camera.CameraInfo.CAMERA_FACING_BACK) {
                CAMERA_POST_POSITION = info.facing;
            }
        }
    }

    public void setAutoFocus(boolean autoFocus) {
        this.autoFocus = autoFocus;
    }

    private int count = 0;

    @Override
    public void onFocusBegin(float x, float y) {
        mFocusView.setVisibility(VISIBLE);
        mFocusView.setX(x - mFocusView.getWidth() / 2);
        mFocusView.setY(y - mFocusView.getHeight() / 2);
        mCamera.autoFocus(new Camera.AutoFocusCallback() {
            @Override
            public void onAutoFocus(boolean success, Camera camera) {
                if (!success) {
                    count++;
                    if (count > 3) {
                        mCamera.cancelAutoFocus();
                        onFocusEnd();
                        count = 0;
                    }
                }
                if (success) {
                    mCamera.cancelAutoFocus();
                    onFocusEnd();
                    count = 0;
                }
            }
        });
    }

    //手动对焦结束
    @Override
    public void onFocusEnd() {
        mFocusView.setVisibility(INVISIBLE);
    }

    public interface CameraViewListener {
        void quit();//关闭界面

        void captureSuccess(Bitmap bitmap);

        void recordSuccess(String url);
    }

    @Override
    public boolean onTouchEvent(final MotionEvent event) {
        if (!autoFocus && event.getAction() == MotionEvent.ACTION_DOWN && SELECTED_CAMERA == CAMERA_POST_POSITION &&
                !isPlay && !isPre) {
            if (mCamera != null) {
                mParam = mCamera.getParameters();
                mParam.setFocusMode(Camera.Parameters.FOCUS_MODE_AUTO);
                mCamera.setParameters(mParam);
            }
            onFocusBegin(event.getX(), event.getY());
        }
        return super.onTouchEvent(event);
    }
}

```
