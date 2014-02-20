---
title: LocalNotification 不随着应用删除的问题
layout: post
tags: iOS 
---

貌似发现本地通知的一个 bug，就是在应用删除后，该应用已注册的 LocalNotification 还是会持续通知。

```
// AppDelegate.m
- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions
{
    ………
    
    // 清除应用删除前的通知
    if (![defaults objectForKey:@"isFirstLaunch"]) {
        [[UIApplication sharedApplication] cancelAllLocalNotifications];
        [defaults setBool:YES forKey:@"isFirstLaunch"];
    }

     ………
}
```

若重新安装 App 后未打开则还是会收到通知╮(￣▽￣")╭ 