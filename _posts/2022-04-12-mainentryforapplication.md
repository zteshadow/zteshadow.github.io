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

Objective-C程序保持了C语言简单直接的方式, 我们创建一个Objective-C App后Xcode会自动创建`main.m`文件.

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
程序会在`UIApplicationMain`中进入`Runloop`, 直到调用`exit()`退出或者异常终止.

## 1.1 初始化

与main.m文件同时生成的还有`AppDelegate`, 可以把初始化操作放在`didFinishLaunchingWithOptions`中进行.
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

上面是程序的入口以及初始化过程, 那么主界面是如何构造的呢? 答案是配置加反射.
- Target -> General -> Main Interface指定`Main` storyboard
- Main storyboard中指定`ViewController`
- `ViewController`构造主界面

上述配置是存储在plist中的, 程序启动后会读取配置, 利用Objective-XC的反射机制初始化`ViewController`实例.

# 2. SwiftUI

基于SwiftUI的App与Objective-C App不同, 主入口被简化成了`@main`.

![swiftui-app](swiftui-start.gif)

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
`@main`标签要求实例必须提供`main`函数, 并且全局只能有一个标识为`@main`的实例.
```swift
public static func main()
```
## 2.1 自定义初始化

与Objective-C的App相比, 主界面的构造更简单直接, 但是如何在launch结束后进行初始化呢? 答案是`@UIApplicationDelegateAdaptor`.

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

# 3. Simple MVVM structure
下面基于上述概念, 实现一个简单的基于`TabView`的application, 样式如下.
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

## 3.2 初始化与主界面展示

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