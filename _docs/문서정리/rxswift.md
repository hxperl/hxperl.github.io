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

![marble diagram](https://hxperl.github.io/img/1_y7RxhZJnOWkgW42jzT8JlQ.png)

### 4. Transformations

Sometimes you want to transform, combine or filter the elements emitted by an observable sequence before the subscriber receives them.

##### 4.1 Map

To transform Elements emitted from an observable Sequence, before they reach their subscribers, you use the **map** operator. Imagine a transformation that multiples each value of a sequence with 10 before emitting.

![map](https://hxperl.github.io/img/1_2h-T4aCgN8eK_86_YbdoRQ.png)

```swift
Observable<Int>.of(1,2,3,4).map { value in
    return value * 10
}.subscribe(onNext: {
    print($0)
})

OUTPUT: 10 20 30 40
```

##### 4.2 FlatMap

Imagine an Observable Sequence that consists of objects that are themselves Observables and you want to create new Sequence from those. This is where *FlatMap* comes into play. *FlatMap* merges the emission of these resulting Observables and emitting these merged results as its own sequence.

![flatmap](https://hxperl.github.io/img/1_pn9jILzX6bjYB562L3qR-g.png)

```swift
let sequence1 = Observable<Int>.of(1,2)
let sequence2 = Observable<Int>.of(1,2)

let sequenceOfSequences = Observable.of(sequence1, sequence2)

sequenceOfSequences.flatMap { return $0 }.subscribe(onNext: {
    print($0)
})

OUTPUT: 1 2 1 2
```

##### 4.3 Scan

Scan starts with an initial seed value and is used to aggregate values just like reduce in Swift.

![scan](https://hxperl.github.io/img/1_EM6Goaram46b3JzvptJhhw.png)

```swift
Observable.of(1,2,3,4,5).scan(0) { seed, value in
    return seed + value
}.subscribe(onNext: {
    print($0)
})

OUTPUT: 1 3 6 10 15
```

##### 4.4 Buffer

The Buffer operator transforms an Observable that emits items into an Observable that emits buffered collections of those items.

![buffer](https://hxperl.github.io/img/1_dsOCThMOM5DR-Pn8AX3dTw.png)

```swift
SequenceThatEmitsWithDifferentIntervals
    .buffer(timeSpan: 150, count: 3, scheduler:s)
    .subscribe(onNext: {
        print($0)
    })

OUTPUT: [1] [2,3] [4] [5,6] [7] []
```

### 5. Filter

If you only want to react on next events based on certain criteria you should use a filter operator.

##### 5.1 Filter

The Basic filter Operation works similar to the swift equivalent. You just define a condition that needs to be passed and if the condition is fulfilled a *.next event* will be emitted to its subscribers.

![filter](https://hxperl.github.io/img/1_33huMkgG4HjdBKoKwMaTpQ.png)

```swift
Observable.of(2,30,22,5,60,1).filter{$0 > 10}.subscribe(onNext: {
    print($0)
})

OUTPUT: 30 22 60
```

##### 5.2 DistinctUntilChanged

If you just want to emit next Events if the values changed from previous ones you need to use distinctUntilChanged.

![distinctuntilchanged](https://hxperl.github.io/img/1_jZI0i4_XbT_YVnR-mz-Aaw.png)

```swift
Observable.of(1,2,2,1,3).distinctUntilChanged().subscribe(onNext: {
    print($0)
})

OUTPUT : 1 2 1 3
```

Other filter operators you should try:

- Debounce
- TakeDuration
- Skip

### 6. Combine

Combining sequence is a common Task. *RxSwift* provides a lot of operators for you.

##### 6.1 StartWith

If you want an Observable to emit a specific sequence of items before it begins emitting the items normally expected from it, use the *startWith* operator.

![startwith](https://hxperl.github.io/img/1_r8JwfAjyrvJKM0PqchNiXg.png)

```swift
Observable.of(2,3).startWith(1).subscribe(onNext: {
    print($0)
})

OUTPUT: 1 2 3
```

##### 6.2 Merge

You can combine the output of multiple Observables so that they act like a single Observable, by using the *Merge* operator.

![merge](https://hxperl.github.io/img/1_ARwzMPTFUptBXSyuv1THTA.png)

```swift
let publish1 = PublishSubject<Int>()
let publish2 = PublishSubject<Int>()

Observable.of(publish1, publish2).merge().subscribe(onNext: {
    print($0)
})

publish1.onNext(20)
publish1.onNext(40)
publish1.onNext(60)
publish2.onNext(1)
publish1.onNext(80)
publish2.onNext(2)
publish1.onNext(100)

OUTPUT: 20 40 60 1 80 2 100
```

##### 6.3 Zip

You use the **Zip** method if you want to merge items emitted by different observable sequences to one observable sequence. **Zip** will operate in strict sequence, so the first two elements emitted by *Zip* will be the first element of the first sequence and the first elemnet of the second sequence combined. Keep also in mind that *Zip* will only emit as many items as the number of items emitted of the source Observables that emits the fewest items.

![zip](https://hxperl.github.io/img/1_ddPtQlF3uI6PwBbCG-rhcw.png)

```swift
let a = Observable.of(1,2,3,4,5)
let b = Observable.of("a","b","c","d")

Observable.zip(a,b){ return ($0,$1) }.subscribe {
    print($0)
}

OUTPUT: (1,"a") (2,"b") (3,"c") (4,"d")
```

Other combination filters you should try:
- Concat
- CombineLatest
- SwitchLatests

### 7. Side Effects

If you want register callbacks that will be executed when certain events take place on an Observable Sequence you need to use the **doOn** Operator. It will not modify the emitted elements but rather just pass them through.

- **do(onNext:)** - if you want to do something just if a *next* event happend
- **do(onError:)** - if errors will be emitted and
- **do(onCompleted:)** - if the sequence finished successfully.

```swift
Observable.of(1,2,3,4,5).do(onNext: {
    $0 * 10
}).subscribe(onNext: {
    print($0)
})
```

### 8. Schedulers

Operators will work on the same thread as where the subscription is created. In RxSwift you use schedulers to force operators do their work on a specific queue. You can also force that the subscription should happen on a specific Queue. You use *subscribeOn* and *observeOn* for those tasks. If you are familiar with the concept of operation-queues or dispatch-queues this should be nothing special for you. A scheduler can be serial or concurrent similar to GCD or OperationQueue. There are 5 types of Schedulers in RxSwift:

- **MainScheduler** - Abstracts work that needs to be performed on MainThread. In case schedule methods are called from the main thread, it will perform the action immediately without scheduling.This scheduler is usually used to perform UI work.
- **CurrentThreadScheduler** - Schedules units of work on the current thread. This is the default scheduler for operators that generate elements.
- **SerialDispatchScheduler** - Abstracts the work that needs to be performed on a specific dispatch_queue_t. It will make sure that even if a concurrent dispatch queue is passed, it's transformed into a serial one.Serial schedulers enable certain optimizations for observeOn.The main scheduler is an instance of SerialDispatchQueueScheduler
- **ConcurrentDispatchQueueScheduler** - Abstracts the work that needs to be performed on a specific dispatch_queue_t. You can also pass a serial dispatch queue, it shouldn't cause any problems. This scheduler is suitable when some work needs to be performed in the background.
- **OperationQueueScheduler** - Abstracts the work that needs to be performed on a specific NSOperationQueue. This scheduler is suitable for cases when there is some bigger chunk of work that needs to be performed in the background and you want to fine tune concurrent processing using maxConcurrentOperationCount.

```swift
let publish1 = PublishSubject<Int>()
let publish2 = PublishSubject<Int>()

let concurrentScheduler = ConcurrentDispatchQueueScheduler(qos: .background)

Observable.of(publish1,publish2)
          .observeOn(concurrentScheduler)
          .merge()
          .subscribeOn(MainScheduler())
          .subscribe(onNext:{
    print($0)
})

publish1.onNext(20)
publish1.onNext(40)

OUTPUT: 20 40 
```