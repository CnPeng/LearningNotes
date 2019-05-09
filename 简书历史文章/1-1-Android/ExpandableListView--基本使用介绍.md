## 1 什么是ExpandableListView？有啥作用?
首先看一张ExpandableListView 的继承关系图：
![ExpandableListView的继承关系](http://upload-images.jianshu.io/upload_images/2551993-6103e8f1fdedb773.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
根据上图可知，ExpandableListView是ListView的子类。而expandable 在英文中的意思是可扩展的，所以ExpandableListView就是一个可以扩展的、有层级的ListView。

ExpandableListView与ListView的区别在于，ExpandableListView 对列表项进行了分组，每个分组中又可以显示具体的子项目。就像QQ中的好友分组，我们登录QQ之后，点击联系人就会显示所有的好友分组，然后点击某个分组，就可以展示这个分组中具体的联系人，这种界面就可以用ExpandableListView 来实现。

## 2 ExpandableListAdapter
根据上面的 **“ExpandableListView 的继承关系”** 图可以看出，ExpandableListView本质是一个AdapterView，既然是AdapterView，那么在展示数据的时候就需要使用到Adapter ——在展示ExpandableListView 的数据时使用就是ExpandableListAdapter。

ExpandableListAdapter的继承关系如下图：

![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-b58afa1b3e8562f4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

通常我们在展示ExpandableListView 的数据时，使用比较多的方式及adapter是：
>* 直接自定义Adapter继承BaseExpandableListAdapter.
这种方式的关键是实现四个方法：getCroupCount() 返回组的数量；getCroupView() 返回组的view对象；getChildCount() ,返回某组的子View数量； getChildView() 返回某组中的子view.
* 使用SimpleExpandableListAdapter将两个List集合包装成ExpandableListAdapter
* 使用SimpleCursorTreeAdapter 将Cursor中的数据包装成SimpleCursorTreeAdapter

## 3 ExpandableListView的几个重要属性及方法
###（1）XML属性
由于ExpandableListView 继承自ListView，所以ListView 可用的属性，ExpandableListView 也可以使用，除此之外，还有如下属性（这里只列举2个属性，其他的可以自己查看文档
https://developer.android.google.cn/reference/android/widget/ExpandableListView.html
#### 1） android:groupIndicator 和android:childIndicator

* android:groupIndicator
  组指示器。取值可以是任意的Drawable对象。显示在 该分组的最左侧。如果不设置的话，默认是一个向下的箭头，点击展开内容之后会变成向上的箭头。如下图：


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-52362398268a8ab5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

* android:childIndicator
  子条目指示器。取值可以是任意的Drawable 对象。显示在分组中的每一个 子条目 的最左侧。没有默认图标。

#### 2) 如何更改indicator
如果你想更改 groupIndicator 或者 childIndicator , 可以将值设置成一个selector。具体如下：
```
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@drawable/weixin_friend" android:state_expanded="true"/>
    <item android:drawable="@drawable/xiangguan"/>
</selector>
```
运行效果如下：


![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-442396ef8bbac810.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



#### 3）设置和更改indicator时的注意事项：
> **A**
默认情况下，指示器会覆盖组条目内容，就向下图中的这个样子。为了避免这种情况，我们就需要手动的在布局文件中或者代码中将 组条目向右移，以保证条目内容不被覆盖。
![Paste_Image.png](http://upload-images.jianshu.io/upload_images/2551993-4a43cf6e5ed6f7ee.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

>**B**
如果想取消 默认的 gropIndicator，可以将它赋值为"@null"，即：` android:groupIndicator="@null"`。这样就不会再显示groupIndicator了

>**C**
**替换默认indicator时，我们使用了一个selector，在编写这个selector时需要特别注意，我们用的状态是 ：**state_expanded**，这个状态在编写的时候不会自动补全，必须完全手动拼写！！**

###（2）主要方法及监听器
* **collapseGroup( int  position)**
    >收起 position 位置的分组
* **expandGroup(int position)**
> 展开position位置的分组
* **isGroupExpanded(int position)**
> 判断position位置的分组是否展开
* **setAdapter(ExpandableListAdapter adapter)**
> 给ExpandableListView 设置适配器
* **setOnChildClickListener(OnChildClickListener listener)**
> 设置分组中子条目的点击监听器
* **setOnGroupClickListener(OnGroupClickListener listener)**
> 设置分组的点击监听器
* **setOnGroupCollapseListener(OnGroupCollapseListener listener)**
> 设置分组收起的监听器
* **setOnGroupExpandListener(OnGroupExpandListener listener)**
> 设置分组展开的监听器




## 4 完整示例代码：
**该示例代码的实现的效果是：**
* 取消默认的 groupIndicator
* 在组条目末尾自定义 展开/收起 的指示器
* 拦截分组的点击事件
* 默认展开第一个分组
* 处理分组中子条目的点击事件
* 处理分组子条目中的强焦点控件的点击事件(这里加入的是CheckBox）

**最终效果gif图：**
![ExpandableListView.gif](http://upload-images.jianshu.io/upload_images/2551993-ef8be016b1ac599e.gif?imageMogr2/auto-orient/strip)

**A  ExpandableListViewActivity.java**
```
/**
 * Created by CnPeng on 2017/1/21.
 * <p>
 * 使用BaseExpandableLsitAdapter 展示ExpandableLsitView 的具体内容
 */

public class ExpandableListViewActivity extends AppCompatActivity {

    private View.OnClickListener ivGoToChildClickListener;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_expandablelv);

        init();
    }

    private void init() {
        final ExpandableListView elv01 = (ExpandableListView) findViewById(R.id.elv_01);

        //模拟数据（数组，集合都可以，这里使用数组）
        final String[] classes = new String[]{"一班", "二班", "三班", "四班","五班"};
        final String[][] students = new String[][]{{"张三1", "李四1", "王五1", "赵六1", "钱七1", "高八1"}, {"张三1", "李四1", "王五1", 
                "赵六1", "钱七1", "高八1"}, {"张三1", "李四1", "王五1", "赵六1", "钱七1", "高八1"}, {"张三1", "李四1", "王五1", "赵六1", "钱七1",
                "高八1"},{}};

        //自定义 展开/收起  图标的点击事件。position和 isExpand 都是通过tag 传递的
        ivGoToChildClickListener = new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                //获取被点击图标所在的group的索引
                Map<String, Object> map = (Map<String, Object>) v.getTag();
                int groupPosition = (int) map.get("groupPosition");
//                boolean isExpand = (boolean) map.get("isExpanded");   //这种是通过tag传值
                boolean isExpand = elv01.isGroupExpanded(groupPosition);    //判断分组是否展开

                if (isExpand) {
                    elv01.collapseGroup(groupPosition);
                } else {
                    elv01.expandGroup(groupPosition);
                }
            }
        };

        //创建并设置适配器
        MyExpandableListAdapter adapter = new MyExpandableListAdapter(classes, students, this, 
                ivGoToChildClickListener);
        elv01.setAdapter(adapter);
        
        //默认展开第一个分组
        elv01.expandGroup(0);

        //展开某个分组时，并关闭其他分组。注意这里设置的是 ExpandListener
        elv01.setOnGroupExpandListener(new ExpandableListView.OnGroupExpandListener() {
            @Override
            public void onGroupExpand(int groupPosition) {
                //遍历 group 的数组（或集合），判断当前被点击的位置与哪个组索引一致，不一致就合并起来。
                for (int i = 0; i < classes.length; i++) {
                    if (i != groupPosition) {
                        elv01.collapseGroup(i); //收起某个指定的组
                    }
                }
            }
        });

        //点击某个分组时，跳转到指定Activity
        elv01.setOnGroupClickListener(new ExpandableListView.OnGroupClickListener() {
            @Override
            public boolean onGroupClick(ExpandableListView parent, View v, int groupPosition, long id) {
                Toast.makeText(ExpandableListViewActivity.this, "组被点击了，跳转到具体的Activity", Toast.LENGTH_SHORT).show();
                return true;    //拦截点击事件，不再处理展开或者收起
            }
        });

        //某个分组中的子View被点击时的事件
        elv01.setOnChildClickListener(new ExpandableListView.OnChildClickListener() {
            @Override
            public boolean onChildClick(ExpandableListView parent, View v, int groupPosition, int childPosition,
                                        long id) {

                Toast.makeText(ExpandableListViewActivity.this, "组中的条目被点击：" + classes[groupPosition] + "的" +
                        students[groupPosition][childPosition] + "放学后到校长办公室", Toast.LENGTH_SHORT).show();
                return false;
            }
        });

    }
}
```
**B  布局文件 activity_expandablelv.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <TextView
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:gravity="center"
        android:padding="@dimen/dp10"
        android:text="扩展BaseExpandableListAdapter展示ExpandableListView"/>

    <!--这个默认的GroupIndicator 还是直接置空吧，默认的太鸡肋-->
    <ExpandableListView
        android:id="@+id/elv_01"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:divider="#ff0000"
        android:dividerHeight="@dimen/dp2"
        android:groupIndicator="@null">

    </ExpandableListView>
</LinearLayout>
```
**C 适配器 MyExpandableListAdapter.java**
```

/**
 * Created by CnPeng on 2017/1/21.
 * <p>
 * 自定义ExpandableListAdapter展示ExpandableListView的内容
 */

public class MyExpandableListAdapter extends BaseExpandableListAdapter {
    private String[]   classes;
    private String[][] stuents;
    private Context    context;
    View.OnClickListener ivGoToChildClickListener;

    public MyExpandableListAdapter() {

    }

    public MyExpandableListAdapter(String[] classes, String[][] stuents, Context context,
                                   View.OnClickListener ivGoToChildClickListener) {
        this.classes = classes;
        this.stuents = stuents;
        this.context = context;
        this.ivGoToChildClickListener = ivGoToChildClickListener;
    }

    @Override
    public int getGroupCount() {    //组的数量
        return classes.length;
    }

    @Override
    public int getChildrenCount(int groupPosition) {    //某组中子项数量
        return stuents[groupPosition].length;
    }

    @Override
    public Object getGroup(int groupPosition) {     //某组
        return classes[groupPosition];
    }

    @Override
    public Object getChild(int groupPosition, int childPosition) {  //某子项
        return stuents[groupPosition][childPosition];
    }

    @Override
    public long getGroupId(int groupPosition) {
        return groupPosition;
    }

    @Override
    public long getChildId(int groupPosition, int childPosition) {
        return childPosition;
    }


    @Override
    public View getGroupView(int groupPosition, boolean isExpanded, View convertView, ViewGroup parent) {
        GroupHold groupHold;
        if (convertView == null) {
            convertView = LayoutInflater.from(context).inflate(R.layout.item_elv_group, null);
            groupHold = new GroupHold();
            groupHold.tvGroupName = (TextView) convertView.findViewById(R.id.tv_groupName);
            groupHold.ivGoToChildLv = (ImageView) convertView.findViewById(R.id.iv_goToChildLV);

            convertView.setTag(groupHold);

        } else {
            groupHold = (GroupHold) convertView.getTag();

        }

        String groupName = classes[groupPosition];
        groupHold.tvGroupName.setText(groupName);

        //取消默认的groupIndicator后根据方法中传入的isExpand判断组是否展开并动态自定义指示器
        if (isExpanded) {   //如果组展开
            groupHold.ivGoToChildLv.setImageResource(R.drawable.delete);
        } else {
            groupHold.ivGoToChildLv.setImageResource(R.drawable.send_btn_add);
        }

        //setTag() 方法接收的类型是object ，所以可将position和converView先封装在Map中。Bundle中无法封装view,所以不用bundle
        Map<String, Object> tagMap = new HashMap<>();
        tagMap.put("groupPosition", groupPosition);
        tagMap.put("isExpanded", isExpanded);
        groupHold.ivGoToChildLv.setTag(tagMap);

        //图标的点击事件
        groupHold.ivGoToChildLv.setOnClickListener(ivGoToChildClickListener);

        return convertView;
    }

    @Override
    public View getChildView(final int groupPosition, final int childPosition, boolean isLastChild, View convertView,
                             ViewGroup parent) {
        ChildHold childHold;
        if (convertView == null) {
            convertView = LayoutInflater.from(context).inflate(R.layout.item_elv_child, null);
            childHold = new ChildHold();
            childHold.tvChildName = (TextView) convertView.findViewById(R.id.tv_elv_childName);
            childHold.cbElvChild = (CheckBox) convertView.findViewById(R.id.cb_elvChild);
            convertView.setTag(childHold);
        } else {
            childHold = (ChildHold) convertView.getTag();
        }

        String childName = stuents[groupPosition][childPosition];
        childHold.tvChildName.setText(childName);

        childHold.cbElvChild.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if (isChecked) {
                    Toast.makeText(context, "条目选中:" + classes[groupPosition] + "的" +
                            stuents[groupPosition][childPosition] + "被选中了", Toast.LENGTH_SHORT).show();
                } else {
                    Toast.makeText(context, "条目选中:" + classes[groupPosition] + "的" +
                            stuents[groupPosition][childPosition] + "被取消选中了", Toast.LENGTH_SHORT).show();
                }
            }
        });

        return convertView;
    }

    @Override
    public boolean isChildSelectable(int groupPosition, int childPosition) {
        return true;    //默认返回false,改成true表示组中的子条目可以被点击选中
    }

    @Override
    public boolean hasStableIds() {
        return true;
    }

    class GroupHold {
        TextView  tvGroupName;
        ImageView ivGoToChildLv;
    }

    class ChildHold {
        TextView tvChildName;
        CheckBox cbElvChild;
    }
}
```
**D 分组的布局 item_elv_group.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:gravity="center_vertical"
              android:orientation="horizontal">
    <TextView
        android:id="@+id/tv_groupName"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        android:layout_weight="1"
        android:padding="@dimen/dp10"
        android:text="班级"/>

    <ImageView
        android:id="@+id/iv_goToChildLV"
        android:layout_width="@dimen/dp40"
        android:layout_height="@dimen/dp20"
        android:src="@drawable/arrow"/>
</LinearLayout>
```

**E 分组中子条目的布局文件 item_elv_child.xml**
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="horizontal"
              android:paddingLeft="@dimen/dp15">
    <!--要取消CheckBox焦点，防止争抢条目的焦点-->
    <CheckBox
        android:id="@+id/cb_elvChild"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:focusable="false"/>
    <TextView
        android:id="@+id/tv_elv_childName"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:padding="@dimen/dp10"/>

</LinearLayout>
```
## 5 总结：
**A**
groupIndicator 以及childIndicator 太鸡肋了，直接赋值为 @null 屏蔽掉吧

**B**
如果列表的条目中有强焦点的控件，如checkBox ，为了避免抢走条目焦点，需要取消他们的焦点：focusale=”false”

**C**
如果在Adapter中如果想知道当前分组的展开/收起状态，那么可以从 adpater 的 getGroupView() 方法中获取，其中一个参数就是isExpand；如果在activity 等中可以调用 isGroupExpandable(position)

**D**
setTag(object) 方法中的参数是object类型的，如果需要传递多个信息可以使用map 或者bundle等进行封装，但是bundle中不能封装view对象

**E**
ExpandListView  类中的 collapseGroup(position) 、expandGroup(position)  作用分别是收起 、展开 position位置的分组。

**F**
监听分组的展开/收起事件时使用  onGroupExpandListener();

**G**
监听分组的点击事件时使用  onGroupClickListener() , 该监听器默认返回false, 会去处理分组的展开/收起。如果我们点击后不需要处理 展开/收起 的事件就返回true.(返回true 表示事件被拦截)

**H**
在处理自定义的 groupIndicator 的点击事件时，使用到了回调。相关知识可以参考我的另一篇文章：http://www.jianshu.com/p/eb226c340450
