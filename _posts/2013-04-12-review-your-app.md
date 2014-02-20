---
title: 应用评分代码
layout: post
tags: iOS 
---

跳转至 App Store 进行评分的代码，APP_ID 字段用应用的相应 ID 替换：

```
 [[UIApplication sharedApplication] openURL:[NSURL URLWithString:@“itms-apps://ax.itunes.apple.com/WebObjects/MZStore.woa/wa/viewContentsUserReviews?type=Purple+Software&id=appID”]];
```

-----------
9.28 更新：上述链接在 iOS 7 中失效，使用新 URL

```
@"itms-apps://itunes.apple.com/app/idAPP_ID"
```

-------------
或者在[应用内打开 App Store](http://rensisi.com/2013/06/29/open-app-store-inside-app.html)。