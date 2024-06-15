---
title: FastAPI 에서 response 로 file 받기
author: yjh
math: true
categories: [Python]
tags: [side project, annotation tool, fastapi]
---

## UploadFile

- 클라이언트로부터 이미지나 기타 파일 등의 데이터를 받을 때는 `UploadFile` 타입을 사용한다.
- 업로드 파일의 매개변수는 `File` 을 사용한다.

### Attribute

- `filename`: 문자열로 된 업로드 파일명이다. (`data.jpg`)
- `content_Type`: 문자열로 된 파일 형식 (`MIME type / media type`)이다. (`image/jpeg`)
- `file`: `SpooledTemporaryFile` 객체이다. *파일*객체를 필요로 하는 다른 라이브러리에 직접 전달 할 수 있다.

### async method

- `UploadFile` 객체에는 `async method`가 존재한다. 따라서 해당 메소드들을 사용하기 위해선 `await`를 사용하여야한다.
- `write(data)`: 데이터를 파일에 작성한다.
- `read(size)`: 파일의 바이트 및 글자의 `size`를 읽는다.
- `seek(offset)`: 파일 내의 `offset(int)` 위치의 바이트로 이동한다. (`awit file.offset(0)`을 사용하면 파일의 시작부분으로 이동한다.)
- `close()`: 파일을 닫는다.

```python
from fatapi import File, UploadFile, FastAPI

app = FastAPI()

@app.post("/upload")
async def upload(file: UploadFile = File(...)):
    content = await file.read()  # file 읽어오기

    # UploadFile 을 이미지로 변환
    bytes_img = BytesIO(content)
    img = Image.open(bytes_img)
    src = cv2.cvtColor(np.array(img), cv2.COLOR_BGR2RGB)

...
```

## Reference

- [FastAPI 공식 문서](https://fastapi.tiangolo.com/ko/tutorial/request-files/)
