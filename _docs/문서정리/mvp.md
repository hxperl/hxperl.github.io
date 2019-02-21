---
title: MVP
permalink: /docs/mvp/
---


### 1. 개요

MVP (Model-View-Presenter)는 MVC (Model-View-Controller) 아키텍처 패턴에서 파생된 것으로 주로 사용자 인터페이스를 작성하는 데 사용한다.

MVP에서 프레젠터는 "중간자"의 기능을 가정한다. MVP에서는 모든 프리젠테이션 논리는 프레젠터에게 푸쉬된다.

- 모델은 사용자 인터페이스에서 표시되거나 작동 할 데이터를 정의하는 인터페이스이다.
- 뷰는 데이터(모델)를 표시하고 사용자 명령을 프레젠터에게 전달하여 해당 데이터를 처리하는 수동 인터페이스이다.
- 프레젠터는 모델과 뷰에 따라 행동합니다. 저장소(모델)에서 데이터를 검색하고 뷰에 표시 할 수 있도록 서식을 지정한다.

### 2. 역사

Model-view-presenter 소프트웨어 패턴은 Apple, IBM 및 Hewlett-Packard의 합작 회사 인 Taligent에서 1990 년대 초반에 시작되었다. MVP는 Taligent의 C ++ 기반 CommonPoint 환경에서의 응용 프로그램 개발을위한 기본 프로그래밍 모델이다. 이 패턴은 나중에 Taligent에서 Java로 마이그레이션되었으며 Taligent CTO 인 Mike Potel의 논문에서 대중화되었다.

Taligent가 1998 년에 중단 된 후, Dolphin Smalltalk의 Andy Bower와 Blair McGlashan은 MVP 패턴을 Smalltalk 사용자 인터페이스 프레임 워크의 기초를 형성하기 위해 채택했다. 2006 년 Microsoft는 .NET Framework에서 사용자 인터페이스 프로그래밍을위한 설명서 및 예제에 MVP를 통합하기 시작했다.