---
title: 从通知中心移除已读的推送
layout: post
tags: iOS 
---

有的 App 在通过通知中心的某一条通知进入时，那条通知不会从通知中心移除，需要用户手动清除。

以下是点击通知进入 App 后，自动从通知中心将其移除的方法：

```
// AppDelegate.m

- (void)application:(UIApplication *)application didReceiveLocalNotification:(UILocalNotification *)notification
{
    if (notification) {
        
        // do something.
        
        // 移除该条通知
        [[UIApplication sharedApplication] cancelLocalNotification:notification];
        // 移除图标上数字
        [[UIApplication sharedApplication] setApplicationIconBadgeNumber:0];
        
    }
}
```