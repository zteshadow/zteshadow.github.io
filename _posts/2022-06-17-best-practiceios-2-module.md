---
layout: post
title: Best Practice iOS Part2 - Modules
date: 2022-06-17 07:32 +0800
categories: [BestPractice]
tags: [iOS, SwiftUI, Swift, MVVM]
img_path: /assets/img/post/mvp/
---

# How to divide modules

Modules can be divided by different perspective, and usually there are two different perspectives: function and business, from the functional perspective, Network, Dependency injection are different modules, and from the business perspective(shown as below), `Club`, `Book`, `Food`, and `Profile` are different modules.

There are also some other factors usually being taken into consideration in modules dividing, such as `Complexibility`, `Extension`, `Cooperation`, take `Book` in the MVP as an example, creating a module named `BookModule` is a very common practice, but if both `List` and `Detail` page are very complicated with lots of fine UI elements, network API, taken by different team or, one of them will be in a `Webview` some day in future, then two modules of `BookList` and `BookDetail` are better choice.

Usually below factors are worth consideration:
- **UI complexity**
- **Extensionbility (may be taken place by a web page?)**
- **Cooperation (in different team or different team members)**
- **Function**

And of course, divied into finner moduels is always not bad idea

![General](mvp-general.png)

Here are the modules we need in our MVP:
- **Network**
- **Dependency injection**
- **ClubList**
- **ClubDetails**
- **BookList**
- **BookDetails**
- **FoodList**
- **FoodDetails**
- **Profile**

The first two modules are functional modles while the other modules are business related.

![General](mvp-modules.png)

# How to organize modules

There are two ways we can organize our moduls:
- Xcode project
- Swift package

And some poins we need to take into consideration:
- Configuration
- Unit test
- SwiftUI preview
- Localization

We will use swift pacakge at first for all modules, and it will certainly be good for funtional modules such as `Network` and `Dependency injection`, but not sure for other business modules. We can discuss this later.
