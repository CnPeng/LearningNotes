##1、BottomNavigationView 是什么？
BottomNavigationView 是安卓官方提供的底部导航栏，能够方便的实现下图中的底部导航效果。

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-fd80b60ee3cbf3fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

按照 [官方的设计规范 https://material.io/guidelines/components/bottom-navigation.html#](https://material.io/guidelines/components/bottom-navigation.html#) 当底部导航的标签在 3--5 个之间的时候（含3、5）, 可以使用BottomNavigationView 。

当导航标签少于3个的时候，也可以使用，不会报错，但实际项目中一般不会有少于三个的；如果导航标签超过5个，在使用BottomNavigationView 时在运行期会报错，错误信息如下：
` java.lang.IllegalArgumentException: Maximum number of items supported by BottomNavigationView is 5. Limit can be checked with BottomNavigationView#getMaxItemCount()`。意思是说，最多不能超过 5 个条目！

##2、BottomNavigationView的使用前提及相关属性
####（1）、使用前提
BottomNavigationView是放置在design包中的，所以，使用前需要先引入` com.android.support:design:25.1.0 `包，引入方式有两种，一种是直接从当前module的 gradle 文件中编辑，一种是从Project Structure 界面的 dependences 选项卡中导入。导入方式，参考下图：

![直接从当前Module的gradle文件中编辑.png](http://upload-images.jianshu.io/upload_images/2551993-67511447e22e0d85.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


![从Project Structure 界面的 dependences 选项卡中导入.png](http://upload-images.jianshu.io/upload_images/2551993-ba9709880ae424e0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

####（2）主要属性及方法
* android:background  
>整个BottomNavigationView 的背景色，设置背景色之后，切换选项时依旧会有水波纹效果（设置背景色也是为了将底部导航和上方的内容进行分割区分）
*  app:itemBackground
>条目的背景色。设置之后在切换选项时将无法看到水波纹效果
* app:itemIconTint
>条目图标的颜色。可以是单一颜色，也可以是颜色selector。通常建议设置selector，当未选中时指定一种颜色，选中时再指定另一种颜色。该selector 定义在 res -- color 目录下。（未设置该属性时，默认未选中状态为深灰色，选中状态时的颜色为当前主题的 colorPrimary 颜色）
* itemTextColor
>条目文本的颜色。可以是单一颜色，也可以是颜色selector。通常建议设置selector，当未选中时指定一种颜色，选中时再指定另一种颜色。该selector 定义在 res -- color 目录下。未设置该属性时，默认未选中状态为深灰色，选中状态时的颜色为当前主题的 colorPrimary 颜色）
* app:menu
>当前BottomNavigationView 所引用的menu 菜单。
* setOnNavigationItemSelectedListener（）
>设置导航条目被选中时的监听器
* getMenu( ) 
>获取当前BottomNavigationView 中所引用的 menu 菜单对象

##3、详细示例代码：
最终实现效果：
![BottomNavigationView.gif](http://upload-images.jianshu.io/upload_images/2551993-896cdbad990dca88.gif?imageMogr2/auto-orient/strip)

* BottomNavigationViewActivity.java

```
/**
 * 作者：CnPeng
 * <p>
 * 时间：2017/4/11:下午8:32
 * <p>
 * 说明：安卓原生底部导航栏 BottomNavigationView 的基本使用示例
 * <p>
 * 使用时需要先引入 design 包
 * <p>
 * 底部Item的数量不能超过5 ，否则会报错，报错信息如下： java.lang.IllegalArgumentException: Maximum number of items supported by
 * BottomNavigationView is 5. Limit can be checked with BottomNavigationView#getMaxItemCount()
 * <p>
 * 当item超过3个时，未被选中的item 只显示图标，只有被选中的才会显示图标和文字；而3个及3个以内时图标和文字都会显示
 * <p>
 * 如果底部Item 想实现小红点，就无法使用BottomNavigationView 了 。/(ㄒoㄒ)/~~
 */

public class BottomNavigationViewActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_bottom_navigation_view);

        final TextView tv_whichItemSelected = (TextView) findViewById(R.id.tv_whichItemSelected);

        BottomNavigationView bnv_001 = (BottomNavigationView) findViewById(R.id.bnv_001);

        //为底部导航设置条目选中监听
        bnv_001.setOnNavigationItemSelectedListener(new BottomNavigationView.OnNavigationItemSelectedListener() {
            @Override
            public boolean onNavigationItemSelected(
                    @NonNull
                            MenuItem item) {
                switch (item.getItemId()) {
                    case R.id.item1:
                        tv_whichItemSelected.setText(item.getTitle());
                        break;
                    case R.id.item2:
                        tv_whichItemSelected.setText(item.getTitle());
                        break;
                    case R.id.item3:
                        tv_whichItemSelected.setText(item.getTitle());
                        break;
                    case R.id.item4:
                        tv_whichItemSelected.setText(item.getTitle());
                        break;
                    case R.id.item5:
                        tv_whichItemSelected.setText(item.getTitle());
                        break;
                }
                
                return true;    //这里返回true，表示事件已经被处理。如果返回false，为了达到条目选中效果，还需要下面的代码 
                // item.setChecked(true);  不论点击了哪一个，都手动设置为选中状态true（该控件并没有默认实现)
                // 。如果不设置，只有第一个menu展示的时候是选中状态，其他的即便被点击选中了，图标和文字也不会做任何更改

            }
        });

        //默认选中底部导航栏中的第三个
        bnv_001.getMenu().getItem(2).setChecked(true);
    }
}
```
* activity_bottom_navigation.xml

```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
                xmlns:app="http://schemas.android.com/apk/res-auto"
                android:layout_width="match_parent"
                android:layout_height="match_parent">

    <TextView
        android:id="@+id/tv_whichItemSelected"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"/>
    <!--
    itemBackground  item的背景  ,设置之后将无法看到默认的水波纹效果
    itemIconTint    item图标的颜色，可以是固定值，可以是 颜色selector(定义在 res —— color 目录)
    itemTextColor   item文字的颜色，可以是固定值，可以是 颜色selector
    
    如果不设置itemIconTint和 itemTextColor的时候，当某一个item被选中，选中时图标和文字的颜色是 当前主题的 colorPrimary 颜色
  
    background    整个底部导航的背景色，设置之后，在切换被选中的item时依旧具有水波纹效果。（设置background 也是为了能够明显的区分底部导航和上方的具体内容）
    -->
    <android.support.design.widget.BottomNavigationView
        android:id="@+id/bnv_001"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:background="@color/e7e7e6"
        app:itemIconTint="@color/selectot_bnv"
        app:itemTextColor="@color/selectot_bnv"
        app:menu="@menu/menu_bottom_navigation"/>


</RelativeLayout>
```
* menu_bottom_navigation.xml

```
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:id="@+id/item1"
          android:icon="@drawable/hot_green"
          android:title="Item1"/>
    <item android:id="@+id/item2"
          android:icon="?android:attr/actionModeShareDrawable"
          android:title="Item2"/>
    <item android:id="@+id/item3"
          android:icon="@drawable/act_attentioned"
          android:title="Item3"/>
    <item android:id="@+id/item4"
          android:icon="@android:drawable/btn_star_big_off"
          android:title="Item4"/>
    <item android:id="@+id/item5"
          android:icon="@drawable/mainfocus"
          android:title="Item5"/>

</menu>
```

* selector_bnv.xml
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="#f4b733" android:state_checked="true"></item>
    <item android:color="#666565"></item>
</selector>
```
## 4、总结

* （1）BottomNavigationView 整体实现比较简单，但是扩展性不强。比如，想在某个条目的右上角加上小红点，这就行不通了。。。

* （2）没有提供直接选中某项的方法，默认选中第一项。如果想更改默认选中，就需要通过getMenu( ) 方法获取menu 对象，然后获取其中的具体 item，最后再调用item 的 setChecked(true)

* （3）  总的条目数量不能超过5个。**当条目数量小于等于三个时，能将条目的图标和title 完全展示出来。如果大于3个小于等于5个，则只有被选中的条目才能同时展示图标和title，未被选中的条目只展示图标。**
* （4）创建 menu 菜单的时候，没必要手写，可以切换到design模式下，直接拖拽，然后再最右侧更改属性即可，大致如下图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-70631e268a1a86dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

参考：
http://codpoe.me/2016/10/27/bottom_navigation_view/
