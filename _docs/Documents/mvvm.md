---
title: MVVM
permalink: /docs/mvvm/
---


### 1. 개요

MVVM(Model–view–viewmodel)은 비지니스 로직이나 백엔드 로직 개발에서 그래픽 사용자 인터페이스의 개발을 용이하게한다.
MVVM의 view model은 값 변환기다. 즉, view model은 모델에서 데이터 객체를 노출 또는 변환하여 객체를 쉽게 관리하고 표시하는 역할을 한다. 이러한 관점에서 view model은 뷰보다 모델이며, 대부분의 디스플레이 로직을 처리한다.

view model은 *mediator pattern*을 구현하여 뷰가 지원하는 use case 세트를 중심으로 백엔드 로직에 대한 액세스를 구성할 수 있다.
MVVM은 Martin Fowler의 Presentation Model 디자인 패턴의 변형이다. MVVM은 뷰의 상태와 동작을 같은 방식으로 추상화하지만, 프레젠테이션 모델은 특정 사용자 인터페이스 플랫폼에 종속되지 않는 방식으로 뷰를 추상화한다.

### 2. 구성 요소

##### Model

모델은 실제 상태 컨텐트 (객체 지향 접근법)를 나타내는 도메인 모델 또는 컨텐트를 나타내는 데이터 액세스 계층 (데이터 중심 접근법)을 나타낸다.

##### View

MVC (Model-View-Controller) 및 MVP (Model-View-Presenter) 패턴에서와 마찬가지로이보기는 사용자가 화면에서 보는 내용의 구조, 레이아웃 및 모양.
모델의 표현을 표시하고 뷰 (클릭, 키보드, 제스처 등)와의 사용자 상호 작용을 수신하고 데이터 바인딩 (속성, 이벤트 콜백 등)을 통해 뷰 모델에 대한 처리를 전달.
뷰와 뷰 모델을 연결하기 위해 정의 된 뷰이다.

##### View model

View model은 공용 속성과 명령을 표시하는 뷰의 추상화이다. MVC 패턴의 Controller 또는 MVP 패턴의 Presenter 대신 MVVM에는 view model에서 뷰와 바운드 속성 간의 통신을 자동화하는 Binder가 있다. view model은 모델의 데이터 상태로 표현된다.
View model과 MVP 패턴의 Presenter 간의 주요 차이점은 Presenter가 뷰에 대한 참조를 가지는 반면, view model은 그렇지 않다. 대신 뷰는 view model 속성에 직접 바인딩되어 업데이트를 보내고 받는다.

### 3. Rationale

MVVM은 WPF (Windows Presentation Foundation)의 데이터 바인딩 기능을 사용하여 뷰 레이어에서 사실상 모든 GUI 코드를 제거하여 나머지 패턴에서 뷰 레이어 개발을 쉽게 분리 할 수 ​​있도록 설계되었다. UX 개발자가 GUI 코드를 작성하는 대신, XAML과 같은 프레임 워크 markup 언어를 사용하고 애플리케이션 개발자가 관리하는 view model에 대한 데이터 바인딩을 생성 할 수 있다. 분리된 역할은 디자이너가 비즈니스 로직 프로그래밍보다 UX요구에 집중할 수 있게 해준다. 따라서, 응용프로그램 레이어를 여러 스트림으로 개발하여 생산성을 높일 수 있다.
MVVM 패턴은 가능한 pure application program model에 가까운 데이터를 바인딩하여 data binding 및 프레임 워크의 이점을 활용하면서 MVC에서 제공하는 기능 개발 분리의 장점을 모두 얻으려고한다.

