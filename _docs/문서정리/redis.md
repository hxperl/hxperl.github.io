### 1. 개요

Redis는 Remote Dictionary Server 의 약자로서, '키-값' 구조의 비정형 데이터를 저장하고 관리하기 위한 오픈 소스 기반의 비관계형 DBMS이다.

모든 데이터를 메모리로 불러와서 처리하는 메모리 기반 DBMS이다.

BSD라이센스를 따른다.

### 2. 지원하는 언어

[ActionScript](https://en.wikipedia.org/wiki/ActionScript), [C](https://en.wikipedia.org/wiki/C_(programming_language)), [C++](https://en.wikipedia.org/wiki/C%2B%2B), [C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)), [Chicken Scheme](https://en.wikipedia.org/wiki/Chicken_Scheme), [Clojure](https://en.wikipedia.org/wiki/Clojure), [Common Lisp](https://en.wikipedia.org/wiki/Common_Lisp), [Crystal](https://en.wikipedia.org/wiki/Crystal_(programming_language)), [D](https://en.wikipedia.org/wiki/D_(programming_language)), [Dart](https://en.wikipedia.org/wiki/Dart_(programming_language)), [Erlang](https://en.wikipedia.org/wiki/Erlang_(programming_language)), [Go](https://en.wikipedia.org/wiki/Go_(programming_language)), [Haskell](https://en.wikipedia.org/wiki/Haskell_(programming_language)), [Haxe](https://en.wikipedia.org/wiki/Haxe), [Io](https://en.wikipedia.org/wiki/Io_(programming_language)), [Java](https://en.wikipedia.org/wiki/Java_(programming_language)), [JavaScript](https://en.wikipedia.org/wiki/Server-side_JavaScript) ([Node.js](https://en.wikipedia.org/wiki/Node.js)), [Julia](https://en.wikipedia.org/wiki/Julia_(programming_language)), [Lua](https://en.wikipedia.org/wiki/Lua_(programming_language)), [Objective-C](https://en.wikipedia.org/wiki/Objective-C), [OCaml](https://en.wikipedia.org/wiki/OCaml), [Perl](https://en.wikipedia.org/wiki/Perl), [PHP](https://en.wikipedia.org/wiki/PHP), [Pure Data](https://en.wikipedia.org/wiki/Pure_Data), [Python](https://en.wikipedia.org/wiki/Python_(programming_language)), [R](https://en.wikipedia.org/wiki/R_(programming_language)),[[18\]](https://en.wikipedia.org/wiki/Redis#cite_note-18) [Racket](https://en.wikipedia.org/wiki/Racket_(programming_language)), [Ruby](https://en.wikipedia.org/wiki/Ruby_(programming_language)), [Rust](https://en.wikipedia.org/wiki/Rust_(programming_language)), [Scala](https://en.wikipedia.org/wiki/Scala_(programming_language)), [Smalltalk](https://en.wikipedia.org/wiki/Smalltalk), [Swift](https://en.wikipedia.org/wiki/Swift_(programming_language)), and [Tcl](https://en.wikipedia.org/wiki/Tcl).

### 3. 데이터 모델

키를 값에 맵핑하는 자료구저의 디렉토리.

Redis는 스트링뿐만 아니라 추상 데이터타입도 지원한다.

### 4. 지속성

Redis는 일반적으로 전체 데이터셋을 메모리에 올린다.

지속성은 두 가지 방식으로 달성되는데 하나는 snapshotting 이고 다른 하나는 semi-persistent durability mode 인데 RDB dump format 으로 쓰여진 데이터셋이 메모리에서 디스크로 비동기적으로 전송된다.

디폴트로 2초마다 디스크에 기록한다.

### 5. 복제

Redis는 master-slave 복제를 지원한다.

임의의 Redis 서버의 데이터는 임이의 수의 슬레이브로 복제 가능하며 슬레이브는 또다른 슬레이브의 마스터일 수 있다. 이를 통해 단일 복제 root tree를 구성할 수 있다. 슬레이브는 쓰기를 허용하도록 구성되어 인스턴스간의 의도된 것과 의도 하지않은 것의 비 일관성을 허용.
