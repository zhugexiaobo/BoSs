---
title: URL 加/解码
layout: post
tags: iOS 
---


```
NSString* string2 = [string1 stringByAddingPercentEscapesUsingEncoding:NSUTF8StringEncoding]; // encode

NSString* string1 = [string2 stringByReplacingPercentEscapesUsingEncoding:NSUTF8StringEncoding]; // decode
```


### 转换不完全，不会处理保留字符，使用以下方法，坑

```
resultString = (NSString *)CFBridgingRelease(CFURLCreateStringByAddingPercentEscapes(kCFAllocatorDefault, (CFStringRef)encodeString, NULL, (CFStringRef)@"!*'();:@&=+$,/?%#[]", kCFStringEncodingUTF8))
```