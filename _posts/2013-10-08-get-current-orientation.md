---
title: 获取设备当前的方向
layout: post
tags: iOS 
---

- 如果在 `UIViewController` 内, `self.interfaceOrientation` 就可以了
- 其他地方 `[[UIDevice currentDevice] orientation]`
- 检测状态栏方向也是一种方法 `[UIApplication sharedApplication].statusBarOrientation`
- 可以接收方向变化的通知:

```
[[UIDevice currentDevice] beginGeneratingDeviceOrientationNotifications];
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(orientationChanged:) name:UIDeviceOrientationDidChangeNotification object:nil];
```