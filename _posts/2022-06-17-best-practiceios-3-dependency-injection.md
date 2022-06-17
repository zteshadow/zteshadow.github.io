---
layout: post
title: best-practiceios-3-dependency-injection
date: 2022-06-17 09:30 +0800
categories: [BestPractice]
tags: [iOS, Swift, DI]
img_path: /assets/img/post/mvp/
---

# Why we need dependency injection

## 1. Dependency

Dependency is the connection between modules, take `BookList` module for example, it needs lots of services, such as:
- **Network** for data retrieving
- **Track** for event tracking
- **UserDefault** for simple configuration
- **Notification Center** for notification

let's take `Track` as an example, maybe we are now using `AppCenter` as our tracking platform (it can be newrelic or any other platforms), and have codes in our project like this:
```swift
public struct AppCenterTracker {
    public func logEvent(_ name: String, info: [String: String] = [:]) {
        //
    }
}

// used in other module such as BookList
struct BookListViewModel {
    private let tracker = AppCenterTracker()

    func loadBookList() async throws {
        tracker.logEvent("Load book list")
    }
}

```

The weakness of concreting tracker with specific implementation is obviously and it will be painfull if we change our tracking platform from `AppCenter` to `Newrelic`. so usually we use `protocol` oriented programming in `Swift`, here is the example.
```swift
public protocol TrackingAPI {
    func logEvent(_ name: String, info: [String: String])
}

public struct AppCenterTracker: TrackingAPI {
    public func logEvent(_ name: String, info: [String: String] = [:]) {
        //
    }
}

struct BookListViewModel {
    private let tracker: TrackingAPI

    init(_ tracker: TrackingAPI) {
        self.tracker = tracker
    }

    func loadBookList() async throws {
        tracker.logEvent("Load book list", info: [:])
    }
}

```

There are two types of dependency injection:
- **initializer based**
- **factory based**

If there are only couple of dependencies needed, just like the `tracker` above, we can use the `initializer based` injection, and use `factory based` injection in more dependencies case.

## 2. Benefits of dependency injection

Let's recap the benefits of dependency injection here:

> **1. De-coupling**

Using protocol instead of concrete implementation to de-couple the service user from service provider, will result in a flexibility of potential change

> **2. Testable**

Mocked service provider can be easly used in unit test

# How to create a dependency injection factory

Let's create a simple dependency injection factory step by step.

## 1. Interface

first we need to figure out the interface, and it is obviously simple.
```swift
public protocol ServiceAPI {
    func registerService<T>(_ type: T.Type, _ builder: @escaping () -> T)
    func getService<T>(_ type: T.Type) -> T
}
```

## 2. Factory or singleton

## 3. Parent and children

