---
title: UITableView 性能优化
layout: post
tags: iOS 
---

- 使用 cell 重用机制
- 图片异步加载，AFNetWorking 自带，也可以使用 [SDWebImage](https://github.com/rs/SDWebImage)
- cell 内容尽量避免透明效果
- 减少使用 `reloadData`，用 `reloadRowsAtIndexPaths` 或 `insertRowsAtIndexPaths:withRowAnimation:`
- cell 高度固定的话直接用 `cell.rowHeight` 设置高度，不要再实现`tableView:heightForRowAtIndexPath:`

> There are performance implications to using `tableView:heightForRowAtIndexPath:` instead of the `rowHeight` property. Every time a table view is displayed, it calls `tableView:heightForRowAtIndexPath:` on the delegate for each of its rows, which can result in a significant performance problem with table views having a large number of rows (approximately 1000 or more).

- 如果 cell 是动态行高，计算出高度后缓存。tableView 会在加载的时候把全部 cell 的高度通过 heightForRowAtIndexPath: 都计算出来，即使 cell 还没有展示。
