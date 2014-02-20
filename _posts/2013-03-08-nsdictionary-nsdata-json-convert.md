---
title: NSDictionary,NSData,JSON 互相转换
layout: post
tags: iOS 
---

- Dictionary -> NSData

```
NSDictionary *params = [NSDictionary dictionaryWithObjectsAndKeys:
                                @"balance", @"key",
                               @"remaining balance", @"label",
                                @"45", @"value",
                                @"USD", @"currencyCode",nil];
NSData *data = [NSKeyedArchiver archivedDataWithRootObject:params];
```

- NSData -> NSDictionary

```
NSData *data = [[NSMutableData alloc] initWithContentsOfFile:[self dataFilePath]];
NSDictionary *myDictionary = (NSDictionary*)[NSKeyedUnarchiver unarchiveObjectWithData:data];
```

- NSDictionary -> JSON

```
NSData *jsonData = [NSJSONSerialization dataWithJSONObject:params options:NSJSONWritingPrettyPrinted error:nil];
```
