# 벤치마크

2018년 만들어진 고성능 웹 프레임워크.

플라스크와 구조가 상당히 유사하다.

Golang과 Nodejs와 속도가 비슷할 정도로 상당히 빠르다.

같은 파이썬으로 만든 장고와 플라스크에 비해서 어떻게 이렇게 빠를 수 있을까?

장고와 플라스크는 일반적으로 Golang과 비교시 10배 정도 퍼포먼스 차이가 난다고 한다.

{% embed url="https://www.techempower.com/benchmarks/\#section=test&runid=7464e520-0dc2-473d-bd34-dbdfd7e85911&hw=ph&test=query&l=zijzen-7" %}

### FastAPI 계층구조

* Uvicorn: ASGI 서버
  * Starlette: \(Uvicorn 사용\) 웹 마이크로 프레임워크
    * FastAPI: \(Starlette 사용\) API 빌드, 데이터 유효성 검사등을 위한 몇가지 추가 기능이 있는 API 마이크로 프레임 워크

### Uvicorn이란?

* [Uvicorn](https://www.uvicorn.org/)은 [uvloop ](https://github.com/MagicStack/uvloop)및 [httptools](https://github.com/MagicStack/httptools)를 사용하는 초고속 ASGI 서버 구현입니다.
* ASGI\(Asynchronous Server Gateway Interface\)는 비동기 Python 웹 서버입니다.

#### uvloop란?

* uvloop는 asyncio의 이벤트 루프 대체재로, asyncio를 더 빠르게 만들고, nodejs, gevent 및 다른 python 프레임워크와 비교해봐도, 2배이상의 퍼포먼스를 보여준다
* [asyncio](https://docs.python.org/ko/3/library/asyncio.html)는 고성능 네트워크 및 웹 서버, 데이터베이스 연결 라이브러리, 분산 작업 큐 등을 제공하는 여러 파이썬 비동기 프레임워크의 기반으로 사용됩니다.

#### httptools

* httptools는 nodejs HTTP 파서에 대한 Python 바인딩입니다.

### Starlette란?

* Starlette는 고성능 Asyncio 서비스를 구축하는 데 이상적인 가벼운 ASGI 프레임 워크/툴킷입니다.

### FastAPI

* FastAPI는 Starlette 맨 위에 더 많은 기능을 제공합니다. 데이터 유효성 검사 및 직렬화와 같은 API를 구축 할 때 거의 항상 필요한 기능을 제공합니다.
* FastAPI를 사용하지 않았고 Starlette를 직접 사용한다면 모든 데이터 유효성 검사 및 직렬화를 직렬화해야합니다. 따라서 최종 응용 프로그램은 FastAPI를 사용하여 작성된 것처럼 동일한 오버 헤드가 여전히 동일합니다.

