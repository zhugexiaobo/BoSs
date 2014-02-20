---
title: 本地化系统控件
layout: post
tags: iOS 
---

一些系统的 UI 控件中，其显示的语言并不是当前设备的系统语言，而是根据 App 内部的语言设置来显示。如果没有设置恰当的话，你的中文 App 可能会出现一些英文的控件文字。

解决方法：在工程的 `Info.plist` 中增加 `CFBundleLocalizations`，然后添加相应的语言如 `zh_CN`。