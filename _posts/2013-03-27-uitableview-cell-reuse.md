---
title: UITableView cell 的重用机制
layout: post
tags: iOS 
---

在 UITableViewCell 上添加 SubView 后，滑动或点击状态（高亮）时，出现重叠现象，原来是 UItableView 的重用机制问题，解决方法：

```
- (UITableViewCell *)tableView:(UITableView *)tableView cellForRowAtIndexPath:(NSIndexPath *)indexPath {
    static NSString *CellIdentifier = @"Cell";
    UITableViewCell *cell = [tableView dequeueReusableCellWithIdentifier:CellIdentifier];
    if (cell == nil) {
        cell = [[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
    }
    
    NSArray *subviews = [[NSArray alloc] initWithArray:cell.contentView.subviews];
    for (UIView *subview in subviews) {
        [subview removeFromSuperview]; // 清除已有的 subview
    }
    
    //custom
    
    return cell;
}
```

---
每一个 UITableView 里都维护着一个 cell 队列，`dequeueResableCellWithIdentifier` 从字面上理解就是「出列可重用的 cell」,也就是根据一个标识 identifier 从 cell 队列里取出一个 UITableViewCell。因此，在 UITableView 刚加载的时候，cell 队列里没有可用的 cell，所以必须通过语句

```
[[UITableViewCell alloc] initWithStyle:UITableViewCellStyleDefault reuseIdentifier:CellIdentifier];
``` 
来创建对应 CellIdentifier 标识的 UITableViewCell 实例。

当 UITableView 滑动导致 UITableViewCell 滚出手机屏幕视图的时候，程序会将这一个 UITalbeViewCell 实例放入 cell 队列中。当有新的 UITableViewCell 需要展现在手机屏幕视图上时，就会调用 `tableView:cellForRowAtIndexPath:` 方法了。

因此我们可以知道以下几点：

- 重取出来的 cell 是有可能已经捆绑过数据或者加过子视图的，所以，如果有必要，要清除数据（比如 textlabel 的 text）和 remove 掉 add 过的子视图
- 这样设计的目的是为了避免频繁的 alloc 和 delloc cell 对象，节省内存
- 设计的关键是实现 cell 和数据的完全分离
- 如果不想重用 UITableViewCell 实例，如在一个每一行都显示不同内容的 UITableView 实例时，我们可以用如下的方法：
    
```
NSString *CellIdentifier = [NSString stringWithFormat:@"Cell%d%d", [indexPath section], [indexPath row]];
```
    
来重新定义标识。这样每一行都有其对应的 identifier,从 cell 队列里取出来只有两个结果：
1. cell 队列里没有此 identifier 对应的 UITableViewCell 实例，返回 nil
2. cell 队列里有此 identifier 对应的 UITableViewCell 实例，而且不会有重用到其他不同行的 cell 的情况