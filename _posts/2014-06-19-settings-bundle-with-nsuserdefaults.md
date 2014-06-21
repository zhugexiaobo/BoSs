---
title: 使用 NSUserDefaults 构建 Settings Bundle
layout: post
tags: iOS 
---

在 iOS 的设置程序中可以看到一些应用集成了他们不常用的的设置项，这个可以通过 `Settings.bundle` 来实现。

### 添加修改 Settings Bundle
- 创建 `Settings.bundle`（New File -> Resources -> Settings Bundle）
- 默认已有4项，可以在 Xcode 里直接编辑，也可以右键「Open As Source Code」编辑 XML 源码
- 普通的元素有以下：`Text Field(可编辑字符)`, `Title(不可编辑字符)`, `Toggle Switch(开关)`, `Slider(滑块)`, `Multi Value(可选值列表)`, `Group(同 grouped tableview)`, `Child Pane(点击加载子 plist)`，每个都包含一些特定的键值对
- `Group` 元素 中的 *`Title`* 和 *`FooterText`* 键类似 `UITableView` 的 *`header`* 和 *`footer`*
- 其他详细的 key 可以查看[文档](https://developer.apple.com/library/ios/documentation/PreferenceSettings/Conceptual/SettingsApplicationSchemaReference/Introduction/Introduction.html)
- [InAppSettingsKit](https://github.com/futuretap/InAppSettingsKit) 开源组件快速集成

### 本地化 Settings.bundle
在 Xcode 中找不到本地化的选项，不过可以遵循以下步骤完美实现：

1. 在 Finder 中打开 `Settings.bundle`，右键 「显示包内容」
2. 复制一份 en.lproj 然后改名为你想要的语言，如「zh-Hans.lproj」，Xcode 中就可以看到
3. 修改对应语言中的 Root.strings 实现本地化

### 存取 Settings Bundle 中的值
在 iOS 中，`NSUserDefaults` 可以非常方便的存储程序选项。我们不仅可以用它存储应用的设置并通过 iCloud 备份，还可以用来存取 Settings Bundle 中的值。

需要在程序启动后将 `Settings.bundle` 中的键值注册到 `NSUserDefaults` 中，才能正常存取。可以创建一个 `SettingsHandler` 类专门处理。

假设 Root.plist 中有一个名为 `bounces` 的布尔值，就在 `SettingsHandler` 中创建存取方法：

```objc
- (BOOL)bounces {
    return [[NSUserDefaults standardUserDefaults] boolForKey:@"bounces"];
}

- (void)setBounces:(BOOL)bounces {
    [[NSUserDefaults standardUserDefaults] setBool:bounces forKey:@"bounces"];
    [[NSUserDefaults standardUserDefaults] synchronize];
}
```

### 监视和同步
实时同步 Settings Bundle 中的值，文档中推荐 `NSUserDefaultsDidChangeNotification`，如果要监视特定的键可以用 `KVO`。

```objc
// 注册 KVO
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
[defaults addObserver:self
           forKeyPath:@"nameOfThingIAmInterestedIn"
              options:NSKeyValueObservingOptionNew
              context:NULL];
              
// 实现方法
-(void)observeValueForKeyPath:(NSString *)keyPath 
                 ofObject:(id)object
                   change:(NSDictionary *)change
                  context:(void *)context 
{
    NSLog(@"KVO: %@ changed property %@ to value %@", object, keyPath, change);
}

// 清除
NSUserDefaults *defaults = [NSUserDefaults standardUserDefaults];
[defaults removeObserver:self forKeyPath:@"nameOfThingIAmInterestedIn"];
```

---
参考：

- [Dancing with NSUserDefaults](http://dimzzy.wordpress.com/2011/02/01/dancing-with-nsuserdefaults/)
- [Preferences and Settings Programming Guide](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/UserDefaults/Introduction/Introduction.html)
- [Settings Application Schema Reference](https://developer.apple.com/library/ios/documentation/PreferenceSettings/Conceptual/SettingsApplicationSchemaReference/Introduction/Introduction.html)
- [SOF](http://stackoverflow.com/questions/10871860/nsuserdefaultsdidchangenotification-whats-the-name-of-the-key-that-changed)