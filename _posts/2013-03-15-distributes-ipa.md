---
title: 应用打包分发 IPA 文件
layout: post
tags: iOS 
---

1. scheme 选中iOS Device，Product -> Archive
2. Window -> Organizer -> Archives -> Distribute... -> Export as Xcode
   Archive
3. 下方列表中右键 -> Show in Finder，右键 -> 显示包内容 -> Products ->
   Applications，将 .app 拖入iTunes -> 应用程序
4. 在生成的应用程序上右键 -> Show in Finder，就能看到生成的 .ipa 文件了


### 12.17 增加
使用以上方法打包的 IPA 包只能在「已设置为开发使用」的设备上安装，最好的办法是使用 Ad-Hoc 证书来发布测试 IPA 安装包。具体做法：

1. 在 developer 网站中申请设置好对应的 Ad Hoc 证书
2. Archive 程序
3. Window -> Organizer -> Archives -> Distribute... -> Save for
   enterprise or Ad Hoc Deployment，选择 Ad Hoc 证书再 Export
4. Xcode 自动完成打包
5. 可以使用 [ios-makefile](https://github.com/lexrus/ios-makefile) 来自动 OTA；或者用 [fir.im](http://fir.im) 这个服务也不错
