# FastAPI

## FastAPI

* FastAPI는 속도가 빠르다 \(Golang과 Js와 비견될 정도\)
* API 문서를 자동으로 생성 해준다.
* 타입 체크가 가능하다.
* 플라스크와 구조가 유사하다.
* 공식 사이트가 문서화가 잘 되어 있다.
* FastAPI는 [Starlette](https://www.starlette.io/)와 [Pydantic](https://pydantic-docs.helpmanual.io/)를 사용합니다.

개인적으로 [플라스크](https://palletsprojects.com/p/flask/)를 취미로 공부하면서, 이 프레임워크를 사용할 필요가 있나? 란 생각을 하게 됐었는데 \(느린 속도\) FastAPI가 그런 부분들을 상쇄 시킬 수 있을 것으로 보여 해당 프레임워크를 사용해 볼 예정이다.

### Starlette

* Starlette는 고성능 Asyncio 서비스를 구축하는 데 이상적인 가벼운 ASGI 프레임 워크/툴킷입니다.
*  Starlette이 강력한 성능을 보장하는 이유는 내부적으로 [uvicorn](https://github.com/encode/uvicorn)을 사용하고 있기 때문이다

### Pydantic

* 데이터 구문 분석 및 유효성 검사에 유용한 라이브러리.
* 파이썬은 타입 선언을 사용하지 않는데, 해당 부분을 타입 선언을 사용하게 만들어 개발자의 실수를 줄일 수 있도록 하는 라이브러리
  * typeScript 같은 것?
* python 3.5 부터 type hint라는게 추가되었고, 개발자가 해당 타입이 무엇인지 판단 할 수 있도록 도와주는 역할을 하게 됨.
* 위 프레임워크는 런타임 단에서 해당 타입을 체크하여 에러 까지 반환해 주는 역할로 보인다.



{% embed url="https://fastapi.tiangolo.com/" %}





