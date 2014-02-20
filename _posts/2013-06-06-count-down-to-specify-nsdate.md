---
title: 显示指定日期的倒计时
layout: post
tags: iOS 
---

- 首先定义 NSTimer

```
NSTimer *timer = [NSTimer scheduledTimerWithTimeInterval:1.0 target:self selector:@selector(timerFireMethod:) userInfo:nil repeats:YES];
```

- 然后计算目标时间

```
NSCalendar *calendar = [NSCalendar currentCalendar];
NSDateComponents *components = [[NSDateComponents alloc] init];
[components setYear:2012];
[components setMonth:8];
[components setDay:13];
[components setHour:12];
[components setMinute:0];
[components setSecond:0];
NSDate *fireDate = [calendar dateFromComponents:components];//目标时间
```

- 倒计时在 timer 的触发方法里完成

```
- (void)timerFireMethod:(NSTimer *)timer
{
    NSCalendar *calendar = [NSCalendar currentCalendar];
    NSDate *today = [NSDate date];//当前时间
    unsigned int unitFlags = NSYearCalendarUnit | NSMonthCalendarUnit | NSDayCalendarUnit | NSHourCalendarUnit | NSMinuteCalendarUnit | NSSecondCalendarUnit;
    NSDateComponents *d = [calendar components:unitFlags fromDate:today toDate:fireDate options:0];//计算时间差
    auctionTime.text = [NSString stringWithFormat:@"%d天%d小时%d%分%d秒", [d day], [d hour], [d minute], [d second]];//倒计时显示
}
```

这样 `actionTime` 就会以1秒的间隔显示倒计时。
如果有专门的服务器，还要把服务器和设备的时间差考虑进去。