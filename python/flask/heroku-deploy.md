# Heroku에 배포하기

## 목적

* 단순한 앱\(ping request를 보내면, pong을 response\)을 Heroku 서비스에 배포

## Heroku

흔한 pass 서비스. 예전에는 hot 했던 것 같은데, 요즘에 이걸 사용한다는 사람은 못본것 같다.

왜 Heroku에 배포하려고 하는가? 학습 목적이 강하다. 여러가지 서비스들을 한번씩은 사용해 보면 도움이 될 것으로 보인다.

가입 부분은 생략 \(이미 가입이 되어 있는 상태\)

### 절차

* heroku cli 툴 설치
* cmd 창에서 아래 명령어 실행

```bash
$ heroku login
heroku: Press any key to open up the browser to login or q to exit
 ›   Warning: If browser does not open, visit
 ›   https://cli-auth.heroku.com/auth/browser/***
heroku: Waiting for login...
Logging in... done
Logged in as me@example.com
```

* heroku login인데 위 처럼 실행하면 브라우저가 원래 나와야 되는 것으로 보이는데 나오지 않음. `heroku login -i` 명령어를 사용하여 아이디 및 비밀번호 입력 후 로그인



* 앱 준비

```bash
git clone https://github.com/heroku/python-getting-started.git
cd python-getting-started
```

* heroku에서 미리 준비한 장고 앱

```bash
$ heroku create
Creating app... done, ⬢ serene-caverns-82714
https://serene-caverns-82714.herokuapp.com/ | https://git.heroku.com/serene-caverns-82714.git
```

* 앱을 만들고 배포 하는 과정

```bash
$ git push heroku main
Counting objects: 407, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (182/182), done.
Writing objects: 100% (407/407), 68.65 KiB | 68.65 MiB/s, done.
Total 407 (delta 199), reused 407 (delta 199)
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Python app detected
remote:        https://serene-caverns-82714.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/serene-caverns-82714.git
 * [new branch]      revert-to-requirements -> main
 
```

* 앱 실행 중인지 확인

```bash
heroku ps:scale web=1
```

* 앱 사이트 오픈

```bash
heroku open
```

## Flask

기존 `flask run` 등을 사용하여 서버를 띄우는 방식은 개발 용도로 실제 서비스에는 적합하지 않다.

Gunicorn + Nginx 등을 사용하여 서버를 구성 해야 한다.





## 참조

* [https://flask.palletsprojects.com/en/1.1.x/deploying/\#deployment](https://flask.palletsprojects.com/en/1.1.x/deploying/#deployment)
* [https://devcenter.heroku.com/articles/getting-started-with-python](https://devcenter.heroku.com/articles/getting-started-with-python)
* [https://gunicorn.org/](https://gunicorn.org/) 

