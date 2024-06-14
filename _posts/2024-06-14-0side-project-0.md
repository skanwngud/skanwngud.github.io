---
title: [사이드 프로젝트] 그 동안의 정리
author: yjh
math: true
categories: [Side Project, Annotation Tool]
tags: [side project, annotation tool]
---

## 프로젝트 목표

- 딥러닝 모델과 `OpenCV` 를 이용한 자동 이미지 라벨링 툴 개발
- 웹페이지 및 `Qt` 등을 이용한 프로그램으로 배포
- [사이드 프로젝트 repo](https://github.com/skanwngud/annotation_tool)

## 프로젝트 일정

### 2023. 08

- 프로젝트 구상을 어느 정도 마무리 지었다.
- `Yolov8`을 기본으로 `detection`, `segmentation`, `pose estimation`, `classification`, `color clustering`을 수행한다.
  - 모델 선정 기준은 각 태스크별로 쉽게 간단하게 사용할 수 있기 때문이다. `onnx` 전환이나 `cpp` 사용등을 official 로 제공하는 등의 편의성이 가장 큰 이유였다.
  - 그와 동시에 성능적인 면에서도 충분히 만족할만한 상태였다.
- 각 기능들을 `docker`로 분리시켜 하나의 `container`로 돌아가게 한다.
- 기능 별 `container`를 연결해주는 하나의 `web-back container`를 만들며 외부에서 들어오는 요청들을 모두 각 기능별 컨테이너로 전달한다.
  - `web-back`은 `FastAPI`를 선정했다.
    - 평소 `Flask` 위주로 개발을 하기도 했었고, `Flask` 대비 `pydantic`을 이용한 클래스 형식의 데이터 지정으로 휴먼 에러를 줄일 수 있는 점이 선정 이유였다.
    - 또한 공식 문서에서는 다른 웹 관련 프레임워크보다 월등히 빠른 속도를 자랑한다는 것도 `Flask` 대신 `FastAPI` 를 선정한 이유였다.

### 2023. 11 ~ 2024. 02

- 기능 별 `docker`를 작성하고 해당 컨테이너들을 한 번에 관리할 수 있도록 `docker-compose` 파일도 작성했다.
- 기타 기능들을 개발했다.
  - 각 서버들이 작동하기 시작하면 자신의 내부 `IP`를 웹백 서버에 전달을 해주며 서로의 주소들을 알게 된다.
  - 웹페이지에서 들어오는 이미지들을 `base64` 형태로 인코딩하거나 해당 데이터를 다시 이미지로 디코딩하는 등의 함수들도 정의해줬다.
  - 기존 서버들을 올릴 때 각 태스크의 모델들과 그 모델들의 모든 사이즈들을 한 번에 올리는 등의 초기화 로직을 짰는데, 초기 구동 시간이 너무 오래 걸려 웨이트 파일들의 주소만 갖고 초기화를 하도록 수정하였다.
    - 해당 로직으로 평균적으로 초기 구동 시간이 약 $230\%$정도 빨라졌다.

### ~ 현재

- 웹페이지를 `html`로 간단하게 제작하고 있다. 디자인이 중요한 요소는 아닐거라 생각해서 디자인적인 요소는 넣지 않을 생각이다.
  - 이미지를 올리고 태스크를 지정하고 필요에 의해 모델의 사이즈와 클래스를 지정한다.(`detection`, `classification`, `pose estimation`, `segmentation`)
- `ngrok`을 통해 배포한 웹페이지를 외부에서 접속할 수 있는 것을 확인하였다.
