---
title: iptables
permalink: /docs/iptables/
---

### 1. 개요

시스템 관리자가 리눅스 커널 방화벽이 제공하는 테이블들과 그것을 저장하는 체인, 규칙들을 구성할 수 있게 해주는 user-space 프로그램이다.리눅스 시스템에서는 /usr/sbin/iptables에 설치된다. iptables라는 용어는 커널 수준의 구성 요소를 아울러 가리킬 때에도 흔히 사용된다.

### 2. 기본 용어

모든 데이터는 인터넷 상에서 패킷 폼의 형태로 전달된다. 리눅스 커널은 패킷 필터 테이블을 사용하여 들어오고 나가는 트래픽 패킷을 모두 필터링 하는 인터페이스를 제공한다.

iptables는 이러한 테이블을 설정, 유지 보수 및 검사하는 데 사용할 수 있는 command line 응용 프로그램 및 리눅스 방화벽이다. 여러 테이블을 정의 할 수 있고 각 테이블은 여러 체인을 포함 할 수 있다. 체인은 일련의 규칙이다. 각 규칙은 해당 패킷과 일치하는 경우 패킷으로 수행 할 작업을 정의한다. 패킷이 일치 할 때, TARGET이 주어진다.

TARGET은 다음의 값중 하나일 수 있다.

- ACCEPT : 패킷이 통과 할수 있음을 의미
- DROP : 패킷이 통과 할수 없음을 의미
- RETURN : 현재 체인을 건너 뛰고 다음 규칙으로 돌아가는 것을 의미

필터 테이블에는 세 개의 체인(규칙 집합)이 있다.

- INPUT - 들어오는 패킷을 제어하는데 사용된다. 포트, 프로토콜 또는 source IP주소를 기반으로 연결을 차단/허용 할 수 있다.
- FORWARD - 들어오지 만 다른 곳으로 전달되어야 하는 패킷을 필터링하는데 사용
- OUTPUT - 나가는 패킷을 필터링하는데 사용

### 3. 설치

##### 3.1 설치 명령 (Ubuntu/Debian 시스템)

```
sudo apt-get install iptables
```

##### 3.2 현재 상태 체크

```
sudo iptables -L -v
```

##### 3.3 체인 룰 정의하기

```
sudo iptables -A  -i <interface> -p <protocol (tcp/udp) > -s <source> --dport <port no.>  -j <target>
```

### 4. 일반적인 명령어들

##### 4.1 localhost에서 트래픽 활성화

```
sudo iptables -A INPUT -i lo -j ACCEPT
```

##### 4.2 HTTP, SSH, SSL port 활성화

```
sudo iptables -A INPUT -p tcp --dport 22 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 80 -j ACCEPT
sudo iptables -A INPUT -p tcp --dport 443 -j ACCEPT
```

##### 4.3 소스기반 패킷 필터링

Ex.1

```
sudo iptables -A INPUT -s 192.168.1.3 -j ACCEPT
```

Ex.2

```
sudo iptables -A INPUT -s 192.168.1.3 -j DROP
```

Ex.3

```
sudo iptables -A INPUT -m iprange --src-range 192.168.1.100-192.168.1.200 -j DROP
```

##### 4.4 다른 모든 트래픽 차단

```
sudo iptables -A INPUT -j DROP
```

##### 4.5 룰 삭제

모두 지우기

```
sudo iptables -F
```

특정 룰 지우기

```
sudo iptables -L --line-numbers
```

output example

```
Chain INPUT (policy ACCEPT)
num  target     prot opt source               destination         
1    ACCEPT     all  --  192.168.0.4          anywhere            
2    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:https
3    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:http
4    ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
```

3번 룰 삭제

```
sudo iptables -D INPUT 3
```

