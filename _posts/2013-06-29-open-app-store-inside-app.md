---
title: 应用内打开 App Store 应用详情界面
layout: post
tags: iOS 
---

iOS 6 新增 API `SKStoreProductViewController`，用来在应用内打开一个 App Store 页面，可直接完成购买下载，不需要跳转至系统的商店 App。不过评分还是得跳出去。

例子代码：

```
#import <StoreKit/StoreKit.h>

if (NSClassFromString(@"SKStoreProductViewController")) {
    SKStoreProductViewController *storeController = [[SKStoreProductViewController alloc] init];
    storeController.delegate = self;
    NSDictionary *productParameters = @{SKStoreProductParameterITunesItemIdentifier:@"696517754"};
    [self.navigationController presentViewController:storeController animated:YES completion:^{
        [storeController loadProductWithParameters:productParameters completionBlock:nil];
    }]; // 先弹出 `storeController`，因为商店加载比较慢
}

- (void)productViewControllerDidFinish:(SKStoreProductViewController *)viewController
{
    [self dismissViewControllerAnimated:YES completion:nil];
}
```

> [Launching the App Store from an iPhone application](https://developer.apple.com/library/ios/qa/qa1629/_index.html)