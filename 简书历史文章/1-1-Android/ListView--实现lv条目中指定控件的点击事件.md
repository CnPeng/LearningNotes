## 1. 目的：
在ListView 的每一个item 条目中，加入一个删除按钮，当点击删除按钮的时候，删除该条目；点击删除按钮之外的条目内容时响应条目的点击事件。具体效果如下所示：
![clickItemContent.gif](http://upload-images.jianshu.io/upload_images/2551993-22e2db82dfd978d0.gif?imageMogr2/auto-orient/strip)

## 2 . 实现思路及步骤：
* 以回调的方式来实现
>**(1)** 
让当前activity实现clickListener（或者在activity中单独new 一个监听器对象）。
**(2)** 
创建lv 适配器的时候，通过构造将监听器传递进适配器，并把该监听器设置给要点击的控件
 **(3)** 
适配器中，通过被点击控件的setTag 方法将当前条目position 传递出去
**(4)**
 在activity中 clcikListener 的 click方法中，通过getTag获取被点击控件所在条目的位置，从而做进一步的操作

## 3 . 完整示例代码：
* **ClickTheLvItemContentActivity.java**

```
/**
 * Created by CnPeng on 2016/12/22.
 * <p>
 * 让 lv 条目中的控件响应点击事件，并且不影响整个条目的点击事件
 * <p>
 * 以回调的方式来实现，
 * <p>
 * 1 让当前activity实现clickListener（或者在activity中单独new 一个监听器对象）。
 * <p>
 * 2 创建lv 适配器的时候，通过构造将监听器传递进适配器，并把该适配器设置给要点击的控件
 * <p>
 * 3 适配器中，通过setTag 将被点击控件当前所在的条目position 传递出去
 * <p>
 * 4 在activity中 clcikListener 的 click方法中，通过getTag获取被点击控件所在条目的位置，从而做进一步的操作
 */

public class ClickTheLvItemContentActivity extends AppCompatActivity implements View.OnClickListener {

    private List<String>            dataList;
    private ClickItemContentAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_click_item_content);

        //初始化数据
        dataList = new ArrayList<>();
        for (int i = 0; i < 30; i++) {
            dataList.add("张三"+i );
        }

        //获取lv 并设置适配器
        ListView listView = (ListView) findViewById(R.id.lv_clickItemContent);
        //创建适配器，传递数据集合，以及条目中被点击控件的的点击监听
        adapter = new ClickItemContentAdapter(ClickTheLvItemContentActivity.this, dataList);
        listView.setAdapter(adapter);

        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                Toast.makeText(ClickTheLvItemContentActivity.this, position + "号位置的条目被点击", Toast.LENGTH_SHORT).show();
            }
        });
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.iv_del:   //lv条目中 iv_del
                final int position = (int) v.getTag(); //获取被点击的控件所在item 的位置，setTag 存储的object，所以此处要强转
            
                //点击删除按钮之后，给出dialog提示
                AlertDialog.Builder builder = new AlertDialog.Builder(ClickTheLvItemContentActivity.this);
                builder.setTitle( position + "号位置的删除按钮被点击，确认删除?");
                builder.setNegativeButton("取消", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                    }
                });
                builder.setPositiveButton("确定", new DialogInterface.OnClickListener() {
                    @Override
                    public void onClick(DialogInterface dialog, int which) {
                        dataList.remove(position);
                        adapter.notifyDataSetChanged();
                    }
                });
                builder.show();
                break;
        }
    }
}

```
*  **activity_click_item_content.xml**     （actvitiy的布局文件）

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="match_parent"
              android:orientation="vertical">
    <ListView
        android:id="@+id/lv_clickItemContent"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

    </ListView>

</LinearLayout>
```
* **ClickItemContentAdapter.java** （LV适配器 ）

```
/**
 * Created by CnPeng on 2016/12/22. lv 适配器
 */
public class ClickItemContentAdapter extends BaseAdapter {
    private final View.OnClickListener listener;
    private final List<String>         dataList;

    public ClickItemContentAdapter(View.OnClickListener listener, List<String> dataList) {
        this.listener = listener;
        this.dataList = dataList;
    }

    @Override
    public int getCount() {
        return dataList.size();
    }

    @Override
    public Object getItem(int position) {
        return dataList.get(position);
    }

    @Override
    public long getItemId(int position) {
        return position;
    }

    @Override
    public View getView(int position, View convertView, ViewGroup parent) {
        ViewHolder holder;
        if (convertView == null) {
            holder = new ViewHolder();
            convertView = LayoutInflater.from(parent.getContext()).inflate(R.layout.item_click_item_content, parent,false);
            holder.iv_del = (ImageView) convertView.findViewById(R.id.iv_del);
            holder.tv_title = (TextView) convertView.findViewById(R.id.tv_title);
            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
        }
        
        holder.tv_title.setText(dataList.get(position));

        //给要被点击的控件加入点击监听，具体事件在创建adapter的地方实现
        holder.iv_del.setOnClickListener(listener);
        //通过setTag 将被点击控件所在条目的位置传递出去
        holder.iv_del.setTag(position);

        return convertView;
    }

    class ViewHolder {
        TextView  tv_title;
        ImageView iv_del;
    }
}

```
* ** item_click_item_content.xml** （LV条目布局）
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
              android:layout_width="match_parent"
              android:layout_height="50dp"
              android:orientation="horizontal">
    <TextView
        android:id="@+id/tv_title"
        android:layout_width="0dp"
        android:layout_height="match_parent"
        android:layout_weight="1"
        android:background="#44ff0000"
        android:gravity="center"
        android:text="abc"/>

    <ImageView
        android:id="@+id/iv_del"
        android:layout_width="@dimen/dp30"
        android:layout_height="@dimen/dp30"
        android:layout_gravity="center"
        android:layout_marginLeft="@dimen/dp10"
        android:layout_marginRight="@dimen/dp10"
        android:src="@drawable/delete"/>
</LinearLayout>
```

## 4. 总结
> **A**
这里重点使用的是回调，通过回调，可以将具体的逻辑放在外部实现
**B**
view 的 setTag( ) 和 getTag( ) 可以灵活传递各种数据。 setTag( object ) 方法中，参数是object类型，所以比较方便，通过getTag( ) 获取时强转一下即可。
**C**
在使用Dialog 及其子类 弹出对话框时 ，设置完相应的参数信息以及点击事件后，不要忘了调用 show( ) 方法将它展示。

## 附录：
> github 地址：https://github.com/CnPeng/MyDemosFrom2016_12
> 其中，a_13_ClickTheLvItemContent 包下是当前文章所述内容代码。
