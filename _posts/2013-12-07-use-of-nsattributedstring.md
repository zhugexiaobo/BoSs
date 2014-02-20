---
title: NSAttributedString 使用 
layout: post
tags: iOS 
---

> An `NSAttributedString` object manages character strings and associated sets of attributes (for example, font and kerning) that apply to individual characters or ranges of characters in the string.

NSAttributedString 维护了一个 NSString，用来保存最原始的字符串，另有一个 NSDictionary 用来保存各个子串/字符的属性。

- 初始化一段字符串

```
NSString *infoString = @"This is an example of Attributed String";
NSMutableAttributedString *attString = [[NSMutableAttributedString alloc] initWithString:infoString];
NSInteger _stringLength = [infoString length];
```
- 普通的颜色和字体设置

```
UIColor *_black = [UIColor blackColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:30.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSForegroundColorAttributeName value:_black range:NSMakeRange(0, _stringLength)];
```
- 字体描边，`NSStrokeWidthAttributeName` 值为正时只改变描边宽度

```
UIColor *_red = [UIColor redColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:72.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeColorAttributeName value:_red range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeWidthAttributeName value:[NSNumber numberWithFloat:3.0] range:NSMakeRange(0, _stringLength)];
```
- 描边，`NSStrokeWidthAttributeName` 为负同时改变文字的描边和填充宽度

```
UIColor *_red = [UIColor redColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:72.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeColorAttributeName value:_red range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeWidthAttributeName value:[NSNumber numberWithFloat:-3.0] range:NSMakeRange(0, _stringLength)];
```
- 描边并设置字体颜色

```
UIColor *_red = [UIColor redColor];
UIColor *_green = [UIColor greenColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:72.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSForegroundColorAttributeName value:_green range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeColorAttributeName value:_red range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSStrokeWidthAttributeName value:[NSNumber numberWithFloat:-3.0] range:NSMakeRange(0, _stringLength)];
```
- 阴影

```
UIColor *_green = [UIColor greenColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:72.0f];

NSShadow *shadowDic = [[NSShadow alloc] init];
[shadowDic setShadowBlurRadius:5.0];
[shadowDic setShadowColor:[UIColor grayColor]];
[shadowDic setShadowOffset:CGSizeMake(0, 3)];
              
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSForegroundColorAttributeName value:_green range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSShadowAttributeName value:shadowDic range:NSMakeRange(0, _stringLength)];
```
- 字间距

```
UIColor *_red = [UIColor redColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:72.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
  [attString addAttribute:NSForegroundColorAttributeName value:_red range:NSMakeRange(0, _stringLength)];              
  [attString addAttribute:NSKernAttributeName value:[NSNumber numberWithInt:5] range:NSMakeRange(0, _stringLength)];
```
- 下划线

```
UIColor *_red = [UIColor redColor];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:30.0f];
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSForegroundColorAttributeName value:_red range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSUnderlineStyleAttributeName value:[NSNumber numberWithInt:2] range:NSMakeRange(0, _stringLength)];
```
- 背景色

```
UIColor *_blue = [UIColor blueColor];
UIColor *_blueL = [UIColor colorWithRed:0 green:0 blue:0.5 alpha:0.7];
UIFont *font = [UIFont fontWithName:@"Helvetica-Bold" size:30.0f];
                
[attString addAttribute:NSFontAttributeName value:font range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSForegroundColorAttributeName value:_blue range:NSMakeRange(0, _stringLength)];
[attString addAttribute:NSBackgroundColorAttributeName value:_blueL range:NSMakeRange(0, 20)];
```