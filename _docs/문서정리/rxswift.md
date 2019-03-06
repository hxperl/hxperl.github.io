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

### 4. Side Effects

Side effect는 기본적으로 Observable events들에 영향을 주지 않고 작업을 수행하는 것이다.

##### 4.1 Do

```swift
let rangeObserver = Observable.ragne(start: 1, count: 4)

rangeObserver.do(
    onNext: { print("Will next", $0) },
    onError: nil,
    onCompleted: { print("Will Complete Range") },
    onSubscribe: { print("Will Subscribe") },
    onSubscribed: { print("Did Subscribe") },
    onDispose: { print("Did dispose subscription") }
    ).subscribe()
```

주로 디버깅에 사용된다.

##### 4.2 Debug

```swift
rangeObserver.debug("Range Debug", trimOutput: true).subscribe(onNext:, onError:, onCompleted:, onDisposed:)
```

디버깅할 때 유용.

### 5. Traits

- Singles
    - Success with value followed by Completed
    - Error
- Completable
    - Completed
    - Error
- Maybe
    - Success with value
    - Completed
    - Error

##### 5.1 Single

RxJava는 Observable 변형 인 *Single*을 개발했다.
Single은 Observable과 같은 것이지만 일련의 값을 내보내는 대신 항상 하나의 값 또는 오류를 내보낸다.
이러한 이유 때문에 Observable에서 세 가지 메소드(onNext, onError 및 onCompleted) 대신 Single에서는 두 가지 메소드만 사용한다.
- onSuccess
- onError

### 6. Subjects

##### 6.1 Publish Subject

PublishSubject는 구독 이후에 방출 된 항목들만을 Observer에게 방출한다.

##### 6.2 Behavior Subject

BehaviorSubject는 Observable 소스에서 가장 최근에 방출 된 항목 또는 아직 방사되지 않은 경우 시드(기본값)를 방출 한 다음 소스에서 나중에 방출되는 다른 항목을 계속 방출한다.

```swift
let subject = BehaviorSubject(value: "Initial value")
```

##### 6.3 Replay Subject

ReplaySubject는 subscription 시기와 관계없이 소스 Observable에서 방출한 값들을 버퍼에 유지하고 그 버퍼를 구독중인 모든 Observer에게 방출한다.
임의로 버퍼 사이즈를 지정할 수 있다.
ReplaySubject를 옵저버로 사용하는 경우 onNext 메서드를 여러 스레드에서 호출하지 않도록 주의해야한다.

```swift
let subject = ReplaySubject<String>.create(bufferSize: 2)
```

##### 6.4 Async Subject

AsyncSubject는 Observable 소스가 방출 한 마지막 값을 보내고 완료한다. 완료되기 전까진 아무런 값도 보내지 않는다.


### 7. Relay

Relay는 Subject와 유사하지만 두 가지 다른 점이 있다.
1. 완료 되지 않음
2. 에러를 보내지 않음

### 8. Ignore Operator

Observable에서 어떤 항목도 방출하지 않고 종료 통지만 반영된다.

```swift
let progress = PublishSubject<Float>()

progress.ignoreElements().subscribe({ print($0) })

progress.onNext(0.2)

progress.onNext(0.6)

progress.onNext(0.9)

progress.onCompleted()

output:
completed
```

임의 갯수 만큼 무시
```swift
let login = PublishSubject<String>()

login.elementAt(2).subscribe( { print($0) } )

login.onNext("One") // ignore

login.onNext("Two") // ignore

login.onNext("Three") // 3번째 출력이후 자동 완료 된다.

login.onNext("Four")

output:
next(Three)
completed
```

### 9. Skip Operator

##### 9.1 skip

```swift
Observable.from(Array(1...10)).skip(7).subscribe({ print($0) } )

output:
next(8)
next(9)
next(10)
completed
```

##### 9.2 skipWhile

```swift
Observable.from([2, 4, 6, 7, 8, 9]).skipWhile({ $0 % 2 == 0 }).subscribe({ print($0) } )

output:
next(7)
next(9)
next(9)
completed
```

##### 9.3 skipUntil

```swift
let subject = PublishSubject<Int>()

let trigger = PublishSubject<Void>()

subject.skipUntil(trigger).subscribe({ print($0) })

subject.onNext(1)

subject.onNext(2)

trigger.onNext(()))

subject.onNext(3)

subject.onNext(4)

output:
next(3)
next(4)
```

### 10. Take Operator

Skip opertor 의 반대

##### 10.1 take

```swift
Observable.from(Array(0...10)).take(4).subscribe({print($0)})

output:
next(0)
next(1)
next(2)
next(3)
completed
```

##### 10.2 takeWhile

```swift
Observable.from([2, 4, 6, 7, 8, 9]).takeWhile({ $0 % 2 == 0 } ).subscribe( { print($0) } )

output:
next(2)
next(4)
next(6)
completed
```

##### 10.3 takeUntil

```swift
let subject = PublishSubject<Int>()

let trigger = PublishSubject<Void>()

subject.takeUntil(trigger).subscribe({ print($0) })

subject.onNext(1)

subject.onNext(2)

trigger.onNext(())

subject.onNext(3)

subject.onNext(4)

output:
next(1)
next(2)
completed
```

### 11. Distinct Operator

##### 11.1 distinctUntilChanged

각 element가 이전 element와 구별 되어야함.

```swift
Observable.from([0, 0, 1, 1, 1, 2, 3, 3, 3, 3]).distinctUntilChanged().subscribe({ print($0) })

output:
next(0)
next(1)
next(2)
next(3)
completed
```

##### 11.2 custom

```swift
Observable.from([0, 1, 0, 2, 1, 0, 3, 2, 0, 4]).distinctUntilChanged { (first, second) -> Bool in
    return first > second // false 인 경우에 방출
}.subscribe({ print($0) })

output:
next(0)
next(1)
next(2)
next(3)
next(4)
completed
```

### 12. Share Operator

하나의 Observable에 대한 모든 subscriber가 같은 값을 전달 받아야 하는 경우.

```swift
let observable = Observable<Int>.create{ ... }.share(replay:, scope:)
```

share Observable 에서 임의로 onCompleted를 호출하지 않도록 해야한다.

