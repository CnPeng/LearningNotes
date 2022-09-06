# 1. TextView文本前面展示红色星号


## 1.1. 方式1：Html.fromHtml

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <import type="android.text.Html" />
    </data>

    <TextView
            android:id="@+id/fragment_perfect_info_nickname"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:layout_marginStart="@dimen/dp15"
            android:layout_marginTop="@dimen/dp20"
            android:text="@{Html.fromHtml(`&lt;font color=#f25742>`+`*`+`&lt;/font>`+` 昵称`)}"
            android:textColor="@color/text_color_title"
            android:textSize="@dimen/text_size_max2"
            android:textStyle="bold"
            />
</layout>
```

## 1.2. 方式2：SpannableString

```java
binding.tvCode.setText(ColorSpanUtil.instance().getRedSpan("* 验证码", "*"));
```

```java
 /**
     * CnPeng:2022/4/23 09:11 获取 span
     *
     * @param pRes   需要处理的文本
     * @param pKey   关键字
     * @param pColor int类型的颜色值
     */
    @NonNull
    public Spanned getSpan (String pRes, String pKey, @ColorInt int pColor) {
        SpannableString spannableStr = new SpannableString(pRes);
        if (!pRes.contains(pKey)) {
            return spannableStr;
        }

        int keyIndex = pRes.indexOf(pKey);
        if (-1 == keyIndex) {
            return spannableStr;
        }

        spannableStr.setSpan(new ForegroundColorSpan(pColor) {
        }, keyIndex, keyIndex + pKey.length(), Spanned.SPAN_INCLUSIVE_EXCLUSIVE);

        return spannableStr;
    }
```


## 1.3. 方式3：DrawableXxx

单独切一张图片，然后使用 `drawableStart` 等属性设置。