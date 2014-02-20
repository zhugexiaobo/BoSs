---
title: UIGestureRecognizer 学习笔记
layout: post
tags: iOS 
---

> 非常详细的 `UIGestureRecognizer` 使用示例，转自[CSDN 博客](http://blog.csdn.net/namehzf/article/details/7424882#)，做备忘。

`UIGestureRecognizer` 是一个具体手势的基类，提供了较为简单的手势实现方式  
The concrete subclasses of UIGestureRecognizer are the following:

- [UITapGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UITapGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UITapGestureRecognizer)
- [UIPinchGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPinchGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UIPinchGestureRecognizer)
- [UIRotationGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIRotateGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UIRotationGestureRecognizer)
- [UISwipeGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UISwipeGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UISwipeGestureRecognizer)
- [UIPanGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UIPanGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UIPanGestureRecognizer)
- [UILongPressGestureRecognizer](https://developer.apple.com/library/ios/documentation/UIKit/Reference/UILongPressGestureRecognizer_Class/Reference/Reference.html#//apple_ref/occ/cl/UILongPressGestureRecognizer)

一个 gesture recognizer 是针对一个特定的 view 的（包含其 subview），用 UIView 的方法 `addGestureRecognize：` 去关联一个 view。一个gesture recognizer是不参与UIView的事件响应链的


各个手势使用时的代码：
#### UITapGestureRecognizer

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    UITapGestureRecognizer *tapGesture = [[UITapGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![tapGesture respondsToSelector:@selector(locationInView:)]) {
        [tapGesture release];
        tapGesture = nil;
    }else {
        tapGesture.delegate = self;
        tapGesture.numberOfTapsRequired = 1; // The default value is 1.
        tapGesture.numberOfTouchesRequired = 1; // The default value is 1.
        [self.view addGestureRecognizer:tapGesture];
    }
}

- (void)handleGesture:(UIGestureRecognizer *)gestureRecognizer
{
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view
    
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded // 正常情况下只响应这个消息
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            break;
        }
        case UIGestureRecognizerStateFailed:{ // 
            NSLog(@"======UIGestureRecognizerStateFailed");
            break;
        }
        case UIGestureRecognizerStatePossible:{ // 
            NSLog(@"======UIGestureRecognizerStatePossible");
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}

// 询问一个手势接收者是否应该开始解释执行一个触摸接收事件
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer{
//    CGPoint currentPoint = [gestureRecognizer locationInView:self.view];
//    if (CGRectContainsPoint(CGRectMake(0, 0, 100, 100), currentPoint) ) {
//        return YES;
//    }
//    
//    return NO;
    
    return YES;
}

// 询问delegate，两个手势是否同时接收消息，返回YES同事接收。返回NO，不同是接收（如果另外一个手势返回YES，则并不能保证不同时接收消息）the default implementation returns NO。
// 这个函数一般在一个手势接收者要阻止另外一个手势接收自己的消息的时候调用
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer{
    return NO;
}

// 询问delegate是否允许手势接收者接收一个touch对象
// 返回YES，则允许对这个touch对象审核，NO，则不允许。
// 这个方法在touchesBegan:withEvent:之前调用，为一个新的touch对象进行调用
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch{
    return YES;
}

```

#### UIPinchGestureRecognizer

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    UIPinchGestureRecognizer *pinchGesture = [[UIPinchGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![pinchGesture respondsToSelector:@selector(locationInView:)]) {
        [pinchGesture release];
        pinchGesture = nil;
    }else {
        pinchGesture.delegate = self;
        [self.view addGestureRecognizer: pinchGesture];
    }  
}


- (void)handleGesture:(UIPinchGestureRecognizer *)gestureRecognizer
{
    
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view
    
    
    /*
     scale属性： 可以理解为两手指之间的距离，其实是个比例，相对距离，不是绝对距离
                以刚开始的两个手指对应的两个point的之间的距离为标准，此时scale=1.
                若两手指之间距离减小，则scale不断变小，当两指重合，则变为0
                若两手指之间距离变大，则scale不断增大，没有上限，看屏幕多大
     
     velocity属性： 可以理解为两手指之间的移动速度，其实是个速度比例，相对速度，不是绝对速度
                以刚开始的两个手指对应的两个point的之间的距离为标准，此时velocity=0.
                若两手指之间距离减小，则velocity为负数，从-0开始，随着手指向里捏合的速度越快，负值越大，没有上限，我测试了下，可以到-20.009099，甚至更大
                若两手指之间距离变大，则velocity不断正数，没有上限，从0开始，随着手指向外捏合的速度越快，值越大，没有上限，我测试了下，可以到170.234663，甚至更大
                注意：在这个过程中，出现了nan值。 
                     有关NAN值的介绍如下：（http://www.cnblogs.com/konlil/archive/2011/07/06/2099646.html）
     
     浮点异常值：NAN，QNAN，SNAN
     
     32位浮点数在机器中的表示按照IEEE的标准是这样的：
     +------+----------------+-------------------------------+   
     | 1bit    |   8bit               |         23bit                            |   
     +------+----------------+-------------------------------+
     其中：1bit表示符号位（0表示正，1表示负），8bit表示指数（0~255，实际指数取值还要减去127，即指数取值区间为-127~128）,23bit表示尾数。
     这里所要说的浮点异常值就是这种表示产生的几种特殊值，IEEE规定根据指数和尾数的不同分别可表示如下几种特殊值：
     1. 零值：按上述的浮点表述形式如果指数部分全部为0，并且尾数全部为0，则表示为浮点0.0，并且规定-0 = +0
     2. 非规格化值：如果指数全部为0，尾数非0，则表示非规格化的值，16进制看到的就是[80xxxxxx]h或者[00xxxxxx]h
     3. 无穷值：如果指数全部为1，尾数全部为0，则根据符号位分别表示正无穷大和负无穷大，16进制看到的就是[FF800000]h或者[7F800000]h
     4. NAN：主角来了，如果指数全部为1，尾数非0，则表示这个值不是一个真正的值（Not A Number）。NAN又分成两类：QNAN（Quiet NAN）和SNAN（Singaling NAN）。QNAN与SNAN的不同之处在于，QNAN的尾数部分最高位定义为1，SNAN最高位定义为0；QNAN一般表示未定义的算术运算结果，最常见的莫过于除0运算；SNAN一般被用于标记未初始化的值，以此来捕获异常。
     那么既然NAN不是一个真实的数值，在程序如何判断变量是否变成了NAN呢？大部分语言中针对NAN值都有一系列的函数定义，C语言中最常见的三个函数：
     _isnan(double x);                  //判断是否为NAN
     _finite(double x);                  //判读是否为无穷大
     _fpclass(double x);                //返回一系列的定义值，如：_FPCLASS_QNAN, _FPCLASS_SNAN，具体参考MSDN

     */
    CGFloat scale = gestureRecognizer.scale;
    NSLog(@"======scale: %f", scale);
    
    CGFloat velocity = gestureRecognizer.velocity;
    NSLog(@"======scvelocityale: %f", velocity);
    
    /*
     捏合手势
     
     这个一般情况下只响应
     UIGestureRecognizerStateBegan、
     UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized、
     UIGestureRecognizerStateChanged消息，
     一个UIGestureRecognizerStateBegan，接下去是N多的UIGestureRecognizerStateChanged，scale的值此时会不断的变化，当手指离开时，响应UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized
     */
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            break;
        }
        case UIGestureRecognizerStateBegan:{ // 
            NSLog(@"======UIGestureRecognizerStateBegan");
            break;
        }
        case UIGestureRecognizerStateChanged:{ // 
            NSLog(@"======UIGestureRecognizerStateChanged");
            
            gestureRecognizer.view.transform = CGAffineTransformScale(gestureRecognizer.view.transform, gestureRecognizer.scale, gestureRecognizer.scale);
            gestureRecognizer.scale = 1; // 重置，很重要！！！
            
            break;
        }
        case UIGestureRecognizerStateCancelled:{ // 
            NSLog(@"======UIGestureRecognizerStateCancelled");
            break;
        }
        case UIGestureRecognizerStateFailed:{ // 
            NSLog(@"======UIGestureRecognizerStateFailed");
            break;
        }
        case UIGestureRecognizerStatePossible:{ // 
            NSLog(@"======UIGestureRecognizerStatePossible");
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}
```

#### UIRotationGestureRecognizer
```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    UIRotationGestureRecognizer *rotationGesture = [[UIRotationGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![rotationGesture respondsToSelector:@selector(locationInView:)]) {
        [rotationGesture release];
        rotationGesture = nil;
    }else {
        rotationGesture.delegate = self;
        
        [self.view addGestureRecognizer:rotationGesture];
    }
}


- (void)handleGesture:(UIRotationGestureRecognizer *)gestureRecognizer
{
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view
    
    
    /*
     rotation属性： 可以理解为两手指之间的旋转的角度，其实是个比例，相对角度，不是绝对角度
     以刚开始的两个手指对应的两个point的之间的那条直线为标准，此时rotation=1.
     向顺时针旋转，则rotation为正数且不断变大，当旋转360度时，rotation大概为6左右，如果继续顺时针旋转，则角度会不断增加，两圈为12左右，此时若逆时针旋转，角度则不断变小
     向逆时针旋转，则rotation为负数且不断变小，当旋转360度时，rotation大概为-6左右
     
     velocity属性： 可以理解为两手指之间的移动速度，其实是个速度比例，相对速度，不是绝对速度
     以刚开始的两个手指对应的两个point的之间的距离为标准，此时velocity=0.
     若两手指向顺时针旋转，则velocity为正数，从0开始，随着手指向里捏合的速度越快，值越大，没有上限
     若两手指向逆时针旋转，则velocity为负数数，没有上限，从-0开始，随着手指向外捏合的速度越快，值越小，没有上限
     */
    CGFloat rotation = gestureRecognizer.rotation;
    NSLog(@"===rotation: %f", rotation);
    
    CGFloat velocity = gestureRecognizer.velocity;
    NSLog(@"======velocity: %f", velocity);
    
    
    
    /*
     旋转手势
     
     这个一般情况下只响应
     UIGestureRecognizerStateBegan、
     UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized、
     UIGestureRecognizerStateChanged消息，
     一个UIGestureRecognizerStateBegan，接下去是N多的UIGestureRecognizerStateChanged，scale的值此时会不断的变化，当手指离开时，响应UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized
     */
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            break;
        }
        case UIGestureRecognizerStateBegan:{ // 
            NSLog(@"======UIGestureRecognizerStateBegan");
            break;
        }
        case UIGestureRecognizerStateChanged:{ // 
            NSLog(@"======UIGestureRecognizerStateChanged");
            
            gestureRecognizer.view.transform = CGAffineTransformRotate(gestureRecognizer.view.transform, gestureRecognizer.rotation);
            gestureRecognizer.rotation = 0; // 重置 这个相当重要！！！ 
            
            break;
        }
        case UIGestureRecognizerStateCancelled:{ // 
            NSLog(@"======UIGestureRecognizerStateCancelled");
            break;
        }
        case UIGestureRecognizerStateFailed:{ // 
            NSLog(@"======UIGestureRecognizerStateFailed");
            break;
        }
        case UIGestureRecognizerStatePossible:{ // 
            NSLog(@"======UIGestureRecognizerStatePossible");
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}
```


#### UISwipeGestureRecognizer

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    /*
     同一个手势只能指定一个方向，不能同时指定多个方向，要指定多个方向 必须用多个手势
     */
    
    // right
    UISwipeGestureRecognizer *swipeGestureRight = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![swipeGestureRight respondsToSelector:@selector(locationInView:)]) {
        [swipeGestureRight release];
        swipeGestureRight = nil;
    }else {
        swipeGestureRight.delegate = self;
        swipeGestureRight.numberOfTouchesRequired = 1;// 手指个数 The default value is 1.
        swipeGestureRight.direction = UISwipeGestureRecognizerDirectionRight;// 同一个手势只能指定一个方向，不能同时指定多个方向，要指定多个方向 必须用多个手势
        [self.view addGestureRecognizer:swipeGestureRight];
    }
    
    // left
    UISwipeGestureRecognizer *swipeGestureLeft = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![swipeGestureLeft respondsToSelector:@selector(locationInView:)]) {
        [swipeGestureLeft release];
        swipeGestureLeft = nil;
    }else {
        swipeGestureLeft.delegate = self;
        swipeGestureLeft.numberOfTouchesRequired = 1;// 手指个数 The default value is 1.
        swipeGestureLeft.direction = UISwipeGestureRecognizerDirectionLeft;// 同一个手势只能指定一个方向，不能同时指定多个方向，要指定多个方向 必须用多个手势
        [self.view addGestureRecognizer:swipeGestureLeft];
    }
    
    // Up
    UISwipeGestureRecognizer *swipeGestureUp = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![swipeGestureUp respondsToSelector:@selector(locationInView:)]) {
        [swipeGestureUp release];
        swipeGestureUp = nil;
    }else {
        swipeGestureUp.delegate = self;
        swipeGestureUp.numberOfTouchesRequired = 1;// 手指个数 The default value is 1.
        swipeGestureUp.direction = UISwipeGestureRecognizerDirectionUp;// 同一个手势只能指定一个方向，不能同时指定多个方向，要指定多个方向 必须用多个手势
        [self.view addGestureRecognizer:swipeGestureUp];
    }
    
    // Down
    UISwipeGestureRecognizer *swipeGestureDown = [[UISwipeGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![swipeGestureDown respondsToSelector:@selector(locationInView:)]) {
        [swipeGestureDown release];
        swipeGestureDown = nil;
    }else {
        swipeGestureDown.delegate = self;
        swipeGestureDown.numberOfTouchesRequired = 1;// 手指个数 The default value is 1.
        swipeGestureDown.direction = UISwipeGestureRecognizerDirectionDown;// 同一个手势只能指定一个方向，不能同时指定多个方向，要指定多个方向 必须用多个手势
        [self.view addGestureRecognizer:swipeGestureDown];
    }
}

- (void)handleGesture:(UISwipeGestureRecognizer *)gestureRecognizer
{
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view
    
    /*
     direction属性： 用来指明手势滑动的方向的。
     */
    UISwipeGestureRecognizerDirection direction = gestureRecognizer.direction;
    switch (direction) {
        case UISwipeGestureRecognizerDirectionRight:
        {
            NSLog(@"direction==UISwipeGestureRecognizerDirectionRight");
            break;
        }
        case UISwipeGestureRecognizerDirectionLeft:
        {
            NSLog(@"direction==UISwipeGestureRecognizerDirectionLeft");
            break;
        }
        case UISwipeGestureRecognizerDirectionUp:
        {
            NSLog(@"direction==UISwipeGestureRecognizerDirectionUp");
            break;
        }
        case UISwipeGestureRecognizerDirectionDown:
        {
            NSLog(@"direction==UISwipeGestureRecognizerDirectionDown");
            break;
        }
        default:
            break;
    }
    
    
    /*
     轻扫手势
     
     这个一般情况下只响应UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized
     */
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}
```


#### UIPanGestureRecognizer

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    
    UIView *view = [[UIView alloc]initWithFrame:CGRectMake(0, 0, 100, 100)];
    view.backgroundColor = [UIColor blueColor];
    [self.view addSubview:view];
    
    
    UIPanGestureRecognizer *panPressGesture = [[UIPanGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![panPressGesture respondsToSelector:@selector(locationInView:)]) {
        [panPressGesture release];
        panPressGesture = nil;
    }else {
        panPressGesture.delegate = self;
        panPressGesture.maximumNumberOfTouches = NSUIntegerMax;// The default value is NSUIntegerMax. 
        /*
         NSUIntegerMax : The maximum value for an NSUInteger.
         */
        panPressGesture.minimumNumberOfTouches = 1;// The default value is 1.
        [view addGestureRecognizer:panPressGesture];
    }
}

// 拖拽手势
- (void)handleGesture:(UIPanGestureRecognizer *)gestureRecognizer
{
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view

    
    
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateBegan:{ 
            NSLog(@"======UIGestureRecognizerStateBegan");
            break;
        }
        case UIGestureRecognizerStateChanged:{ 
            NSLog(@"======UIGestureRecognizerStateChanged");
            
            /*
             让view跟着手指移动
             
             1.获取每次系统捕获到的手指移动的偏移量translation
             2.根据偏移量translation算出当前view应该出现的位置
             3.设置view的新frame
             4.将translation重置为0（十分重要。否则translation每次都会叠加，很快你的view就会移除屏幕！）
             */
            
            CGPoint translation = [gestureRecognizer translationInView:self.view];
            view.center = CGPointMake(gestureRecognizer.view.center.x + translation.x, gestureRecognizer.view.center.y + translation.y);
            [gestureRecognizer setTranslation:CGPointMake(0, 0) inView:self.view];//  注意一旦你完成上述的移动，将translation重置为0十分重要。否则translation每次都会叠加，很快你的view就会移除屏幕！
            break;
        }
        case UIGestureRecognizerStateCancelled:{ 
            NSLog(@"======UIGestureRecognizerStateCancelled");
            break;
        }
        case UIGestureRecognizerStateFailed:{ 
            NSLog(@"======UIGestureRecognizerStateFailed");
            break;
        }
        case UIGestureRecognizerStatePossible:{ 
            NSLog(@"======UIGestureRecognizerStatePossible");
            break;
        }
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
            
            /*
             当手势结束后，view的减速缓冲效果
             
             模拟减速写的一个很简单的方法。它遵循如下策略：
             计算速度向量的长度（i.e. magnitude）
             如果长度小于200，则减少基本速度，否则增加它。
             基于速度和滑动因子计算终点
             确定终点在视图边界内
             让视图使用动画到达最终的静止点
             使用“Ease out“动画参数，使运动速度随着时间降低
             */
            
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            
            CGPoint velocity = [gestureRecognizer velocityInView:self.view];// 分别得出x，y轴方向的速度向量长度（velocity代表按照当前速度，每秒可移动的像素个数，分xy轴两个方向）
            CGFloat magnitude = sqrtf((velocity.x * velocity.x) + (velocity.y * velocity.y));// 根据直角三角形的算法算出综合速度向量长度
            
            // 如果长度小于200，则减少基本速度，否则增加它。
            CGFloat slideMult = magnitude / 200;
            
            NSLog(@"magnitude: %f, slideMult: %f", magnitude, slideMult);
            float slideFactor = 0.1 * slideMult; // Increase for more of a slide
            
            // 基于速度和滑动因子计算终点
            CGPoint finalPoint = CGPointMake(view.center.x + (velocity.x * slideFactor),
                                             view.center.y + (velocity.y * slideFactor));
            
            // 确定终点在视图边界内
            finalPoint.x = MIN(MAX(finalPoint.x, 0), self.view.bounds.size.width);
            finalPoint.y = MIN(MAX(finalPoint.y, 0), self.view.bounds.size.height);
            
            [UIView animateWithDuration:slideFactor*2 delay:0 options:UIViewAnimationOptionCurveEaseOut animations:^{
                view.center = finalPoint;  
            } completion:nil];
            
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}
```


#### UILongPressGestureRecognizer

```
- (void)viewDidLoad
{
    [super viewDidLoad];
    // Do any additional setup after loading the view from its nib.
    UILongPressGestureRecognizer *longPressGesture = [[UILongPressGestureRecognizer alloc] initWithTarget:self action:@selector(handleGesture:)];
    if (![longPressGesture respondsToSelector:@selector(locationInView:)]) {
        [longPressGesture release];
        longPressGesture = nil;
    }else {
        longPressGesture.delegate = self;
        longPressGesture.numberOfTapsRequired = 0;      // The default number of taps is 0.
        longPressGesture.minimumPressDuration = 0.1f;    // The default duration is is 0.5 seconds.
        longPressGesture.numberOfTouchesRequired = 1;   // The default number of fingers is 1.
        longPressGesture.allowableMovement = 10;        // The default distance is 10 pixels.
        [self.view addGestureRecognizer:longPressGesture];
    }
}


- (void)handleGesture:(UIGestureRecognizer *)gestureRecognizer
{
    UIView *view = [gestureRecognizer view]; // 这个view是手势所属的view，也就是增加手势的那个view
    
    switch (gestureRecognizer.state) {
        case UIGestureRecognizerStateEnded:{ // UIGestureRecognizerStateRecognized = UIGestureRecognizerStateEnded
            NSLog(@"======UIGestureRecognizerStateEnded || UIGestureRecognizerStateRecognized");
            break;
        }
        case UIGestureRecognizerStateBegan:{ // 
            NSLog(@"======UIGestureRecognizerStateBegan");
            break;
        }
        case UIGestureRecognizerStateChanged:{ // 
            NSLog(@"======UIGestureRecognizerStateChanged");
            break;
        }
        case UIGestureRecognizerStateCancelled:{ // 
            NSLog(@"======UIGestureRecognizerStateCancelled");
            break;
        }
        case UIGestureRecognizerStateFailed:{ // 
            NSLog(@"======UIGestureRecognizerStateFailed");
            break;
        }
        case UIGestureRecognizerStatePossible:{ // 
            NSLog(@"======UIGestureRecognizerStatePossible");
            break;
        }
        default:{
            NSLog(@"======Unknow gestureRecognizer");
            break;
        }
    }  
}

// 询问一个手势接收者是否应该开始解释执行一个触摸接收事件
- (BOOL)gestureRecognizerShouldBegin:(UIGestureRecognizer *)gestureRecognizer{
    CGPoint currentPoint = [gestureRecognizer locationInView:self.view];
    if (CGRectContainsPoint(CGRectMake(0, 0, 100, 100), currentPoint) ) {
        return YES;
    }
    
    return NO;
}

// 询问delegate，两个手势是否同时接收消息，返回YES同事接收。返回NO，不同是接收（如果另外一个手势返回YES，则并不能保证不同时接收消息）the default implementation returns NO。
// 这个函数一般在一个手势接收者要阻止另外一个手势接收自己的消息的时候调用
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldRecognizeSimultaneouslyWithGestureRecognizer:(UIGestureRecognizer *)otherGestureRecognizer{
    return NO;
}

// 询问delegate是否允许手势接收者接收一个touch对象
// 返回YES，则允许对这个touch对象审核，NO，则不允许。
// 这个方法在touchesBegan:withEvent:之前调用，为一个新的touch对象进行调用
- (BOOL)gestureRecognizer:(UIGestureRecognizer *)gestureRecognizer shouldReceiveTouch:(UITouch *)touch{
    return YES;
}
```

