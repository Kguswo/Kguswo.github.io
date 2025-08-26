---
title: "Pycharm and FastAPI - (2)"
description: "이어서 FastAPI 기본 학습 기록입니다"
date: 2024-10-29T11:54:19.420Z
tags: ["FastAPI","pycharm","python"]
slug: "Pycharm-and-FastAPI-2"
thumbnail: "/assets/posts/869fedea14d6a66694e5d6d745994096c9b56cc518eb763b6d6be16296d0752a.png"
toc: true
velogSync:
  lastSyncedAt: 2025-08-26T02:05:46.646Z
  hash: "e5ef5368dc42b58357cccf55db4819634a573d46b553e0dc9568d5f9b3c462a4"
---

> 이어서 FastAPI 기본 학습 기록입니다

![](/assets/posts/869fedea14d6a66694e5d6d745994096c9b56cc518eb763b6d6be16296d0752a.png)


## Multiple Parameters

```python
from fastapi import Body, FastAPI, Path
from pydantic import BaseModel

app = FastAPI()

"""
Part 7 -> Body - Multiple Parameters
"""


class Item(BaseModel):
    name: str
    description: str | None = None
    price: float
    tax: float | None = None


class User(BaseModel):
    username: str
    full_name: str | None = None


@app.put("/items/{item_id}")
async def update_item(
        *,
        item_id: int = Path(..., title="The ID of the item to get", ge=0, le=150),
        q: str | None = None,
        item: Item = Body(..., embed=True),
        user: User,
        importance: int = Body(...),
):
    results = {"item_id": item_id}
    if q:
        results.update({"q": q})
    if item:
        results.update({"item": item.name})
    if user:
        results.update({"user": user})
    if importance:
        results.update({"importance": importance})
    return results

```

## Body - Fields
```python
from fastapi import FastAPI, Body
from pydantic import BaseModel, Field

app = FastAPI()

"""
Part 8 -> Body - Fields
"""


class Item(BaseModel):
    name: str
    description: str | None = Field(
        None, title="The description of the item", max_length=300
    )
    price: float = Field(..., gt=0, description="The price must be greater than zero")
    tax: float | None = None


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item = Body(..., embed=True)):
    results = {"item_id": item_id, "item": item}
    return results
```

## Nested Models
```python
from fastapi import FastAPI, Body
from pydantic import BaseModel, HttpUrl

app = FastAPI()

"""
Part 9 -> Body - Nested Models
"""


class Image(BaseModel):
    url: HttpUrl
    name: str


class Item(BaseModel):
    name: str
    description: str | None
    price: float
    tax: float | None = None
    tags: set[str] = set()
    image: Image | None = None


class Offer(BaseModel):
    name: str
    description: str | None = None
    price: float
    items: list[Item]


@app.put("/items/{item_id}")
async def update_item(item_id: int, item: Item):
    results = {"item_id": item_id, "item": item}
    return results


@app.put("/offers")
async def create_offer(offer: Offer = Body(..., embed=True)):
    return offer


@app.pose("images/multiple")
async def create_multiple_images(images: list[Image]):
    return images


@app.post("/blah")
async def create_some_blahs(blahs: dict[int, float]):
    return blahs

```