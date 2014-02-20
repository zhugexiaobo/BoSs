---
title: Grand Central Dispatch(GCD)介绍和使用 
layout: post
tags: iOS 
---

## 介绍
Grand Central Dispatch 简称（GCD）是苹果公司开发的技术，以优化的应用程序支持多核心处理器和其他的对称多处理系统的系统。这建立在任务并行执行的线程池模式的基础上的。它首次发布在Mac OS X 10.6 ，iOS 4及以上也可用。

## 设计
GCD的工作原理是：让程序平行排队的特定任务，根据可用的处理资源，安排他们在任何可用的处理器核心上执行任务。
一个任务可以是一个函数(function)或者是一个 block。 GCD的底层依然是用线程实现，不过这样可以让程序员不用关注实现的细节。
GCD 中的 FIFO 队列称为 dispatch queue，它可以保证先进来的任务先得到执行。
dispatch queue分为下面三种：

- **Serial**	     
又称为private dispatch queues，同时只执行一个任务。Serial queue通常用于同步访问特定的资源或数据。当你创建多个Serial queue时，虽然它们各自是同步执行的，但Serial queue与Serial queue之间是并发执行的。
- **Concurrent**	
又称为global dispatch queue，可以并发地执行多个任务，但是执行完成的顺序是随机的。
- **Main dispatch queue**	
它是全局可用的serial queue，它是在应用程序主线程上执行任务的。

## 使用
#### 常用的方法 dispatch_async
为了避免界面在处理耗时的操作时卡死，比如读取网络数据，IO,数据库读写等，我们会在另外一个线程中处理这些操作，然后通知主线程更新界面。
用GCD实现这个流程的操作比前面介绍的NSThread  NSOperation的方法都要简单。代码框架结构如下：

```
dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
    // 耗时的操作
    dispatch_async(dispatch_get_main_queue(), ^{
        // 更新界面
    });
});
```
如果这样还不清晰的话，那我们还是用上两篇博客中的下载图片为例子，代码如下：

```
    dispatch_async(dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0), ^{
        NSURL * url = [NSURL URLWithString:@"http://avatar.csdn.net/2/C/D/1_totogo2010.jpg"];
        NSData * data = [[NSData alloc]initWithContentsOfURL:url];
        UIImage *image = [[UIImage alloc]initWithData:data];
        if (data != nil) {
            dispatch_async(dispatch_get_main_queue(), ^{
                self.imageView.image = image;
             });
        }
    });
```
是不是代码比NSThread  NSOperation简洁很多，而且GCD会自动根据任务在多核处理器上分配资源，优化程序。
系统给每一个应用程序提供了三个concurrent dispatch queues。这三个并发调度队列是全局的，它们只有优先级的不同。因为是全局的，我们不需要去创建。我们只需要通过使用函数dispath_get_global_queue去得到队列，如下：

```
dispatch_queue_t globalQ = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0); 
```

#### dispatch_group_async的使用
dispatch_group_async可以实现监听一组任务是否完成，完成后得到通知执行其他的操作。这个方法很有用，比如你执行三个下载任务，当三个任务都下载完成后你才通知界面说完成的了。下面是一段例子代码：

```
    dispatch_queue_t queue = dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0);
    dispatch_group_t group = dispatch_group_create();
    dispatch_group_async(group, queue, ^{
        [NSThread sleepForTimeInterval:1];
        NSLog(@"group1");
    });
    dispatch_group_async(group, queue, ^{
        [NSThread sleepForTimeInterval:2];
        NSLog(@"group2");
    });
    dispatch_group_async(group, queue, ^{
        [NSThread sleepForTimeInterval:3];
        NSLog(@"group3");
    });
    dispatch_group_notify(group, dispatch_get_main_queue(), ^{
        NSLog(@"updateUi");
    });
    dispatch_release(group);
```
dispatch_group_async是异步的方法，运行后可以看到打印结果：

```
2012-09-25 16:04:16.737 gcdTest[43328:11303] group1
2012-09-25 16:04:17.738 gcdTest[43328:12a1b] group2
2012-09-25 16:04:18.738 gcdTest[43328:13003] group3
2012-09-25 16:04:18.739 gcdTest[43328:f803] updateUi
```
每个一秒打印一个，当第三个任务执行后，upadteUi被打印。

#### dispatch_barrier_async的使用

dispatch_barrier_async是在前面的任务执行结束后它才执行，而且它后面的任务等它执行完成之后才会执行。
例子代码如下：

```
    dispatch_queue_t queue = dispatch_queue_create("gcdtest.rongfzh.yc", DISPATCH_QUEUE_CONCURRENT);
    dispatch_async(queue, ^{
        [NSThread sleepForTimeInterval:2];
        NSLog(@"dispatch_async1");
    });
    dispatch_async(queue, ^{
        [NSThread sleepForTimeInterval:4];
        NSLog(@"dispatch_async2");
    });
    dispatch_barrier_async(queue, ^{
        NSLog(@"dispatch_barrier_async");
        [NSThread sleepForTimeInterval:4];

    });
    dispatch_async(queue, ^{
        [NSThread sleepForTimeInterval:1];
        NSLog(@"dispatch_async3");
    });
```
打印结果：

```
2012-09-25 16:20:33.967 gcdTest[45547:11203] dispatch_async1
2012-09-25 16:20:35.967 gcdTest[45547:11303] dispatch_async2
2012-09-25 16:20:35.967 gcdTest[45547:11303] dispatch_barrier_async
2012-09-25 16:20:40.970 gcdTest[45547:11303] dispatch_async3
```
请注意执行的时间，可以看到执行的顺序如上所述。

#### dispatch_apply 
执行某个代码片段N次。

```
dispatch_apply(5, globalQ, ^(size_t index) {
    // 执行5次
});
```
本篇使用的到的例子代码：[link](http://download.csdn.net/detail/totogo2010/4596471)
GCD还有很多其他用法，可以参考[官方文档](http://developer.apple.com/library/ios/#documentation/Performance/Reference/GCD_libdispatch_Ref/Reference/reference.html#//apple_ref/doc/uid/TP40008079)
参考的文档还有：[link](http://en.wikipedia.org/wiki/Grand_Central_Dispatch)

> 转载自[容芳志的博客](http://blog.csdn.net/totogo2010/article/details/8016129#)