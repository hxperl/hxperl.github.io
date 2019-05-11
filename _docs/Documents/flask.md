---
title: Flask
permalink: /docs/flask/
---

### 1. 개요

파이썬으로 작성된 웹 어플리케이션 프레임워크.

Armin Ronacher에 의해 개발되고 있다.

Werkzeug WSGI 툴킷 및 Jinja2 템플릿 엔진을 기반으로 한다.

모두 Pocco 프로젝트이다.

### 2. Route

##### app.route()

URL 규칙을 받아 해당 규칙의 URL로 요청이 들어온경우 등록한 함수를 실행하게끔 설정

e.g)

```python
# http://flask.pocoo.org/docs/api/#url-route-registrations

@app.route('/')
def index():
    pass

@app.route('/<username>')
def show_user(username):
    pass

@app.route('/post/<int:post_id>')
def show_post(post_id):
    pass
```

##### url_for()

핸들러에서 URL을 생성하는 기능 지원

e.g)

```python
@app.route('/')
def index():
    return ""

@app.route('/<username>')
def show_user(username):
    return username

@app.route('/post/<int:post_id>')
def show_post(post_id):
    return str(post_id)

from flask import url_for

@app.route("/routes")
def routes():
    return "<br />".join([
            url_for("index"),
            url_for("show_user", username="longfin"),
            url_for("show_post", post_id=3)
            ])
```

### 3. Template

기본 템플릿 엔진으로 Jinja2 사용.

기본적으로 템플릿엔진은 별도의 규칙에 맞게 작성된 템플릿 파일을 읽어 환경(Context)에 맞게 적용한 결과물을 돌려주는데 flask에서는 ```render_template()``` 가 담당하고 있다.

### 4. Request

HTTP 요청을 다루기 위해서 때로는 'environ' 의 내용은 원시적일때가 있다.

HTML Form으로 부터 입력받는 값이 좋은 예인데, flask에서는 ```request```라는 객체를 통해 보다 다루기 쉽게 해준다.

e.g)

```python
from flask import request

@app.route("/reverse")
def reverse():
    message = request.values["message"]
    return "".join(reversed(message))
```

### 5. Session

로그인등의 상태유지를 처리하는데 ```session``` 객체 지원

flask는 기본적으로 시큐어 쿠키 (Secure Cookie)를 통해 세션을 구현하므로 길이 제한이 있다. 때문에 파일이나 DB기반의 세션을 구현하려면 Beaker와 같은 프레임워크를 통한 확장이 필요하다.

### 6. Flask - SQLAlchemy

SQLAlchemy 객체 관계형 메퍼는 DB 테이블을 이용해 사용자가 정의한 파이썬 클래스의 메소드와 각각의 행을 나타내는 인스턴스로 표현된다.

객체와 연관된 행들의 모든 변경점들이 자동으로 동기되어 인스턴스에 반영되며, 사용자가 정의한 클래스와 각 클래스 사이에 정의된 관계에 대해 쿼리할 수 있는 시스템을 포함하고 있다.



### 7. Flask 클래스 인스턴스화에 자주 사용되는 인자들

| 인자명          | 설명                                                         |
| --------------- | ------------------------------------------------------------ |
| import_name     | 어플리케이션 패키지의 이름을 지정하는 인자이며, 문자열 값으로 정의. 보통 \_\_name\_\_ 변수를 넘겨서 생성. Flask 클래스로 부터 인스턴스 생성시 꼭 필요한 인자. |
| static_url_path | static_folder를 웹에서 접근할 때 어떤 경로를 사용할 것인지 지정. 정의하지 않으면 기본값으로 static 사용. |
| static_folder   | 프로그램 소스 트리에서 정적 파일을 서비스하는 폴더명 지정. 정의하지 않으면 기본값으로 static 사용. |
| Template_folder | 프로그램 소스트리에서 뷰 함수가 사용할 HTML 파일이 위치하는 폴더명 지정. 기본값으로 templates를 사용. |

### 8. Flask 클래스로부터 객체 생성완로후 할 수 있는 작업들

- 글로벌 객체
- 사용자 응답 객체 생성
- HTTP 요청 전후에 대한 핸들러 관리
- 사용자 정의 URL 처리 함수 관리
- 미들웨어 등록을 위한 순수 WSGI 객체 접근
- 디버그 모드 설정
- 뷰 함수 등록
- 로거 사용
- 테스트 서버 실행
- 템플릿 필터 등록
- HTTP 에러 핸들러 관리
- Blueprint 사용
- 테스트 클라이언트 생성

##### 8.1 글로벌 객체

파이썬은 언어 특성상 전역 영역은 어플 전체에 걸친 영역이 아닌 모듈 단위의 영역으로 한정. Flask는 프로그래머가 전역적으로 데이터를 보관/사용 할수 있도록 글로벌 객체 제공

```python
from flask import g
```

g 객체는 Flask 인스턴스 객체의 app_ctx_globals_class 클래스의 인스턴스 변수

##### 8.2 사용자 응답 객체 생성

사용자 응답 객체를 반환하기 위해서 flask 모듈의 make_response함수 이용.

이 함수는 5가지 사용자 응답객체 지원.

| 객체 종류      | 설명                                                         |
| -------------- | ------------------------------------------------------------ |
| response_class | Response 클래스로부터 생성한 인스턴스 객체                   |
| str            | 유니코드가 아닌 일반 문자열                                  |
| unicode        | 유니코드 문자열                                              |
| WSGI 함수      | 프로그래머가 정의한 WSGI 함수는 호출되면 버퍼링된 응답 객체로 반환 |
| 튜플(tuple)    | (response, status, headers)형식을 갖춘 튜플을 인자로 제공받음.  response는 response_class, str, unicode, WSGI 함수 중 하나만 올수 있음 |

##### 8.3 HTTP 요청 전후에 대한 핸들러 관리

Flask는 HTTP 요청 전후에 사용할 수 있는 데코레이터 제공

- before_first_request: 웹 어플리케이션 기동 이후 가장 처음 들어오는 HTTP 요청에서만 실행
- before_request : HTTP 요청이 들어올 때마다 실행
- after_request : HTTP 요청이끝나 브라우져에 응답하기 전에 실행
- teardown_request : HTTP 요청의 결과가 브라우져에 응답한 다음 실행
- teardown_appcontext : HTTP 요청이 완전히 완료되면 실행, 어플리케이션 컨텍스트 내에서 실행

##### 8.4 사용자 정의 URL 처리 함수 관리

Flask 에서는 URL의 일부로 다뤄지는 파라미터를 프로그래머가 뷰 함수 내에서 객체 또는 특정한 결과로 반환받을 수 있는 방법 제공

```python
class BoardView(BaseConverter):
    def to_python(self, value):
        ...
    def to_url(self, record):
        ...
    
app.url_map.converters['board'] = BoardView

@app.route("/board/<board:reord>", endpoint="view")
def board_view_route(record):
    return url_for("view", record=record)
```

##### 8.5 미들웨어 등록을 위한 순수 WSGI 객체 접근

미들웨어는 HTTP통신 과정에서 다양한 일 처리 가능

- 타겟 URL에 따른 어플리케이션 기동에 필요한 HTTP 환경 재설정과 Request Path 재설정
- 다수의 어플리케이션 또는 다른 프레임워크의 프로그램을 같은 프로세스에서 동작 시키기
- 네트워크 대역폭 분산 처리를 위해 로드 밸런싱과 원격 처리
- 콘텐츠의 전처리

##### 8.6 뷰 함수 등록

1. route 데코레이터 사용

   ```python
   @app.route("/flask")
   def index():
       pass
   ```

2. add_url_rule 메서드 사용

   ```python
   def index():
       pass
   app.add_url_rule("/", "index", index)
   ```

   add_url_rule 메서드는 대규모 어플리케이션 제작 시에 특정 라우팅을 빼고 싶을 때 유용.

서로 기능의 차이보단 관리의 차이

둘다 공통으로 받는 인자

| 인자명      | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| default     | 사용자 URL 변수에 기본값 지정하는 옵션,  Dictionary 타입으로 전달 |
| methods     | 뷰 함수가 처리할 HTTP메서드 지정                             |
| host        | 라우팅 요청이 어떤 호스트에 응답할지 지정                    |
| subdomain   | 뷰 함수가 특정 서브 도메인에만 응답하도록 지정               |
| redirect_to | 뷰 함수가 처리하지 않고 다른 곳으로 요청 전달                |
| alias       | Endpoint 옵션과 같은 역할                                    |

