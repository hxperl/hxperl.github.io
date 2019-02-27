---
title: RxSwift
permalink: /docs/rxswift/
---

### 1. Observablie Sequences

Everything in **RxSwift** is an **observable sequence** or something that operates on or subscribes to events emitted by an **observable sequence**.

*Arrays, Strings or Dictionaries* will be converted to **observable sequences** in *RxSwift*. You can create an *observable sequence* of any Object conforms to the **Sequence Protocol** from the Swift Standard Library.

create some observable sequences:

```swift
let helloSequence = Observable.just("Hello Rx")

let fibonacciSequence = Observable.from([0,1,1,2,3,5,8])

let dictSequence = Observable.from([1:"Hello", 2:"World"])
```

Subscribe to observable sequences by calling **subscribe(on:(Event<T>) -> ()).**