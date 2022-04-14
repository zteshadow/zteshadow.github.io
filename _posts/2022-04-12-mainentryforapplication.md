---
layout: post
title: Main Entry For iOS Application
date: 2022-04-12 07:32 +0800
categories: [Foundation]
img_path: /assets/img/post/
---

我们知道所有程序的主入口是`main`函数, 这个概念在C语言里面的实现简单而直接.
```c
int main(int argc, char **argv) {
    printf("hello world\n");
    return 0;
}
```
对于一个与用户交互的应用程序, 初始化之后通常会构造交互界面(比如`CLI`会接受用户输入), 然后进入事件循环处理各种用户事件, 系统事件, 伪代码如下.
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

> 基于Objective-C的应用程序

![current picker](objc-start.gif)

基于Objective-C的应用程序保持了C语言简单直接的风格, 应用程序创建后Xcode会自动生成`main.m`文件.

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
程序会在`UIApplicationMain`中进入`Runloop`处理用户交互与系统事件, 直到调用`exit()`退出程序或者异常终止.

## 1.1 初始化

与`main.m`文件同时生成的还有`AppDelegate`, 通常在`didFinishLaunchingWithOptions`中进行初始化操作.
```objc
@implementation AppDelegate

- (BOOL)application:(UIApplication *)application didFinishLaunchingWithOptions:(NSDictionary *)launchOptions {
    // Override point for customization after application launch.
    return YES;
}

@end

```


## 1.2 主界面

 ![current picker](objc-main-screen.jpg)

启动程序完成初始化操作之后, 就要构造应用程序的交互界面了, 在`Objective-C`中是靠配置文件以及反射来完成的.
- Target -> General -> Main Interface指定`Main` storyboard
- Main storyboard中指定`ViewController`
- `ViewController`构造主界面

上述配置存储在`Info.plist`文件中, 程序启动后读取配置, 利用`Objective-C`的反射机制初始化`ViewController`实例, 完成主界面构造.

# 2. SwiftUI

> 基于SwiftUI的应用程序

![swiftui-app](swiftui-start.gif)

## 2.1 入口与主界面
```swift
import SwiftUI

@main
struct SwiftUIMainEntryApp: App {
    var body: some Scene {
        WindowGroup {
            ContentView()
        }
    }
}
```

与`Objective-C`应用程序不同, 基于`SwiftUI`的应用程序直接呈现的是主界面的构造代码, 程序入口和初始化被简化隐藏了. 主入口被简化成了一个`@main`标签, 该标签要求实例提供一个全局静态`main`函数作为入口, 全局只能有一个标识为`@main`的实例.
```swift
public static func main()
```

## 2.2 自定义初始化

那么初始化操作该如何进行呢?答案是`@UIApplicationDelegateAdaptor`.

```swift
@main
struct SwiftUIMainEntryApp: App {
    // swiftlint:disable:next weak_delegate
    @UIApplicationDelegateAdaptor(AppDelegate.self) var appDelegate

    var body: some Scene {
        WindowGroup {
            appDelegate.createContentView()
        }
    }
}

class AppDelegate: NSObject, UIApplicationDelegate, UISceneDelegate {

    override init() {
        super.init()
    }

    func createContentView() -> some View {
        ContentView()
    }

    func application(_ application: UIApplication,
                     didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]? = nil) -> Bool {
        return true
    }
}

```
我们可以自定义一个实现`UIApplicationDelegate`协议的类进行初始化操作.

# 3. Simple MVVM application
下面是一个简单的基于`TabView`的应用程序展示如何进行初始化以及创建主界面, 样式如下.

![simple-tabview](simple-tabview.gif)

## 3.1 View and View Model

由于没有涉及到与外部的数据交互, 因此这里只有`view`和`view model`, 并没有`MVVM`中的第一个`M: model`.

> view model

```swift
class RootViewModel: ObservableObject {
    @Published var tabList: [RootViewModelItem]
    var selected: TabType = .home

    init() {
        tabList = [
            .init(id: .home, title: "Home", image: Image(systemName: "house"), backgroundColor: .red),
            .init(id: .setting, title: "Setting", image: Image(systemName: "gear"), backgroundColor: .blue)
            ]
    }
}
enum TabType: Int, Hashable {
    case home, setting
}

struct RootViewModelItem: Identifiable {
    var id: TabType
    var title: LocalizedStringKey
    var image: Image
    var backgroundColor: Color
}

```

> view

```swift
struct RootView: View {
    @ObservedObject var viewModel: RootViewModel

    var body: some View {
        TabView(selection: $viewModel.selected) {
            ForEach (viewModel.tabList) { item in
                tabView(item)
                    .tabItem {
                        item.image
                        Text(item.title)
                }
            }
        }
    }

    func tabView(_ item: RootViewModelItem) -> some View {
        NavigationView {
            ZStack {
                item.backgroundColor
                Text(item.title)
            }.navigationTitle(item.title)
        }
    }
}

```

## 3.2 初始化与主界面构造

> 入口与初始化

```swift
@main
struct SimpleApp: App {

    @UIApplicationDelegateAdaptor var appDelegate: AppDelegate

    var body: some Scene {
        WindowGroup {
            appDelegate.createRootView()
        }
    }
}

```

> 主界面构造

```swift
class AppDelegate: NSObject, UIApplicationDelegate {
    private let rootViewModel: RootViewModel

    override init() {
        rootViewModel = RootViewModel()
    }

    func createRootView() -> some View {
        return RootView(viewModel: rootViewModel)
    }
}

```

[完整代码](https://github.com/zteshadow/swift/tree/main/Simple)