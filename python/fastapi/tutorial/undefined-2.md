# 쿼리 매개 변수 및 문자열 유효성 검사

{% embed url="https://fastapi.tiangolo.com/tutorial/query-params-str-validations/" %}

**FastAPI를** 사용하면 매개 변수에 대한 추가 정보와 유효성 검사를 선언 할 수 있습니다.

이 애플리케이션을 예로 들어 보겠습니다.

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/")
async def read_items(q: Optional[str] = None):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

 쿼리 매개 변수 `q`는 유형 `Optional[str]`이며 이는 타입이 `str`이지만 `None`일 수도 있음 을 의미하며 실제로 기본값은 `None`이므로 FastAPI는 이 매개 변수가 필수가 아님을 알게 됩니다.

`Optional[str]`은 FastAPI에 의해 직접 사용되지 않지만 당신이 사용하는 편집기에 더 나은 지원을 제공하고 오류를 감지 할 수 있습니다.

* [http://127.0.0.1:8000/items/?q=1214](http://127.0.0.1:8000/items/?q=1214)
* [python None 객체](https://docs.python.org/ko/3/c-api/none.html)

## 추가 검증

 `q`선택 사항 이지만 제공 될 때마다 **길이가 50자를 초과하지 않도록** 강제 할 것입니다 .

```python
from typing import Optional

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Optional[str] = Query(None, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

* Query 모듈 가져오기
* Query를 매개 변수의 기본값으로 사용하고 매개 변수 `max_length`를 50으로 설정합니다.
* 이렇게하면 데이터의 유효성을 검사하고 데이터가 유효하지 않은 경우 명확한 오류를 표시하며 OpenAPI 스키마 _경로 작업에_ 매개 변수를 문서화합니다 .

## 더 많은 유효성 검사 추가

```python
from typing import Optional

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: Optional[str] = Query(None, min_length=3, max_length=50)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

* `min_length`.매개 변수를 추가 할 수도 있습니다

## 정규식 추가

```python
from typing import Optional

from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(
    q: Optional[str] = Query(None, min_length=3, max_length=50, regex="^fixedquery$")
):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

이 특정 정규 표현식은 수신 된 매개 변수 값을 확인합니다.

* `^`: 다음 문자로 시작하며 이전에 문자가 없습니다.
* `fixedquery`: `fixedquery`.정확한 값을가집니다
* `$`: 거기에서 끝나고, `fixedquery`뒤에 더 이상 문자가 없습니다 .

이 모든 **"정규 표현"** 식에 대해 모르더라도 걱정하지 마십시오. 많은 사람들에게 어려운 주제입니다. 아직 정규 표현식 없이도 많은 작업을 수행 할 수 있습니다.

* [https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Regular\_Expressions](https://developer.mozilla.org/ko/docs/Web/JavaScript/Guide/Regular_Expressions)

## 기본값

위 예시의`None`을 기본값으로 사용할 첫 번째 인수로 전달할 수있는 것과 같은 방법으로 다른 값을 전달할 수 있습니다. 아래는 `"fixedquery"`기본 값을 가지게 됩니다. 아래는 따로 Optional 옵션을 주지 않아도 기본값이 존재하여 매개 변수는 선택 사항이 됩니다.

```python
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query("fixedquery", min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

## 필수로 만들기

 필요에 따라 값을 선언해야하는 경우 `Query`첫 번째 인수로 `...`사용할 수 있습니다.

```python
from fastapi import FastAPI, Query

app = FastAPI()


@app.get("/items/")
async def read_items(q: str = Query(..., min_length=3)):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}]}
    if q:
        results.update({"q": q})
    return results
```

  이렇게하면 **FastAPI** 에 이 매개 변수가 필수라는 것을 **알립니다** .

`...`전에 본 적이없는 경우 : 특수한 단일 값 [이며 Python의 일부이며 "Ellipsis"라고](https://docs.python.org/3/library/constants.html#Ellipsis) 합니다.

Ellipsis 객체는 실제로 어떤 동작도 수행하지 않으므로 `작동 없음`을 기술하는데 사용할 수도 있습니다.

