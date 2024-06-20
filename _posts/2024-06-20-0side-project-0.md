---
title: 사이드 프로젝트 ~ 06. 20
author: yjh
math: true
categories: [Side Project, Annotation Tool]
tags: [side project, annotation tool]
---

1. 서버단에서 `FastAPI`와 관련 된 에러 메세지를 확인하기 위해 `error handler`를 추가했다.

```python
...

from fastapi import FastAPI, Request, status
from fastapi.encoders import jsonable_encoder
from fastapi.exception import RequestValidationError
from fastapi.response import JSONResponse

...

APP = FastAPI()

@APP.exception_handler(RequestValidationError)
async def validation_exception_handler(request: Requset, exc: RequestValidationError):
    return JSONResponse(
        status_code = status.HTTP_422_UNPROCESSABLE_ENTITY,
        content = jsonable_encoder({"detail": exc.errors(), "body": exc.body})
    )

...
```

2. `Detection` 서버에서 들어오는 `classes` 의 기본값을 `classes: Optional[List[int]] = Form(None)` 에서 `classes: List[int] = [range(0, 80)]`으로 수정했다.

3. `Docker`의 `IP`를 고정으로 할당해주었다. 이제 `IP`가 고정이므로 서버를 올릴 때 `ip_scan` 함수의 호출을 할 필요가 없게 되었다.

- `docker-compose.yml`에서 각 이미지의 옵션 중 `networks` 에서 `ipv4_address`를 추가했다.
  - 본인이 원하는 `IP` 주소를 적어주면 된다. (e.g. `127.0.0.20` 등)
  - 전체 `networks` 옵션에선 `config`의 `subnet`에서 서브넷마스크를 지정해주면 된다. (e.g. `127.0.0.1/16`)

```yml
version: "3"

services:
    server:
        build: ./path/to/DockerFile
        image: server:init
        networks:
            bridge:
                ipv4_address: x.x.x.x
            ports:
                - "0000:8000"
    
    detect:
        build: ./path/to/DockerFile
        image: detect:init
        networks:
            bridge:
                ipv4_address: x.x.x.x

networks:
    bridge:
        ipam:
            driver: default
            config:
                - subnet: x.x.x.x/16
```

`detection` 부분은 어느 정도 마무리가 되어가는 듯 하다. 추후, 다른 서버들도 작업을 완료한 뒤에 다음 스텝으로 넘어가면 될 것 같다.

## Reference

- [FastAPI 공식문서](https://fastapi.tiangolo.com/tutorial/handling-errors/)
