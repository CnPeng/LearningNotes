### （1）什么时候使用ViewStub？为什么使用ViewStub?

当我们需要根据某个条件控制某个View的显示或者隐藏的时候，通常是把可能用到的View都写在布局上，然后设置可见性为View.GONE或View.InVisible ，之后在代码中根据条件动态控制可见性。虽然操作简单，但是耗费资源，因为即便该view不可见，仍会被父窗体绘制，仍会创建对象，仍会被实例化，仍会被设置属性。

而android.view.ViewStub，是一个大小为0 ，默认不可见的控件，只有给他设置成了View.Visible或调用了它的inflate()之后才会填充布局资源，也就是说占用资源少。所以，推荐使用viewStub 

###（2）ViewStub基本介绍

官方文档地址：https://developer.android.com/reference/android/view/ViewStub.html

ViewStub 继承自View。

>官方文档原文：A ViewStub is an invisible, zero-sized View that can be used to lazily inflate layout resources at runtime. When a ViewStub is made visible, or when inflate() is invoked, the layout resource is inflated. The ViewStub then replaces itself in its parent with the inflated View or Views. Therefore, the ViewStub exists in the view hierarchy until setVisibility(int) or inflate() is invoked. The inflated View is added to the ViewStub's parent with the ViewStub's layout parameters. Similarly, you can define/override the inflate View's id by using the ViewStub's inflatedId property. For instance:

我的翻译：

ViewStub 是一个不可见的，大小为0的视图，可以在运行过程中延时加载布局资源。**当ViewStub被设置成可见，或者它的inflate() 方法被调用的时候，布局资源才会被填充，然后ViewStub本身就会被填充起来的布局资源替换掉**。也就是说 ViewStub 被设置成可见或者它的inflate() 方法被调用之后，**在视图树中就不存在了**。被填充的**布局在替换ViewStub的时候会使用ViewStub的布局参数（LayoutParameters）**，比如 width ，height等。此外，你也可以通过**ViewStub的inflateId 属性定义或者重写 被填充布局资源的id。**

```
 <ViewStub android:id="@+id/stub"
               android:inflatedId="@+id/subTree"
               android:layout="@layout/mySubTree"
               android:layout_width="120dip"
               android:layout_height="40dip" />
```

>官方文档原文：The ViewStub thus defined can be found using the id "stub." After inflation of the layout resource "mySubTree," the ViewStub is removed from its parent. The View created by inflating the layout resource "mySubTree" can be found using the id "subTree," specified by the inflatedId property. The inflated View is finally assigned a width of 120dip and a height of 40dip. The preferred way to perform the inflation of the layout resource is the following:

我的翻译：

在上面的示例代码中，可以通过 id `stub`  获取到 ViewStub 实例，当 layout 属性引用的布局资源 `mySubTree` 被填充之后，ViewStub 就会从它的父窗体中移除，取而代之的就是mySubTree。**通过 inflatedId 对应的属性值 `subTree` 可以获取到 填充之后的 `mySubTree` 布局实例**。mySubTree 在替代ViewSub 的时候会使用 ViewStub 的 layoutParames, 也就是说mySubTree 的宽高会被定义成 120dp 、40dp。 推荐用如下方式去实现mySubTree布局资源的填充：

```
ViewStub stub = (ViewStub) findViewById(R.id.stub);
View inflated = stub.inflate();
```

>When inflate() is invoked, the ViewStub is replaced by the inflated View and the inflated View is returned. This lets applications get a reference to the inflated View without executing an extra findViewById().

我的翻译：

当ViewStub 的**inflate() 方法被调用之后**，ViewStub就会被填充起来的布局替换掉，并**返回填充起来的View**。这样，当我们想使用被填充起来的View时就不再需要调用findViewById () 方法。(实际使用的时候，如果我们需要操作被填充布局里面的数据时用inflate()，否则可以直接使用setVisibility)

###（3）示例代码：
最终效果展示：


![ViewStub.gif](http://upload-images.jianshu.io/upload_images/2551993-1f801a80fdd31f64.gif?imageMogr2/auto-orient/strip)

* activity_viewstub_test02.xml

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">
    <LinearLayout android:layout_width="match_parent"
                  android:layout_height="wrap_content">
        <Button
            android:id="@+id/btn_vs_showView"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="显示ViewStub"/>
        <Button
            android:id="@+id/btn_vs_changeHint"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="更改ViewStub"/>
        <Button
            android:id="@+id/btn_vs_hideView"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_alignParentRight="true"
            android:layout_weight="1"
            android:text="隐藏ViewStub"/>
    </LinearLayout>


    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="正在加载。。。"/>

    <!--ViewStub 展示或者隐藏内容-->
    <ViewStub
        android:id="@+id/viewstub_test"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:inflatedId="@+id/iv_VsContent"
        android:layout="@layout/iv_vs_content"/>

</RelativeLayout>
```

* ViewStubTestActivitiy.java

```
/**
 * Created by CnPeng on 2017/1/11. ViewStub 的使用示例
 */

public class ViewStubTestActivitiy extends AppCompatActivity implements View.OnClickListener {

    private ViewStub viewStub;
    private TextView hintText;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_viewstub_test02);

        init();
    }

    /**
     * 初始化
     */
    private void init() {
        viewStub = (ViewStub) findViewById(R.id.viewstub_test);

        Button btn_show = (Button) findViewById(R.id.btn_vs_showView);
        Button btn_hide = (Button) findViewById(R.id.btn_vs_hideView);
        Button btn_change = (Button) findViewById(R.id.btn_vs_changeHint);

        btn_show.setOnClickListener(this);
        btn_hide.setOnClickListener(this);
        btn_change.setOnClickListener(this);

    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_vs_showView:

                //inflate 方法只能被调用一次，因为调用后viewStub对象就被移除了视图树；
                // 所以，如果此时再次点击显示按钮，就会崩溃，错误信息：ViewStub must have a non-null ViewGroup viewParent；
                // 所以使用try catch ,当此处发现exception 的时候，在catch中使用setVisibility()重新显示
                try {
                    View iv_vsContent = viewStub.inflate();     //inflate 方法只能被调用一次，
                    hintText = (TextView) iv_vsContent.findViewById(R.id.tv_vsContent);
                    //                    hintText.setText("没有相关数据，请刷新");
                } catch (Exception e) {
                    viewStub.setVisibility(View.VISIBLE);
                } finally {
                    hintText.setText("没有相关数据，请刷新");
                }
                break;
            case R.id.btn_vs_hideView:  //如果显示
                viewStub.setVisibility(View.INVISIBLE);
                break;
            case R.id.btn_vs_changeHint:
                if (hintText!=null) { 
                    hintText.setText("网络异常，无法刷新，请检查网络");
                }
                break;
        }
    }
}


```

* iv_vs_content.xml  --要通过ViewStub展示出来的内容

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
    <ImageView
        xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:src="@mipmap/ic_launcher"/>

    <TextView
        android:id="@+id/tv_vsContent"
        android:layout_width="match_parent"
        android:layout_height="@dimen/dp30"
        android:gravity="center"
        android:text="eeee"/>
</LinearLayout>

```

###(4) 使用总结
**总结：**

 1）ViewStub 引用布局时使用 layout 属性，取值 `@layout/xxxxx`
 
 2）InflateId 表示给被引用的布局设置一个 id，是可选的。
 
 **3）inflate() 方法只能被调用一次，如果再次调用会报异常信息 ViewStub must have a non-null ViewGroup viewParent**。
 >这是因为，当ViewStub 调用inflate() 将其引用的 布局/view 展示出来之后，ViewStub本身就会从视图树中被移除，此时viewStub 就获取不到他的 父布局， 而 inflate() 方法中，上来就需要获取它的父布局，然后根据父布局是否为空再去执行具体的填充逻辑，如果为空就报上面的错，所以，inflate() 之后如果还想再次显示ViewStub 引用的布局/view 就需要 在调用inflate() 的时候try catch，当 catch 到异常的时候，调用setVisibility()设置viewStub 的View.Visible即可。ViewStub类中Inflate() 的具体逻辑如下： 
 
```
public View inflate() {
    final ViewParent viewParent = getParent();

    if (viewParent != null && viewParent instanceof ViewGroup) {
        if (mLayoutResource != 0) {
            final ViewGroup parent = (ViewGroup) viewParent;
            final LayoutInflater factory;
            if (mInflater != null) {
                factory = mInflater;
            } else {
                factory = LayoutInflater.from(mContext);
            }
            final View view = factory.inflate(mLayoutResource, parent,
                    false);

            if (mInflatedId != NO_ID) {
                view.setId(mInflatedId);
            }

            final int index = parent.indexOfChild(this);
            parent.removeViewInLayout(this);

            final ViewGroup.LayoutParams layoutParams = getLayoutParams();
            if (layoutParams != null) {
                parent.addView(view, index, layoutParams);
            } else {
                parent.addView(view, index);
            }

            mInflatedViewRef = new WeakReference<View>(view);

            if (mInflateListener != null) {
                mInflateListener.onInflate(this, view);
            }

            return view;
        } else {
            throw new IllegalArgumentException("ViewStub must have a valid layoutResource");
        }
    } else {
        throw new IllegalStateException("ViewStub must have a non-null ViewGroup viewParent");
    }
}  
``` 

 **5) ViewStub的setVisibility()中也调用了inflate(),但是为什么多次调用setVisibility()不会导致崩溃呢？**
 >ViewStub 的setVisibility() 方法中，会先判断 WeakReference 类型的成员变量 mInflatedViewRef 是否为空。第一次调用setVisibility()的时候，mInflatedViewRef并没有初始化，也就是说是null,那么这时候就会走inflate()，在inflate() 方法中给被填充起来的布局/view创建一个WeakReference弱引用，并赋值给mInflatedViewRef，从而完成mInflatedViewRef的初始化。当第二次走setVisibility() 的时候，mInflatedViewRef已经不再是null,就会调用  WeakReference 的父类Reference  中的get() 方法获取该引用指向的实体对象，也就是说通过get() 拿到 被填充的view对象，然后再走View类的setVisibility()。ViewStub类中的setVisibility()具体实现如下：
 
```
@Override
@android.view.RemotableViewMethod
public void setVisibility(int visibility) {
    if (mInflatedViewRef != null) {
        View view = mInflatedViewRef.get();
        if (view != null) {
            view.setVisibility(visibility);
        } else {
            throw new IllegalStateException("setVisibility called on un-referenced view");
        }
    } else {
        super.setVisibility(visibility);
        if (visibility == VISIBLE || visibility == INVISIBLE) {
            inflate();
        }
    }
}
```

**6) 根据上面 第4点和第5点可以得出如下结论：**

>（1）	ViewStub 的inflate() 只能被调用一次！
>
>（2）	如果想控制/修改 被填充布局中的内容并重复显示被填充的view，就用try 将viewStub.inflate() 以及修改内容的代码包裹起来，并在catch 中setVisibility.

 **7) 在xml 中定义ViewStub 节点时，内部不能包含其他节点，也就是说，ViewStub 是一个自闭合节点，如果一个布局/view如果想通过ViewStub显示，只能定义在单独的xml 文件中。**

## 版权声明：
**以上内容均为原创，如需转载请注明出处**
如文章中有不当之处，敬请指正。
