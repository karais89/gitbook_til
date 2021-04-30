# SQL \(관계형\) 데이터베이스

{% embed url="https://fastapi.tiangolo.com/tutorial/sql-databases/" %}

**FastAPI**는 SQL \(관계형\) 데이터베이스를 반드시 사용할 필요는 없습니다. 그러나 관계형 데이터베이스를 사용하길 원한다면 사용할 수 있습니다. 여기에서 SQLAlchemy를 사용한 예를 볼 수 있습니다. 다음과 같이 SQLAlchemy에서 지원하는 모든 데이터베이스에 쉽게 적용 할 수 있습니다.

* PostgreSQL
* MySQL
* SQLite
* Oracle
* Microsoft SQL Server, etc.

이 예제에서는 단일 파일을 사용하고 Python이 통합 지원을 제공하기 때문에 SQLite를 사용합니다. 따라서 이 예제를 복사하여 그대로 실행할 수 있습니다.

나중에 프로덕션 애플리케이션의 경우 PostgreSQL과 같은 데이터베이스 서버를 사용할 수 있습니다.

## ORMs

FastAPI는 모든 데이터베이스 및 모든 유형의 라이브러리에서 작동하여 데이터베이스와 대화합니다.

일반적인 패턴은 ORM\(object-relational mapping\) 라이브러리를 사용하는 것이다.

ORM은 코드와 데이터베이스 테이블 \( "Relations"\)의 객체간에 변환 할 수있는 도구가 있습니다.

ORM을 사용하면 일반적으로 SQL 데이터베이스의 테이블을 나타내는 클래스를 만들고 클래스의 각 속성은 이름과 유형이있는 열을 나타냅니다

예를 들어 `Pet` 클래스는 SQL 테이블 `pets`를 나타낼 수 있습니다.

그리고 해당 클래스의 각 인스턴스 객체는 데이터베이스의 행을 나타냅니다.

예를 들어 개체 `orion_cat` \(Pet의 인스턴스\)은 열 유형에 대해 `orion_cat.type` 속성을 가질 수 있습니다. 그리고 해당 속성의 값은 예를 들면 다음과 같습니다. `"cat"`

이러한 ORM에는 테이블 또는 엔터티 간의 연결 또는 관계를 만드는 도구도 있습니다

이 방법으로 `orion_cat.owner` 속성을 가질 수 있으며 소유자는 테이블 소유자에서 가져온이 애완 동물의 소유자에 대한 데이터를 포함합니다.

따라서 `orion_cat.owner.name`은 이 애완 동물 소유자의 이름 \(owners 테이블의 이름 열에있는\) 일 수 있습니다. `"Arquilian"`과 같은 값을 가질 수 있습니다.

그리고 ORM은 애완 동물 개체에서 액세스하려고 할 때 해당 테이블 소유자로부터 정보를 얻기 위해 모든 작업을 수행합니다.

일반적인 ORM은 예를 들어 Django-ORM \(Django 프레임 워크의 일부\), SQLAlchemy ORM \(SQLAlchemy의 일부, 프레임 워크와 무관\) 및 Peewee \(프레임 워크와 무관\) 등입니다.

여기서는 SQLAlchemy ORM을 사용하는 방법을 살펴 보겠습니다. 비슷한 방식으로 다른 ORM을 사용할 수 있습니다.

## 파일 구조

이 예제의 경우, 다음과 같은 구조로 SQL\_APP라는 하위 디렉토리가 포함 된 `my_super_project`라는 디렉토리가 있다고 가정 해 봅시다.

```text
.
└── sql_app
    ├── __init__.py
    ├── crud.py
    ├── database.py
    ├── main.py
    ├── models.py
    └── schemas.py
```

**init**.py 파일은 빈 파일 일뿐 만 아니라 모든 모듈 \(Python 파일\)이있는 SQL\_APP가 패키지 인 Python을 알려줍니다.

이제 각 파일 / 모듈이 수행하는 것을 보겠습니다.

## SQLAlchemy 생성

{% code title="sql\_app/database.py" %}
```python
# sqlalchemy 임포트 하기
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# SQLAlchemy에 대한 데이터베이스 URL 만들기
# 이 예에서는 SQLite 데이터베이스에 "연결"하고 있습니다 (SQLite 데이터베이스로 파일 열기)
SQLALCHEMY_DATABASE_URL = "sqlite:///./sql_app.db"
# SQLALCHEMY_DATABASE_URL = "postgresql://user:password@postgresserver/db"

# SQLAlchemy 엔진 만들기
# connect_args={"check_same_thread": False} SQLite에만 필요합니다. 다른 데이터베이스에는 필요하지 않습니다.
engine = create_engine(
    SQLALCHEMY_DATABASE_URL, connect_args={"check_same_thread": False}
)

# SessionLocal 클래스 생성
# SessionLocal 클래스의 각 인스턴스는 데이터베이스 세션이 됩니다. 
# 클래스 자체는 아직 데이터베이스 세션이 아닙니다.
# 나중에 세션 (SQLAlchemy에서 가져온 세션)을 사용합니다.
# SessionLocal 클래스를 만들려면 sessionmaker 함수를 사용하십시오
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

# Base 클래스 생성
# 이제 클래스를 반환하는 함수 declarative_base()를 사용할 것입니다
# 나중에이 클래스에서 상속하여 각 데이터베이스 모델 또는 클래스 (ORM 모델)를 만듭니다.
Base = declarative_base()
```
{% endcode %}

## 데이터베이스 모델 만들기

{% code title="sql\_app/models.py" %}
```python
from sqlalchemy import Boolean, Column, ForeignKey, Integer, String
from sqlalchemy.orm import relationship

# 데이터베이스에서 Base를 가져옵니다(위에서 database.py 파일).
from .database import Base

# 상속되는 클래스를 만듭니다. 이러한 클래스는 SQLAlchemy 모델입니다.
class User(Base):
    # __tablename__ 속성은 SQLAlchemy에게 이러한 각 모델에 대해 데이터베이스에서 사용할 테이블의 이름을 알려줍니다.
    __tablename__ = "users"
    
    # 모델 속성/열을 만듭니다
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String, unique=True, index=True)
    hashed_password = Column(String)
    is_active = Column(Boolean, default=True)

    # 관계를 만듭니다.
    # 이를 위해 SQLAlchemy ORM에서 제공하는 "relationship" 함수를 사용합니다. 
    # 이것은이 테이블과 관련된 다른 테이블의 값을 포함하는 "마법"속성이 될 것입니다
    items = relationship("Item", back_populates="owner")

class Item(Base):
    __tablename__ = "items"

    id = Column(Integer, primary_key=True, index=True)
    title = Column(String, index=True)
    description = Column(String, index=True)
    owner_id = Column(Integer, ForeignKey("users.id"))

    owner = relationship("User", back_populates="items")

```
{% endcode %}

## Pydantic 모델 생성

데이터를 생성하거나 읽는 동안 공통 속성을 갖도록 `ItemBase` 및 `UserBase` Pydantic 모델 \(또는 "스키마"\)을 생성합니다.

그리고 그들로부터 상속되는 `ItemCreate` 및 `UserCreate`를 생성합니다 \(따라서 동일한 속성을 갖게 됨\), 생성에 필요한 추가 데이터 \(속성\).

따라서 사용자는 암호를 생성 할 때도 암호를 갖게됩니다.

그러나 보안을 위해 다른 Pydantic 모델에는 암호가 없습니다. 예를 들어 사용자를 읽을 때 API에서 암호가 전송되지 않습니다.

{% code title="sql\_app/schemas.py" %}
```python
from typing import List, Optional
from pydantic import BaseModel

class ItemBase(BaseModel):
    title: str
    description: Optional[str] = None


class ItemCreate(ItemBase):
    pass

# 이제 API에서 데이터를 반환 할 때 데이터를 읽을 때 사용할 Pydantic 모델(스키마)을 만듭니다.
# 예를 들어 item을 만들기 전에 할당 된 ID가 무엇인지 알 수 없지만 item을 읽을 때 (API에서 반환 할 때) 이미 해당 ID를 알고 있습니다.
class Item(ItemBase):
    id: int
    owner_id: int

    # 이제 읽기를위한 Pydantic 모델에서 내부 Config 클래스를 추가합니다. 
    # 이 Config 클래스는 Pydantic에 구성을 제공하는 데 사용됩니다. 
    # Config 클래스에서 orm_mode = True 속성을 설정합니다.
    # Pydantic의 orm_mode는 Pydantic 모델이 딕셔너리가 아니라 ORM 모델 (또는 속성이있는 다른 임의의 개체)이더라도 데이터를 읽도록 지시합니다.
    class Config:
        orm_mode = True


class UserBase(BaseModel):
    email: str


class UserCreate(UserBase):
    password: str

# 위와 같은 방식으로 사용자를 읽을 때 이제 item에 이 사용자에게 속한 items 포함되도록 선언 할 수 있습니다.
class User(UserBase):
    id: int
    is_active: bool
    items: List[Item] = []

    class Config:
        orm_mode = True
```
{% endcode %}

Pydantic의 orm\_mode는 Pydantic 모델이 딕셔너리가 아니라 ORM 모델 \(또는 속성이있는 다른 임의의 개체\)이더라도 데이터를 읽도록 지시합니다.

```python
# orm_mode 설정시 딕셔너리 형태가 아닌
id = data["id"]

# 아래 처럼도 사용 가능
id = data.id
```

## CRUD 유틸

이 파일에는 데이터베이스의 데이터와 상호 작용하는 재사용 가능한 함수가 있습니다.

CRUD는 생성, 읽기, 업데이트 및 삭제에서 제공됩니다.

...이 예에서 우리는 단지 만들고 읽고 있습니다.

sqlalchemy.orm에서 세션을 가져 오면 db 매개 변수의 유형을 선언하고 함수에서 더 나은 유형 검사 및 완성 기능을 사용할 수 있습니다.

모델 \(SQLAlchemy 모델\) 및 스키마 \(Pydantic 모델 / 스키마\)를 가져옵니다.

다음을위한 유틸리티 함수 생성 :

* ID와 email로 단일 사용자를 읽습니다. 
* 여러 users를 읽습니다. 
* 여러 Items을 읽습니다.

```python
from sqlalchemy.orm import Session

from . import models, schemas

# id로 단일 사용자를 읽습니다.
def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()


# email로 단일 사용자를 읽습니다.
def get_user_by_email(db: Session, email: str):
    return db.query(models.User).filter(models.User.email == email).first()


# 여러 사용자를 읽습니다.
def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()

# 이제 데이터를 생성하는 유틸리티 함수를 생성합니다.
# 단계는 다음과 같습니다.
# 1. 데이터로 SQLAlchemy 모델 인스턴스를 만듭니다.
# 2. add: 해당 인스턴스 개체를 데이터베이스 세션에 추가
# 3. commit: 변경 사항을 데이터베이스에 커밋하여 저장
# 4. refresh: 인스턴스를 새로 고칩니다 (생성 된 ID와 같이 데이터베이스의 새 데이터를 포함하도록).
def create_user(db: Session, user: schemas.UserCreate):
    fake_hashed_password = user.password + "notreallyhased"
    db_user = models.User(email=user.email, hashed_password=fake_hashed_password)
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user


# Items 항목을 읽습니다.
def get_items(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.Item).offset(skip).limit(limit).all()


# 각 키워드 인수를 Item에 전달하고 Pydantic 모델에서 각각을 읽는 대신 다음을 사용하여 Pydantic 모델의 데이터로 dict를 생성합니다.
# item.dict()
# 그런 다음 dict의 키-값 쌍을 키워드 인수로 SQLAlchemy 항목에 다음과 함께 전달합니다.
# Item(**item.dict())
# 그런 다음 Pydantic 모델에서 제공하지 않는 추가 키워드 인수 owner_id를 다음과 함께 전달합니다.
# Item(**item.dict(), owner_id=user_id)
def create_user_item(db: Session, item: schemas.ItemCreate, user_id: int):
    db_item = models.Item(**item.dict(), owner_id=user_id)
    db.add(db_item)
    db.commit()
    db.refresh(db_item)
    return db_item
```



