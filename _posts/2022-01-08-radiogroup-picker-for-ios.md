---
layout: post
title: RadioGroup Picker for iOS
date: 2022-01-08 21:48 +0800
categories: [Artistry, SwiftUI]
img_path: /assets/img/post/
---

## 1. 当前的SwiftUI Picker

SwiftUI提供了一个Picker控件用来进行选择操作, 目前支持`segmented`, `inline`, `wheel`, `menu`四种种样式, 接口简单, 使用方便, 效果也不错.

接口:
```swift
struct Picker<Label, SelectionValue, Content> : View 
    where Label : View, SelectionValue : Hashable, Content : View
```

使用:
```swift
Picker("", selection: $selectedFlavorFromSegment) {
    Text("Chocolate").tag(Flavor.chocolate)
    Text("Vanilla").tag(Flavor.vanilla)
    Text("Strawberry").tag(Flavor.strawberry)
}.pickerStyle(.segmented)
```
效果:
![current picker](currentpicker.gif)

## 2. 简单的RadioGroup Picker

SwiftUI只为macOS提供了`radioGroup`样式, iOS并没有对应的支持. 如果要实现简单的统一样式的radio group picker也不难, 参考`Picker`的设计, 实现一个支持数组输入的`RadioGroupPicker`.

使用:
```swift
RadioGroupPicker("", selection: $selectedFlavor) {
    [
        Text("Chocolate").radioTag(Flavor.chocolate),
        Text("Vanilla").radioTag(Flavor.vanilla),
        Text("Strawberry").radioTag(Flavor.strawberry)
    ]
}
```

> 注意1: 传入的参数是数组

> 注意2: 自定义`radioTag`函数传入tag, 因为SwiftUI中的`View.tag`无法获取

参考[源代码]()中的`RadioGroupPicker+String.swift`

### 2.1 问题: radio tag的存取

如果使用系统内置的`View.tag`, 我们无法得到`tag`的值, 因此只能自己添加函数`radioTag`获取tag值.

```swift
    /// cause some View is generic with constrain, cant't saved in list, so `AnyView` needed
    func radioTag<T: Hashable>(_ tag: T) -> AnyView where T: Hashable
```

最初的思路是参考`View.tag`, 返回`View`, 这样便于在`SwiftUI`中继续构建`View`, 但是这样设计有个问题, 由于传入的是`[AnyView]`, 因此在`RadioGroupPickerSimple`内部需要从`AnyView`中获取`radioTag`的值, 由于`radioTag`也是一个`Generic with constrain`, 因此无法扩展`AnyView`来存储(如果有方法, 请及时指正, 谢谢)
只能得到导致这样的, 给View添加环境变量radio tag, 存储[View], 在选择的时候取出环境变量即可, 这样做甚至可以利用现有的ViewBuilder优化传入的数组, 但是环境变量容易设置, 取出却只能通过@Envrionment这个property wrapper进行, 因此无法用envrionment把radio tag存储在View中. 

那么能否自定义存储机制, 把radio tag存储到View中呢, 由于View是一个带有associatedtype的协议, 所以无法把radio tag存储在View中.

那么能否存储在AnyView中呢, 再试试, 无法在View/AnyView内部存储radio tag

那么就只能退化到外部存储结构了, 在View外部存储radio tag
所以自定义存储结构, 把view和radio tag的关系存储起来, 这样以来会导致一个小缺陷: Text("hi").radioTag("sdf")返回的不是一个view, 而是自定义的结构

定义外部结构:
struct RadioGroupItem<SelectionValue> where SelectionValue: Hashable {
    var content: AnyView
    var tag: SelectionValue
}
在RadioGroupPickerSimple内部展示List of RadioGroupItem, 并响应点击选择.


## 3. 支持自定义的RadioGroup Picker

## 4. 完全自定义的RadioGroup Picker

