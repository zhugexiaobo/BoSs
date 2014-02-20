---
title: 关于枚举的使用
layout: post
tags: iOS 
---

枚举值 它是一个整形(int) ，不参与内存的占用和释放，枚举定义变量即可直接使用，不用初始化。
在代码中使用枚举的目的只有一个，那就是增加代码的可读性。

- 常规

```
typedef NS_ENUM(NSInteger, Test1)
{
    Test1A = 0,
    Test1B = 1,
    Test1C = 2,
    Test1D = 3
};
```
- 支持位运算的方式定义:

```
typedef NS_ENUM(NSInteger, Test)
{
    TestA       = 1,      //1   1   1
    TestB       = 1 << 1, //2   2   10      转换成 10进制  2
    TestC       = 1 << 2, //4   3   100     转换成 10进制  4
    TestD       = 1 << 3, //8   4   1000    转换成 10进制  8
    TestE       = 1 << 4  //16  5   10000   转换成 10进制  16
};
```
在一个枚举变量可能要代表多个枚举值的时候用位运算定义。原因是给一个枚举变量赋予多个枚举值的时候，原理只是把各个枚举值加起来。当加起来以后，位运算能保证新值的唯一性。因为位运算的计算方式是将二进制转换成十进制，也就是说，枚举值里面存取的是计算后的十进制值。

- 用 `unit` 能快速初始化一个位运算枚举:

```
typedef NS_ENUM(uint, Test)
{
    TestA,
    TestB,
    TestC,
    TestD,
    TestE  
};
```
- 多枚举值赋值方式:

```
Test tes = TestA|TestB;
```
- 可以随时为枚举变量累加某个值:

```
tes |=TestC;
```
要自己控制不添加已经加入过的枚举值, 枚举变量的值不会有变动,但可能会误导阅读代码的人。

- 累减，如果累减不存在的枚举值，那么本次累减的枚举值，会自动累加上去：

```
tes^= TestE;
```

- 判断枚举变量是否包含某个固定的枚举值,使用前需要确保枚举值以及各个组合的唯一性: 

```
NSLog(@"%d %d %d %d %d",TestA,TestB,TestC,TestD,TestE);
Test tes = (TestA|TestB);
NSLog(@"%d",tes);
NSLog(@"%d",(tes & TestA));

if ((tes & TestA)) {
    NSLog(@"有");
}else
{
    NSLog(@"没有");
}
NSLog(@"%d",(tes & TestB));
if ((tes & TestA)) {
    NSLog(@"有");
}else
{
    NSLog(@"没有");
}

NSLog(@"%d",(tes & TestC));
if ((tes & TestC)) {
    NSLog(@"有");
}else
{
    NSLog(@"没有");
}
```

转载自[这里](http://blog.csdn.net/ysy441088327/article/details/8012677)，有少量修改。