---
title: RxSwift
permalink: /docs/rxswift_old/
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

##### 1.3 From

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

### 13. Map Operator

일반적인 functional programming language에서의 map 기능과 유사함.

```swift
var dates = [Date]()

for day in 0...5 {
    guard let date = Calendar.current.date(byAdding: .day, value: day, to: Date()) else { return }
    dates.append(date)
}

let formatter = DateFormatter()
formatter.dateStyle = .medium
formatter.timeStyle = .none

Observable.from(dates).map({ formatter.string(from: $0) }).subscribe({ print($0) })
```

###### enumerated

인덱스를 참조해야 하는 경우.

```swift
Observable.of("Apple", "Amazon", "Alphbet").enumerated().map({ "Rank \($0.index + 1) = \($0.element)"}).subscribe({print($0)})

output:
next(Rank 1 = Apple)
next(Rank 2 = Amazone)
next(Rank 3 = Alphabet)
```

### 14. Flatmap Operator

Observable에 의해 방출 된 항목을 Observables로 변환 한 다음, 단일 Observable로 배출을 평평하게한다.

##### without flapMap

```swift
struct Student {
    var score: BehaviorSubject<Int>
}

func flatMap() {
    let student1 = Student(score: BehaviorSubject<Int>(value: 70))
    let student2 = Student(score: BehaviorSubject<Int>(value: 80))

    let bag = DisposeBag()

    let subject = PublishSubject<Student>()

    subject.map({ $0.score }).map({ $0.subscriebe({ print($0) }).disposed(by: bag) }).subscribe({print($0, "== Void")}).disposed(by: bag)

    // subject.flatMap({ $0.score }).subscribe({ print($0) }).disposed(by: bag)

    subject.onNext(student1) // 70

    student1.score.onNext(90)

    subject.onNext(student2) // 80

    student2.score.onNext(100)
}

output:
next(70)
next(()) == Void
next(90)
next(80)
next(()) == Void
next(100)
```

##### with flatMap

```swift
struct Student {
    var score: BehaviorSubject<Int>
}

func flatMap() {
    let student1 = Student(score: BehaviorSubject<Int>(value: 70))
    let student2 = Student(score: BehaviorSubject<Int>(value: 80))

    let bag = DisposeBag()

    let subject = PublishSubject<Student>()

    // subject.map({ $0.score }).map({ $0.subscriebe({ print($0) }).disposed(by: bag) }).subscribe({print($0, "== Void")}).disposed(by: bag)

    subject.flatMap({ $0.score }).subscribe({ print($0) }).disposed(by: bag)

    subject.onNext(student1) // 70

    student1.score.onNext(90)

    subject.onNext(student2) // 80

    student2.score.onNext(100)
}

output:
next(70)
next(90)
next(80)
next(100)
```

### 15. Materialize

materialize가 필요한 이유

```swift
struct Student {
    var score: BehaviorSubject<Int>
}

enum StudentError: Error { case unknown }

func materialize() {
    let student1 = Student(score: BehaviorSubject<Int>(value: 70))

    let student2 = Student(score: BehaviorSubject<Int>(value: 80))

    let subject = BehaviorSubject<Student>(value: student1)

    let bag = DisposeBag()

    let observable = subject.flatMap({ $0.score })

    observable.subscribe({ print($0) }).disposed(by: bag)

    student1.score.onError(StudentError.unknown)  // outter subscriber 까지 종료 시킴

    student1.score.onNext(90) 

    subject.onNext(student2) // subject 가 이미 종료되었음

    student2.score.onNext(100)
}

materialize()

output:
next(70)
error(unknown) 
```

내부 subject(student1)에서 error 이벤트를 받아 바깥 subject 까지 종료되어 버림.

```swift
struct Student {
    var score: BehaviorSubject<Int>
}

enum StudentError: Error { case unknown }

func materialize() {
    let student1 = Student(score: BehaviorSubject<Int>(value: 70))

    let student2 = Student(score: BehaviorSubject<Int>(value: 80))

    let subject = BehaviorSubject<Student>(value: student1)

    let bag = DisposeBag()

    let observable = subject.flatMap({ $0.score.materialize() })

    observable.subscribe({ print($0) }).disposed(by: bag)

    student1.score.onError(StudentError.unknown)  // studen1 만 종료.

    student1.score.onNext(90) // ignore

    subject.onNext(student2) 

    student2.score.onNext(100)
}

materialize()

output:
next(next(70))
next(error(unknown))
next(next(80))
next(next(100))
```

materialize 메소드로 이벤트를 받도록 변경하면 다른 내부 subject가 error로 종료되더라도 다른 subject에 대한 이벤트를 계속 받을 수 있다.

### 16. Concat

##### 16.1 Concat

Concat 연산자는 여러 Observable의 출력을 연결하여 단일 Observable처럼 작동한다. 
첫 번째 Observable에서 방출 된 모든 항목은 두 번째 Observable에서 방출 된 항목보다 먼저 방출된다.
concat 연산에 사용되는 Observable은 모두 같은 타입이어야 한다.

```swift
let first = Observable.from(Array(1...3))
let second = Observable.from(Array(4...6))
let third = Observable.from(Array(7...9))

Observable.concat(first, second, third).subscribe({ print($0) })

// first.concat(second).concat(third).subscribe({ print($0) })

output:
next(1)
next(2)
next(3)
next(4)
next(5)
next(6)
next(7)
next(8)
next(9)
completed
```

##### 16.2 ConcatMap

```swift
let dict = ["first": Observable.from(Array(1...3)), "second": Observable.from(Array(4...6))]

Observable.of("first", "second").concatMap { (key) -> Observable<Int> in
    return dict[key] ?? .empty()
}.subscribe({ print($0) })

output:
next(1)
next(2)
next(3)
next(4)
next(5)
next(6)
completed
```

### 17. Merge Operator

여러 Observable을 하나의 Observable로 병합

```swift
let first = PublishSubject<Int>()

let second = PublishSubject<Int>()

let observable = Observable.of(first, second)

let merge = observable.merge() // 하나의 Observable로 병합

merge.subscribe( { print($0) })

second.onNext(1)

first.onNext(2)

first.onNext(3)

second.onNext(4)

output:
next(1)
next(2)
next(3)
next(4)
```

병합된 Subject 중에 완료된 것이 있다면 해당 subject에 대해서만 더이상 값을 받지 않는다. 하지만 Error 의 경우 전체 Observable이 중단 된다.

###### additional parameter (maxConcurrent)

```swift
let first = PublishSubject<Int>()

let second = PublishSubject<Int>()

let observable = Observable.of(first, second)

let merge = observable.merge(maxConcurrent: 1) // set 1

merge.subscribe( { print($0) })

second.onNext(1)

first.onNext(2)  // first 에 대한 값만 방출

first.onNext(3)  // first 에 대한 값만 방출

second.onNext(4)

output:
next(2)
next(3)
```

네트워크 요청을 병합하는경우에 유용하다.

### 18. Combine Latest

Observable 중 하나가 항목을 방출 할 때 각 Observable이 방출하는 최신 항목을 결합하고이 함수의 결과에 따라 항목을 내보낸다.

```swift
let first = PublishSubject<Int>()

let second = PublishSubject<Float>()

Observable.combineLatest(first, second) { (firstLatest, secondLatest) -> String in
    return "\(firstLatest), \(secondLatest)"
}.subscribe({ print($0) })

second.onNext(0.1)      //  no emit

first.onNext(1)         //  (1, 0.1)

first.onNext(2)         //  (2, 0.1)

second.onNext(0.5)      //  (2, 0.5)
```

error 이벤트를 받을 경우 전체 Observable은 종료된다.

### 19. Zip

CombineLatest와 유사하지만 새로 방출된 값에 대해서만 결합하며 방출된 값은 다시 사용하지 않는다.

```swift
enum Weather { case sunny, cloudy }

let first = Observable<Weather>.of(.sunny, .cloudy, .sunny)

let second = Observable.of("LA", "SF", "NY", "Boston")

Observable.zip(first, second) { (weather, city) -> String in
    return "It's \(weather) day in \(city)"
}.subscribe({ print($0) })

output:
next(It's sunny day in LA)
next(It's cloudy day in SF)
next(It's sunny day in NY)
completed
```

error 이벤트를 받을 경우 아무런 값도 출력하지 않고 전체 Observable은 종료된다.

### 20. Trigger

trigger operator 또한 combine operator 이다.

##### 20.1 withLatestFrom

```swift
let nameTextField = PublishSubject<String>()

let updateButton = PublishSubject<Void>()   // trigger

updateButton.withLatestFrom(nameTextField).subscribe({ print($0) }) 

nameTextField.onNext("Apoo")

nameTextField.onNext("Apoor")

updateButton.onNext(())     // next(Apoor)

nameTextField.onNext("Apoorv")

updateButton.onNext(())     // onNext(Apoorv)

updateButton.onNext(())     // onNext(Apoorv)

output:
next(Apoor)
next(Apoorv)
next(Apoorv)
```

##### 20.2 sample

withLatestFrom과 유사하지만 trigger 되기 위해서는 이전 값과 다른 새로운 값이 방출되야 한다.

```swift
let nameTextField = PublishSubject<String>()

let updateButton = PublishSubject<Void>()   // trigger

nameTextField.sample(updateButton).subscribe({ print($0) })

nameTextField.onNext("Apoo")

nameTextField.onNext("Apoor")

updateButton.onNext(())     // onNext(Apoor)

updateButton.onNext(())     // nameTextField 의 값이 변경되지 않았기 때문에 아무런 값도 출력하지 않음

nameTextField.onNext("Apoorv")

updateButton.onNext(())     // onNext(Apoorv)

output:
onNext(Apoor)
onNext(Apoorv)
```

### 21. Ambiguous

첫 번째 이벤트를 받은 observable에 대해서만 통과시킨다.

```swift
let odd = PublishSubject<Int>()

let even = PublishSubject<Int>()

let observable = odd.amb(even)

observable.subscribe({ print($0) })

odd.onNext(1)       // odd의 이벤트가 먼저 들어왔기 때문에 odd에 대한 이벤트만 받게 된다.

even.onNext(2)      // ignore

odd.onNext(3)       

even.onNext(4)      // ignore

odd.onNext(5)

output:
next(1)
next(3)
next(5)
```

```swift
let odd = PublishSubject<Int>()

let even = PublishSubject<Int>()

let observable = odd.amb(even)

observable.subscribe({ print($0) })

// odd.onNext(1)  

even.onNext(2)      // even이 먼저 이므로 이에 대한 이벤트만 통과하게 된다.

odd.onNext(3)       // ignore

even.onNext(4)     

odd.onNext(5)       // ignore

output:
next(2)
next(4)
```

### 22. Switch Latest

amb 와 유사하다.

```swift
let first PublishSubject<Int>()

let second = PublishSubject<Int>()

let third = PublishSubject<Int>()

let subject = PublishSubject<Observable<Int>>()

subject.switchLatest().subscribe({ print($0) })

subject.onNext(first)   // subcribe 'first' only

first.onNext(1)

first.onNext(2)
 
subject.onNext(second)  // unsubscribe 'first', subscribe 'second' only

first.onNext(3)     //  전달 x

second.onNext(4)

second.onNext(5)

subject.onNext(third)   // unsubscribe 'second', subscribe 'third' only

first.onNext(6)     // 전달 x

second.onNext(7)    // 전달 x

third.onNext(8)

third.onNext(9)

output:
onNext(1)
onNext(2)
onNext(4)
onNext(5)
onNext(8)
onNext(9)
```

### 23. Reduce & Scan

##### 23.1 Reduce

Observable에 의해 방출 된 각 항목에 순차적으로 함수를 적용하고 최종 값을 내보낸다.
완료되기전까진 값을 방출하지 않는다.

```swift
Observable.from(Array(1...5)).reduce(0, accumulator: +).subscribe({ print($0) })

output:
next(15)
completed
```
###### custom logic

```swift
Observable.from(Array(1...5)).reduce(0) { (summery, next) -> Int in
    return summery + next
}.subscribe({ print($0) })

output:
next(15)
completed
```

##### 23.2 Scan

Observable에 의해 방출 된 각 항목에 함수를 순차적으로 적용하고 각 연속 값을 방출한다.
scan은 reduce와 달리 완료될 필요가 없다.

```swift
Observable.from(Array(1...5)).scan(0, accumulator: +).subscribe({ print($0) })

output:
next(1)
next(3)
next(6)
next(10)
next(15)
completed
```

### 24. Replay

Subject뿐만 아니라 Observable에도 추가 할 수 있다.
Observable이 항목을 방출하기 시작한 후에 구독하는 경우에도 Observer들이 동일한 항목 순서를 보도록 한다.
Subscribe 할 때 항목을 내보내지 않고 Connect 연산자가 적용될 때만 항목을 방출한다. 이 방법으로 Observable이 선택한 시간에 항목을 내보내도록 요청할 수 있다.

```swift
let elementsPerSecond: Double = 1

let bufferSize = 1

let delay: TimeInterval = 3

let sourceObservable = Observable<Int>
    .interval(RxTimeInterval(1/elementsPerSecond), scheduler: MainSchedular.instance)
    .replay(bufferSize)

_ = sourceObservable.subscribe({ print("Control", $0) })

DispatchQueue.main.asyncAfter(deadkine: .now() + delay) {
    _ = sourceObservable.subscribe({ print("Delay", $0)})
}

_ = sourceObservable.connect()

output:
Control next(1)
Control next(2)
Control next(3)
Delay next(3)
Control next(4)
Delay next(4)
Control next(5)
Delay next(5)
```

### 25. Delay

Observable의 배출량을 특정 시점까지 시간상으로 이동시킨다.

### 26. Buffer

Observable에 의해 방출 된 항목을 번들로 주기적으로 수집하고 한 번에 하나씩 항목을 내보내는 대신 이러한 번들을 방출한다.
Buffer 연산자는 항목을 내보내는 Observable을 해당 항목의 buffered collections를 방출하는 Observable로 변환한다.

```swift
let timeout: RxTimeInterval = 4

let capacity = 2        // buffer 가용량

let subject = PublishSubject<Int>()

_ = subject.subscribe({ print("Control", $0) })

_ = subject
    .buffer(timeSpan: timeout, count: capacity, scheduler: MainScheduler.instance)
    .map({ $0.count })  // 버퍼 내부 요소 개수 출력
    .subscribe({ print("Buffer Count", $0) })

DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
    Array(1...3).forEach({ subject.onNext($0) })
}

output:
Buffer Count next(0)
Control next(1)
Control next(2)
Buffer Count next(2)
Control next(3)
Buffer Count next(1)
Buffer Count next(0)
Buffer Count next(0)
```

### 27. Window Buffer

Window는 Buffer와 비슷하지만 Observable 소스에서 번들을 내보내는 대신 Observable 을 내보낸다. 각각은 Observable 소스에서 항목의 하위 집합을 내보내고 onCompleted 알림으로 종료된다.

### 28. Timeout

Timeout 연산자를 사용하면 Observable이 지정된 시간 동안 항목을 방출하지 못하는 경우 onError 종료로 Observable을 중단 할 수 있다.

