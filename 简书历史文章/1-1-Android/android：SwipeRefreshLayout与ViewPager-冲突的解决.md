##一、问题描述

![整体架构示意图](http://upload-images.jianshu.io/upload_images/2551993-d1d27fcdcf4c98e9.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

当前项目首页面的整体架构如上图，标号及其对应内容为：

标号|内容
--|--
1|自定义的SwipeRefreshLayout的子类 + ListView
2|ListView的头布局（含轮播图、话题栏、最新动态标题栏）
3|轮播图

存在的问题是，**在轮播图区域侧划切换图片的时候，只要稍微有一点点的下移动作，就会触发外层SwipeRefreshLayout的下拉操作**，只有你当前的划动动作是水平的，才能实现轮播图的测划切换，。实际也就是说，当产生下划动作时，触摸事件被 SwipeRefreshLayout 拦截并处理了。

##二、问题分析过程：
😔 ╮(╯▽╰)╭哎 ，当时处理的时候，脑袋一直不清醒，根本谈不上啥分析了。

当时的思路一直是处理轮播，想法让轮播消耗掉触摸事件，先后处理了轮播界面的 onInterceptTouchEvent( ) 、dispatchTouchEvent( ) 、onTouchEvent( ) 、getParent.requestDisallowInterceptTouchEvent( ) ，轮播界面中跟触摸相关的都处理了一遍，然后效果都不理想。

最终，内部不行就从外部入手，在 SwipeRefreshLayout 的 onInterceptTouchEvent( ) 中处理触摸事件。具体如下：
>触摸事件发生后，判断水平移动是否大于垂直移动，是则表示侧拉，不让SwipeRefreshLayout 拦截触摸事件，传递给内部的轮播；否则，如果是垂直位移大于水平位移，则表示下拉或上拉，让SwipeRefreshLayout 拦截触摸事件，执行下拉或上拉操作。

##三、实际代码：

>* 确定是侧划的时候不拦截触摸事件，将事件交由子View处理
>* 垂直滑动的时候，事件交由父类处理，即：`return super.onInterceptTouchEvent(ev);`
```
/**
 * 自定义上拉下拉的控件 
 */
public class UpDownRefreshLayout extends SwipeRefreshLayout {
   
    private int   mTouchSlop; //触发移动事件的最短距离
    private int   mDownY;
    private int   mDownX;

    public UpDownRefreshLayout(Context context) {
        super(context);
        initRefreshLayout(context);
    }

    public UpDownRefreshLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        initRefreshLayout(context);
    }

    /**
     * 初始化刷新控件
     */
    private void initRefreshLayout(Context pContext) {
        //得到触发移动事件的最短距离
        mTouchSlop = ViewConfiguration.get(pContext).getScaledTouchSlop();
     
        //其他内容省略
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
      
        int _action = ev.getAction();
        switch (_action) {
            case MotionEvent.ACTION_DOWN:
                mDownY = (int) ev.getY();
                mDownX = (int) ev.getX();
                break;
            case MotionEvent.ACTION_MOVE:
                int curX = (int) ev.getX();
                int curY = (int) ev.getY();
                int absX = Math.abs(curX - mDownX);
                int absY = Math.abs(curY - mDownY);

                if (absX < absY) {      //如果垂直移动
                  //执行你想要的操作，
                } else if (absX > absY) {   //左右划的时候不拦截，触摸事件直接交给子View处理
                    return false;  
                }
                break;
            case MotionEvent.ACTION_UP:
              //内容省略
                break;
            default:
                break;
        }
        return super.onInterceptTouchEvent(ev);
    } 
}
```
##四、参考资料：
比较尴尬的是，解决了问题之后，发现网上已经有网友整理过该问题 ，而且很详细。详情请见——[Android：SwipeRefreshLayout和ViewPager滑动冲突的原因和正确的解决方式](http://blog.csdn.net/u010386612/article/details/50548977)

当然了，为了加深自己的理解和记忆我还是又自己简单的整理了一遍。

###**最后，摘录上述文章中的重点部分如下：**

因为事件是先从上层往下层传递的，既然ViewPager的事件被吃掉了，那么肯定是在SwipeRefreshLayout中被消费了。 
我们去看看SwipeRefreshLayout的源码。 
1. 先看dispatch方法，发现重写此方法。 
2. 然后看onIntercept方法，发现是在这里拦截了。那么onTouchEvent方法就不用看了。下面我们就来分析一下onInterceptTouchEvent方法的源码。

```
 @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        // 确保有SwipeRefreshLayout有Target
        // 遍历所有child，第一个child就是target(除了刷新的那个圈)。
        // 这就是为啥SwiperefreshLayout只能有一个child的原因。
        // 先无视掉这句代码，和我们分析目的无关
        ensureTarget();

        final int action = MotionEventCompat.getActionMasked(ev);

        // 这个也无视吧， mReturningToStart一直都是false的，源码中并没有赋值
        // 估计原本用于判断是否正在刷新中，后来用了其他方式判断。（猜测）
        if (mReturningToStart && action == MotionEvent.ACTION_DOWN) {
            mReturningToStart = false;
        }

        if (!isEnabled() || mReturningToStart || canChildScrollUp() || mRefreshing) {
            // Fail fast if we're not in a state where a swipe is possible
            return false;
        }

        switch (action) {
            case MotionEvent.ACTION_DOWN:
                setTargetOffsetTopAndBottom(mOriginalOffsetTop - mCircleView.getTop(), true);
                mActivePointerId = MotionEventCompat.getPointerId(ev, 0);
                // 一个记录是否正在进行拖拽的标记，初始化false。
                mIsBeingDragged = false;
                // 获取按下的Y轴位置
                final float initialDownY = getMotionEventY(ev, mActivePointerId);
                if (initialDownY == -1) {
                    return false;
                }
                mInitialDownY = initialDownY;
                break;

            case MotionEvent.ACTION_MOVE:
                if (mActivePointerId == INVALID_POINTER) {
                    Log.e(LOG_TAG, "Got ACTION_MOVE event but don't have an active pointer id.");
                    return false;
                }
                // 获取当前的Y轴位置
                final float y = getMotionEventY(ev, mActivePointerId);
                if (y == -1) {
                    return false;
                }
                // 获取手指在Y轴的滑动距离
                final float yDiff = y - mInitialDownY;
                // 如果滑动距离大于mTouchSlop（不同手机的值不同，一般为8px）
                // 并且当前不是在拖拽中
                if (yDiff > mTouchSlop && !mIsBeingDragged) {
                    mInitialMotionY = mInitialDownY + mTouchSlop;
                    // 设置当前拖拽标记为true
                    mIsBeingDragged = true;
                    mProgress.setAlpha(STARTING_PROGRESS_ALPHA);
                }
                break;

            case MotionEventCompat.ACTION_POINTER_UP:
                onSecondaryPointerUp(ev);
                break;

            case MotionEvent.ACTION_UP:
            case MotionEvent.ACTION_CANCEL:
                //当手指抬起的时候设置拖拽标记为false；
                mIsBeingDragged = false;
                mActivePointerId = INVALID_POINTER;
                break;
        }
        // 如果是拖拽中，拦截事件，否则不拦截。
        return mIsBeingDragged;
    }
```
重点是mIsBeingDragged这个参数的值是否为true。

重写SwipeRefreshLayout的onIntercept方法就可以很简单的解决了。 
思路： 
1. 因为下拉刷新，只有纵向滑动的时候才有效，那么我们就判断此时是纵向滑动还是横向滑动就可以了。 
2. 纵向滑动就拦截事件，横向滑动不拦截。 
3. 怎么判断是纵向滑动还是横向滑动，只要判断Y轴的移动距离大于X轴的移动距离那么就判定为纵向滑动就行了。

以下就是重写后的SwipeRefreshLayout，直接复制到项目就可以使用了。
```

public class VpSwipeRefreshLayout extends SwipeRefreshLayout {

    private float startY;
    private float startX;
    // 记录viewPager是否拖拽的标记
    private boolean mIsVpDragger;
    private final int mTouchSlop;

    public VpSwipeRefreshLayout(Context context, AttributeSet attrs) {
        super(context, attrs);
        mTouchSlop = ViewConfiguration.get(context).getScaledTouchSlop();
    }

    @Override
    public boolean onInterceptTouchEvent(MotionEvent ev) {
        int action = ev.getAction();
        switch (action) {
            case MotionEvent.ACTION_DOWN:
                // 记录手指按下的位置
                startY = ev.getY();
                startX = ev.getX();
                // 初始化标记
                mIsVpDragger = false;
                break;
            case MotionEvent.ACTION_MOVE:
                // 如果viewpager正在拖拽中，那么不拦截它的事件，直接return false；
                if(mIsVpDragger) {
                    return false;
                }

                // 获取当前手指位置
                float endY = ev.getY();
                float endX = ev.getX();
                float distanceX = Math.abs(endX - startX);
                float distanceY = Math.abs(endY - startY);
                // 如果X轴位移大于Y轴位移，那么将事件交给viewPager处理。
                if(distanceX > mTouchSlop && distanceX > distanceY) {
                    mIsVpDragger = true;
                    return false;
                }
                break;
            case MotionEvent.ACTION_UP:
            case MotionEvent.ACTION_CANCEL:
                // 初始化标记
                mIsVpDragger = false;
                break;
        }
        // 如果是Y轴位移大于X轴，事件交给swipeRefreshLayout处理。
        return super.onInterceptTouchEvent(ev);
    }
}
```
