---
title: Layer of UIView
layout: post
tags: iOS 
---

本笔记仅记录 `UIView` 的 `layer` 用法，其原理待另行补充，或查看 [官方文档](https://developer.apple.com/library/ios/documentation/graphicsimaging/reference/CALayer_class/Introduction/Introduction.html)。

### 简介
- 每个 `UIView` 都有 `CALayer`，同时 `UIView` 是系统中界面元素的基础，所以 `CALayer` 应用很广泛。
- `CALayer` 能够对 `UIView` 做许多设定，如：阴影、边框、圆角和透明效果等，易于使用且效果很好。

### 使用
- 必须引入 **`<QuartzCore/QuartzCore.h>`** 才能使用
- **边框（Border）**

    ```
    view.layer.borderColor = [UIColor greyColor].CGColor;
    view.layer.borderWidth = 1;    
    ```
边框在视图的 frame 内绘制。
- **圆角（Corner）**

    ```
    view.layer.cornerRadius = 20;
    view.layer.masksToBounds = YES;
    view.layer.borderColor = [UIColor grayColor].CGColor;
    view.layer.borderWidth = 1;
    ```
`masksToBounds` ~~或 view 的 `clipsToBounds` 属性至少1个~~设置为 `YES` 才能起作用。
- **阴影（Shadow）**

    ```
    view.layer.shadowColor = [UIColor blackColor].CGColor;
    view.layer.shadowOpacity = 1.0;
    view.layer.shadowRadius = 5.0;
    view.layer.shadowOffset = CGSizeMake(0, 3);
    view.clipsToBounds = NO;
    ```
`UIView` 的 `clipsToBounds` 属性默认为 `NO`，设置是为了保证阴影能在 frame 外展示出来。可以通过自定义 `shadowPath` 来绘制自定义形状样式的阴影。



