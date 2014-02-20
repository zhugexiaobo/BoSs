---
title: 自定义系统服务授权时的提示信息
layout: post
tags: iOS 
---

从 iOS 6 开始系统中集成了新的隐私保护功能，当用户第一次打开新应用时，系统会询问是否允许该应用访问设备的通讯录、定位、相机胶卷等，由用户来决定是否授权，而且在设置-隐私中可以查看当前授权的应用并随时关闭授权。

我们的应用在第一次请求授权时，如果能够向用户说明请求的原因，而非恶意访问，用户体验会好很多。

具体的做法是在 `info.plist` 中添加一些相应的键值。

- `NSBluetoothPeripheralUsageDescription` 蓝牙共享
- `NSCalendarsUsageDescription` 日历
- `NSContactsUsageDescription` 通讯录
- `NSLocationUsageDescription` 定位服务
- `NSPhotoLibraryUsageDescription` 照片
- `NSRemindersUsageDescription` 提醒事项
- `NSCameraUsageDescription` 相机胶卷（iOS 7+）
- `NSMicrophoneUsageDescription` 麦克风（iOS 7+）
- `NSMotionUsageDescription`  Core Motion（iOS 7+）
