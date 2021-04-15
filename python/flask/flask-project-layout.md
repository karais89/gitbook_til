# 플라스크 기본 구조

처음 플라스크 프로젝트를 구성할때 아래 프로젝트 구조로 구성을 하되, 이 후 익숙해 지면 사용자가 원하는 방식으로 자유롭게 프로젝트 구조를 구성하면 된다.

{% code title="플라스크 기본 구조" %}
```text
/home/user/Projects/flask-tutorial
├── flaskr/
│   ├── __init__.py
│   ├── db.py
│   ├── schema.sql
│   ├── auth.py
│   ├── blog.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── auth/
│   │   │   ├── login.html
│   │   │   └── register.html
│   │   └── blog/
│   │       ├── create.html
│   │       ├── index.html
│   │       └── update.html
│   └── static/
│       └── style.css
├── tests/
│   ├── conftest.py
│   ├── data.sql
│   ├── test_factory.py
│   ├── test_db.py
│   ├── test_auth.py
│   └── test_blog.py
├── venv/
├── setup.py
└── MANIFEST.in
```
{% endcode %}

* `flasker/:`애플리케이션 코드와 파일을 포함하는 Python 패키지.
* `tests/:`테스트 모듈을 포함하는 디렉토리.
* `venv/:`Flask 및 기타 종속성이 설치된 Python 가상 환경.

gitignore의 경우 아래 파일을 추가한다.

{% code title=".gitignore" %}
```text
venv/

*.pyc
__pycache__/

instance/

.pytest_cache/
.coverage
htmlcov/

dist/
build/
*.egg-info/
```
{% endcode %}



[https://flask.palletsprojects.com/en/1.1.x/tutorial/layout/](https://flask.palletsprojects.com/en/1.1.x/tutorial/layout/)

