pop 中不能使用 VP +Fragment的模式，会一直报 fragment 中找不到View。只能使用 VP + PagerAdapter 
找不到View时的错误提示：
https://stackoverflow.com/questions/29141729/recyclerview-no-adapter-attached-skipping-layout
https://github.com/firebase/FirebaseUI-Android/issues/457
https://forums.bignerdranch.com/t/on-rotation-rotation-e-recyclerview-no-adapter-attached-skipping-layout/10533
https://openclassrooms.com/forum/sujet/recyclerview-no-adapter-attached-skipping-layout

pop中可以使用 VP+PagerAdapter实现多页面侧滑效果，but, 如果我点击了返回键，整个Popup就被dismiss了---这是popup源码中干的好事！！popup正在展示的时候，点击了返回键，popup就会将该事件销毁，此时actviity 接收不到事件

最终解决方案 --  额外加一个空ViewGroup，在其中展示FM作为引导页视图，然后使用 FragmentTransaction 事务 replay(fragment) 、remove(fragment)  即可。
