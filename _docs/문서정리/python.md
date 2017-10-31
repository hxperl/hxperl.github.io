---
title: About Python
permalink: /docs/python/
---

# Python

[TOC]



### 1. 개요

1991 년에 발표된 프로그래밍 언어



> 창시자 : 귀도 판 로썸

### 2. 언어 구현

일반적으로 C언어로 구현되었으며, 다른 구현체와 구분해서 언급할때는 CPython으로 표기.

C#으로 구현된 닷넷프레임워크 위에서 동작하는 IronPython, Cpython에서 C 스택을 없앤 Stackless Python, Java로 구현되 있어서 JVM 위에서 돌아가는 Jython, 파이썬 자체로 구현된 Pypy등이 있다.

##### 2.1 Stackless Python

파이썬 표준 구현인 Cpython은 이름 그대로 C로 만들어져 있는데, 파이썬 프로그램의 함수 호출 스택 (Call stack)을 구현 할 때 그만 C의 호출 스택에 그대로 얹어가도록 구현되고 말았다.

때문에 파이썬에서 메모리 가용량에 관계 없이 C 호출 스택을 다 채우면 스택오버 플로우 에러가 뜨게 되 버렸다.

파이썬 프로그램의 호출 스택을 Cpython 스스로 제어할 수 없게 되어 코루틴 등의 실행 흐름을 제어하는 언어 기능을 쓸 수 없게 되고 말았다.

Christian Tismer 라는 개발자는 Cpython 소스코드를 수정해서 C 스택을 쓰는 부분을 전부 들어내고 새로 호출 스택을 짤 수 밖에 없다고 생각했고 그것을 실제로 실행에 옮긴 것이 Stackless Python 이다.

##### 2.2 Pyston

2014년 4월 프로젝트 시작.

Python 2.7 호환, x86m 64비트 플랫폼을 목표로 개발 중에 있었다.

2017년 1월 31일 부로 Dropbbox에서 공식적으로 스폰싱 종료.

Dropbox의 코드가 Go랑 Python3로 많이 이전된 것이 원인으로 보인다.

##### 2.3 Pypy

본격 Python으로 직접 구현되는 Python.

Cpython보다 느릴 것 같지만 실제로는 더 빠른 실행 결과도 보여주기도 한다.

##### 2.4 Brython

JavaScript로 구현되었고, JavaScript를 대신하여 웹 브라우저에서 스크립트 형태로 Python을 실행할 목적으로 구현되었다.

##### 2.5 Jython

Java로 구현되어 JVM 위에서 실행 된다.

Python Module이 제공하는 API는 물론이고, JDK가 제공한느 모든 API를 그대로 사용할 수 있다.

##### 2.6 IronPython

MS .NET framework의 가상머신인 CLR 상에서 구현되고 동작하는 Python이다.

##### 2.7 Jython이나 IronPython은 왜 쓰는가?

둘 다 Cpython에 비하면 실행 속도가 매우 느리지만 보조 기능에서 사용하면 번거로운 작업들을 손쉽게 해결할 수 있기 때문에 개발 공수와 편리함에서 큰 장점이 있다.

### 3. 장점

##### 3.1 높은 생산성

##### 3.2 반복 가능한 객체

파이썬의 가장 큰 특징 중 하나.

반복 가능한 객체(iterable)라는 강력한 기능 제공.

이 객체는 집합, 문자열, 리스트, 튜플, 딕셔너리, 그리고 함수까지도 반복이 가능.

##### 3.3 디자인 철학

- 아름다운 것이 추한 것보다 낫다. (Beautiful is better than ugly)
- 명시적인 것이 암시적인 것보다 낫다. (Explicit is better than implicit)
- 간결한 것이 복잡한 것보다 낫다. (Simple is better than complex)
- 정교한 것이 난잡한 것보다 낫다. (Complex is better than complicated)

##### 3.4 만능 언어

빠른 아이디어 구현이 생명인 연구소에서 각광 받고 있다.

### 4. 단점

##### 4.1 멀티스레딩 문제

GIL (Global Interpreter Lock) 때문에 10개의 쓰레드가 실행 되어도 동시에 실행가능한 쓰레드는 1개이다.