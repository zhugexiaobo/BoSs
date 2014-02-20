---
title: 关于 Prefix.pch 文件
layout: post
tags: iOS
---

`<AppName>-Prefix.pch` 为 XCode 工程默认生成的预编译头文件，在其中我们可以定制一些全局的宏，放置常用的框架头文件，目的是提高编译速度和方便使用。

### 宏
这是来自 [CIMGF](http://www.cimgf.com/2010/05/02/my-current-prefix-pch-file/#more-977) 的一些宏定义：

```objc
#ifdef DEBUG
  #define DLog(...) NSLog(@"%s %@", __PRETTY_FUNCTION__, [NSString stringWithFormat:__VA_ARGS__])
  #define ALog(...) [[NSAssertionHandler currentHandler] handleFailureInFunction:[NSString stringWithCString:__PRETTY_FUNCTION__ encoding:NSUTF8StringEncoding] file:[NSString stringWithCString:__FILE__ encoding:NSUTF8StringEncoding] lineNumber:__LINE__ description:__VA_ARGS__]
#else
  #define DLog(...) do { } while (0)
  #ifndef NS_BLOCK_ASSERTIONS
    #define NS_BLOCK_ASSERTIONS
  #endif
  #define ALog(...) NSLog(@"%s %@", __PRETTY_FUNCTION__, [NSString stringWithFormat:__VA_ARGS__])
#endif

#define ZAssert(condition, ...) do { if (!(condition)) { ALog(__VA_ARGS__); }} while(0)
```
  - Debug 编译模式下 `GCC_PREPROCESSOR_DEFINITIONS` 中默认添加了 `DEBUG=1`，所以 `#ifdef DEBUG` 下的定义只在 Debug 模式有效
  - DLog 是详细版的 NSLog
  - ALog 是 Assert Log 的简写，在 Debug 模式下，相当于强制 assert；其他模式下相当于 NSLog
  - ZAssert 是带有条件判断的 ALog

除此之外，还可以定义一些使用频繁的长语句，如：

```
#define IS_IPHONE_5 (fabs((double)[[UIScreen mainScreen] bounds].size.height - (double)568) < DBL_EPSILON)
#define IS_IPAD UI_USER_INTERFACE_IDIOM() == UIUserInterfaceIdiomPad
#define IOS7_OR_LATER (floor(NSFoundationVersionNumber) > NSFoundationVersionNumber_iOS_6_1)
#define APP_DELEGATE ((AppDelegate *)[[UIApplication sharedApplication] delegate])
```
或者这样的：

```
#define VIEW_X(view) view.frame.origin.x
#define VIEW_Y(view) view.frame.origin.y
#define VIEW_WIDTH(view) view.frame.size.width
#define VIEW_HEIGHT(view) view.frame.size.height
#define RGB(r, g, b) [UIColor colorWithRed:r/255.0 green:g/255.0 blue:b/255.0 alpha:1]
```

### 头文件
Prefix.pch 文件初始状态：

```
#import <Availability.h>

#ifndef __IPHONE_4_0
#warning "This project uses features only available in iOS SDK 4.0 and later."
#endif

#ifdef __OBJC__
  #import <UIKit/UIKit.h>
  #import <Foundation/Foundation.h>
#endif
```
默认包含了2个最常用的基本框架。可以把其他较常用的也包含进来：

```
#import <SystemConfiguration/SystemConfiguration.h>
#import <MobileCoreServices/MobileCoreServices.h>
#import "AFMyClient.h"
#import "MBProgressHUD.h"
#import "MyConstants.h"
#import "MyTools.h"
```

### 注意事项
编译器只编译改动过的文件，假如 pch 中某个文件修改了，那么它包含的其他文件也会重新编译一次，这样就会消耗大量时间。所以里面添加的最好是很少变动或不变动的头文件或者是预编译的代码片段。