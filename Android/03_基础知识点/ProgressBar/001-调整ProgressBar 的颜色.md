# 1. 001-调整ProgressBar 的颜色


## 1.1. 方案1-修改ProgressBar样式

要在Android中修改ProgressBar的进度颜色，您可以使用以下步骤：

1. 在您的布局文件中，将ProgressBar的样式设置为自定义样式，例如：


```xml
<ProgressBar
    android:id="@+id/progressBar"
    style="@style/MyProgressBarStyle"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

2. 在您的样式文件中，创建一个自定义样式，并设置进度颜色属性，例如：


```xml
<style name="MyProgressBarStyle" parent="Widget.AppCompat.ProgressBar">
    <item name="android:progressTint">@color/my_progress_color</item>
</style>
```

3. 在您的颜色文件中，定义您想要使用的进度颜色，例如：


```xml
<color name="my_progress_color">#FF00FF</color>
```

4. 运行您的应用程序，您应该看到 ProgressBar 的进度颜色已经更改为您指定的颜色。

请注意，这些步骤假设您正在使用 AppCompat ProgressBar。如果您使用的是其他类型的 ProgressBar，请根据相应的文档或示例进行修改。

## 1.2. 方案2-使用progressDrawable属性

在 Android 中，可以通过设置 `android:progressDrawable` 属性来修改 ProgressBar 的进度颜色。`android:progressDrawable` 属性用于设置 ProgressBar 的进度条外观，可以是一个 XML 文件或者一个 View 实例。

以下是一个使用 XML 文件设置 ProgressBar 进度颜色的示例：

```xml
<ProgressBar
    android:id="@+id/progressBar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:progressDrawable="@drawable/progress_bar" />
```

在上面的代码中，`android:progressDrawable` 属性设置了一个名为 `progress_bar` 的 XML 文件作为 ProgressBar 的进度条外观。该 XML 文件应该定义一个圆形的进度条，并设置其颜色为所需的颜色。

以下是一个使用 View 实例设置 ProgressBar 进度颜色的示例：

```xml
<ProgressBar
    android:id="@+id/progressBar"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:progressDrawable="@drawable/progress_bar" />
```

在上面的代码中，`android:progressDrawable` 属性设置了一个名为 `progress_bar` 的 View 实例作为 ProgressBar 的进度条外观。该 View 实例应该是一个圆形的进度条，并设置其颜色为所需的颜色。

需要注意的是，以上示例中的 `android:progressDrawable` 属性需要在 XML 文件或者代码中设置。如果在 XML 文件中设置了 `android:progressDrawable` 属性，那么在代码中不能再次设置该属性。如果在代码中设置了 `android:progressDrawable` 属性，那么在 XML 文件中不能再次设置该属性。

`progress_bar.xml` 文件的内容应该是一个圆形的进度条，并设置其颜色为所需的颜色。以下是一个示例 `progress_bar.xml` 文件的内容：

```xml
<shape xmlns:android="http://schemas.android.com/apk/res/android"
    android:shape="ring"
    android:innerRadiusRatio="3"
    android:thicknessRatio="10"
    android:useLevel="false">
    <solid android:color="@color/colorPrimary" />
</shape>
```

在上面的代码中，`<shape>` 元素用于定义一个圆形的形状。`android:shape` 属性设置为 `ring`，表示该形状是一个圆形的进度条。`android:innerRadiusRatio` 属性设置为 `3`，表示进度条的内径是外径的 3/10。`android:thicknessRatio` 属性设置为 `10`，表示进度条的厚度是外径的 1/10。`android:useLevel` 属性设置为 `false`，表示进度条的颜色不会随着进度的增加而改变。

`<solid>` 元素用于设置圆形进度条的颜色。`android:color` 属性设置为 `@color/colorPrimary`，表示进度条的颜色为当前主题的颜色。

需要注意的是，`progress_bar.xml` 文件中的颜色值应该是十六进制的颜色值，例如 `#FF0000` 表示红色。如果需要使用当前主题的颜色，可以使用 `@color/colorPrimary`，`@color/colorAccent` 等颜色资源。


## 1.3. 方案3-修改全局样式中的progressDrawable

要修改 ProgressBar 的进度颜色，你可以在应用的主题中设置 `android:progressDrawable` 属性。以下是一个示例：

1. 首先，在 `res/values/styles.xml` 文件中创建一个新的主题，例如 `CustomProgressBarStyle`，并设置 `android:progressDrawable` 属性：

```xml
<resources>
    <!-- Base application theme. -->
    <style name="AppTheme" parent="Theme.AppCompat.Light.DarkActionBar">
        <!-- Customize your theme here. -->
    </style>

    <style name="CustomProgressBarStyle" parent="AppTheme">
        <item name="android:progressDrawable">@drawable/custom_progressbar</item>
    </style>
</resources>
```

2. 接下来，在 `res/drawable` 文件夹下创建一个名为 `custom_progressbar.xml` 的文件，并定义一个颜色渐变背景：

```xml
<?xml version="1.0" encoding="utf-8"?>
<layer-list xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@android:id/background">
        <shape>
            <corners android:radius="5dp"/>
            <solid android:color="#FFC107"/>
        </shape>
    </item>
    <item android:id="@android:id/secondaryProgress">
        <clip>
            <shape>
                <corners android:radius="5dp"/>
                <solid android:color="#FFA500"/>
            </shape>
        </clip>
    </item>
    <item android:id="@android:id/progress">
        <clip>
            <shape>
                <corners android:radius="5dp"/>
                <solid android:color="#FF0000"/>
            </shape>
        </clip>
    </item>
</layer-list>
```

3. 最后，在布局文件中使用 `CustomProgressBarStyle` 作为主题，并将 `ProgressBar` 的 `style` 属性设置为 `CustomProgressBarStyle`：

```xml
<ProgressBar
    style="@style/CustomProgressBarStyle"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:indeterminate="true"/>
```

这样，ProgressBar 的进度颜色将根据你在 `custom_progressbar.xml` 文件中定义的颜色进行更改。

