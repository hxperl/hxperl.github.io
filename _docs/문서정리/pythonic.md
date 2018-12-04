### 1. 철학과 개념

Pythonic의 사전적 정의는 관습적으로 사용되는 파이썬의 사용방법이다.

파이썬을 파이썬답고 파이썬스럽게 사용하기 위한 코드 작성 가이드라인이다.

##### 예시1

Python3 에서 삭제된 *has_key*라는 내장함수는 사전 안에 있는 key값을 찾을때 사용하는 것인데 파이썬답지 않은 코드라고 하여 Python3에서는 삭제되고 *in* 이라는 연산자를 사용하도록 가이드 됐다.

```python
def main():
    dic = {"korea":82, "japen":81}
    
    print("=== has_key ===")
    print(dic.has_key("korea"))
    print(dic.has_key("china"))
    
    print("=== in ===")
    print("korea" in dic)
    print("china" in dic)
```

*in* 연산자를 사용하는 것이 더 보기 좋다.

##### 예시2

```python
Don't compare boolean values to True or False using ==.

Yes:	if greeting:
No:		if greering == True:
Worse:	if greeting is True:
```

##### 1.1 변수

###### 1.1.1 Scope

파이썬에서 유효 범위를 계산할 때 namespace를 기반으로 계산한다. 네임스페이스에 없다면 NameError Exception을 일으킨다.

파이썬의 네임스페이스는 built-in, global, enclosed, local로 나눌 수 있다.

LEGB 규칙

| 분류     | 설명                                                         |
| -------- | ------------------------------------------------------------ |
| Built-in | 파이썬에 내장되어 있는 네임스페이스로 어디서나 사용가능      |
| global   | 전역적으로 사용가능한 네임스페이스로 파일 단위의 모듈 안에서 유효 |
| enclosed | 내부함수가 외부 함수의 변수에 접근할 수 있는 것              |
| local    | 지역적 네임스페이스로 클래스나 함수의 내부로 한정            |

###### 1.1.2 Global

변수 앞에 global 키워드를 붙여 전역변수로 사용할 수 있다.

```python
msg = "Hello"

def write():
    global msg
    msg += "World"
    print(msg)
```

###### 1.1.3 Nonlocal

지역 변수가 정의되지 않은 Nested function에서 사용된다. 지역변수도 전역변수도 아닌 의미이다.

```python
def greeting(name):
    greeting_msg = "Hello"

    def add_name():
        nonlocal greeting_msg
        greeting_msg += " "
        return ("%s%s" % (greeting_msg, name))
    
    msg = add_name()
    print(msg)
```

*nonlocal* 키워드는 파이썬3에서 추가되었다.

###### 1.1.4 Variable Shadowing

네임스페이스에 의해서 변수가 가려지게 되는 것.

###### 1.1.5 Free variable

코드에서 사용됐지만 정의되지 않은 변수. *nonlocal* 키워드로 사용한 변수.



##### 1.2 First-Class

###### 1.2.1 First-Class Citizen

프로그래밍 언어에서 first-class citizen 속성을 가진다는 것은 어떤 개체를 다른 개체의 매개변수로 전달하거나, 함수의 반환값으로 사용하거나, 변수에 값으로 할당할 수 있다는 것을 의미.



###### 1.2.2 First-Class Function

함수가 first-class citizen 속성을 가지면 first-class function이라고 한다. 변수에 함수를 할당할 수 있고 매개변수로 함수를 전달하거나 반환값으로 함수를 사용할 수 있다. 자바스크립트나 FL 에서 많이 사용하는 클로저나, 데코레이터와 같은 기능이 대표적이다.



###### 1.2.3 Higher-Order Function

first-class function과 유사한 개념으로 고차 함수라고 주로 표현. 함수가 매개변수로 전달되거나, 함수를 반환값으로 사용할 때 higher-order function 이라고 한다.



###### 1.2.4 Nested Function

내부의 함수들이 scope chain에 의해서 자신을 감싸고 있는 외부 함수의 메모리에 접근 가능. 정확하게는 외부 함수의 메모리를 복사해서 가지고 있다. LEGB규칙 중 enclosed에 해당.



###### 1.2.5 Closure

사전적 정의: first-class function을 지원하는 언어에서 유효 범위의 이름을 바인딩하는 기술.

함수와 함수가 사용하는 환경(nonlocal 변수들)을 저장하는 것이라고 할수 있음. 클로저는 내부 함수를 반환하지만 이때 이 함수와 관련된 환경을 따로 저장하고 있어 반환된 내부 함수에서 이 자원들을 사용할 수 있다.



###### 1.2.6 Closure의 사용

1. *global* 변수를 사용하고 싶지 않을때. (*nonlocal* 변수가 대신하기 때문이다.)
2. 클래스를 사용하지 않기 위해. (클래스로 만드는 것보다 함수로 만드는것이 그리고 클로저로 구현하는 것이 효율적일 수 있다.)
3. *decorator* 를 사용하기 위해. (어떤 함수를 실행하기 전이나 실행하고 난 뒤에 특정 기능을 수행하기 위한 기능)



###### 1.2.7 Partial Application

매개변수의 일부를 미리 전달해서 래핑(wrapping) 함수를 만들고 이 래핑된 함수를 사용해 가변적인 매개변수만 매개변수로 사용하는 기법. 기본적으로 higher-order function 속성을 이용한다.



###### 1.2.8 *args와 **kwargs

*args는 non-keyworded 가변 인자를 다루고, **kwargs는 keyworded 가변 인자를 다룬다.

