---
title: FastAPI 실행 시 전체코드가 두 번씩 실행 되는 문제
author: yjh
math: true
categories: [Trouble Shooting]
tags: [python, trouble shooting]
---

## 문제

사이드 프로젝트 진행 중 `FastAPI`를 실행할 때 전체 코드가 두 번 실행 되는 문제가 발생했다.

```python
# main.py

import uvicorn

from fastapi import FastAPI

...

APP = FastAPI()

print("hello world!")

...

if __name__ == "__main__":
    uvicorn.run("main:APP", host="localhost", port=1234)
```

위의 코드를 실행시키면 `hello world!`가 두 번씩 찍혔다.

## 해결

해당 문제는 `__main__`과 `main.py`이 두 번 실행되는 문제였다.

`uvicorn.run` 을 할 때, `app` 인자를 파일명이 아닌 위에서 지정해준 `APP`으로 실행시키면 된다.

```python
# main.py

...

if __name__ == "__main__":
    uvicorn.run(APP, host="localhost", port=1234)

```
