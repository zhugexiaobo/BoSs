---
title: 自定义对象的存取方法
layout: post
tags: iOS 
---

有时我们需要将一些数据保存到本地，记录当前的一些状态或者数据信息。NSArray, NSData, NSDictionary, NSMutableDictionary, NSString, NSURL 有相应的读写方法。但如果是自定义的类对象怎么做呢？

系统提供了实现存储自定义对象的方式，通过在自定义类中实现 `NSCoding` 代理方法，使用时通过 `NSKeyedArchiver` 类方法可将自定义类对象转化成 NSData，然后写入文件或 `NSUserDefault`。

### 自定义类实现 `NSCoding` 代理方法
```
// .h
@interface CustomObject : NSObject <NSCoding>

@property (readonly)NSInteger *aValue;
@property (readonly)BOOL bValue;;

@end

// .m
@implementation CustomObject

- (id)initWith......{
     if((self = [super init])){
          // init:
     }
     return self;
}

#pragma NSCoding delegate
- (void)encodeWithCoder:(NSCoder *)aCode
{
     [aCoder encodeInt:aValue forKey:@"avalue"];
     [aCoder encodeBool:bValue forKey:@"bvalue"];
}
- (id)initWithCoder:(NSCoder *)aDecoder{
     if (self = [super init]){
          aValue = [aDecoder decodeIntForKey:@"avalue"];
          bValue = [aDecoder decodeBoolForKey:@"bvalue"];
     }
     return self;
}
@end
```

### 对自定义对象进行存取

- 写入：
    
```
// 实例化自定义对象
CustomObject *aObject = [[CustomObject alloc] initWith......];
// 对数据进行编码，转成NSData数据
NSData* aData = [NSKeyedArchiver archivedDataWithRootObject:aObject];
// 存储到 `NSUserDefaults`
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
[defaults setObject:aData forKey:@"adata"];
[defaults synchronize];
```
如果有很多个同类型的自定义对象，也可以直接将它们存入 NSArray，然后将 NSArray 转换成 NSData，这样取数据时只需转换一次。

- 读取：

```
// 取出 NSData
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
NSData *aData = [defaults objectForKey:@"adata"];
// 解码
CustomObject* aObject = [NSKeyedUnarchiver unarchiveObjectWithData:aData];
```

### 总结
直接保存程序中的对象信息，更加方便我们进行数据存取。如解析完一个 JSON 数据，直接用该方法存储，就能简单高效的实现缓存了。