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
struct Picker<Label, SelectionValue, Content> 
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

SwiftUI只为macOS提供了`radioGroup`样式, iOS并没有对应的支持. 如果要实现简单的只有文本形式的radio group picker也不难.



## 可以自定义Item View的RadioGroup Picker
### ViewModifier
### Resuilt builder
