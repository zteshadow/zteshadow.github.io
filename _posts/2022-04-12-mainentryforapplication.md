---
layout: post
title: Main Entry For iOS Application
date: 2022-04-12 07:32 +0800
categories: [Foundation]
img_path: /assets/img/post/
---

我们知道所有程序的主入口是main函数, 这个概念在C语言里面的实现很直接.
```c
int main(int argc, char **argv) {
    printf("hello world\n");
    return 0;
}
```
对于一个与用户交互的程序, 那么初始化之后会构造主交互界面(比如CLI会循环接受用户输入), 然后进入一个事件循环处理各种用户事件, 系统事件, 伪代码如下.
```c
int main(int argc, char **argv) {
    printf("hello world\n");
    while (event) {
        // do process
    }
    return 0
}
```
# 1. Objective-C

> 创建基于Objective-C的程序

![current picker](objc-start.gif)

Objective-C程序保持了C语言的方式, 我们创建一个Objective-C App后Xcode会自动创建`main.m`文件.

```objc
#import <UIKit/UIKit.h>
#import "AppDelegate.h"

int main(int argc, char * argv[]) {
    NSString * appDelegateClassName;
    @autoreleasepool {
        // Setup code that might create autoreleased objects goes here.
        appDelegateClassName = NSStringFromClass([AppDelegate class]);
    }
    return UIApplicationMain(argc, argv, nil, appDelegateClassName);
}

```
## 1.1 初始化

自动生成的文件除了main.m文件, 还有`AppDelegate`, 可以把初始化操作放在`didFinishLaunchingWithOptions`中进行.
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    return YES;
}

@end

```

程序会在`UIApplicationMain`中进入`Runloop`, 直到调用`exit()`退出或者异常终止.

## 1.2 主界面

 ![current picker](objc-main-screen.jpg)

上面是程序的入口以及初始化过程, 那么主界面是如何构造的呢?
- Target -> General -> Main Interface指定`Main` storyboard
- Main storyboard中指定`ViewController`
- `ViewController`构造主界面

# 2. SwiftUI
