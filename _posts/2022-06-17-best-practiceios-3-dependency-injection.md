---
layout: post
title: best-practiceios-3-dependency-injection
date: 2022-06-17 09:30 +0800
categories: [BestPractice]
tags: [iOS, Swift, DI]
img_path: /assets/img/post/mvp/
---

# Why we need dependency injection

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

## de-couple




# How to create a simple dependency injection

