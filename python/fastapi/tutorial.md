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

{% embed url="https://fastapi.tiangolo.com/tutorial/" %}



