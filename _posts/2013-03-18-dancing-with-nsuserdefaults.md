---
title: 应用在系统中的设置菜单
layout: post
tags: iOS 
---

[原文](http://www.dimzzy.com/blog/2011/02/dancing-with-nsuserdefaults/)为英文博客，简单翻译摘录。

在 iOS 中，`NSUserDefaults` 可以非常方便的存储程序选项。我们不仅可以用它存储应用的设置并通过 iTunes 备份，还可以让它们在系统的设置程序中显示。要实现这个，你需要创建一个 Settings.bundle 并描述你想要的选项。

### 添加一个布尔值 'bounce' 选项
- 创建 Settings.bundle（New File -> Resources -> SettingsBundle）
- 编辑里面的 Root.plist 文件，添加一个名为'bounce' 的 Boolean 值，默认为 'true'，此时该文件内容如下

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE plist PUBLIC "-//Apple//DTD PLIST 1.0//EN"
    "http://www.apple.com/DTDs/PropertyList-1.0.dtd">
<plist version="1.0">
<dict>
    <key>StringsTable</key>
    <string>Root</string>
    <key>PreferenceSpecifiers</key>
    <array>
        <dict>
            <key>Type</key>
            <string>PSToggleSwitchSpecifier</string>
            <key>Title</key>
            <string>Bounces</string>
            <key>Key</key>
            <string>bounces</string>
            <key>DefaultValue</key>
            <true/>
        </dict>
    </array>
</dict>
</plist>
```
- 然后为这个属性添加 helper 类用于存取

```
- (BOOL)bounces {
    return [[NSUserDefaults standardUserDefaults] boolForKey:@"bounces"];
}

- (void)setBounces:(BOOL)bounces {
    [[NSUserDefaults standardUserDefaults] setBool:bounces forKey:@"bounces"];
    [[NSUserDefaults standardUserDefaults] synchronize];
}
```
- 这里有2个 bug
  1. 如果存在默认值，你必需在 Settings.bundle/Root.plist 中加以说明同时需在代码中注册默认值。
  2. ~~当应用程序进入后台，需要 synchronize~~ 「已不存在该问题」

