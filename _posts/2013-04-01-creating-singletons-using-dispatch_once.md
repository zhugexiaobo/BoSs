---
title: 利用 dispatch_once 创建单例
layout: post
tags: iOS 
---

有时候我们难免会需要创建个单例，实际上每个 iOS 或 Mac OS 应用都至少会有一个单例，如 `UIApplication` 或 `NSApplication`。

## 单例定义
- Wikipedia
> In software engineering, the singleton pattern is a design pattern used to implement the mathematical concept of a singleton, by restricting the instantiation of a class to one object.

- 我的理解
> 单例是一种类，该类只能实例化一个对象。

- 尽管这是单例的实际定义，但在 Foundation 框架中不一定是这样。比如 `NSFileManger` 和 `NSNotificationCenter`，分别通过它们的类方法 `defaultManager` 和 `defaultCenter` 获取。尽管不是严格意义的单例，这些类方法返回一个可以在应用的所有代码中访问到的类的共享实例。在本文中我们也会采用该方法。

## Objective-C 实现
- `dispatch_once`。 使用 Objective-C 实现单例模式的最佳方式向来有很多争论，开发者（包括 Apple 在内）似乎每几年就会改变他们的想法。当 Apple 引入了 Grand Central Dispatch (GCD)（Mac OS 10.6 和 iOS4.0），他们也引入了一个很适合用于实现单例模式的函数。就是 `dispatch_once`:

```
void dispatch_once( dispatch_once_t *predicate, dispatch_block_t block);
```
该函数接收一个 `dispatch_once` 用于检查该代码块是否已经被调度的谓词（是一个长整型，实际上作为BOOL使用）。它还接收一个希望在应用的生命周期内仅被调度一次的代码块，对于本例就用于 shared 实例的实例化。`dispatch_once`不仅意味着代码仅会被运行一次，而且还是线程安全的，这就意味着你不需要使用诸如`@synchronized`之类的来防止使用多个线程或者队列时不同步的问题。

Apple 的 [GCD Documentation](http://developer.apple.com/library/mac/#documentation/Performance/Reference/GCD_libdispatch_Ref/Reference/reference.html) 证实了这点：
> If called simultaneously from multiple threads, this function waits synchronously until the block has completed.

- 实际使用。假设有一个 AccountManager 类，你想在整个应用中访问该类的共享实例。你可以按如下代码简单实现一个类方法：

```
+ (AccountManager *)sharedManager {
    static AccountManager *sharedAccountManagerInstance = nil;
    static dispatch_once_t predicate; dispatch_once(&predicate, ^{
          sharedAccountManagerInstance = [[self alloc] init];
    });
    return sharedAccountManagerInstance;
}

// 这就意味着你任何时候访问共享实例，需要做的仅是：

AccountManager *accountManager = [AccountManager sharedManager];
```

就这些，你现在在应用中就有一个共享的实例，该实例只会被创建一次。
该方法有很多优势:

1. 线程安全
2. 很好满足静态分析器要求
3. 和自动引用计数（ARC）兼容
4. 仅需要少量代码

该方法的劣势就是它仍然运行创建一个非共享的实例：

```
AccountManager *accountManager = [[AccountManager alloc] init];
```

有些时候你希望有这种行为，但如果正在想要的是仅一个实例被实例化就需要注意这点。


## [原文](http://objcolumnist.com/2011/07/06/creating-singletons-using-dispatch_once/)简单摘录