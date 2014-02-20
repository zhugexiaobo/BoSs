---
title: 自定义 tabBar 双击事件
layout: post
tags: iOS 
---

`UITabBarController` 有个默认的功能：双击某个标签，会出栈到根视图（NavigationController）。与点击状态栏列表会滚到顶端类似，是个隐藏的实用功能。

这次需要一个纵向的 tabBar，所以用了一个自定义的具有 `UITabBarController` 功能的 `VerticalTabBarController`，为了让它具有上述功能，我们在 `tabBarController: didSelectViewController:` 代理方法中使用通知实现双击事件。

```
#pragma mark UITab 双击事件

- (void)tabBarController:(MHVerticalTabBarController *)tabBarController didSelectViewController:(UIViewController *)viewController
{
    int newTabIndex = self.tabBarController.selectedIndex;
    if (newTabIndex == m_lastTabIndex) {
        [[NSNotificationCenter defaultCenter] postNotificationName:Notification_TabClick object:[NSString stringWithFormat:@"%d", newTabIndex]];
    }
    else
    {
        m_lastTabIndex = newTabIndex;
    }
}


// 各个主视图接收通知
[[NSNotificationCenter defaultCenter] addObserver:self selector:@selector(tabbarDoubleTapped:) name:Notification_TabClick object:nil];

// 双击事件
- (void)tabbarDoubleTapped:(NSNotification *)notification
{
    if ([(NSString *)notification.object isEqualToString:@"0"]) {
        // 不在初始根视图
        if ([self.navigationController.viewControllers count] > 1) {
            [self.navigationController popToRootViewControllerAnimated:YES];
        }
        // 不在列表顶端
        else if (self.qpointList.contentOffset.y > 0) {
            [self.qpointList scrollRectToVisible:CGRectMake(0, 0, 1, 1) animated:YES];
        }
        // 刷新列表
        else {
            [self.qpointList triggerPullToRefresh];
        }
    }
} 
```