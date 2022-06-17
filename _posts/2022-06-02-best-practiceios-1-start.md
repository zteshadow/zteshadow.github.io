---
layout: post
title: Best Practice iOS Part1 - Start
date: 2022-06-02 14:41 +0800
categories: [BestPractice]
tags: [iOS, SwiftUI, Swift, MVVM]
img_path: /assets/img/post/mvp/
---

# Best practice - artistry

I've been a software engineer for more than 16 years, have been using C, C++, Objective-C, Swift lauguage and have built lots of products such as USB driver on digital wireless phone, video chat application on feature phone, input method on both feature phone and iPhone, and recently a iOS application for e-commercial.

I've been stumbled and occasionally lost in the shit of code mountain, until I was so lucky to meet some amazing guys in a beautiful team recently. I finally re-fall in love with coding and engineering and finally I realize that we need to persuit art out of normal life and work, that's how we can enjoy our life.

So I want to conclude some best practice in our daily work though some of it seems common sense, they are really fun our work more. hope you will enjoy it.

## 1. Target

we are going to build a MVP(minimum viable product) with tabs and navigation(shown in below image), and investigate the best practice to do this.

![General](mvp-general.png)

Tech used:
- SwiftUI
- Combine
- await/async

Subjects involved:
- MVVMC design pattern
- How to divide module
- Do we need a coordinator
- Dependency injection
- Navigator in SwiftUI
- Dark mode
- Unit test
- Localization
- Tracking
- Remote configuration

## 2. Get started

Let's initialize our HappyTime application follow this post. <a href="https://zteshadow.github.io/posts/mainentryforapplication/" target="_blank">Simple</a>, you can find project files here <a href="https://github.com/zteshadow/best-practice-ios" target="_blank">Code</a>.

## 3. MVVM

We can see here, the root view of tab view follow the MVVM pattern.

![mvvm](mvp-mvvm.png)

<a href="https://github.com/zteshadow/best-practice-ios/releases/tag/start" target="_blank">Tag</a>

