---
title: 应用版本号问题总结
layout: post
tags: iOS 
---

- 文档定义：

    - `CFBundleShortVersionString`(Bundle versions string, short): The release version of the application. The value of this key is a string comprised of three period-separated integers.
    - `CFBundleVersion`(Bundle version): The bundle version string specifies the build version number of the bundle. This value is a monotonically increased string, comprised of one or more period-separated integers. This value cannot be localized. 

- Xcode 项目中的 `Version` 对应 iTunes Connect 中每个版本应用的 `Bundle Short Version String`，为发布版本，用户可见；`Build` 对应 `Bundle Version`，为内部版本，用户不可见。
- 版本号支持4位（28.1.1500.16），一般情况下 `Version` 和 `Build` 应保持一致，不过也有 Version = 2.7.2; Build = 20130814; 的情况。
- 每次升级时版本号必须要大于当前版本。