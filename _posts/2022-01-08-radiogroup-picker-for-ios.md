---
layout: post
title: RadioGroup Picker for iOS
date: 2022-01-08 21:48 +0800
categories: [Artistry, SwiftUI]
---

SwiftUI提供了一个Picker控件用来menu, segment等选择操作, 接口简单, 使用方便, 效果也不错.


```swift
struct Picker<Label, SelectionValue, Content> where Label : View, SelectionValue : Hashable, Content : View
```

![current picker](/assets/img/post/currentpicker.gif)

## 只包含Text的简单RadioGroup Picker

## 可以自定义Item View的RadioGroup Picker
### ViewModifier
### Resuilt builder
