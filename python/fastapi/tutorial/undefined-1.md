# 요청 본문

{% embed url="https://fastapi.tiangolo.com/tutorial/body/" %}

클라이언트 \(예\) 브라우저\)에서 API로 데이터를 전송해야 하는 경우 request body\(요청 본문\)으로 전송합니다.

요청 본문은 당신의 API에 클라이언트가 전송한 데이터입니다. response body\(응답 본문\)은 당신의 API가 클라이언트로 전송하는 데이터 입니다.

API는 거의 항상 응답 본문을 보내야 합니다. 그러나 클라이언트가 항상 요청 본문을 보낼 필요는 없습니다.

요청 본문을 선언하려면 모든 기능과 이점을 갖춘 [Pydantic](https://pydantic-docs.helpmanual.io/) 모델을 사용 합니다.

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    return item
```

* pydantic의 BaseModel 가져오기
* BaseMode을 상속받은 Item 데이터 모델 생성
* 결과
  * 요청 본문을 json으로 읽는다.
  * 해당 유형을 반환한다.
  * 데이터를 검증한다.
  * 매개 변수에서 받은 데이터를 제공한다.
  * 모델에 대한 json 스키마 정의를 생성한다.

### 모델 사용

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.post("/items/")
async def create_item(item: Item):
    item_dict = item.dict()
    if item.tax:
        price_with_tax = item.price + item.tax
        item_dict.update({"price_with_tax": price_with_tax})
    return item_dict
```

* 함수 내에서 모델 객체의 모든 속성에 대해 직접 액세스 할 수 있다.
* [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs) 에서 바로 테스트 가능.

### 요청 본문 + 경로 매개 변수

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item):
    return {"item_id": item_id, **item.dict()}
```

경로 매개 변수와 요청 본문을 동시에 선언 할 수 있습니다.

FastAPI 는 경로 매개 변수와 일치하는 함수 매개 변수를 경로 에서 가져와야하며 Pydantic 모델로 선언 된 함수 매개 변수를 요청 본문에서 가져와야 함을 자동으로 인식 합니다.

### 요청 본문 + 경로 + 쿼리 매개 변수 <a id="request-body-path-query-parameters"></a>

```python
from typing import Optional

from fastapi import FastAPI
from pydantic import BaseModel


class Item(BaseModel):
    name: str
    description: Optional[str] = None
    price: float
    tax: Optional[float] = None


app = FastAPI()


@app.put("/items/{item_id}")
async def create_item(item_id: int, item: Item, q: Optional[str] = None):
    result = {"item_id": item_id, **item.dict()}
    if q:
        result.update({"q": q})
    return result
```

 매개 변수는 다음과 같이 인식됩니다.

* 매개 변수가 경로에 선언 된 경우 경로 매개 변수로 해석 됩니다. 
* 매개 변수가 단수 형\(int, float, str, bool 등\)으로 선언 된 경우 쿼리 매개 변수로 해석 됩니다. 
* 매개 변수가 Pydantic 모델 유형으로 선언 된 경우 요청 본문 으로 해석됩니다 .

