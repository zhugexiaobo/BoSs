---
title: UILocalNotification 使用
layout: post
tags: iOS 
---

```
/**
* Add local notification
*
* @param myData Custom data to transfer
*
*/
- (void)addLocalNotificationWithData:(MyData *)myData
{
    UILocalNotification *notification = [[UILocalNotification alloc] init];
    notification.fireDate = [[NSCalendar currentCalendar] dateFromComponents:components]; // 通知触发时间
    notification.timeZone = [NSTimeZone defaultTimeZone];
    notification.repeatInterval = NSDayCalendarUnit; // 每天重复
    notification.alertBody = [NSStringstringWithFormat:@"%@到点", myData.text]; // 通知弹框中的提示内容
    notification.alertAction = @"好";
    notification.soundName = UILocalNotificationDefaultSoundName; // 可以添加自定义的提示声
    notification.userInfo = [NSDictionary dictionaryWithObject:[NSKeyedArchiver archivedDataWithRootObject:myData] forKey:@"myData"]; // 通知传递的数据
    [[UIApplication sharedApplication] scheduleLocalNotification:notification]; // 使通知生效
}

/**
* Cancel local notification
*
* @param myData Custom data to transfer
*
- (void)deleteLocalNotificationWithData:(MyData *)myData
{
    NSArray *allLocalNotification = [[UIApplication sharedApplication] scheduledLocalNotifications]; // 所有已有的通知
    UILocalNotification *localNotification;
    for (localNotification in allLocalNotification) {
        MyData *userInfo = [NSKeyedUnarchiver unarchiveObjectWithData:[localNotification.userInfoobjectForKey:@"myData"]];
        if ([userInfo.url isEqualToString:myData.url]) { // 根据 userInfo 中的数据找出要删除的通知
            [[UIApplication sharedApplication] cancelLocalNotification:localNotification];
            return;
        }
    }
}
```