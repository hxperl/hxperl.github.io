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

