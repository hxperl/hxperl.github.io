---
title: PHP
permalink: /docs/php/
---

### 1. 개요

대표적인 서버사이트 스크립트 언어로 한국을 비롯한 전 세계 수많은 웹 시스템의 기반이 되는언어. 비슷한 언어로는 ASP, JSP, CGI, ROR등이 있다. C-like 문법을 사용하며, 소규모 웹 페이지 제작시 쉽고 빠르다.

PHP라는 이름은 원래 Personal Home Page Tools이나, 지금은 Hypertext Preprocessor라는 재귀 약자를 사용하고 있다.

워드프레스, 미디어위키 등의 많은 어플리케이션이 PHP로 짜여져 있다.

### 2. 특징

다른 서버 사이드 스크립트 언어와 마찬가지로 스크립트 실행 영역이 있다.

프리프로세서가 오픈소스이다보니 이식성이 좋아서 거의 모든 웹 서버에서 실행할 수 있다. 윈도우용은 그 특성상 실행파일(바이너리)로 만들어서 제공한다.

예전에는 협업이 쉽지 않은 언어였다. MVC 패턴을 적용하기에는 PHP가 주로 사용되는 개발 환경상의 난점이 있었고, 프레임워크도 그동안 변변치 않았다. MVC 패턴은 Codeigniter와 Laravel이 나오면서 거의 대부분 해소되었다. 적용문제는 개발 환경과 개발자들의 문제가 대다수 이고 언어 자체에 결함이 있는 것은 아니다.

사실 전체적으로 잘 설계된 언어는 결코 아니다. 예를 들어 내장 함수나 인자 이름 명명에 일관성이 부족하다. 이는 필요할 때마다 그 기능을 그때그때 추가하는 방식으로 개발되었기 때문이다.

### 3. PHP 파일

- PHP 파일은 HTML, CSS, JavaScript와 PHP code를 포함할 수 있다.
- 서버사이드에서 실행되고 결과를 html형태로 브라우저에 리턴한다.
- .php확장자를 가지고 있다.

### 4. PHP로 할수 있는 것들

- 동적 웹페이지 컨텐트를 생성할 수 있다.
- 서버에서 파일 생성, 열기, 읽기, 쓰기, 삭제, 닫기가 가능하다.
- 폼데이터를 collect 할수 있다.
- 데이베이스의 데이터를 추가, 삭제, 수정할 수 있다.
- 사용자 접근 제어가 가능하다.
- 데이터 암호화

### 5. 주목할만한 언어 특징

##### 개체지향 프로그래밍

PHP는 클래스, 추상 클래스, 인터페이스, 상속, 생성장, 복제, 예외 등 완전한 개체지향 프로그래밍 개념을 가지고 있다.

- Traits - A mechanism for code reuse in single inheritance languages such as PHP.

Trait 는 class와 유사하다.

Example #1

```php
<?php
  trait ezcReflectionReturnInfo {
    function getReturnType() { /*1*/ }
  	function getReturnDescription() { /*2*/ }
}

class ezcReflectionMethod extends ReflectionMethod {
    use ezcReflectionReturnInfo;
  	/* ... */
}

class ezcReflectionFunction extends ReflectionFunction {
    use ezcReflectionReturnInfo;
  	/* ... */
}
```



##### 함수형 프로그래밍

일급 함수(first-class function)를 지원한다.

함수가 변수에 할당될 수 있다는 뜻. 

익형 함수와 클로저는 PHP 5.3 부터 지원

##### 메타 프로그래밍

Reflection API와 특수 매서드 (Magic Method)같은 메커니즘을 통해서 다양한 메타 프로그래밍 지원.

### 6. 네임스페이스

다음 두 가지 문제를 해결하기 위해 설계되었다.

1. Name collisions between code you create, and internal PHP classes/functions/constants or third-party classes/functions/constants.
2. Ability to alias (or shorten) Extra_Long_Names designed to alleviate the first problem, improving readability of source code.

Example #1

```php
<?php
namespace my\name;

class MyClass {}
function myfunction() {}
const MYCONST = 1;

$a = new MyClass;
$c = new \my\name\MyClass;

$a = strlen('hi');

$d = namespace\MYCONST;
$d = __NAMESPACE__ . '\MYCONST';
echo constant($d);
?>
```

