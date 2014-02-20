---
title: 给任意 UIView 添加 touch 事件
layout: post
tags: iOS 
---

先看文档中对 UIView 的概述：[The UIView class defines a rectangular area on the screen and the interfaces for managing the content in that area. At runtime, a view object handles the rendering of any content in its area and also handles any interactions with that content.](https://developer.apple.com/library/ios/documentation/uikit/reference/uiview_class/uiview/uiview.html)

UIView 是应用与用户交互的主要方式，担当 MVC 中的视图层。文档中列举了它的几个主要功能：绘制和动画，布局和子视图管理，事件处理。因为 UIView 继承自 UIResponder，UIResponder 是所有事件响应的基石，所以 UIView 能响应和处理 touch，motion 和 remoteControl 事件。

部分 UIKit 中的控件已自带手势处理，但是当自定义一个 UIView 并处理触摸时，我们可以用 `addGestureRecognizer:` 方法给视图添加手势识别功能，不过更方便和易于维护的方法是改写该视图的 `touchesBegan:withEvent:`，`touchesEnded:withEvent:`等方法。

上代码：

**MyUIView.h**

```
@class MyUIView;
@protocol MyViewDelegate <NSObject >

- (void)myUIView:(MyUIView *)view touchesWithTag:(NSInteger)tag; // touch 后的回调方法

@end

@interface MyUIView : UIView 

@property (nonatomic, assign) id <MyViewDelegate> delegate;

@end
```


**MyUIView.m**

```
......

- (void)touchesBegan:(NSSet *)touches withEvent:(UIEvent *)event
{
    [self setBackgroundColor:[UIColor whiteColor]]; // 点击高亮显示
}

// 还原 view 颜色，获取手指离开屏幕时的坐标点, 在view范围内就可以触发自定义的操作
- (void)touchesEnded:(NSSet *)touches withEvent:(UIEvent *)event
{
	[self setBackgroundColor:[UIColor blackColor]];
    UITouch *touch = [touches anyObject];
    CGPoint points = [touch locationInView:self];
    if (points.x >= self.frame.origin.x && points.y >= self.frame.origin.x && points.x <= self.frame.size.width && points.y <= self.frame.size.height)
    {
        [_delegate myUIView:self touchesWtihTag:self.tag];
    }
}

```
    
**实例化：**
    
```
MyUIView *aView = [[MyUIView alloc] initWithFrame:CGRectMake(10, 10, 100, 20)];
[aView  setDelegate:self];
[self.view addSubview:aView];

#pragma mark MyUIView Delegate Methods

- (void)myUIView:(MyUIView *)myUIView touchesWtihTag:(NSInteger)tag
{
    // do something.
}
```
