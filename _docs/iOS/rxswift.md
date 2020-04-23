---
title: RxSwift
permalink: /docs/rswfit/
---

> 여기에 정리된 내용은 모두 RxSwift Reactive Programming with Swift - raywenderlich 책 내용을 기초로 합니다.

# 1. RxSwift

**RxSwift** is a library for composing asynchronous and event-based code by using observable sequences and functional style operators, allowing for parameterized execution via scheduler.

RxSwift는 Observable 시퀀스와 함수형 스타일 연산자를 사용하여 비동기 및 이벤트 기반 코드를 구성하기 위한 라이브러리로 스케줄러를 통한 파라미터화된 실행이 가능합니다.

RxSwift는 본질적으로 코드가 새로운 데이터에 반응하고 그것을 순차적으로 분리하여 처리할 수 있도록 함으로써 비동기 프로그램 개발을 단순화합니다.

## 1.1 Cocoa and UIKit asynchronous APIs

애플은 비동기 코드를 쓰도록 도와주는 많은 API를 iOS SDK에 제공합니다. 프로젝트에서 이런 것들을 사용했고, 모바일 앱 개발에 있어 기본적입니다.

- NotificationCenter: Device의 Orientation이 변하거나 소프트웨어 키보드가 나타나거나 사라지는 등의 이벤트가 발생할때 코드를 실행하기 위해 사용합니다.

- The delegate pattern: 다른 오브젝트를 대신하거나 다른 오브젝트와 협력하여 동작하는 객체를 정의할 수 있습니다.

- Grand Central Dispatch: 작업의 실행을 추상화하는데 도움이 됩니다. 코드의 실행을 순처적이거나 여러 작업을 동시적으로 스케줄링 할 수 있습니다.

- Closures: 코드 블럭을 생성한 다음 다른 개체가 실행 여부, 횟수 및 어떤 context에서 실행할지를 결정할 수 있습니다.

## 1.2 Asynchronouse programming glossary

1. State, and specifically, shared mutable state

    여러 비동기 구성 요소간에 공유되는 경우 앱 상태를 관리하는 것은 중요한 문제중 하나입니다.

2. Imperative programming

    Imperative 프로그래밍은 프로그램 상태를 바꾸기 위해 사용하는 프로그래밍 패러다임입니다.

3. Side effects

    Side effects는 코드의 현재 범위를 벗어나는 상태에 대한 모든 변화를 나타냅니다.
    중요한 것은 side effects가 통제된 방식으로 이루어져야 합니다. 어떤 코드 조각이 부작용을 일으키는지, 그리고 어떤 코드 조각이 단순히 데이터를 처리하고 출력하는지 결정할 수 있어야 합니다.

4. Declarative code

    Imperative 프로그래밍에서, 상태를 변경한다고 했습니다. functional 프로그래밍은 side effects를 최소화하는 것을 목표로 합니다. 이 균형은 중간 어딘가 있을 것입니다. RxSwift는 Imperative 코드와 functional 코드의 가장 좋은 측면을 결합합니다.

5. Reactive systems

    - Responsive: 항상 UI를 최신 상태로 유지하여 최신 앱 상태를 나타냅니다.
    - Resilient: 각 기능은 분리되어 정의되며 유연한 에러 복구 기능을 제공합니다.
    - Elastic: 코드는 다양한 workload를 처리하며, 종종 풀 기반 데이터 수집, 이벤트 조절 및 리소스 공유와 같은 기능을 구현합니다.
    - Message driven: 컴포넌트는 메시지 기반 통신을 사용하여 재사용 가능성과 독립성을 향상시키고, 클래스의 라이프사이클과 구현을 분리합니다.


**RxSwift**는 필수적인 Cocoa 코드와 순수 기능 코드를 찾아냅니다. 변경 불가능한 코드 정의를 사용하여 결정적이고 구성 가능한 방식으로 비동기 입력을 처리함으로써 이벤트에 반응 할 수 있습니다.


 
