---
title: 代码行数统计
layout: post
tags: terminal tips
---

### Terminal shell 命令

- 统计当前目录下 `.m` 文件的代码行数

```bash
find . -name "*.m" | xargs wc -l
```

- 在当前目录统计 `.h`、`.m`、`.xib`、`.c` 文件中的代码行数

```bash
find . -name "*.m" -o -name "*.h" -o -name "*.xib" -o -name "*.c" | xargs wc -l
```

- 排除某个目录下的文件

```bash
find . -path './*/Vendors' -prune -o -name "*.m" -o -name "*.h" -o -name "*.xib" -o -name "*.c" | xargs wc -l
```

- 排除多个目录，注意空格

```bash
find . \( -path './*/Vendors' -o -path './*/Crashlytics.framework' \) -prune -o -name "*.m" -o -name "*.h" -o -name "*.xib" -o -name "*.c" | xargs wc -l
```

- 排除空行

```bash
find . \( -path './*/Vendors' -o -path './*/Crashlytics.framework' \) -prune -o -name "*.m" -o -name "*.h" -o -name "*.xib" -o -name "*.c" | xargs grep -v "^$" | wc -l
```

- 排除 `/` 开头的注释

```bash
find . \( -path './*/Vendors' -o -path './*/Crashlytics.framework' \) -prune -o -name "*.m" -o -name "*.h" -o -name "*.xib" -o -name "*.c" | xargs grep -v -e "^$" -e "^\s*\/.*$" | wc -l
```


### Sublime Text 正则搜索

- 统计打开的目录下所有代码行数

```bash
Find: ^.*$
```

- 统计打开的目录下所有代码行数并去除空行

```bash
Find: ^[ \t]*[^ \t\n\r]+.*$
```

- 统计打开的目录下所有代码行数并排除空行和注释行(包括`/` 和 `#`，`?` 不是必需的)

```bash
Find: ^[ \t]*?[^ \t\n\r/#]+?.*$
```

- 统计特定文件并排除特定文件夹

```bash
Find: ^[ \t]*?[^ \t\n\r/#]+?.*$
Where: *.h, *.m, *.xib, -*/Vendors/*
```

---
参考：

- [一句shell命令搞定代码行数统计](http://hittyt.iteye.com/blog/1700584)
- [统计代码行数的小技巧 ](http://zhouhua.github.io/2013/06/20/skills/)
