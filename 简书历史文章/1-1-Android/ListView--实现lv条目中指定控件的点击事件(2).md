在上一篇 [ListView--实现lv条目中指定控件的点击事件](http://www.jianshu.com/p/947e3d97fc8f)  中，我们用回调的方式实现了lv条目中指定控件的点击事件。当前要写的这篇文章中，思路和步骤跟上一篇一致，不同点就是我们在代码中使用了 **DataBinding数据绑定** 。

>DataBinding 数据绑定 是MVVM 模式的体现，在代码中使用DataBinding 之后可以简化代码，将view 层 和modle 层更好的分离。

>关于如何使用DataBinding ,我当前一直没有时间总结，之后有时间再说吧。如果想现在了解，可以自行百度或谷歌搜索，也可以参考 [谷歌官方文档](https://developer.android.com/topic/libraries/data-binding/index.html)。


## 1. 目的：
在ListView 的每一个item 条目中，加入一个删除按钮，当点击删除按钮的时候，删除该条目；点击删除按钮之外的条目内容时响应条目的点击事件。并且**代码中使用DataBinding数据绑定**。具体效果如下所示：
![clickItemContent.gif](http://upload-images.jianshu.io/upload_images/2551993-22e2db82dfd978d0.gif?imageMogr2/auto-orient/strip)

## 2 . 实现思路及步骤：
* 以回调的方式来实现
>**1)** 
让当前activity实现clickListener（或者在activity中单独new 一个监听器对象）。
**2)** 
创建lv 适配器的时候，通过构造将监听器传递进适配器，并把该适配器设置给要点击的控件
 **3)** 
适配器中，通过setTag 将被点击控件当前所在的条目position 传递出去
**4)**
 在activity中 clcikListener 的 click方法中，通过getTag获取被点击控件所在条目的位置，从而做进一步的操作



## 3 . 完整示例代码：
> 关于数据绑定的详细介绍后期会有相应的总结文章，当前先直接给出使用的代码。

* 在当前modle 的 **build.gradle**  中启用数据绑定
> 注意：我的文章中所有关于安卓的，除特殊声明外，代码均是基于AndroidStudio编写的。
>**此处是在当前modle下的 build.gradle  文件中 的 android 闭包按照下面示例的样子，引入并启用数据绑定**

```
apply plugin: 'com.android.application'

android {
   ......
    // 加入这一小段才是最重要的，这就是启用数据绑定的代码 
    dataBinding {
        enabled = true;
    }
   ......
}

```

* **ClickTheLvItemContentActivity.java**

```

public class ClickTheLvItemContentActivity extends AppCompatActivity implements View.OnClickListener {

    private List<String>            dataList;
    private ClickItemContentAdapter adapter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        //此处使用DataBinding数据绑定填充布局
        ActivityClickItemContentBinding binding = DataBindingUtil.setContentView(this, R.layout
                .activity_click_item_content);

        //初始化数据
        dataList = new ArrayList<>();
        for (int i = 0; i < 30; i++) {
            dataList.add("张三" + i);
        }

        //获取控件，不用findViewById
        ListView listView = binding.lvClickItemContent;
        //创建适配器，传递数据集合，以及条目中被点击控件的的点击事件
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

                AlertDialog.Builder builder = new AlertDialog.Builder(ClickTheLvItemContentActivity.this);
                builder.setTitle(position + "号位置的删除按钮被点击，确认删除?");
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
<layout>

    <data>
    
    </data>
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
</layout>
```
* **ClickItemContentAdapter.java** （LV适配器 ）

```
package com.cnpeng.cnpeng_mydemosfrom2016_12.a_13_ClickTheLvItemContent.adapter;

import android.databinding.DataBindingUtil;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;

import com.cnpeng.cnpeng_mydemosfrom2016_12.R;
import com.cnpeng.cnpeng_mydemosfrom2016_12.databinding.ItemClickItemContentBinding;

import java.util.List;

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
        ItemClickItemContentBinding binding;
        if (convertView == null) {
            holder = new ViewHolder();

            //使用DataBinding数据绑定方式填充条目布局,布局中的控件都封装进了binding
            binding = DataBindingUtil.inflate(LayoutInflater.from(parent.getContext()), R.layout
                    .item_click_item_content, parent, false);

            //将binding数据传递给holder
            holder.setmBinding(binding);

            //binding不是view,所以通过getRoot 获取binding的view作为返回值
            convertView = binding.getRoot();

            convertView.setTag(holder);
        } else {
            holder = (ViewHolder) convertView.getTag();
            //从holder中取回binding
            binding = holder.getmBinding();
        }

        //直接使用相应控件，不用findViewById
        binding.tvTitle.setText(dataList.get(position));

        //给要被点击的控件加入点击监听，具体事件在创建adapter的地方实现
        binding.ivDel.setOnClickListener(listener);
        //通过setTag 将被点击控件所在条目的位置传递出去
        binding.ivDel.setTag(position);

        return convertView;
    }

    class ViewHolder {
        //holder中直接声明binding类及其set get ，其实就是将条目布局封装进holder
        ItemClickItemContentBinding mBinding;

        public ItemClickItemContentBinding getmBinding() {
            return mBinding;
        }

        public void setmBinding(ItemClickItemContentBinding mBinding) {
            this.mBinding = mBinding;
        }
    }
}


```
* ** item_click_item_content.xml** （LV条目布局）

```
<?xml version="1.0" encoding="utf-8"?>
<layout>
    <data>
        
    </data>

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
</layout>

```

## 4. 总结
> **A**
启用数据绑定的方式，在 AndroidStudio 当前modle 下的build.gradle 文件中的android 对应的大括号内加入 `dataBinding{ enabled=true }`启用数据绑定：
  ```
android {
   ......省略其他内容......
    dataBinding {
        enabled = true;
    }
    ......省略其他内容......
}
```
>**B**
在Activity等需要setContentView（）的控件中，使用DataBindingUtil.setContentView( ) 替代setContentView( ),这样就会得到一个Binding对象
>**C**
在 AdapterView 及其子类的Adapter中， 在填充条目布局的 getView ( ) 方法内，使用 DataBindingUtil.inflate（）方法填充view，得到一个Binding对象，然后获取该 Binding 的root 作为convertView.
>**D**
通过上面的代码可以看出，使用数据绑定之后，不用findViewById( ),而是直接使用 binding.XXXX ，XXX就代表xml布局中相应的控件ID，如 ：布局文件中的  textView 的id 是 tv_title, 那么 就可以通过 binding.tvTitle 拿到并操作该控件。 id 转换成XXX的规律是： 去掉下划线，从第二个单词起首字母大写。
> >关于DataBinding 数据绑定 更多详细易懂的说明，敬请期待

。
