# 자습서

튜토리얼 문서가 굉장히 자세히 나와있음.

FastAPI 관련 서적이 없어서, 선택지는 없지만 그래도 사이트만 봐도 충분히 어떤 프레임워크인지 알 수 있는 점이 좋은 것 같다. 실제 한국어 번역이 되어 있는 포스트가 별로 없다는게 단점이긴 하다.

사실 자습서의 내용을 굳이 다시 옮길 필요는 없을 것 같긴 한데.. 트래킹 추적 용도로 적을 예정이다.

## 자습서 - 사용자 안내서 - 도입부

이 자습서는 FastAPI의 대부분의 기능을 단계별로 사용하는 방법을 보여준다.

각 섹션은 이전 섹션을 기반으로 점진적으로 만들어 졌지만, 주제를 구분해서 구성하였기 때문에 특정 API 요구사항을 해결하기 위해 어떤 특정 항목만 선택하여 볼 수 있다.

### 코드 실행하기

```bash
uvicorn main:app --reload
```

* 자습서의 모든 코드 블록은 복사하고 직접 실행 가능하다.
* main.py 파일에 예제를 복사하고, 위 명령어를 사용하여 실행할 수 있다.

### FastAPI 설치

```bash
pip install fastapi
pip install uvicorn
```

## 첫 걸음

{% code title="main.py" %}
```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
async def root():
    return {"message": "Hello World"}
```
{% endcode %}

* `uvicorn main:app --reload`로 실행
  * `main`: 파일 `main.py` \(파이썬 "모듈"\).
  * `app`: `main.py` 내부의 `app = FastAPI()` 줄에서 생성한 오브젝트.
  * `--reload`: 코드 변경 시 서버 자동 재시작. 개발에만 사용.
* [http://127.0.0.1:8000/](http://127.0.0.1:8000/) 로 접속해 확인
* [http://127.0.0.1:8000/docs](http://127.0.0.1:8000/docs)로 접속해 자동 생성된 문서 확인
* [http://127.0.0.1:8000/redoc](http://127.0.0.1:8000/redoc)로 접속해 자동 생성된 문서 확인
* async로 선언하였는데, 따로 await 처리를 하지 않아도 문제가 없나 보다..
  * 비동기 함수 선언
  * 일반적인 경우에는 async를 사용하지 않고 def를 사용하면 된다.
  * 저런 기본적인 return 하는 부분에서 async를 사용하면 장점이 있나? 이 부분은 모르겠다. FastAPI 내부적으로 뭔가 따로 또 처리 해주는 부분이 있는지..

## 경로 파라미터

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/items/{item_id}")
async def read_item(item_id):
    return {"item_id": item_id}
```

* Python 형식에 사용되는 것과 동일한 구문을 사용하여 경로 "매개변수" 또는 "변수"를 선언할 수 있습니다.
* 경로에 지정된 itemid 값이 함수의 매개변수 itemid로 넘어옵니다.

### 경로 파라미터에 타입 지정

함수에서 표준 Python 유형을 사용하여 경로 매개 변수의 유형을 선언할 수 있습니다

```python
@app.get("/items/{item_id}")
async def read_item(item_id: int):
    return {"item_id": item_id}
```

* itemid는 int형으로 선언되어있다.
* 이렇게 하면 오류 검사, 완료 등과 함께 기능 내에서 편집자를 지원합니다. \(int가 이 아닌 경우 오류 메시지 반환\)

### 데이터 변환

* 이 예를 실행하고 [http://127.0.0.1:8000/items/3](http://127.0.0.1:8000/items/3에서) 브라우저를 열면 다음과 같은 응답이 나타납니다.
* 함수에서 받은 값\(반환된 값\)은 문자열 "3"이 아니라 파이썬 int로 3입니다.

  따라서 이 형식 선언을 통해 FastAPI는 자동 요청 "파싱"을 제공합니다.

### 데이터 검증

* 그러나 [http://127.0.0.0.1:8000/items/foo에서](http://127.0.0.0.1:8000/items/foo에서) 브라우저로 이동하면 다음과 같은 멋진 HTTP 오류가 표시됩니다.

```javascript
{
    "detail": [
        {
            "loc": [
                "path",
                "item_id"
            ],
            "msg": "value is not a valid integer",
            "type": "type_error.integer"
        }
    ]
}
```

### 표준 기반 이점, 대체 문서

생성된 스키마가 Open에서 생성되었기 때문에API 표준에는 호환 가능한 툴이 많이 있습니다.

이 때문에 FastAPI는 대체 API 설명서\(ReDoc 사용\)를 제공합니다

이와 마찬가지로 호환 가능한 툴도 많습니다. 여러 언어를 위한 코드 생성 도구를 포함합니다.

### Pydantic <a id="pydantic"></a>

모든 데이터 검증은 Pydantic에 의해 뒤에서 수행되므로 모든 이점을 얻을 수 있습니다.

str, float, boole 및 기타 여러 복잡한 데이터 형식과 동일한 형식 선언을 사용할 수 있습니다.

### 순서 문제

경로 작업을 생성할 때 고정된 경로가 있는 상황을 찾을 수 있습니다.

/users/me와 같이, 그것은 현재 사용자에 대한 데이터를 얻기 위한 것이라고 가정해 보자.

그런 다음 일부 사용자 ID에서 특정 사용자에 대한 데이터를 가져오는 /users/{user\_id} 경로를 가질 수도 있습니다.

경로 작업은 순서대로 평가되므로 /users/me에 대한 경로가 /users/{user\_id}에 대한 경로보다 먼저 선언되었는지 확인해야 합니다.

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/users/me")
async def read_user_me():
    return {"user_id": "the current user"}


@app.get("/users/{user_id}")
async def read_user(user_id: str):
    return {"user_id": user_id}
```

그렇지 않으면 /users/{user\_id}에 대한 경로도 /users/me와 일치하며 "me" 값을 가진 user\_id 매개 변수를 받는다고 "생각"합니다.

### 미리 정의된 값

경로 매개 변수를 수신하는 경로 작업이 있지만 가능한 유효한 경로 매개 변수 값을 미리 정의하려는 경우 표준 Python Enum을 사용할 수 있습니다.

#### Enum 클래스 만들기

Enum을 가져오고 Enum에서 strand를 상속하는 하위 클래스를 만듭니다.   
str에서 상속함으로써 API 문서는 값이 문자열 유형이어야 하며 올바르게 렌더링될 수 있음을 알 수 있습니다.

```python
from enum import Enum

from fastapi import FastAPI


class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"


app = FastAPI()


@app.get("/models/{model_name}")
async def get_model(model_name: ModelName):
    if model_name == ModelName.alexnet:
        return {"model_name": model_name, "message": "Deep Learning FTW!"}

    if model_name.value == "lenet":
        return {"model_name": model_name, "message": "LeCNN all the images"}

    return {"model_name": model_name, "message": "Have some residuals"}
```

* 해당 부분으로 처리할지는 생각을 해봐야 될 문제로 보임.
* 그룹핑을 지어서 처리한다고 생각하면 될 것 같은데, 엄청 자주 사용하게 될지는 의문이다.
* enum이랑 다른 값을 넘겨주면 에러를 반환함. 더 견고한 코드를 작성할 수는 있을 것으로 보임
* [http://127.0.0.1:8000/models/123](http://127.0.0.1:8000/models/123) 접속후 에러 발

```javascript
{
  "detail": [
    {
      "loc": [
        "path",
        "model_name"
      ],
      "msg": "value is not a valid enumeration member; permitted: 'alexnet', 'resnet', 'lenet'",
      "type": "type_error.enum",
      "ctx": {
        "enum_values": [
          "alexnet",
          "resnet",
          "lenet"
        ]
      }
    }
  ]
}
```

### 경로를 포함하는 경로 매개 변수

경로 작업이 /files/{file\_path}인 경우를 가정해 보겠습니다.

그러나 file\_path 자체는 home/johndoe/myfile.txt와 같은 경로를 포함해야 합니다.txt

따라서 해당 파일의 URL은 /files/home/johndoe/myfile.txt과 같습니다.

#### OpenAPI 지원 <a id="openapi-support"></a>

OpenAPI는 내부에 경로를 포함하도록 경로 매개 변수를 선언하는 방법을 지원하지 않습니다. 테스트 및 정의하기 어려운 시나리오가 발생할 수 있습니다.

그럼에도 불구하고 Starlette의 내부 도구 중 하나를 사용하여 FastAPI에서 이 작업을 수행할 수 있습니다.

매개변수에 경로가 포함되어야 한다는 문서를 추가하지 않더라도 문서는 계속 작동합니다.

#### 경로 변환

Starlette에서 직접 옵션을 사용하여 다음과 같은 URL을 사용하여 경로를 포함하는 경로 매개 변수를 선언할 수 있습니다.

`/files/{file_path:path}`

이 경우 매개 변수의 이름은 file\_path이고 마지막 부분인 :path는 매개 변수가 모든 경로와 일치해야 함을 나타냅니다.

* [http://127.0.0.1:8000/files/home/johndoe/myfile.txt](http://127.0.0.1:8000/files/home/johndoe/myfile.txt)



{% embed url="https://fastapi.tiangolo.com/tutorial/" %}



