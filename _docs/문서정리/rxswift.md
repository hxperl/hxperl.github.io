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

Subscribe to observable sequences by calling subscribe(on:(Event<T>) -> ()).
The passed block will receive all events emitted by that sequence.

```swift
let helloSequence = Observable.of("Hello Rx")

let subscription = helloSequence.subscribe { event in
    print(event)
}

OUTPUT:
next("Hello Rx")
completed
```

Observable sequences can emit zero or more events over their lifetimes.
In **RxSwift** an *Event* is just an *Enumeration Type* with 3 possible states:

- **.next**(value: T) -- When a value or collection of values is added to an observable sequence it will send the *next event* to its subscribers as seen above. The associated value will contain the actual value from the seqence.
- **.error**(error: Error) -- If an Error is encountered, a sequence will emit an *error event*. This will also terminate the sequence.
- **.completed** -- If a sequence ends normally it sends a *completed event* to its subscribers

```swift
let helloSequence = Observable.from(["H", "e", "l", "l", "o"])

let subscription = helloSequence.subscribe { event in
    switch event {
        case .next(let value):
            print(value)
        case .error(let error):
            print(error)
        case .completed:
            print("completed")
    }    
}

OUTPUT:
H e l l o
completed
```

If you want to cancel a subscription you can do that by calling **dispose** on it.
You Can also add the subscription to a **Disposebag** which will cancel the subscription for you automatically on **deinit** of the *DisposeBag* Instance.
Another thing you can do is to subscribe just to a specific Event. For Example, if just want to receive the error events emitted by a sequence, you can use:
*subscribe(onError: (Error ->())).*

This Code snippet will aggregate all the things by now:

```swift
// Createing a DisposeBag so subscribtion will be cancelled correctly
let bag = DisposeBag()

// Creating an Observable Sequence that emits a String value
let observable = Observable.just("Hello Rx!")

// Creating a subscription just for next events
let subscription = observable.subscribe(onNext: {
    print($0)
})

// Adding the Subscription to a Dispose Bag
subscription.addDisposableTo(bag)
```

### 2. Subjects

A Subject is a special form of an Observable Sequence, you can subscribe and dynamically add elements to it. There are currently 4 different kinds of *Subjects* in *RxSwift*.

- **PublishSubject**: If you subscribe to it you will get all the events that will happen after you subscribed.
- **BehaviourSubject**: A behavior subject will give any subscriber the most recent element and everything that is emitted by that sequence after the subscription happend.
- **ReplySubject**: If you want to replay more than the most recent element to new subscribers on the initial subscription you need to use a *ReplaySubject*. With a *ReplaySubject*, you can define how many recent items you want to emit to new subscribers.
- **Variable**: A Variable is just a *BehaviourSubject* wrapper that feels more natural to a none reactive programmers. It can be used like a normal Variable.

##### PublishSubject

Create an actual *PublishSubject* instance.
```swift
let bag = DisposeBag()
var publishSubject = PublishSubject<String>()
```

You can add new Values to that sequence by using the **onNext()** function.
**onCompleted()** will complete the sequence and **onError(error)** will result in emitting an error event.

Let's add some values to **PublishSubject**.
```swift
publishSubject.onNext("Hello")
publishSubject.onNext("World")
```

If you subscribe to that subject after adding "Hello" and "World" using **onNext()**, you won't receive these two values through events. In contrast to a *BehaviourSubject*, that will receive "World", which is the most recent event.

Now let's create a subscription and add some new values to the Subject. We also create a second subscription and add even more values to it. Please read the comments to understand what actually is going on.

```swift
let subscription1 = publishSubject.subscribe(onNext: {
    print($0)
}).addDisposableTo(bag)

// Subscription1 receives these 2 events, Subscription2 won't
publishSubject.onNext("Hello")
publishSubject.onNext("Again")

// Sub2 will not get "Hello" and "Again" because it subscribed later
let subscription2 = publishSubject.subscribe(onNext: {
    print(#line,$0)
})

publishSubject.onNext("Both Subscriptions receive this message")
```

If you kept up reading to this point you should know the basics of *RxSwift*. There is a lot more to learn, but everything around *Rx* is based on these simple principles. 

### 3. Marble Diagrms

If you work with *RxSwift* or *Rx* in general, you should get to know **Marble Diagrams**. A **Marble Diagram* visualizes the transformation of an observable sequence. It consists of the input stream on top, the output stream at the bottom and the actual transformation function in the middle.

### 4. Transformations

