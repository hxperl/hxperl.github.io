---
title: Concurrency Programming Guide
permalink: /docs/concurrency/
---

Concurrency는 여러가지 일이 동시에 발생한다는 개념.
멀티코어 CPU의 확산과 각 프로세서의 코어 수가 증가 할것이라는 인식에서 이를 활용할 수 있는 새로운 방법이 필요.

# 1. Concurrency and Application Design

초기에 컴퓨터가 수행 할 수 있는 단위 시간당 최대 작업량은 CPU의 클럭 속도에 의해 결정되었다.
그러나 기술이 발전하고 프로세서 디자인이 더욱 소형화되면서 열 및 기타 물리적 제약으로 인해 프로세서의 최대 클럭 속도가 제한되기 시작했다. 따라서 칩 제조업체는 칩의 성능을 향상시킬 수있는 다른 방법을 찾았다.

솔루션은 각 칩의 프로세서 코어 수를 늘리는 것이 었다. 코어 수를 늘리면 단일 칩이 CPU 속도를 높이거나 칩 크기 나 열 특성을 변경하지 않고도 초당 더 많은 명령을 실행할 수 있다.

실제로 필요한 것은 개별 애플리케이션이 추가 코어를보다 효과적으로 사용하는 방법이다.

프로그램이 여러 코어를 사용하는 일반적인 방법은 여러 스레드를 만드는 것이다. 그러나 코어 수가 증가하면 스레드 솔루션에 문제가 있다. 가장 큰 문제는 스레드 코드가 임의의 수의 코어로 잘 확장되지 않는다는 것이다. 알아야 할 것은 효율적으로 사용할 수있는 코어의 수 인데 애플리케이션이 자체적으로 계산하기 어려운 문제다. 스레드 숫자를 올바르게 처리하더라도 여전히 많은 스레드를 프로그래밍하고 효율적으로 실행하고 서로 방해하지 않도록 해야하는 어려움이 있다.

문제를 요약하면 응용 프로그램이 다양한 수의 컴퓨터 코어를 활용할 수있는 방법이 필요하다. 
단일 응용 프로그램에서 수행하는 작업량도 변화하는 시스템 조건에 맞게 동적으로 확장 할 수 있어야 한다. 
솔루션은 이러한 코어를 활용하는데 필요한 작업량을 늘리지 않을 정도로 간단해야 한다.
Apple의 운영 체제가 이러한 모든 문제에 대한 솔루션을 제공한다.

# 2. The Move Away from Threads

스레드는 수년 동안 사용되어 왔지만 계속 사용되지만 여러 작업을 확장 가능한 방식으로 실행하는 일반적인 문제를 해결하지는 못한다.

OS X와 ​​iOS는 동시성 문제를 해결하기 위해 스레드에 의존하는 대신 asynchronous design approach 디자인 방식을 사용한다. 이제 OS X와 ​​iOS는 스레드를 직접 관리하지 않고도 작업을 비동기 적으로 수행 할 수있는 기술을 제공한다.

비동기식으로 작업을 시작하는 기술 중 하나는 GCD (Grand Central Dispatch)다. 이 기술은 일반적으로 응용 프로그램에서 작성하는 스레드 관리 코드를 사용하여 해당 코드를 시스템 레벨로 이동시킨다. 실행할 작업을 정의하고 적절한 dispatch queue에 추가하기만 하면 된다. GCD는 필요한 스레드를 생성하고 해당 스레드에서 실행되도록 작업을 예약한다. 스레드 관리는 이제 시스템의 일부이므로 GCD는 작업 관리 및 실행에 대한 전체적인 접근 방식을 제공하여 기존 스레드보다 효율성을 향상 시킨다.

Operation queue는 dispatch queue와 매우 유사한 Objective-C 객체이다. 실행하려는 작업을 정의한 다음 operation queue에 추가하여 해당 작업의 예약 및 실행을 처리한다. GCD와 마찬가지로 operation queue는 모든 스레드 관리를 처리하여 시스템에서 작업을 최대한 빠르고 효율적으로 실행할 수 있도록 한다.

## 2.1 Dispatch Queues

Dispatch queue는 사용자 작업을 실행하기위한 C 기반 메커니즘이다. Dispatch queue는 작업을 순차적으로 또는 동시에 수행하지만 항상 first-in, first-out 순서로 실행한다.

- 간단하고 명확한 프로그래밍 인터페이스 지원
- 자동적이고 전체적인 스레드 풀 관리 기능을 제공
- 조정된 assembly 속도 제공
- Thread stack이 응용 프로그램 메모리에 남아 있지 않기 때문에 메모리 효율적
- 로드 중인 커널에 trap되지 않음
- Task를 dispatch queue에 비동기 적으로 dispatching하면 큐를 deadlock 할 수 없음.
- Serial dispatch queue는 lock 및 기타 동기화 기본 요소에 대한 효율적인 대안 제공

## 2.2 Dispatch Sources

dispatch source는 특정 타입의 시스템 이벤트를 비동기 적으로 처리하기위한 C 기반 메커니즘이다. 시스템 이벤트에 대한 정보를 캡슐화하고 해당 이벤트가 발생할 때마다 특정 블록 오브젝트 또는 함수를 dispatch queue에 제출한다. 다음 유형의 시스템 이벤트를 모니터링 할 수 있다.

- Timers
- Signal handlers
- Descriptor-related events
- Process-related events
- Mach prots events
- Custom events that you trigger

## 2.3 Operation Queues

Operation Queue는 concurrent dispatch queue와 동등한 Cocoa이며 NSOperationQueue 클래스로 구현 된다. 
Dispatch queue는 항상 first-in, first-out 순서로 작업을 실행하지만 Operation queue는 작업 실행 순서를 결정할 때 다른 요소를 고려한다. 요소 중 가장 중요한 것은 주어진 작업이 다른 작업의 완료에 의존하는지 여부이다. 작업을 정의 할 때 종속성을 구성하고이를 사용하여 작업에 대한 복잡한 실행 순서 그래프를 만들 수 있다.

Operation queue에 제출 한 작업은 NSOperation 클래스의 인스턴스이어야 한다. Operation object는 수행하려는 작업과 작업에 필요한 데이터를 캡슐화하는 Objective-C  object 이다. NSOperation 클래스는 기본적으로 추상 기본 클래스이므로 일반적으로 작업을 수행 할 사용자 지정 서브 클래스를 정의하기도 하지만 Foundation 프레임 워크에는 그대로 만들고 사용할 수있는 구체적인 서브 클래스가 포함되어 있다.

Operation object는 key-value observing (KVO) notification을 생성하여 작업 진행 상황을 유용하게 모니터링 할 수 있다. Operaion queue은 항상 작업을 동시에 실행하지만 종속성을 사용하여 필요할 때 순차적으로 실행되도록 할 수 있다.

# 3. Asynchronous Design Techniques

Concurrency를 지원하기 위해 코드를 다시 디자인하는 것을 고려하기 전에 그렇게 하는게 필요한 것인지 생각해봐야 한다. Concurrency는 기본 스레드가 사용자 이벤트에 자유롭게 반응 할 수 있도록하여 코드의 반응성을 향상시킬 수 있다. 또한 더 많은 코어를 활용하여 동일한 시간에 더 많은 작업을 수행함으로써 코드의 효율성을 향상시킬 수 있다. 그러나 오버 헤드가 추가되고 코드의 전체적인 복잡성이 증가하여 코드 작성 및 디버깅이 더 어려워진다.

모든 Application마다 요구 사항과 수행하는 작업이 다르다.이 문서가 Application 및 관련 작업을 디자인하는 방법을 정확하게 알려주는 것은 불가능하다. 그러나 다음 섹션에서는 디자인 프로세스 중에 올바른 선택을 할 수 있도록 몇 가지 지침을 제공한다.

## 3.1 Define Your Application’s Expected Behavior

Application에 concurrency를 추가하는 것에 대해 생각하기 전에 항상 Application의 올바른 동작으로 여기는 것을 정의해야한다. Application의 예상 동작을 이해하면 나중에 디자인을 검증 할 수 있다. 또한 concurrency를 도입하여 얻을 수있는 예상 성능 이점에 대한 정보도 제공해야한다.

가장 먼저해야 할 일은 Application이 수행하는 작업과 각 작업과 관련된 object 또는 data structure를 열거하는 것이다. 처음에는 사용자가 메뉴 아이템을 선택하거나 버튼을 클릭 할 때 수행되는 작업으로 시작할 수 있다. 이러한 작업은 개별 동작을 제공하며 시작 및 종료 지점이 잘 정의되어 있습니다. 또한 타이머 기반 작업과 같이 사용자 상호 작용없이 응용 프로그램이 수행 할 수있는 다른 유형의 작업도 열거해야 한다.

High-level task 목록이 있으면 각 작업을 작업을 성공적으로 완료하기 위해 수행해야하는 일련의 단계로 세분화해야 한다. 이 단계에서는 주로 data structure 및 object에 필요한 수정 사항과 이러한 수정 사항이 Application의 전체 상태에 어떤 영향을 미치는지 고려해야 한다. 또한 object와 data structure 사이의 종속성에 유의해야 한다. 예를 들어, 작업이 object 배열을 동일하게 변경하는 경우 한 object에 대한 변경 사항이 다른 object에 영향을 미치는지 여부는 주목할 가치가 있다.

## 3.2 Factor Out Executable Units of Work

Application 작업에 대한 이해를 통해 코드가 concurrency로 이익을 얻을 수있는 위치를 이미 식별 할 수 있어야 한다. 작업에서 하나 이상의 단계 순서를 변경했을 때 결과가 변경되면 해당 단계를 연속적으로 수행해야한다(serially). 순서를 변경해도 output에 영향을 미치지 않으면 해당 단계를 동시에 수행하는 것이 좋다(concurrently). 두 경우 모두 수행 작업 단위를 정의한다. 그런 다음 작업 단위는 block 또는 operation object를 사용하여 캡슐화하고 적절한 queue로 디스패치한다.

## 3.3 Identify the Queues You Need

task들이 별도의 작업 단위로 분류되고 block object 또는 operation object를 사용하여 캡슐화되었으므로 해당 코드를 실행하는 데 사용할 queue를 정의해야 한다. 그리고 주어진 작업에 대해 올바르게 실행해야하는 순서를 검증한다.

block을 사용하여 작업을 구현 한 경우 block을 serial 또는 concurrent dispatch queue에 추가 할 수 있다. 순서가 필요한 경우, serial dispatch queue에 추가한다. 순서가 필요하지 않은 경우 필요에 따라 concurrent dispatch queue에 추가하거나 여러 다른 dispatch queue에 추가 할 수 있다.

Operation object로 구현 한 경우 queue 선택 보단 object 구성이 더 고려된다. Operation object를 순차적으로 실행하려면 관련 object 간의 종속성을 구성해야 한다. 종속성은 종속 된 object가 작업을 완료 할 때까지 operation이 실행되지 못하게 한다.

## 3.4 Tips for Improving Efficiency

- **메모리 사용량이 중요한 경우 작업 내에서 직접 계산 값을 고려한다.** Application이 이미 메모리에 바인딩되어있는 경우 메인 메모리에서 캐시 된 값을 로드하는 것보다 값을 직접 계산하는 것이 더 빠를 수 있다. 컴퓨팅 값은 주어진 프로세서 코어의 레지스터와 캐시를 직접 사용하는데, 이는 메인 메모리보다 훨씬 빠르다. 물론 테스트 결과 성능이 우수하다고 판단되는 경우에만이 작업을 수행해야 한다.

- **lock 사용 자제**. 대부분의 상황에서 lock은 불필요하다. lock을 사용해서 공유 리소스를 보호하는 대신 되도록 Serial queue에 디스패치 하거나 Operation object 종속성을 사용한다.



 
