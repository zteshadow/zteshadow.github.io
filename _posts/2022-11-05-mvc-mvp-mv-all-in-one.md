---
layout: post
title: MVC, MVP, MVVM, VIPER and MVVMC(SwiftUI) All In One
date: 2022-11-05 11:09 +0800
categories: [BestPractice]
tags: [iOS, SwiftUI, Swift, MVVM, MVC, MVP, VIPER, MVVMC]
img_path: /assets/img/post/architecture/
---

# Architecture

iOS开发最初遵循原始的`MVC`架构, 但是随着业务的演进代码越来越复杂, 很容易变成`Massive View Controller`, 最终导致代码难以维护, 难以测试, 难以复用. 为了解决这个问题逐步演化出:
- `MVP`
- `MVVM`
- `VIPER`
- `MVVM-C`

等架构. 今天我们就用各个架构实现一个`ToDoList`来看看他们是如何解决问题的以及各自的优缺点.

要实现的功能是一个to do list页面, 功能如下:
- 点击删除条目
- 点击`+`添加条目
- 输入字符超过3个(包含)才可添加

示例如下:

![](todolist.gif)

## MVC
[源代码](https://github.com/zteshadow/best-practice/tree/main/native-ios/MVC)

![](mvc.png)

```swift
override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        guard indexPath.section == Section.todos.rawValue else {
            return
        }
        
        todos.remove(at: indexPath.row)    //更新model
        title = "TODO - (\(todos.count))"  //更新model
        tableView.reloadData() //刷新View
    }

```

><span style="color:red">问题</span>
- 无法测试business logic是否正确, 包括:
```
1. 添加时button的enabled状态是否正确
2. 添加时新增的条目是否正确
3. 是否删除正确的条目
...
```
- View与business logic耦合在一起, 无法复用
```
1. 如果要换个界面展示business, 代码修改, 无法保证business不动只切换UI
```

> 优点
- 代码量少, 开发速度快
- 不需要太多经验就能开发维护

## MVP

[源代码](https://github.com/zteshadow/best-practice/tree/main/native-ios/MVP)

![](mvp.png)

`MVP`架构中, 将`View+Controller`组成一个`PassiveView`, `PassiveView`负责转发用户交互事件, 业务逻辑都在`Presenter`中实现, 由`Presenter`负责更新视图.


```swift
protocol ToDoListView: AnyObject {
    func update(list: [String], title: String)
    func enableAdd(_ enable: Bool)
}

protocol ToDoListPresenter {
    func load()
    func remove(at index: Int)
    func edit(_ text: String)
    func add()
}

class ToDoListPresenterImpl: ToDoListPresenter {
    unowned var view: ToDoListView

    var todos: [String] = [] {
        didSet {
            view.update(list: todos, title: "TODO - (\(self.todos.count))")
        }
    }
    var text = "" {
        didSet {
            view.enableAdd(text.count >= 3)
        }
    }

    init(_ view: ToDoListView) {
        self.view = view
    }
}
```

> Controller和Presenter的绑定:
```swift
...
let controller: TableViewController = UIStoryboard(name: "Table", bundle: nil)
    .instantiateViewController(withIdentifier: "tableViewController") as! TableViewController

let presenter = ToDoListPresenterImpl(controller)
controller.presenter = presenter

controller.navigationItem.hidesBackButton = true
self.navigationController?.pushViewController(controller, animated: false)
...
```
><span style="color:red">注意`view`持有`presenter`, 而`presenter`连接`unowned view`</span>

业务逻辑在`presenter`中, 很容易通过mock view进行测试(代码覆盖率达到`97.8`).
![](mvp-test.png)

><span style="color:red">问题</span>
- 需要手动将数据的变化绑定到view
> 优点
- 业务逻辑与view隔离, 容易测试
- 业务逻辑与view隔离, 容易复用, 如果只修改界面, 换一个`ToDoListView`实现即可.

## MVVM
[源代码](https://github.com/zteshadow/best-practice/tree/main/native-ios/MVVM)

![](mvvm.png)

从架构图中也可以看到, `MVVM`架构与`MVP`架构基本相同, 但有以下几点区别
- 对视图的更新, 在`MVP`中是手动实现的, 而`MVVM`中是自动完成的(用callback, combine, RxSwift技术等)
- `view model`的设计原则是: 持有`view`中对应的状态, 修改状态 -> 自动更新view
```
这点稍微比较隐晦, 比如在Presenter中, 更新todos会同步更新title, 因此Presenter中可以不持有title, 但是设计view model的时候, 一定是将view中需要的状态都保存在view model中.
```

```swift
class ToDoListViewModel {
    @Published var todos: [String] = [] {
        didSet {
            title = "TODO - (\(self.todos.count))"
        }
    }
    @Published var title: String = ""
    @Published var enableAdd: Bool = false

    var text = "" {
        didSet {
            if text.count >= 3 {
                enableAdd = true
            } else {
                enableAdd = false
            }
        }
    }
}
```
- `business`和`view`的耦合不同, 如果使用combine来实现`MVVM`中的绑定, 那么这个`view model`是和`view`紧密结合在一起的, 不像`MVP`中`view`与`Presenter`解耦的那么彻底, 更适合复用.

- 测试方式也不同, 同上, 如果使用combine来实现`MVVM`中的绑定, 那么一些基本的数据操作逻辑是不用测试的.

## VIPER

[源代码](https://github.com/zteshadow/newlife/tree/master/study/Architect/MVX/VIPER)

![](viper.png)

><span style="color:red">与MVP的区别</span>

- 将`Presenter`进一步的细化, 分拆出来`Interactor`和`Router`, 分别负责外部数据交互以及路由

## MVVMC

![architecture](diagram_mvvmc.jpeg)

> Typical coordinator(统筹者,协调人)
```swift
public protocol MyTicketsCoordinator: Coordinator {
    /// Creates a new view to show for the home-page tab.
    func createMyTicketsView() -> AnyView
    /// or
    func pushMyTicketsScreen() -> ChildCoordinator
}

/// The MyTickets public APIs to be used by other modules
public protocol MyTicketsAPI {
    /// Creates a new coordinator for the home page.
    func createMyTicketsCoordinator(router: RoutingAPI) -> MyTicketsCoordinator
}

```
## 总结
架构的最终目的
- UI可复用可更新
- 业务逻辑可测试
