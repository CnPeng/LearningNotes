[原文链接：iOS-UITableView的HeaderSection/FooterSection悬停和取消方法](https://www.jianshu.com/p/9f5f3bd82c11)

## 1.设置style:    

UITableViewStyleGrouped 默认不悬停，UITableViewStylePlain 默认悬停  


```swift
// 悬停
let tableView = UITableView(frame: self.view.bounds, style: .plain)
// 不悬停，但组间距太大
let tableView = UITableView(frame: self.view.bounds, style: .grouped)
```


## 2.改变颜色

一般来说，大家都相对喜欢用 `UITableViewStylePlain`，想要其不悬停，或者 HeaderSection 或FooterSection 某一个悬停。网上大多的方法就是设置内间距，这类方法很多，我就不做赘诉了。但我喜欢用另一种方法来实现这个效果。

提示：本方法仅适用于纯色Header/Footer的情况

2.1.设置tableView的背景色为footer的颜色，一般来说就算tableView周围有间隙，颜色也和footer颜色一致。 self.tableView.backgroundColor = [UIColor lightGrayColor];

2.2.设置想要取消FooterSection的悬停，设置其背景颜色为 透明，这样其还是悬停的状态，但只在滑动到最底部时才有颜色(tableView.backgroundColor)，从而在效果上实现取消FooterSection悬停


```swift
- (UIView *)tableView:(UITableView *)tableView viewForFooterInSection:(NSInteger)section{

    UIView *view = [UIView new];//1.此处可选择是否重用；

    view.backgroundColor = [UIColor clearColor];

    return view;

}
```

2.2 取消HeaderSection悬停也一样


## 3.监听滚动

```swift
-(void)scrollViewDidScroll:(UIScrollView *)scrollView { // if (scrollView == self.myTableView) {

        //YOUR_HEIGHT 为最高的那个headerView的高度  //        CGFloat sectionHeaderHeight = 20;          if (scrollView.contentOffset.y<=sectionHeaderHeight&&scrollView.contentOffset.y>=0) { 

            scrollView.contentInset = UIEdgeInsetsMake(-scrollView.contentOffset.y, 0, 0, 0); 

        } else if (scrollView.contentOffset.y>=sectionHeaderHeight) { 

            scrollView.contentInset = UIEdgeInsetsMake(-sectionHeaderHeight, 0, 0, 0); 

        } 
    } 
}
```

## 4.

>CnPeng 这个没看明白。！！！

头部悬停 底部不悬停

```swift
self.tableView.contentInset = UIEdgeInsetsMake(0, 0, -105, 0); //首先改变内边距 -105是底部的距离 

 CGRect rectInTableView = [self.tableView rectForRowAtIndexPath:[NSIndexPath indexPathForRow:5 inSection:2]]; //现在是写死的 你可以根据模型数据 写成变量    

CGRect rect = [self.tableView convertRect:rectInTableView toView:[self.tableView superview]];//这是是最后cell row的高度

 if(rect.origin.y<=-1288){ //-1288 这个位置 是可以根据你的位置调整出来 self.tableView.contentInset = UIEdgeInsetsMake(0, 0, 0, 0);

    }else{

        self.tableView.contentInset = UIEdgeInsetsMake(0, 0, -105, 0);

    }
```