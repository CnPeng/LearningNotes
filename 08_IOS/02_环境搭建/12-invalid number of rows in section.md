## 一、报错信息：


Exception NSException *   "Invalid update: invalid number of rows in section 0. The number of rows contained in an existing section after the update (5) must be equal to the number of rows contained in that section before the update (5), plus or minus the number of rows inserted or deleted from that section (0 inserted, 1 deleted) and plus or minus the number of rows moved into or out of that section (0 moved in, 0 moved out)."    0x0000000282bb7fc0


## 二、问题场景：

UITableView 列表条目中有删除事件，执行删除操作时，会同时删除数据源中的数据和视图中的数据，代码如下：

```swift
self.viewModel.itemDel(index: index)
tableView.deleteRows(at: [index], with: .fade)
```

但是，在 `self.viewModel.itemDel(index: index)` 的方法中又触发了一次 `self.tableView.reloadData()`, 所以，**当执行 `reloaData()` 时 `deleteRows() ` 也在执行，这样就导致了 `section` 和 `row` 的数据混乱，然后就报错了**。

## 三、解决方案

在执行 `itemDel()` 后，要么仅执行 `reloadData()` 要么仅执行 `deleteRows()`, 推荐执行后者，前者会刷新整个列表。


