---
title: Git 版本问题
layout: post
tags: Git Mac
---

今天升级了 Git 到 1.8.3.1，但是看了下当前的版本发现不对

```
$ git --version
$ git version 1.7.12.4 (Apple Git-37)
```

显示的 Git 是系统自带的，但是需要的是用 Homebrew 升级管理的 Git

```
$ which git
$ /usr/bin
```

这里看出来不对了，Homebrew 的包是在 `/usr/local/bin` 中，所以把这个路径加入：

在 `~/.bash_profile` 中添加 `export PATH="/usr/local/bin:$PATH"` 即可。