布局文件中使用了EditText后，它会默认获取焦点。

如果需要取消这个默认焦点，当它被点击时才将焦点给它，则需要在EditText的父布局设置如下属性：
```
 android:focusable="true"  
 android:focusableInTouchMode="true"
```

完整示例代码如下：

```
                <RelativeLayout
                    android:id="@+id/rl_coverParent"
                    android:layout_width="match_parent"
                    android:layout_height="150dp"
                    android:layout_marginBottom="10dp"
                    android:layout_marginLeft="15dp"
                    android:layout_marginRight="15dp"
                    android:focusable="true"
                    android:focusableInTouchMode="true">


                    <EditText
                        android:id="@+id/et_title"
                        android:layout_width="match_parent"
                        android:layout_height="wrap_content"
                        android:layout_alignParentBottom="true"
                        android:layout_margin="10dp"
                        android:background="@null"
                        android:ellipsize="end"
                        android:hint="请输入文章标题"
                        android:maxLines="2"
                        android:textColor="#fff"
                        android:textColorHint="@color/white"
                        android:textSize="16sp"/>

                </RelativeLayout>
```