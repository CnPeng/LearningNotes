## 一、问题现象
在下面的布局文件中，为 tv_addDesc 设置了maxLine 和 ellipsize , 但是 ellipsize不生效，末尾一直不展示省略号。

* 出问题时的布局文件
```
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:bind="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data class="ArticleCreateBinding">

        <import type="android.view.View" />

        <import type="android.text.TextUtils" />

        <variable
            name="handler"
            type="com.zjelite.richEditorArticle.handler.CreateNewArticleHandler" />

        <variable
            name="showGuide"
            type="Boolean" />

        <variable
            name="coverUrl"
            type="String" />

        <variable
            name="showMusicLayout"
            type="Boolean" />

        <variable
            name="hideBottomBt"
            type="Boolean" />
    </data>

    <RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical">

            <!--标题栏-->
            <RelativeLayout
                android:id="@+id/titleLayout"
                android:layout_width="match_parent"
                android:layout_height="45dp"
                android:background="@drawable/shape_bottom_divicer">

                <ImageView
                    android:id="@+id/iv_back"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_centerVertical="true"
                    android:paddingBottom="@dimen/dp5"
                    android:paddingLeft="@dimen/dp15"
                    android:paddingRight="@dimen/dp5"
                    android:paddingTop="@dimen/dp5"
                    android:src="@drawable/nav_back_button" />

                <TextView
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:layout_centerInParent="true"
                    android:text="创作"
                    android:textColor="@color/c_3e3e3e"
                    android:textSize="18sp" />

                <TextView
                    android:id="@+id/tv_preView"
                    android:layout_width="wrap_content"
                    android:layout_height="match_parent"
                    android:layout_alignParentRight="true"
                    android:gravity="center_vertical"
                    android:paddingLeft="@dimen/dp10"
                    android:paddingRight="@dimen/dp15"
                    android:text="预览" />

            </RelativeLayout>

            <ScrollView xmlns:android="http://schemas.android.com/apk/res/android"
                android:layout_width="match_parent"
                android:layout_height="0dp"
                android:layout_weight="1"
                android:background="@color/white">

                <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
                    android:layout_width="match_parent"
                    android:layout_height="match_parent"
                    android:orientation="vertical">

                    <!--封面和标题 -->
                    <RelativeLayout
                        android:id="@+id/rl_coverParent"
                        android:layout_width="match_parent"
                        android:layout_height="170dp"
                        android:layout_marginBottom="10dp"
                        android:layout_marginLeft="15dp"
                        android:layout_marginRight="15dp"
                        android:layout_marginTop="@dimen/dp10"
                        android:background="@drawable/shape_bk_rect_stroke"
                        android:focusable="true"
                        android:focusableInTouchMode="true">

                        <ImageView
                            android:id="@+id/iv_cover"
                            android:layout_width="match_parent"
                            android:layout_height="match_parent"
                            android:scaleType="centerCrop"
                            android:visibility="@{TextUtils.isEmpty(coverUrl)?View.INVISIBLE:View.VISIBLE}"
                            bind:imageUrl="@{coverUrl}" />

                        <!--没有封面时，使用这个居中的提示按钮提示用户添加封面-->
                        <ImageView
                            android:id="@+id/iv_changeCover1"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_centerHorizontal="true"
                            android:layout_marginTop="@dimen/dp10"
                            android:onClick="@{handler.onChangeCoverBtClick}"
                            android:src="@drawable/icon_change_cover_article2"
                            android:visibility="@{TextUtils.isEmpty(coverUrl)?View.VISIBLE:View.INVISIBLE}" />

                        <!--这是封面遮罩，如果没有封面，这一层隐藏-->
                        <FrameLayout
                            android:id="@+id/fl_shade"
                            android:layout_width="match_parent"
                            android:layout_height="match_parent"
                            android:background="@drawable/shape_black_gradient"
                            android:visibility="@{ivCover.visibility}" />

                        <!--如果已经有了封面，使用右上角的这个提示按钮提示用户修改封面-->
                        <ImageView
                            android:id="@+id/iv_changeCover"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_alignParentRight="true"
                            android:layout_marginRight="5dp"
                            android:layout_marginTop="5dp"
                            android:onClick="@{handler.onChangeCoverBtClick}"
                            android:src="@drawable/icon_change_cover_article"
                            android:visibility="@{flShade.visibility}" />

                        <!--<EditText-->
                        <TextView
                            android:id="@+id/et_title"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:layout_alignParentBottom="true"
                            android:layout_margin="6dp"
                            android:background="@null"
                            android:ellipsize="end"
                            android:hint="请输入文章标题"
                            android:maxLength="40"
                            android:maxLines="2"
                            android:onClick="@{handler.addTitle}"
                            android:textColor="@{TextUtils.isEmpty(coverUrl)?@color/black_3e3e3e:@color/white}"
                            android:textColorHint="@{TextUtils.isEmpty(coverUrl)?@color/c_666666:@color/white}"
                            android:textSize="16sp" />

                    </RelativeLayout>

                    <FrameLayout
                        android:layout_width="match_parent"
                        android:layout_height="10dp"
                        android:background="@color/f2f2f2" />

                    <!--简介编辑-->
                    <LinearLayout
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:background="@drawable/shape_bk_white_rect_with_bottom_dot"
                        android:orientation="vertical">

                        <TextView
                            android:id="@+id/tv_addDesc"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:drawableLeft="@drawable/icon_add_article_desc"
                            android:drawablePadding="@dimen/dp5"
                            android:ellipsize="end"
                            android:gravity="center_vertical"
                            android:hint="添加简介"
                            android:maxLength="100"
                            android:maxLines="3"
                            android:onClick="@{handler.addDesc}"
                            android:paddingBottom="10dp"
                            android:paddingLeft="@dimen/dp15"
                            android:paddingRight="@dimen/dp15"
                            android:paddingTop="10dp"
                            android:textColor="@color/acacac"
                            tools:text="随便写的文" />

                        <ImageView
                            android:id="@+id/iv_delDesc"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:layout_gravity="right"
                            android:onClick="@{handler.delDesc}"
                            android:paddingBottom="@dimen/dp5"
                            android:paddingLeft="20dp"
                            android:paddingRight="20dp"
                            android:src="@drawable/icon_del_article_desc"
                            android:visibility="@{TextUtils.isEmpty(tvAddDesc.text)?View.GONE:View.VISIBLE}" />

                    </LinearLayout>

                    <!--音乐模块-->
                    <LinearLayout
                        android:id="@+id/ll_musicParent"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_marginLeft="@dimen/dp15"
                        android:layout_marginRight="@dimen/dp15"
                        android:layout_marginTop="@dimen/dp10"
                        android:background="@color/f9f9fa"
                        android:gravity="center_vertical"
                        android:onClick="@{handler.onMusicBtClick}"
                        android:visibility="@{showMusicLayout?View.VISIBLE:View.GONE}">

                        <TextView
                            android:id="@+id/tv_musicName"
                            android:layout_width="0dp"
                            android:layout_height="wrap_content"
                            android:layout_weight="1"
                            android:baselineAligned="false"
                            android:drawableLeft="@drawable/icon_music_article_content"
                            android:drawablePadding="5dp"
                            android:ellipsize="marquee"
                            android:gravity="center_vertical"
                            android:paddingBottom="@dimen/dp5"
                            android:paddingLeft="5dp"
                            android:paddingTop="@dimen/dp5"
                            android:singleLine="true"
                            android:textColor="@color/c_3e3e3e"
                            android:textSize="12sp"
                            tools:text="歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称" />

                        <ImageView
                            android:id="@+id/iv_delMusic"
                            android:layout_width="wrap_content"
                            android:layout_height="wrap_content"
                            android:onClick="@{handler.delMusic}"
                            android:paddingBottom="10dp"
                            android:paddingLeft="10dp"
                            android:paddingRight="5dp"
                            android:paddingTop="10dp"
                            android:src="@drawable/icon_del_article_desc" />
                    </LinearLayout>

                    <!--这里需要嵌套一层相对布局，否则在android6.0的手机RV的item无法全部展现，为啥？？？？-->
                    <RelativeLayout
                        android:layout_width="match_parent"
                        android:layout_height="match_parent">

                        <android.support.v7.widget.RecyclerView
                            android:id="@+id/rv_contentList"
                            android:layout_width="match_parent"
                            android:layout_height="match_parent" />

                    </RelativeLayout>

                </LinearLayout>
            </ScrollView>

            <!--底部功能按钮-->
            <LinearLayout
                android:id="@+id/ll_bottomBtParent"
                android:layout_width="match_parent"
                android:layout_height="40dp"
                android:background="@drawable/layerlist_topline_1px"
                android:gravity="center_vertical"
                android:visibility="@{hideBottomBt?View.GONE:View.VISIBLE}"
                android:weightSum="4">

                <ImageView
                    android:id="@+id/text"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:onClick="@{handler.onTextBtClick}"
                    android:src="@drawable/icon_text_article_bottom" />

                <ImageView
                    android:id="@+id/gallery"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:onClick="@{handler.onPicBtClick}"
                    android:src="@drawable/icon_pic_article_bottom" />

                <ImageView
                    android:id="@+id/camera"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:onClick="@{handler.onCameraBtClick}"
                    android:src="@drawable/icon_video_article_bottom" />

                <ImageView
                    android:id="@+id/music"
                    android:layout_width="0dp"
                    android:layout_height="wrap_content"
                    android:layout_weight="1"
                    android:onClick="@{handler.onMusicBtClick}"
                    android:src="@drawable/icon_music_article_bottom" />

            </LinearLayout>

        </LinearLayout>


        <!--引导图文-->
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_marginBottom="@dimen/dp30"
            android:src="@drawable/guide_article_edit_content"
            android:visibility="@{showGuide?View.VISIBLE:View.GONE}" />
    </RelativeLayout>
</layout>


```

## 二、问题分析：

* 出了问题之后，先单独写了一个DEMO页验证 maxLines 和 ellipsize 到底能不能配合使用，在 Activtiy 的 onCreate 方法中直接 setText ,一切正常——省略号没有任何问题
* 然后又怀疑是布局文件写的有问题，毕竟嵌套层次比较多。然后就复制了布局文件，同样是在 onCreate 中直接setTex——也是没有问题
* 经过以上两步之后，感觉代码应该没啥问题，就再次 clean 和 rebuild 项目 ,并且重启AS，重启电脑。但是问题依旧（由于最近AS升级频繁，而且用的时候确实感觉有很多BUG，所以保险起见，就重启了AS和电脑）
* 接着祭出google 大法，搜了一大堆，但是试过之后都没能解决问题——自定义TextView的方式除外，因为，感觉直接用自定义TextView太重了，本身就是一个特别简单的需求。
* 接着又在 项目中 activity 页面的 onCreate 方法中直接setText ,也很好。
* 然后就考虑是不是由于 tv_addDesc 中的数据是网络中请求回来的，线程没处理好呢——仔细看了逻辑后，确信请求回数据之后已经通过handler将数据抛给主线程了。——线程没有问题
* 接着就考虑 onCreate 中 setText 和 请求回来数据之后 再 setText 的区别。。。好像没有什么明显的差异，都是在主线程中操作的；onCreate 中会走测量和绘制的逻辑，请求完之后再setText也会走。然后灵光一闪——
* 请求完之后直接 add 一个TextView是否会正常显示呢？——竟然一切好好的，然后就有了下面这个临时的解决方案：



## 三、解决方案：

* 代码中的处理

```
 /**
     * 作者：CnPeng
     * 时间：2018/4/27 下午5:55
     * 功用：展示文章的描述信息。如果描述信息为空，就展示添加描述的提示，否则就展示用户输入的提示
     * 说明：这里之所以通过addView的方式添加了一个TextView用来展示描述信息是因为——之前直接将描述内容设置给tv_addDescHint时，末尾不具有省略号啊啊啊啊啊啊。。。
     * 这个省略号问题可参考：https://stackoverflow.com/questions/2160619/android-ellipsize-multiline-textview。但是，这个链接中除了自定义TextView外其他都试了不好使！！
     */
    private void showArticleDesc(String articleDesc) {
        mCurDesc = articleDesc;

        if (TextUtils.isEmpty(articleDesc)) {
            //如果为空
            mBinding.setHideAddDescHint(false);
            if (null != mArticleDescTv) {
                mArticleDescTv.setVisibility(View.GONE);
            }
        } else {
            //如果描述不空,移除提示，设置文本，添加或展示新TextView
            mBinding.setHideAddDescHint(true);
            if (null == mArticleDescTv) {
                mArticleDescTv = new TextView(mContext);
                mArticleDescTv.setMaxLines(3);
                mArticleDescTv.setEllipsize(END);
                mArticleDescTv.setTextColor(getResources().getColor(R.color.acacac));
                mBinding.flDescParent.addView(mArticleDescTv);
            }
            mArticleDescTv.setText(articleDesc);
            mArticleDescTv.setVisibility(View.VISIBLE);
        }
    }

```
* 修正后的布局文件
```
<?xml version="1.0" encoding="utf-8"?>

<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:bind="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools">

    <data class="ArticleCreateBinding">
        <!--<data>-->

        <import type="android.view.View" />

        <import type="android.text.TextUtils" />

        <variable
            name="handler"
            type="com.zjelite.richEditorArticle.handler.CreateNewArticleHandler" />

        <variable
            name="showGuide"
            type="Boolean" />

        <variable
            name="coverUrl"
            type="String" />

        <variable
            name="showMusicLayout"
            type="Boolean" />

        <variable
            name="hideBottomBt"
            type="Boolean" />

        <variable
            name="hideAddDescHint"
            type="Boolean" />
    </data>

    <RelativeLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <!--标题栏-->
        <RelativeLayout
            android:id="@+id/titleLayout"
            android:layout_width="match_parent"
            android:layout_height="45dp"
            android:background="@drawable/shape_bottom_divicer">

            <ImageView
                android:id="@+id/iv_back"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerVertical="true"
                android:paddingBottom="@dimen/dp5"
                android:paddingLeft="@dimen/dp15"
                android:paddingRight="@dimen/dp5"
                android:paddingTop="@dimen/dp5"
                android:src="@drawable/nav_back_button" />

            <TextView
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:layout_centerInParent="true"
                android:text="创作"
                android:textColor="@color/c_3e3e3e"
                android:textSize="18sp" />

            <TextView
                android:id="@+id/tv_preView"
                android:layout_width="wrap_content"
                android:layout_height="match_parent"
                android:layout_alignParentRight="true"
                android:gravity="center_vertical"
                android:paddingLeft="@dimen/dp10"
                android:paddingRight="@dimen/dp15"
                android:text="预览" />

        </RelativeLayout>

        <ScrollView
            android:id="@+id/scrollView"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:layout_above="@id/ll_bottomBtParent"
            android:layout_below="@id/titleLayout"
            android:background="@color/white">

            <LinearLayout
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:orientation="vertical">

                <!--封面和标题 -->
                <RelativeLayout
                    android:id="@+id/rl_coverParent"
                    android:layout_width="match_parent"
                    android:layout_height="170dp"
                    android:layout_marginBottom="10dp"
                    android:layout_marginLeft="15dp"
                    android:layout_marginRight="15dp"
                    android:layout_marginTop="@dimen/dp10"
                    android:background="@drawable/shape_bk_rect_stroke"
                    android:focusable="true"
                    android:focusableInTouchMode="true">

                    <ImageView
                        android:id="@+id/iv_cover"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:scaleType="centerCrop"
                        android:visibility="@{TextUtils.isEmpty(coverUrl)?View.INVISIBLE:View.VISIBLE}"
                        bind:imageUrl="@{coverUrl}" />

                    <!--没有封面时，使用这个居中的提示按钮提示用户添加封面-->
                    <ImageView
                        android:id="@+id/iv_changeCover1"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_centerHorizontal="true"
                        android:layout_marginTop="@dimen/dp10"
                        android:onClick="@{handler.onChangeCoverBtClick}"
                        android:src="@drawable/icon_change_cover_article2"
                        android:visibility="@{TextUtils.isEmpty(coverUrl)?View.VISIBLE:View.INVISIBLE}" />

                    <!--这是封面遮罩，如果没有封面，这一层隐藏-->
                    <FrameLayout
                        android:id="@+id/fl_shade"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent"
                        android:background="@drawable/shape_black_gradient"
                        android:visibility="@{ivCover.visibility}" />

                    <!--如果已经有了封面，使用右上角的这个提示按钮提示用户修改封面-->
                    <ImageView
                        android:id="@+id/iv_changeCover"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_alignParentRight="true"
                        android:layout_marginRight="5dp"
                        android:layout_marginTop="5dp"
                        android:onClick="@{handler.onChangeCoverBtClick}"
                        android:src="@drawable/icon_change_cover_article"
                        android:visibility="@{flShade.visibility}" />

                    <!--<EditText-->
                    <TextView
                        android:id="@+id/et_title"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_alignParentBottom="true"
                        android:layout_margin="6dp"
                        android:background="@null"
                        android:ellipsize="end"
                        android:hint="请输入文章标题"
                        android:maxLength="40"
                        android:maxLines="2"
                        android:onClick="@{handler.addTitle}"
                        android:textColor="@{TextUtils.isEmpty(coverUrl)?@color/black_3e3e3e:@color/white}"
                        android:textColorHint="@{TextUtils.isEmpty(coverUrl)?@color/c_666666:@color/white}"
                        android:textSize="16sp" />

                </RelativeLayout>

                <FrameLayout
                    android:layout_width="match_parent"
                    android:layout_height="10dp"
                    android:background="@color/f2f2f2" />

                <!--简介编辑-->
                <LinearLayout
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:background="@drawable/shape_bk_white_rect_with_bottom_dot"
                    android:onClick="@{handler.addDesc}"
                    android:orientation="vertical">

                    <!--之所以包裹这一层实在是无奈，不知道为啥，如果直接通过setText将网络请求回来的草稿简介或者
                    新编辑的简介设置给tv_addDescHint的话，末尾的省略号效果死活没有。无奈之下，只得出此下策。有了
                    简介内容之后就添加到这个descParent上，如果没有就展示这个descHint-->
                    <FrameLayout
                        android:id="@+id/fl_descParent"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:paddingBottom="10dp"
                        android:paddingLeft="@dimen/dp15"
                        android:paddingRight="@dimen/dp15"
                        android:paddingTop="10dp">

                        <TextView
                            android:id="@+id/tv_addDescHint"
                            android:layout_width="match_parent"
                            android:layout_height="wrap_content"
                            android:drawableLeft="@drawable/icon_add_article_desc"
                            android:drawablePadding="@dimen/dp5"
                            android:hint="添加简介"
                            android:textColor="@color/acacac"
                            android:visibility="@{hideAddDescHint?View.INVISIBLE:View.VISIBLE}" />
                    </FrameLayout>


                    <ImageView
                        android:id="@+id/iv_delDesc"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:layout_gravity="right"
                        android:onClick="@{handler.delDesc}"
                        android:paddingBottom="@dimen/dp5"
                        android:paddingLeft="20dp"
                        android:paddingRight="20dp"
                        android:src="@drawable/icon_del_article_desc"
                        android:visibility="@{hideAddDescHint?View.VISIBLE:View.GONE}" />

                </LinearLayout>

                <!--音乐模块-->
                <LinearLayout
                    android:id="@+id/ll_musicParent"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:layout_marginLeft="@dimen/dp15"
                    android:layout_marginRight="@dimen/dp15"
                    android:layout_marginTop="@dimen/dp10"
                    android:background="@color/f9f9fa"
                    android:gravity="center_vertical"
                    android:onClick="@{handler.onMusicBtClick}"
                    android:visibility="@{showMusicLayout?View.VISIBLE:View.GONE}">

                    <TextView
                        android:id="@+id/tv_musicName"
                        android:layout_width="0dp"
                        android:layout_height="wrap_content"
                        android:layout_weight="1"
                        android:baselineAligned="false"
                        android:drawableLeft="@drawable/icon_music_article_content"
                        android:drawablePadding="5dp"
                        android:ellipsize="marquee"
                        android:gravity="center_vertical"
                        android:paddingBottom="@dimen/dp5"
                        android:paddingLeft="5dp"
                        android:paddingTop="@dimen/dp5"
                        android:singleLine="true"
                        android:textColor="@color/c_3e3e3e"
                        android:textSize="12sp"
                        tools:text="歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称歌曲名称" />

                    <ImageView
                        android:id="@+id/iv_delMusic"
                        android:layout_width="wrap_content"
                        android:layout_height="wrap_content"
                        android:onClick="@{handler.delMusic}"
                        android:paddingBottom="10dp"
                        android:paddingLeft="10dp"
                        android:paddingRight="5dp"
                        android:paddingTop="10dp"
                        android:src="@drawable/icon_del_article_desc" />
                </LinearLayout>

                <!--这里需要嵌套一层相对布局，否则在android6.0的手机RV的item无法全部展现，为啥？？？？-->
                <RelativeLayout
                    android:layout_width="match_parent"
                    android:layout_height="match_parent">

                    <android.support.v7.widget.RecyclerView
                        android:id="@+id/rv_contentList"
                        android:layout_width="match_parent"
                        android:layout_height="match_parent" />

                </RelativeLayout>

            </LinearLayout>
        </ScrollView>


        <!--底部功能按钮-->
        <LinearLayout
            android:id="@+id/ll_bottomBtParent"
            android:layout_width="match_parent"
            android:layout_height="40dp"
            android:layout_alignParentBottom="true"
            android:background="@drawable/layerlist_topline_1px"
            android:gravity="center_vertical"
            android:visibility="@{hideBottomBt?View.GONE:View.VISIBLE}"
            android:weightSum="4">


            <ImageView
                android:id="@+id/text"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:onClick="@{handler.onTextBtClick}"
                android:src="@drawable/icon_text_article_bottom" />

            <ImageView
                android:id="@+id/gallery"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:onClick="@{handler.onPicBtClick}"
                android:src="@drawable/icon_pic_article_bottom" />

            <ImageView
                android:id="@+id/camera"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:onClick="@{handler.onCameraBtClick}"
                android:src="@drawable/icon_video_article_bottom" />

            <ImageView
                android:id="@+id/music"
                android:layout_width="0dp"
                android:layout_height="wrap_content"
                android:layout_weight="1"
                android:onClick="@{handler.onMusicBtClick}"
                android:src="@drawable/icon_music_article_bottom" />

        </LinearLayout>

        <!--引导图文-->
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="wrap_content"
            android:layout_alignParentBottom="true"
            android:layout_marginBottom="@dimen/dp30"
            android:src="@drawable/guide_article_edit_content"
            android:visibility="@{showGuide?View.VISIBLE:View.GONE}" />
    </RelativeLayout>
</layout>

```

## 四、思考
* 虽然通过上面临时的方案达到了设计需求的效果，但是还是没想明白为啥。
* 后面需要再试一下直接在 onCreate 中通过代码添加用来展示简介的TextView的方式，然后依旧是在请求回来数据之后 setText ,看看这样是不是还会出现不展示省略号的问题。——如果通过这种方式解决了，那就说明，代码添加view的方式相对于xml方式会有莫名加成！！！
