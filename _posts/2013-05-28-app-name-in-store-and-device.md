---
title: 应用名字在 App Store 中与设备上的显示
layout: post
tags: iOS 
---

- App Store 上显示的应用名是在 iTunes Connect 创建应用时设定的，这个名字只能在提交新版本时更改。
- 设备上显示的应用名在 info.plist 的 `Bundle display name` 项填写。
- 这两个地方的名字可以不同，但是不能相差太大。

	> [3.4 App names in iTunes Connect and as displayed on a device should be similar, so as not to cause confusion](https://developer.apple.com/appstore/resources/approval/guidelines.html#metadata)

- info.plist 中的 `Display Name(CFBundleDisplayName)` 和 `Bundle Name(CFBundleName)` 较容易搞混，文档中的定义：
	
	- CFBundleName
	`CFBundleName` (String - iOS, OS X) identifies the short name of the bundle. This name should be less than 16 characters long and be suitable for displaying in the menu bar and the app's Info window. You can include this key in the `InfoPlist.strings` file of an appropriate `.lproj` subdirectory to provide localized values for it. If you localize this key, you should also include the key “[CFBundleDisplayName](https://developer.apple.com/library/ios/documentation/General/Reference/InfoPlistKeyReference/Articles/CoreFoundationKeys.html#//apple_ref/doc/uid/20001431-110725).”

	- CFBundleDisplayName
	`CFBundleDisplayName` (String - iOS, OS X) specifies the display name of the bundle. If you support localized names for your bundle, include this key in both your information property list file and in the `InfoPlist.strings` files of your language subdirectories. If you localize this key, you should also include a localized version of the `CFBundleName` key.

	If you do not intend to localize your bundle, do not include this key in your `Info.plist` file. Inclusion of this key does not affect the display of the bundle name but does incur a performance penalty to search for localized versions of this key.

	Before displaying a localized name for your bundle, the Finder compares the value of this key against the actual name of your bundle in the file system. If the two names match, the Finder proceeds to display the localized name from the appropriate `InfoPlist.strings` file of your bundle. If the names do not match, the Finder displays the file-system name.

- 简单来说，`Display Name` 是显示在设备中图标下方、对话框标题中及设置菜单中的名称；`Bundle Name` 是安装包的名称，普通用户一般见不到。