ScrollView 本身的高度设置为 match_parent ,其子View的高度也设置为 match_parent,自然状态下该子View的高度并不会占满 ScrollView 的高度。
 
原因是
* match_parent 针对一般布局而言，是子view的高度和 parent 的高度一致，但在 ScrollView 身上，工作机制并非如此，而是 ScrollView的高度随着子View的高度变化而变化(子View高度大于ScrollView时)。
* 在子View高度小于ScrollView高度时，必需在xml里为 ScrollView 加上 `android:fillViewport="true"`，这样子View小于ScrollView高度时就会占满父View.

原文：https://blog.csdn.net/yanzi1225627/article/details/52163460 
