在使用 `CoordinatorLayout + AppBarLayout + ToolBar` 的组合结构时，`ToolBar` 下方一直会显示阴影。

去除该阴影的方案是：为 AppBarLayout 设置 `app:elevation="dp0"`

**注意：** `elevation` 前面的命名空间为 **app**, 如果写成了 **android** 是不会生效的！！！


示例如下：

```xml
<androidx.coordinatorlayout.widget.CoordinatorLayout
            android:id="@+id/cdl_content"
            android:layout_width="match_parent"
            android:layout_height="0dp">

            <com.google.android.material.appbar.AppBarLayout
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:background="@color/white"
                app:elevation="@dimen/dp0"
                tools:background="#FFC">

                <com.google.android.material.appbar.CollapsingToolbarLayout
                    android:id="@+id/toolbar_layout"
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    app:layout_scrollFlags="scroll|snap"
                    app:toolbarId="@+id/toolbar">
                
                    <androidx.appcompat.widget.Toolbar
                        android:id="@+id/toolbar"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        app:layout_collapseMode="parallax">

                            <TextView
                                android:layout_width="match_parent"
                                android:layout_height="wrap_content"
                                android:gravity="center"
                                android:text="@{`考核预估总分：`+totalScore}"
                                android:textColor="@color/colorPrimary"
                                android:textSize="@dimen/sp18"
                                android:textStyle="bold"
                                tools:text="考核预估总分：500" />

                        </LinearLayout>
                    </androidx.appcompat.widget.Toolbar>
                </com.google.android.material.appbar.CollapsingToolbarLayout>
            </com.google.android.material.appbar.AppBarLayout>

            <androidx.recyclerview.widget.RecyclerView
                android:id="@+id/rv_result"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:background="@color/white"
                app:layoutManager="androidx.recyclerview.widget.LinearLayoutManager"
                app:layout_behavior="@string/appbar_scrolling_view_behavior" />
</androidx.coordinatorlayout.widget.CoordinatorLayout>
```