---
title: "Pycharm and FastAPI"
date: 2024-10-29T05:05:32.943Z
tags: ["FastAPI","pycharm","python"]
slug: "Pycharm-and-FastAPI"
image: "../assets/posts/e0f4f9be73940c3b377c83654f6931328697e2e02ac418a8f9141766ef38c16c.png"
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:48.041Z
  hash: "be600319959a1173cdc419895354c4bdfdf7fa3db1541d46d86e3e850f356392"
---

> fastapi를 pycharm을 통해 학습해봅시다
실습환경 : Mac(M3), IDE : Pycharm

![](/assets/posts/e0f4f9be73940c3b377c83654f6931328697e2e02ac418a8f9141766ef38c16c.png)


## Python 다운로드 및 프로젝트 생성
먼저 python을 brew를 통해 다운받는다.
만약 brew가 없다면
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
brew로 다운로드
```
brew update
brew install python
python3 --version
```

pycharm 프로젝트 생성 후 requirements.txt를 생성 및 작성
```
fastapi
uvicorn[standard]
```

이제 가상환경을 생성하고 활성화한다.   
```
python -m venv env
source env/bin/activate
```
참고로 비활성화 하고싶을 땐 `deactivate`

이제 이 요구사항을 다운받는다
```
pip install -r requirements.txt
```

main.py를 생성 후 작성한다
```python
from math import trunc

from fastapi import FastAPI

app = FastAPI()

@app.get("/", description="This is our first route.", deprecated=True)
async def base_get_route():
    return {"message": "Hello World"}

@app.post("/")
async def post():
    return {"message": "Hello From the Post Route"}

@app.put("/")
async def put():
    return {"message": "Hello From the Put Route"}
```

이후  ` uvicorn main:app --reload ` 으로 실행해보면 제대로 동작한다.
Swagger 명세서를 보고싶다면 뒤에 /docs, 다른 버전으로 보려면 /redoc 붙이면 된다.

## Path Parameters
```python
from enum import Enum

from math import trunc
from pyexpat.errors import messages

from fastapi import FastAPI

app = FastAPI()

@app.get("/users")
async def list_users():
    return {"message": "list users route"}

@app.get("/users/{user_id}")
async def get_user(user_id: str):
    return {"user_id": user_id}

@app.get("/users/me")
async def get_current_user():
    return {"Message": "this is the current user"}

class FoodEnum(str, Enum):
    fruits = "fruits"
    vegetables = "vegetables"
    dairy = "dairy"

@app.get("/foods/{food_name}")
async def get_food(food_name: FoodEnum):
    if food_name == FoodEnum.vegetables:
        return {"food_name": food_name,
                "message": "you are healthy"}

    if food_name.value == "fruits":
        return {"food_name": food_name,
                "message": "you are still healthy, but like sweet things"}

    return {"food_name": food_name,
            "message": "you are not healthy"}
```

## Request Body
```python
from enum import Enum

from fastapi import FastAPI
from pydantic import BaseModel
from typing import Optional

app = FastAPI()

@app.get("/items2")
async def read_items(
        q: str
           | None = Query(
            ...,
            min_length=3,
            max_length=10,
            regex="^[a-zA-Z]*$",
            title="Sample Query String",
            description="This is a sample query string",
            deprecated=True,
            alias="item-query"
            )
        ):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}, {"item_id": "Baz"}]}
    if q:
        results.update({"q": q})
    return results

@app.get("/items2/hidden")
async def hidden_query_route(hidden_query: str | None = Query(None, include_in_schema=False)):
    if hidden_query:
        return {"hidden_query": hidden_query}
    return {"hidden_query": "Not Found"}
```

## Query Parameter and Numeric Validation
```python
from math import trunc
from pyexpat.errors import messages

from fastapi import FastAPI
from fastapi import FastAPI, Query
from pydantic import BaseModel
from typing import Optional

@app.get("/items2")
async def read_items(
        q: str
           | None = Query(
            ...,
            min_length=3,
            max_length=10,
            regex="^[a-zA-Z]*$",
            title="Sample Query String",
            description="This is a sample query string",
            deprecated=True,
            alias="item-query"
            )
        ):
    results = {"items": [{"item_id": "Foo"}, {"item_id": "Bar"}, {"item_id": "Baz"}]}
    if q:
        results.update({"q": q})
    return results
@app.get("/items2/hidden")
async def hidden_query_route(hidden_query: str | None = Query(None, include_in_schema=False)):
    if hidden_query:
        return {"hidden_query": hidden_query}
    return {"hidden_query": "Not Found"}
```
