---
title: UITableView 的滚动方法
layout: post
tags: iOS 
---

在有 UITableView 的页面上添加自定义的上下滑动手势是不可行的，因为会和 UITableView 的滑动方法冲突，造成自定义手势失效。所以要在列表拖动时实现自定方法就需要用到 UITableView 的超类代理方法（UISCrollViewDelegate）。

### UIScrollView.h 中的代理方法声明：

```
- (void)scrollViewDidScroll:(UIScrollView *)scrollView;          // any offset changes
- (void)scrollViewDidZoom:(UIScrollView *)scrollView NS_AVAILABLE_IOS(3_2); // any zoom scale changes

// called on start of dragging (may require some time and or distance to move)
- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView;
// called on finger up if the user dragged. velocity is in points/second. targetContentOffset may be changed to adjust where the scroll view comes to rest. not called when pagingEnabled is YES
- (void)scrollViewWillEndDragging:(UIScrollView *)scrollView withVelocity:(CGPoint)velocity targetContentOffset:(inout CGPoint *)targetContentOffset NS_AVAILABLE_IOS(5_0);
// called on finger up if the user dragged. decelerate is true if it will continue moving afterwards
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate;


- (void)scrollViewWillBeginDecelerating:(UIScrollView *)scrollView;   // called on finger up as we are moving
- (void)scrollViewDidEndDecelerating:(UIScrollView *)scrollView;      // called when scroll view grinds to a halt


- (void)scrollViewDidEndScrollingAnimation:(UIScrollView *)scrollView; // called when setContentOffset/scrollRectVisible:animated: finishes. not called if not animating


- (UIView *)viewForZoomingInScrollView:(UIScrollView *)scrollView;     // return a view that will be scaled. if delegate returns nil, nothing happens
- (void)scrollViewWillBeginZooming:(UIScrollView *)scrollView withView:(UIView *)view NS_AVAILABLE_IOS(3_2); // called before the scroll view begins zooming its content
- (void)scrollViewDidEndZooming:(UIScrollView *)scrollView withView:(UIView *)view atScale:(float)scale; // scale between minimum and maximum. called after any 'bounce' animations


- (BOOL)scrollViewShouldScrollToTop:(UIScrollView *)scrollView;   // return a yes if you want to scroll to the top. if not defined, assumes YES
- (void)scrollViewDidScrollToTop:(UIScrollView *)scrollView;      // called when scrolling animation finished. may be called immediately if already at top
```

### 以下为具体实现代码，可根据上一次手指离开屏幕时的座标判断是向上还是向下拖拽

**PushListView.h**

```
@interface PushListView : UIView <UITableViewDelegate, UITableViewDataSource>
{
    CGFloat contentOffsetY;
    CGFloat lastContentOffsetY;
}
```

**PushListView.m**

```
//开始拖拽视图

- (void)scrollViewWillBeginDragging:(UIScrollView *)scrollView
{
    contentOffsetY = scrollView.contentOffset.y;
}


// 滚动时调用此方法(手指离开屏幕后)
- (void)scrollViewDidScroll:(UIScrollView *)scrollView
{
    //NSLog(@"scrollView.contentOffset:%f, %f", scrollView.contentOffset.x, scrollView.contentOffset.y);
    CGFloat newContentOffsetY = scrollView.contentOffset.y;
    if (newContentOffsetY > lastContentOffsetY && lastContentOffsetY > contentOffsetY)
    {  // 向上滚动
        NSLog(@"up");
    }
    else if (newContentOffsetY < lastContentOffsetY && lastContentOffsetY < contentOffsetY)
    { // 向下滚动
        NSLog(@"down");
    }
    else
    {
        NSLog(@"dragging");
    }
    if (scrollView.dragging)
    {  // 拖拽
        NSLog(@"scrollView.dragging");
        NSLog(@"contentOffsetY: %f", contentOffsetY);
        NSLog(@"newContentOffsetY: %f", scrollView.contentOffset.y);
        if ((scrollView.contentOffset.y - contentOffsetY) > 5.0f)
        {  // 向上拖拽
            // [self layoutView];
            [something do];
        }
        else if ((contentOffsetY - scrollView.contentOffset.y) > 5.0f)
        {   // 向下拖拽
            //[self layoutView];
            [something do];
        }
    }
}


// 完成拖拽(滚动停止时调用此方法，手指离开屏幕前)
- (void)scrollViewDidEndDragging:(UIScrollView *)scrollView willDecelerate:(BOOL)decelerate
{
    NSLog(@"scrollViewDidEndDragging");
    lastContentOffsetY = scrollView.contentOffset.y;
}
```