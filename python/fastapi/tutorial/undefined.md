# 쿼리 매개 변수

{% embed url="https://fastapi.tiangolo.com/tutorial/query-params/" %}

경로 매개 변수의 일부가 아닌 다른 함수 매개 변수를 선언하면 자동으로 쿼리 매개 변수로 해석 됩니다.

```python
from fastapi import FastAPI

app = FastAPI()

fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


@app.get("/items/")
async def read_item(skip: int = 0, limit: int = 10):
    return fake_items_db[skip : skip + limit]
```

* 쿼리는 `?`URL에서 `&`문자로 구분 된 뒤에 오는 키-값 쌍 집합입니다 .
* [python 배열 슬라이스](https://dojang.io/mod/page/view.php?id=2208)
* FastAPI에서는 자동으로 유효성 검사 실행.
* 기본값 설정 가능.

### 선택적 매개 변수

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Optional[str] = None):
    if q:
        return {"item_id": item_id, "q": q}
    return {"item_id": item_id}
```

* 이 경우 함수 매개 변수 q는 선택 사항이며 None을 기본값으로 사용한다.

### 검색어 매개 변수 유형 반환

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id: str, q: Optional[str] = None, short: bool = False):
    item = {"item_id": item_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```

* bool 유형도 선언 가능 하다.

```text
http://127.0.0.1:8000/items/foo?short=1
http://127.0.0.1:8000/items/foo?short=True
http://127.0.0.1:8000/items/foo?short=true
http://127.0.0.1:8000/items/foo?short=on
http://127.0.0.1:8000/items/foo?short=yes
```

* 위 url 모두 유효한 url로 인식한다.

### 여러 경로 및 쿼리 매개 변수

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/users/{user_id}/items/{item_id}")
async def read_user_item(
    user_id: int, item_id: str, q: Optional[str] = None, short: bool = False
):
    item = {"item_id": item_id, "owner_id": user_id}
    if q:
        item.update({"q": q})
    if not short:
        item.update(
            {"description": "This is an amazing item that has a long description"}
        )
    return item
```

* FastAPI가 자동으로 판단하여 인식한다.

### 필수 쿼리 매개 변수

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_user_item(item_id: str, needy: str):
    item = {"item_id": item_id, "needy": needy}
    return item
```

* 기본값을 선언하지 않은 경우, 반드시 쿼리 매개 변수를 url로 입력해야 한다. 하지 않으면 오류 표시 

```python
from typing import Optional

from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_user_item(
    item_id: str, needy: str, skip: int = 0, limit: Optional[int] = None
):
    item = {"item_id": item_id, "needy": needy, "skip": skip, "limit": limit}
    return item
```

* 일부 매개 변수는 필수 매개 변수, 선택적 매개 변수\(기본값 지정\), 완전 선택형 매개변수로 선언 가능.
* needy는 필수적
* skip는 선택적 기본값 지정 0
* limit는 선택적

