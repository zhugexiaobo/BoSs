---
title: 动态改变 UITableViewCell 高度
layout: post
tags: iOS 
---

实现点击列表中的某个 cell 使其高度变大。

```
// cell 状态为选中时高度变为 2 倍
- (CGFloat)tableView:(UITableView *)tableView heightForRowAtIndexPath:(NSIndexPath *)indexPath 
{
    if(selectedRow == indexPath.row) {
        return kCellHeight * 2;
    }
    return kCellHeight;
}
```
在点击触发的地方，`tableView:didSelectRowAtIndexPath:` 或者 cell 上的控件事件中加入以下代码：

```
// 不刷新列表数据，只更新可见 cell 的界面，并带有动画
[self.tableView beginUpdates];
[self.tableView endUpdates];
```
如果要刷新该 cell 的数据，可以用 `reloadRowsAtIndexPaths:withRowAnimation:` 方法。