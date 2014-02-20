---
title: Xcode 中断点调试技巧
layout: post
tags: iOS 
---

A collection of useful Xcode debugging technique, using Breakpoints and LLDB debugger.

### Adding a special Breakpoint
#### Exception breakpoint
- 在 Xcode 断点列表栏左下角 Add Exception Breakpoint...
- 捕获类似下面的异常

    ```
    *** Terminating app due to uncaught exception ’NSRangeException’, reason: -[__NSCFArray objectAtIndex:]: index (10) beyond bounds (3)
    ```

#### Symbolic breakpoint
- 在 Xcode 断点列表栏左下角 Add Symbolic Breakpoint...
- 在 Symbol 栏填入想中断的方法名，这样每次运行至这个方法都能触发这个断点，如

    ```
    - [UIViewController viewDidLoad]
    - [__NSCFArray objectAtIndex:]
    ```
    
#### Logging to console
- Log string with NSLog
    - 在某行添加断点，添加 Action 为 `Log Message`，然后填入想要输出的字符串
    - 勾上 `Automatically continue after evaluating`
    - 每次运行至断点处，就会打印一个字符串，然后继续运行，等同与在代码中写 `NSLog()`        
- Log objects with NSLog (po)
    - 在某行添加断点，添加 Action 为 `Debugger Command`，然后填入`po anObj`
    - 勾上 `Automatically continue after evaluating`
    - 每次运行至断点处，就会打印 `anObj` 的信息，然后继续运行，等同在代码中写
    
    ```
    NSLog(@"anObj: %@", anObj);
    ```
- Conditional log
    - Condition 中填入条件，就能在只满足该条件下触发断点
- Log something in a loop
    - 在循环语句内部添加断点，并设置 `Ignore` 次数，就能只在该次数之后触发断点
- Set breakpoints in runtime
    - 在某行添加断点，添加 Action 为 `Debugger Command`，然后填入`breakpoint set -f 类名 -l 33`，动态添加断点
- Debug with sound :)
    - 添加 Action 为 `Sound`

### Useful LLDB commands
当运行到断点程序中断时，可以在控制台用代码进行调试。

- Help 
    - `(lldb) help`
- Print backtrace (bt) 
    - `(lldb) bt`
- Print primitives (p) 
    - `(lldb) print anInt`
- Print objects (po) 
    - `(lldb) po anObj`
    - `(lldb) po 0x0715aa40`
- Print expressions (expr)
    - (lldb) expr 5+2
    - (lldb) expr aString = @"aNewValue"
- Print something in thread
    - `(lldb) help frame`
    
### [原文](http://www.albertopasca.it/whiletrue/2013/06/xcode-power-of-breakpoints/)简单翻译转载