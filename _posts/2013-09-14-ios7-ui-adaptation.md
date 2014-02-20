---
title: iOS7 UI 适配
layout: post
tags: iOS 
---

iOS7 在界面上做了颠覆性的改动，但用户还是会逐渐接受并喜欢上这个改变，这就是苹果的魅力吧~

废话少说，早日升级应用，适配 iOS7 才是正事。好在苹果给出了详细的文档，[iOS 7用户界面过渡指南](https://developer.apple.com/library/iOs/documentation/UserExperience/Conceptual/TransitionGuide/index.html)「[中文版](http://mued.sohu.com/2013/06/ios-7-ui-transition-guide/)」（中文版不是最新的，建议看文档），看完就基本能搞定了。

因为状态栏和导航栏都变成透明，除了重新为 iOS7 设计界面，还可以添加以下几行代码让 UIViewController 保持之前的样式。

```
#if __IPHONE_OS_VERSION_MAX_ALLOWED >= 70000
if ( IOS7_OR_LATER )
{
    self.edgesForExtendedLayout = UIRectEdgeNone;
    self.extendedLayoutIncludesOpaqueBars = NO;
    self.modalPresentationCapturesStatusBarAppearance = NO;
}
#endif// #if __IPHONE_OS_VERSION_MAX_ALLOWED >= 70000

#define IOS7_OR_LATER	(floor(NSFoundationVersionNumber) > NSFoundationVersionNumber_iOS_6_1)
```

还有个要注意的就是 UITableViewCell 的层级改变——在 `UITableViewCellContentView` 下方多了一层 `UITableViewCellScrollView`。
