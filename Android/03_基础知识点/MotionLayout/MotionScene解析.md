# 1.  MotionScene 文件的节点解析

MotionScene 文件中完整的节点嵌套结构如下：

    ├─ MotionScene
    │  ├─ Transition
    |    ├─ OnClick
    |    ├─ OnSwipe
    |    └─ KeyFrameSet
    |        ├─ KeyPosition
    |        ├─ KeyAttribute
    |        ├─ KeyCycle
    |        ├─ KeyTrigger
    │        └─ KeyTimeCycle
    │  └─ ConstraintSet
    |    └─ Constraint
    │        └─ CustomAttribute

## 1.1. MotionScene

`<MotionScene>` 节点是文件的根节点。

### 1.1.1. 必选子节点

节点名 | 作用 | 数量限制| 备注
---|---|---|---
`<Transition>` | 指定动画的起始和结束状态，<br> 以及两种状态之间的转换 | 一个或多个 | 有多个 `<Transition>` 元素时，<br>MotionLayout 会根据用户的动作选择最合适的元素。<br>例如，`<MotionScene>` 可能有四个 `<Transition>` 子元素，<br>每个元素都包含一个 `<onSwipe>`，<br>分别响应用户在不同方向的滑动操作。<br>当用户在屏幕上滑动时，<br>MotionLayout 会使用适当的 `<Transition>` 以便沿该方向滑动。

### 1.1.2. 可选子节点

节点名 | 作用 | 数量限制| 备注
---|---|---|---
`<ConstraintSet>` | 指定 `<Transition>` 节点的开始和结束状态 | 零个或多个 `<ConstraintSet>`  | 

### 1.1.3. 属性

属性 | 作用/含义 | 备注
---|---|---|---
`defaultDuration` | 定义 `<Transition>` 节点的默认持续时间<br>（以毫秒为单位）。 | 例如，设置 `defaultDuration="300"` 后，<br>如果所有 `<Transition>` 节点都没有指定自己 `duration` 属性，<br>则默认持续时间为 300 毫秒。如果 `<Transition>` 节点有设置，则不使用。

## 1.2. Transition

用于指定动效的开始和结束状态、任何所需的中间状态以及触发该运动的用户交互

### 1.2.1. 可选子节点

子节点均为可选节点

节点名 | 作用 | 数量限制| 备注
---|---|---|---
`<onClick>` | 指定触发动效的 viewID | 零个或多个 | 具体参考 onClick 一节
`<onSwipe>` | 指定触发动效的滑动事件 | 零个或多个 | 具体参考 onSwipe 一节
`<KeyFrameSet>` | 指定动效的关键帧信息 | 零个或多个 | 具体参考 KeyFrameSet 一节

### 1.2.2. 属性

属性 | 作用/含义 | 备注
---|---|---|---
`constraintSetStart` | 动效的初始状态 | 取值可以是 `<ConstraintSet>` 的 ID，也可以是布局。<br>如需指定 `<ConstraintSet>`，取值为 `@+id/constraintSetId`。<br>如需指定布局，取值为 `@layout/layoutID`。
`constraintSetEnd` |动效的最终状态 | 取值同上
`duration` | 动效的时长 | 以毫秒为单位。<br>如果未指定，则使用 `<MotionScene>`  元素的 `defaultDuration` 属性。

## 1.3. Transition 的子节点

### 1.3.1. onClick

指定当用户**点按**某个特定视图时触发动效。

单个 `<Transition>` 可以具有多个 `<onClick`> 节点，其中每个 `<onClick>` 可指定一个不同的目标视图和一个在点按此视图时要执行的操作。

属性 | 作用/含义 | 备注
---|---|---|---
`targetId` |  触发 `Transition` 事件的 ViewID | 点击 targetId 对应的 View 时，触发 `Transition`
`clickAction` | 点按视图时要执行的操作 | 可选。<br>默认情况下，如果当前处于 `constraintSetStart` 状态，<br>点击后则移动到 end 状态并带有动效；<br>反之，点击后则移动到 start 状态。

`clickAction` 的取值如下：

* `transitionToStart` ：让 `<Transition>` 关联的 `<ConstraintSet>` 中指定的  View 移动到 start 位置/状态 ，并添加平缓动画效果；如果 View 已经在 start 位置/状态，则无变化。
* `transitionToEnd`：让 `<Transition>` 关联的 `<ConstraintSet>` 中指定的  View 移动到 end 位置/状态 ，并添加平缓动画效果；如果 View 已经在 end 位置/状态，则无变化。
* `jumpToStart`：让 `<Transition>` 关联的 `<ConstraintSet>` 中指定的  View 直接跳转到 start 位置/状态 ，无动画效果；如果已经在 start 位置/状态，则无变化。
* `jumpToEnd`：让 `<Transition>` 关联的 `<ConstraintSet>` 中指定的  View 直接跳转到 end 位置/状态 ，无动画效果；如果已经在 end 位置/状态，则无变化。
* `toggle`：如果 `<Transition>` 关联的 `<ConstraintSet>` 中指定的  View 处于 start 位置/状态 ，则切换为 end 位置/状态；反之，则切换为 start 位置/状态。不设置 `clickAction` 时默认为该效果。


示例：

点击 button2 按钮时触发 `<Transtion>`：如果 id 为 `button` 的按钮处于 start 状态（视图左上方）, 则移动到 `<ConstraintSet android:id="@+id/end">` 约束的位置/状态；反之，则移动到 start 状态。代码如下：

`activity_motion_deitor.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">
    <data>
        <variable
            name="vm"
            type="com.cnpeng.newapi.motionlayout.vm.MotionEditorVm" />
    </data>

    <androidx.constraintlayout.motion.widget.MotionLayout
        android:id="@+id/constraintLayout"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layoutDescription="@xml/activity_motion_editor_scene">

        <include
            android:id="@+id/title"
            layout="@layout/include_title_layout"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toTopOf="parent"
            app:leftBtnClick="@{()->vm.onBackBtnClick()}"
            app:title="@{`MotionEditor 使用`}" />

        <Button
            android:id="@+id/button"
            android:layout_width="60dp"
            android:layout_height="44dp"
            android:background="#FAA"
            android:gravity="center"
            android:text="BTN"
            app:layout_constraintLeft_toLeftOf="parent"
            app:layout_constraintTop_toBottomOf="@id/title" />

        <Button
            android:id="@+id/button2"
            android:layout_width="60dp"
            android:layout_height="44dp"
            android:background="#FAA"
            android:gravity="center"
            android:text="BTN2"
            android:layout_marginTop="@dimen/dp10"
            app:layout_constraintRight_toRightOf="parent"
            app:layout_constraintTop_toBottomOf="@id/title" />
    </androidx.constraintlayout.motion.widget.MotionLayout>
</layout>
```

`activity_motion_editor_scene.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<MotionScene xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:motion="http://schemas.android.com/apk/res-auto">

    <!--表示运动过程，从 id 为 start 的 ConstrainSet 运动
    到 id 为 end 的 ConstraintSet。duration 表示时长。 -->
    <Transition
        motion:constraintSetEnd="@+id/end"
        motion:constraintSetStart="@id/start"
        motion:duration="1000">

        <!--运动过程中的关键帧信息-->
        <KeyFrameSet></KeyFrameSet>
        <!--点击 id 为 button2 的 view 之后，触发 Transtion 操作-->
        <OnClick motion:targetId="@+id/button2" motion:clickAction="toggle"/>
    </Transition>

    <!--id 为 start 的 ConstraintSet, 描述运动的起始状态-->
    <ConstraintSet android:id="@+id/start"></ConstraintSet>

    <!--id 为 start 的 ConstraintSet, 描述运动的结束状态-->
    <ConstraintSet android:id="@+id/end">
        <!--动效结束时的信息-->
        <Constraint
            android:id="@+id/button"
            android:layout_width="60dp"
            android:layout_height="44dp"
            android:layout_marginLeft="348dp"
            android:layout_marginTop="216dp"
            motion:layout_constraintLeft_toLeftOf="parent"
            motion:layout_constraintTop_toBottomOf="@id/title" />
    </ConstraintSet>
</MotionScene>
```

### 1.3.2. onSwipe

指定用户在布局上**滑动**时要执行的操作以及动画速度。

单个 `<Transition>` 可以具有多个 `<onSwipe>` 节点，其中每个 `<onSwipe>` 可指定一个不同的滑动方向和一项在用户执行该滑动时要执行的其他操作。

属性 | 作用/含义 | 备注
---|---|---|---
`touchAnchorId` | 在滑动之后移动的视图。| 
`touchAnchorSide` | 滑动所固定到的目标视图的一侧。| MotionLayout 将尝试在该固定点与用户手指之间保持恒定的距离。<br>可接受的值包括 "`left`"、"`right`"、"`top`" 和 "`bottom`"。<br>（**CnPeng : 这个属性有点难理解，实测也没看出来有啥不一样。** ）
`dragDirection` | 用户滑动动作的方向。 | 如果设置了此属性，此 `<onSwipe>` 将仅适用于沿特定方向的滑动。<br>可接受的值包括 "`dragLeft`"、"`dragRight`"、"`dragUp`" 和 "`dragDown`"。
`dragScale` | 控制视图相对于滑动长度的移动距离。| 默认值为 1，表明视图移动的距离应与滑动距离一致。<br>如果 dragScale 小于 1，视图移动的距离会远远小于滑动距离<br>（例如，dragScale 为 0.5 意味着如果滑动移动 4 厘米，目标视图会移动 2 厘米）。<br>如果 dragScale 大于 1，视图移动的距离会大于滑动距离<br>（例如，dragScale 为 1.5 意味着如果滑动移动 4 厘米，目标视图会移动 6 厘米）。
`maxVelocity` | 目标视图的最大速度。 | 
`maxAcceleration` | 目标视图的最大加速度。| 

### 1.3.3. KeyFrameSet

指定动效过程中视图的位置和属性。使 MotionLayout 能够将视图平滑地从起点移至每个中间点，然后移至最终目标位置。

默认情况下，运动会从初始状态逐渐进入结束状态；通过使用 `<KeyFrameSet>`，可以构建更复杂的运动。

`<KeyFrameSet>` 包含 `<KeyPosition>` 或 `<KeyAttribute>` 节点。这些节点中的每个节点都指定目标视图在运动的特定点处的位置或属性。

例如，假设动效的初始状态为在视图左下角有一个不透明球，而最终状态为该球到达右上角且完全透明。默认情况下，MotionLayout 会沿对角线移动此球，球体逐渐透明，直到到达目标位置为止。使用 `<KeyFrameSet>` 则可以更改此行为。

假设我们需要将球设置为纵向移至左上角，同时保持完全不透明，然后水平移至右上角，同时淡出。我们就可以通过创建 `<KeyFrameSet>` 并在其中添加 `<KeyPosition>` 和 `<KeyAttribute>` 实现此目的。`<KeyPosition>` 表示球的中间位置，`<KeyAttribute>` 指定球在运动的中点保持不透明。

在 MotionEditor 中添加关键帧（Frame）的方式如下：

![](pics/20211202121351105_883820444.png)

#### 1.3.3.1. 子节点-KeyPosition

指定视图在动效中特定时刻的位置。该属性用于**调整默认的运动路径**。

例如，如果某个对象从左上角开始到右下角结束，则默认的动效将按直线路径沿屏幕对角线向下移动该对象。通过添加一个或多个 `<KeyPosition>` 元素，可以使路径变形（实现弧线、折线等路径）。

属性 | 作用/含义 | 备注
---|---|---|---
`motionTarget` | 指定当前 `<KeyPosition>` 作用于哪个 View。| 取值为目标 view 的 id
`framePosition` | 指定动效中视图何时到达该 `<KeyPosition>` 指定的点。| 取值为 1 到 99 之间的整数。<br>例如，framePosition 为 25，则视图在动画的四分之一处到达指定点。
`percentX`、<br>`percentY` | 指定视图应到达的位置。| 取值为 0~1 之间的浮点数。<br>具体位置会受到 `keyPositionType` 属性值的影响。
`keyPositionType` | 指定 `percentX` 和 `percentY` 的计算方式。 | 

`keyPositionType` 的取值说明如下：

取值|说明
---|---
`parentRelative` | 表示计算相对于**父视图**  的 `percentX` 和 `percentY` 值。<br>X 为横轴，范围从 0（左端）到 1（右端）。<br>Y 为纵轴，其中 0 为顶部，1 为底部。<br><br>例如，如果我们希望目标视图到达父视图右端中间的某个点，可以将 `percentX` 设置为 1，将 `percentY` 设置为 0.5。
`deltaRelative` | 此时 ,  `percentX` 的取值相对于 `<Transtion>` 在 X 轴方向的移动距离；<br> `percentY` 的取值相对于 `<Transtion>` 在 Y 轴方向的移动距离。<br><br>例如，假设目标视图向上移动 100 dp，然后再向右移动 100 dp。<br>但我们希望视图首先在运动的前四分之一部分向上移动 40 dp，然后向上呈弧形移动。<br>为此，**需要将 `framePosition` 设置为 25，将 `keyPositionType` 设置为 `deltaRelative` ，并将 `percentY` 设置为 **-0.4**。**
`pathRelative` | 此时，以 `<Transtion>` 起点和终点的连线作为 x 轴，与该线垂直的线作为 y 轴。<br> x 轴向右为正，y 轴向下为正。 `percentX` 和 `percentY` 是相对于起点和终点连线长度的取值。<br><br>假设我们希望视图：在动效前半部分的移动距离占总距离的 10％，然后加速移动以覆盖剩余 90％ 的距离。<br>为此，**需要将 `framePosition` 设置为 50，将 `keyPositionType` 设置为 `pathRelative` ，并将 `percentX` 设置为 0.1。**


* parentRelative 示意图

以父视图的宽高作为 `percentX`、`percentY` 的基准。

当我们设置  `keyPositionType="parentRelative"`，`framePosition="50"` 时，调整 `percentX` 和 `percentY` 的值会得到如下的效果：

![](pics/20211202200536755_631506366.png)

* deltaRelative 示意图

动效起始点的坐标为 `(0,0)`，终点的坐标为 `(1,1)`。`percentX` 的取值基于从起点到终点时在 X 轴的移动距离，`percentY` 的取值基于从起点到终点时在 Y 轴的移动距离。

当我们设置  `keyPositionType="deltaRelative"`，`framePosition="50"` 时，调整 `percentX` 和 `percentY` 的值会得到如下的效果：

![](pics/20211202163627828_509472291.png)



![](pics/20211202165404058_1268082065.png)

* pathRelative 示意图

当我们设置  `keyPositionType="pathRelative"`，`framePosition="50"` 时，调整 `percentX` 和 `percentY` 的值会得到如下两幅图的效果（在下图中，如果 percenty 取负值，将会在连线的右上方取点。）：

![](pics/20211202163659328_683494248.png)

![](pics/20211202163724035_597798333.png)



#### 1.3.3.2. 子节点-KeyAttribute

指定动效执行过程中视图在特定时刻的[标准属性](https://developer.android.google.cn/reference/android/support/constraint/motion/MotionLayout#standard-attributes)及其值。

* 标准属性如下：

属性 | 说明
---|---
`android:visibility`	| View 的可见性
`android:alpha` 	| View 的透明度
`android:elevation`	| View 在 Z 轴的倾斜度
`android:rotation`	| View 的旋转角度
`android:rotationX`	| View 在 X 轴的旋转角度
`android:rotationY`	| View 在 Y 轴的旋转角度
`android:scaleX`	| View 在 X 轴方向的缩放幅度
`android:scaleY`	| View 在 Y 轴方向的缩放幅度
`android:translationX` 	| View 在 X 轴方向的移动距离
`android:translationY` 	| View 在 Y 轴方向的移动距离
`android:translationZ`     | View 在 Z 轴方向的移动距离

* `<KeyAttribute>` 的属性如下：

属性 | 作用/含义 | 备注
---|---|---|---
`motionTarget` |  受该 `<KeyAttribute>` 控制的 ViewID
`framePosition` | 取值为 1 到 99 之间的整数。<br>用于指定在动效中视图何时具有该 `<KeyAttribute>` 指定的属性。<br>例如，如果 `framePosition` 为 25，则表示 View 在动效执行到四分之一时具有指定属性。

* 示例：

```xml
<KeyAttribute
    android:scaleX="0.2"
    android:scaleY="0.2"
    motion:framePosition="50"
    motion:motionTarget="@+id/button" />
```

上述代码中，

`motion:motionTarget="@+id/button"` 指定了受控的 ViewID,  `motion:framePosition="50"` 指定动效执行到 50% 时变让 `button` 在 x、y 轴上分别变为原始视图的 0.2 倍。

* 运行效果如下：

![](pics/关键属性-缩放.gif)


#### 1.3.3.3. 子节点-KeyCycle

根据给定的 `waveShape`（波形）和 `wavePeriod`（波动周期） 对指定 View 在动效过程中的**标准属性** 进行周期性变化。

（注意，重点是控制此处设置的标准属性，如果不设置标准属性，则没有波形效果）。

* 可控制的标准属性（不支持 Visibility）：

属性 | 说明
---|---
`android:alpha` 	| View 的透明度
`android:elevation`	| View 在 Z 轴的倾斜度
`android:rotation`	| View 的旋转角度
`android:rotationX`	| View 在 X 轴的旋转角度
`android:rotationY`	| View 在 Y 轴的旋转角度
`android:scaleX`	| View 在 X 轴方向的缩放幅度
`android:scaleY`	| View 在 Y 轴方向的缩放幅度
`android:translationX` 	| View 在 X 轴方向的移动距离
`android:translationY` 	| View 在 Y 轴方向的移动距离
`android:translationZ`     | View 在 Z 轴方向的移动距离

* `<KeyCycle>` 的属性值

属性 | 作用/含义
---|---
`motionTarget` | 受影响的 View 的 ID。
`wavePeriod` | 波动周期
`waveShape`  | 波形。取值有： `sin`-正弦,  `square`-方形 , ` triangle`-三角形 , `sawtooth`-锯齿 ， `reverseSawtooth`-反向锯齿 ,  `cos`-余弦 , `bounce`-反射（鱼鳞形）
`waveOffset` | 波形在 Y 轴的偏移距离（即视图整体下移多少距离）

* 示例：

我们先修改前述示例中  button 的移动方向和位置，让它从窗口左侧移动到窗口右侧，如下图：

![](pics/20211203092343023_1474763230.png)

然后，我们添加 `<KeyCycle>` ，让 Button 在动画过程中在 Y 轴向下偏移 50dp，并以 1 个周期的三角形波形进行移动。核心代码如下：


```xml
 <KeyCycle
      android:translationY="50dp"
      motion:motionTarget="@+id/button"
      motion:wavePeriod="1"
      motion:waveShape="triangle" />
```

效果如下：

![](pics/三角形波形.gif)

当 `motion:wavePeriod="2"` 时的效果如下：

![](pics/三角形波形2.gif)

更多波形的效果如下：

![](pics/20211203095700999_89352936.png)

![](pics/20211203100258256_663726099.png)

![](pics/20211203100421900_1214483375.png)

![](pics/20211203100634484_2137502537.png)

![](pics/20211203100729641_1898692343.png)

![](pics/20211203100855873_1862698028.png)

![](pics/20211203100937499_349150630.png)

![](pics/20211203101043512_1249314806.png)

#### 1.3.3.4. 子节点-KeyTrigger

动画执行过程中调用 View 的**空参方法**。

* 属性说明

属性|说明
---|---
`motionTarget`  | 被控制的 ViewId
`framePosition` | 动画的 frame(帧) 的百分比，取值为 1~99 之间的整数。<br>当动画执行到该百分比位置时，执行 `onCross`、`onPositiveCross`、`onNegativeCross` 中指定的方法。
`onCross` | 动画执行到 `framePosition` 时需要调用的方法名。<br>**该方法名称必须是 `motionTarget` 指定的 View 中所具备的`空参`方法**。<br>正向动画（start 到 end）和反向动画 (end 到 start) 都会执行。
`onPositiveCross` | 正向动画执行到 `framePosition` 时需要调用的方法名。
`onNegativeCross` |  正向动画执行到 `framePosition` 时需要调用的方法名。

* 示例

我们先修改前一小节示例中的 Button 按钮，让其高度包裹内容，并为 text 属性设置一个比较长的值，然后增加 `<KeyTrigger>` , 控制动画执行到一半时，让文本变成单行，核心代码如下：

```xml
<KeyTrigger
    motion:framePosition="50"
    motion:motionTarget="@+id/button"
    motion:onCross='setSingleLine' />
```

效果如下：

![](pics/KeyTrigger示例.gif)


代码内容截图：

![](pics/20211203150838333_117228594.png)


* 注意事项及源码分析

**注意：** `onCross`、`onPositiveCross`、`onNegativeCross` 中指定的方法名必须是空参方法。关于该注意事项的简要分析如下（以 onCross 为例）：

在 `KeyTrigger` 类中通过 `public void read(,) ` 读取属性信息，

![](pics/20211203141301332_1258035548.png)

如上图，该方法中又调用了其内部静态类 `KeyTrigger` 的静态方法 `read(,,)`, 该静态方法定义如下：

![](pics/20211203141814240_1826950521.png)

在上图中，读取到通过 `onCross` 声明的方法名之后，赋值给 `mCross`。

当动画执行时，会触发 `conditionallyFire(,)` 方法，其定义如下：

![](pics/20211203144315631_836221641.png)

在上述方法中，我们会看到在触发 `onCross` 声明的方法时，本质上是调用了反射。在通过反射读取方法信息时，没有声明参数类型；在通过 `invoke()` 调用方法时也没有传递参数，所以，`onCross` 只能接收空参方法名。

#### 1.3.3.5. 子节点-KeyTimeCycle

`KeyTimeCycle` 的属性和 `KeyCycle` 的属性一致。但是， `KeyTimeCycle` 指定的效果自始至终都会伴随 View 展示，不论 View 是否正在执行 `<Transition>` 声明的动画。

* 示例

核心代码：

```xml
<!--wave 参数无效-->
<KeyTimeCycle
    android:rotation="320"
    motion:motionTarget="@+id/button"
    motion:wavePeriod="1"
    motion:waveShape="sin" />
```

效果如下：

![](pics/keyTimeCycle.gif)

通过上图我们发现，我们设置的 `waveShape`、`wavePerid` 并未生效。

完整代码截图（因为动画会一直伴随 View ，所以，为了触发动画，我们又新增一个 button1）：

![](pics/20211203155607801_1018475059.png)

* KeyCycle 和 KeyTimeCycle 混合使用

核心代码：

```xml
<KeyCycle
    android:translationY="50dp"
    motion:motionTarget="@+id/button
    motion:wavePeriod="1"
    motion:waveShape="bounce" />
<!--wave 参数无效-->
<KeyTimeCycle
    android:rotation="320"
    motion:motionTarget="@+id/button
    motion:wavePeriod="1"
    motion:waveShape="sin" />
```

效果如下：

![](pics/KeyCycle和KeyTimeCycle混合.gif)


## 1.4. ConstraintSet

指定所有视图在动画序列中某一点上的位置和属性。

通常，一个 `<Transition>`  元素可指向两个 `<ConstraintSet>` 元素，一个定义动画序列的开始，另一个定义结束。

### 1.4.1. 必选子节点

节点名 | 作用 | 数量限制| 备注
---|---|---|---
`<Constraint>` | 指定动效中某个 view 中的约束信息 | 一个或多个 | 

### 1.4.2. 属性

属性 | 作用/含义 | 备注
---|---|---|---
`id` | 此约束条件集的唯一标识符。 | `<Transition>` 需要此 ID 来确定动画序列的起点和终点。
`deriveConstraintsFrom` | 另一个 ConstraintSet 的 ID | 使用该 id 对应的 ConstrainSet 中的约束信息替换当前 ConstraintSet 中的全部约束信息。<br>除非当前 ConstraintSet 中有明确声明的约束信息。

## 1.5. ConstraintSet 的子节点

### 1.5.1. Constraint

指定动效中某个 view 的位置和属性。

#### 1.5.1.1. 可选子节点-CustomAttribute

可包含一个或多个 `<CustomAttribute>` 节点，通过该节点可以约定视图的背景色、字体、字号等信息。

`CustomAttribute` 的属性说明：

属性|含义|备注
---|---|---
`attributeName`| 属性名称 | 是必需属性，且该属性名必须有对应的 getter 和 setter 方法。<br>例如， 属性名称 `backgroundColor` 在此处为合法值，因为视图具有基本的 `getBackgroundColor()` 和 `setBackgroundColor()` 方法。

我们通过指定了 `attributeName` 指定了属性名称之后，还必须增加一行为该属性赋值的 xml 语句。在该语句中，值的接收者必须是下列选项之一：

接收者|说明
---|---
`motion:customColorValue`|  适用于颜色
`motion:customIntegerValue` |  适用于整数
`motion:customFloatValue` | 适用于浮点值
`motion:customStringValue` |  适用于字符串
`motion:customDimension` |  适用于尺寸
`motion:customBoolean` |  适用于布尔值

**注意**:

* 在指定自定义属性时，必须在 `<Transtion>` 中描述开始和结束的 `<ConstraintSet>`  的 `<Constraint>` 中分别定义一次。也就是说，要使用 `<CustomAttribute>`， 就必须在 Transtion 的开始和结束时都定义（成对出现）；否则，不生效。


#### 1.5.1.2. 属性

`<Constraint>` 节点支持 [`ConstraintLayout` 的完整属性](https://developer.android.google.cn/reference/android/support/constraint/ConstraintLayout#developer-guide)。


