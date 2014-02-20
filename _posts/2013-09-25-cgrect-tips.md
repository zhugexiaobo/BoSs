---
title: CGRect 使用技巧
layout: post
tags: iOS 
---

在 iOS 开发中经常需要处理点,大小以及矩形,对应的数据结构分别为 `CGPoint`,`CGSize`,`CGRect`，这些数据结构都是C结构体,定义分别如下:

```
CGPoint {
  CGFloat x;
  CGFloat y;
};
CGSize {
  CGFloat width;
  CGFloat height;
};
struct CGRect {
  CGPoint origin;
  CGSize size;
};
```
操作这些数据结构有一些方便的方法,总结如下:

#### 创建 CGPoint,CGSize,CGRect

```
CGSize size = CGSizeMake (10.0, 20.0);
CGPoint center = CGPointMake (60.0, 240.0);
CGRect bounds = CGRectMake (5.0, 10.0, 100.0, 200.0);
```
一些特殊情况

- 零点 `CGPointZero`; 即(x=0.0, y=0.0),等价于 `GPointMake(0,0)`;
- 零大小 `CGSizeZero`; 即(width=0.0, height= 0.0),等价于`CGSizeMake(0,0)`;
- 零矩形 `CGRectZero`; 即((x=0.0,y= 0.0),(width=0.0,height=0.0)),等价于 `CGRectMake(0,0,0,0)`.
- 空矩形 `CGRectNull`; 取两个不相交矩形的相交区域会返回 `CGRectNull`,一个矩形与 `CGRectNull` 的并集为原矩形。,使用 `CGRectIsNull` 判断一个矩形是否为 `CGRectNull`。`CGRectIsEmpty` 对`CGRectZero` 和 `CGRectNull`都返回 true;
- 无限矩形 `CGRectInfinite` 具有无限区域的矩形,可使用 `CGRectIsInfinite` 进行判断。

#### 判断两个点，大小，矩形是否相等

```
bool CGPointEqualToPoint (CGPoint point1, CGPoint point2);
bool CGSizeEqualToSize (CGSize size1, CGSize size2);
bool CGRectEqualToRect (CGRect rect1, CGRect rect2);
```
CGPoint,CGSize,CGRect的内部数据都是 CGPoint,对于浮点数,由于可能存在舍入误差，不能直接判断两者是否相等，而应该判断两个值的差是否足够小。使用上述方法可以避免手工做这些判断,简化代码。

#### 获取矩形边界，中点坐标值，长宽

- 获取边界坐标值，即x,y的最大值与最小值。

```
CGFloat CGRectGetMinX(CGRect rect);
CGFloat CGRectGetMaxX(CGRect rect);
CGFloat CGRectGetMinY(CGRect rect);
CGFloat CGRectGetMaxY(CGRect rect);
```
- 获取中点坐标值,即x,y的中点

```
CGFloat CGRectGetMidX(CGRect rect);
CGFloat CGRectGetMidY(CGRect rect);
```
- 获取矩形长，宽

```
CGFloat CGRectGetWidth(CGRect rect);
CGFloat CGRectGetHeight(CGRect rect);
```

#### 对称缩进 CGRect

```
CGRect aRectangle = CGRectMake(0,0, 100, 200);
CGRect smallerRectangle = CGRectInset(aRectangle, 10, 20);
// result origin (10, 20) and size (80, 160)
```

#### 非对称缩进 CGRect

```
CGRect rect = CGRectMake(0, 0, 100, 200);
UIEdgeInsets contentInsets = UIEdgeInsetsMake(10, 20, 30, 40);
CGRect result = UIEdgeInsetsInsetRect(rect, contentInsets);
//result origin(20,10) and size(40,160)
```

#### 调整小数像素 CGRect 到整数像素
将 origin 值向下调整到最近整数，size 向上调整到最近整数，使生成的 CGRect 可以完全包含原来的 
CGRect.

```
CGRect rect = CGRectMake(5, 7.5, 50, 29);
UIEdgeInsets contentInsets = UIEdgeInsetsMake(10, 20, 30, 40);
CGRect result = CGRectIntegral(rect);
//result origin(5,7) and size(50,30)
```
补充：在 iOS7 之前非视网膜屏会因为小数的像素值导致内容模糊，需要用 `CGRectIntegral` 转换。

#### 检查矩形内是否包含某点

CGRect enemyRect = CGRectMake(0, 0, 100, 200);
CGPoint hitPoint = CGPointMake(50, 50);
if(CGRectContainsPoint(enemyRect, hitPoint))    {
        // YES!
}

#### 检查两矩形是否相交

```
CGRect playerRect = CCRectMake(200, 300, 10, 10);
    CGRect mineRect = CGRectMake(10, 10, 20, 20);
    if(CGRectIntersectsRect(playerRect, mineRect))    {
        // OUCH!
    }
```
    
#### CGRect 与 NSString 转换

```
CGRect rect = CGRectMake(0, 0, 100, 200);
   NSString *rectString = NSStringFromCGRect(rect))
   CGRect newRect = CGRectFromString(rectString);
```

#### CGRect 与 NSDictionary 转换

```
CGRect rect = CGRectMake(0, 0, 100, 200);
CFDictionaryRef frameDictRef = CGRectCreateDictionaryRepresentation(rect);
NSDictionary*frameDict = [NSDictionary dictionaryWithDictionary:(NSDictionary*)frameDictRef]; // autoreleased
CFRelease(frameDictRef);
NSLog(@"%@", frameDict);

CGRect newRect;
CGRectMakeWithDictionaryRepresentation((CFDictionaryRef)frameDict, &rect);
```

#### CGRect 与 NSValue 转换
在 NSArray,NSDictionary 容器中只能存储 Objetive-C 对象，不能直接存储C结构体，所以需要存储矩形时，需要将其转换为 NSValue 值。

```
CGRect rect = CGRectMake(0, 0, 100, 200);
NSValue *value = [NSValue valueWithCGRect:rect];
CGRect newRect = [value rectValue];
```

### 参考:
- [CGGeometry Reference](http://developer.apple.com/library/ios/#documentation/GraphicsImaging/Reference/CGGeometry/Reference/reference.html)
- [CGRect Tricks](http://www.cocoanetics.com/2010/07/cgrect-tricks/)
- [Rectangles, Part 1](http://blog.bignerdranch.com/1087-rectangles-part-1/)
- [Rectangles, Part 2](http://blog.bignerdranch.com/1136-rectangles-part-2/)
- [A potential pitfall of CGRectIntegral](http://markpospesel.wordpress.com/2013/02/27/cgrectintegral/)

###本文出自 [清风徐来，水波不兴](http://www.winddisk.com/2013/01/05/cgrect%e4%bd%bf%e7%94%a8%e6%8a%80%e5%b7%a7/) 的博客，少量修正。