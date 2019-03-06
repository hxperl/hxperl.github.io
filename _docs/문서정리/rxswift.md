---
title: RxSwift
permalink: /docs/rxswift/
---

### 1. Observables

##### 1.1 Just

오브젝트나 오브젝트 셋을 방출하는 Observable로 변환.

```swift
let justObservable = Observable.just(5)
```

##### 1.2 Of

여러개의 값을 Observable 로 변환.

```swift
let multiObservable = Observable.of(1, 2, 3, 4)
```
1 부터 4까지 차례대로 방출한다.

###### 1.3 From

다른 객체나 데이터 구조를 Observable로 변환.

```swift
let fromObservable = Observable.from([1, 2, 3, 4])
```

##### 1.4 Range

정수 sequence 를 방출하는 Observable 생성.

```swift
let rangeObserver = Observable.range(start:1, count: 4)
```

##### 1.5 Empty

상황에 따라 아무런 값을 전송하지 않고 그저 completed event를 보내고 싶은 경우.

```swift
let emptyObserver = Observable<Void>.empty()
```

##### 1.6 Never

무한 요소를 갖는 끝나지 않는 Observable.

```swift
let neverObservable = Observable<Any>.never()
```

##### 1.7 Defer

Observer가 subscribe 하기 전까지 Observable 을 생성하지 않고, 각 Observer에 대해 새로운 Observable을 생성한다.

```swift
var toggle = false

let factory = Observable.deferred { () -> Observable<Int> in
    toggle = !toggle

    return toggle ? Observable.of(1,2,3) : Observable.of(3,4,5)
}

for _ in 0...3 {
    factory.subscribe { (event) in
        print(event.element ?? event)
    }
}

output:
1
2
3
completed
3
4
5
completed
1
2
3
completed
3
4
5
completed
```

##### 1.8 Create

프로그래밍 방식으로 Observer 메소드를 호출해서 Observable 생성.

```swift
Observable<Int>.create { (observer) -> Disposable in
    observer.onNext(1)

    observer.onError(NumError.InvalidNumber)

    observer.onNext(2)

    observer.onNext(3)

    return Disposables.create()
}
```

### 2. Subscribers

Subscriber가 없는 Observable 자체로는 의미가 없다.

```swift
let multiObserver = Observable.of(1, 2, 3, 4)

multiObserver.subscribe { (event) in
    print(event)
}

output:
next(1)
next(2)
next(3)
next(4)
completed
```

Event는 enum 타입으로 next, error, completed 의 세 가지 타입이 있다.

위 코드와 동일한 작업을 하는데 편리한 방법이 있다.

```swift
multiObserver.subscribe(onNext: { (element) in
    print(element)
}, onError: nil, onCompleted: { print("Multi is Completed") }, onDisposed: nil)
```

### 3. Disposing

Disposing 은 다른 말로 Cancelling 

```swift
let rangeObserver = Observable.range(start: 1, count: 4)

let subscription = rangeObserver.subscribe(onNext: { print($0) }, onError: nil, onCompleted: { print("range completed") }, onDisposed: { print("range disposed") })

subscription.dispose()


output:
1
2
3
4
range completed
range disposed
```

##### 3.1 DisposeBag

```swift
func range() {
    let bag = DisposeBag()
    let rangeObserver = Observable.range(start: 1, count: 4)

    let subscription = rangeObserver.subscribe(onNext: { print($0) }, onError: nil, onCompleted: { print("range completed") }, onDisposed: { print("range disposed") }).disposed(by: bag)
}

range()
```

subscription에 dispose bag 을 추가하면 bag 안에 이러한 subscription들이 저장된다.
그리고 bag 변수가 deallocate 되면 모든 subscription 들을 dispose 한다.