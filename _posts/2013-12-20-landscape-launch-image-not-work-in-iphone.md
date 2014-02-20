---
title: 横向启动图片无法在 iPhone 上显示
layout: post
tags: iOS 
---

昨天发现一个只支持横向使用的应用的启动图片无法显示，傻傻的找了好久原因。最后在[文档](https://developer.apple.com/library/ios/DOCUMENTATION/iPhone/Conceptual/iPhoneOSProgrammingGuide/App-RelatedResources/App-RelatedResources.html#//apple_ref/doc/uid/TP40007072-CH6-SW12)的 App Launch (Default) Images 段落中发现，iPhone and iPod touch 的 Landscape 一栏赫然写着 **Not supported**。

……

最后还是比较完美的解决了这个问题，记录一下。

- 首先，设置应用支持竖屏（Portrait）
- 添加图片内容为横向，但图片属性为 640×960（320×480、640×1136）的竖向图作为 Portrait LaunchImage
- 在应用启动后立刻将屏幕旋转到横向

```
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    [application setStatusBarOrientation:UIInterfaceOrientationLandscapeLeft animated:NO];

    ……

}
```
- 因为应用已设置支持竖屏，所以每个 UIViewController 中要将屏幕锁定为横屏

```
- (NSUInteger)supportedInterfaceOrientations
{
    return UIInterfaceOrientationMaskLandscape;
}
```

---------------
可以看到，这个办法其实是用一张“伪装”成横向的图片作为 LaunchImage，然后在应用启动后旋转屏幕到横屏，不然 LaunchImage 就会旋转了。