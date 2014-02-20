---
title: 自定义出/入栈动画效果
layout: post
tags: iOS 
---

- Push

```
MainView *nextView = [[MainView alloc] init];
[UIView animateWithDuration:0.75 animations:^{
    [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    [super pushViewController:nextView animated:NO];
    [UIView setAnimationTransition:UIViewAnimationTransitionFlipFromRight forView:self.navigationController.view cache:NO]; 
}];
```

- Pop

```
[UIView animateWithDuration:0.75 animations:^{
    [UIView setAnimationCurve:UIViewAnimationCurveEaseInOut];
    [UIView setAnimationTransition:transition forView:self.navigationController.view cache:NO];
}];
[self.navigationController popViewControllerAnimated:NO];
```