---
title: 页面下拉后露出「背景」
layout: post
tags: iOS 
---

小技巧，露出的其实不是背景，而是该视图的「扩展」。就是在该页面上添加一个 `UIView`，该 `UIView` 的位置是在页面的正上方，下拉的时候就能显现出来。具体实现：

```
UIImageView *backgroundImage = [[UIImageView alloc]initWithImage:UIImage];
backgroundImage.frame = CGRectMake(0.0f, -400.0f, 320.0f, 400.0f);
[_view addSubView:backgroundImage];
```
