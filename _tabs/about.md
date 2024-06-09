---
# the default layout is 'page'
icon: fas fa-info-circle
order: 4
---

# Projects

---

## 인터마인즈 (2022. 05 ~ 2024. 03, 1년 11개월)

### 상품 진열 비율 체크

- 매대에 고객사 상품이 얼마나 진열 되어있는지 딥러닝 모델을 통한 비율 선정
- Detection, Classification

#### 프로젝트 기간: 2023. 12 ~ 2024. 03 (4개월)

#### 사용툴

- Python
- OpenCV
- Flask
- Redis, SQL Server
- Docker
- Linux

#### 상세 내용

- SQL Server 를 이용한 DB 설계 및 구축
- 각 기능 별 서버를 docker 로 모듈화 후 배포 및 운영
  - Detection Inference
  - Classification Inference
  - Web - Backend
- 고객사 제공 기 모델 성능 개선
  - 데이터 클래스 카테고리 별 재지정 및 라벨링
  - **기 모델 대비 정확도 향상 (87% -> 95%)**
- Rdeis Queue 를 이용한 모델 자동 학습 관련 스케쥴링 기능 구현
  - 특정 시간에 전체 상품 confidence 확인 후 재학습
  - 일정 시간동안 재학습 되지 않은 상품들 확인 후 재학습
- 고객사 상품 진열 관련 관리자 및 사용자 페이지 서버 개발

---

### 무인 냉장고 Door - Pop 개발

- 냉장고 내의 카메라를 통한 상품 인식
- Detection, Classification

#### 프로젝트 기간: 2023. 06 ~ 2024. 03 (1년 10개월)

#### 사용툴

- Python
- OpenCV, NumPy
- StreamLit, FastAPI
- AWS S3, EC2, Docker
- Linux

#### 상세 내용

- Vision 알고리즘 개발을 통한 성능 개선 시도
  - 카메라 틀어짐 확인 기능 구현
  - 냉장고 내 상품 쓰러짐 확인 기능 구현
  - 캔 음료 상품 관련 용량 확인 기능 구현
  - 광량 차이에 의한 이미지 틀어짐 수정 기능 구현
- 기존 상품 인식 모델 성능 고도화
  - Activation Function 교체
    - Softmax -> Binary
  - Feature Matching 모델 선정 및 성능 고도화
    - **SuperGlue**
  - **객체 탐지 성능 향상 (85% -> 99%)**
  - **상품 탐지 성능 향상 (83% -> 99%)**
  - **상품 분류 성능 향상 (90% -> 98%)**
- 각 모델 docker 로 빌드 후 서버에 배포
- StreamLit 과 FastAPI 를 기반으로 한 사내 관리자 페이지 개발

---

### 무인매장 Store - Pop 개발

- 실시간 매장 모니터링 시스템 개발
- Tracking

#### 프로젝트 기간: 2022. 07 ~ 2024. 03 (1년 9개월)

#### 사용툴

- Python
- OpenCV, NumPy
- PyQt5
- FastAPI
- PostgreSQL
- Docker
- Linux

#### 상세 내용

- 사람 입퇴장 감지 알고리즘 개발
- 포즈 추정 모델 선정 및 성능 개선
  - **RTMPose**
- Tracking 알고리즘 개발
  - **ByteTrack**기반의 자체 알고리즘 개발
- 사내 사용을 위한 자체 데이터 라벨링 툴 개발
- 물품 구매 알고리즘 구현
- 추가 성능 고도화 진행
  - **OKS 기반의 포즈 추정 정확도 98% 달성**
  - **ID Switching 발생률 2% 미만**

---

### 상품 유통기한 인식

- 매장 내 상품 이미지를 통해 유통기한 인식
- Detection, OCR

#### 프로젝트 기간: 2023. 07 ~ 2023. 08 (1개월)

#### 사용툴

- Python
- OpenCV, NumPy
- PyQt5
- Linux

#### 상세 내용

- 유통기한 ROI 탐지 모델 선정 및 성능 개선
  - **Oriented R-CNN**
  - **800여종의 상품 데이터 기준 정확도 93% 달성**
- 유통기한 텍스트 인식
  - **PORORO 모델 선정**
- 사내 사용을 위해 PyQt5 를 이용한 자체 rotated bounding box 데이터 어노테이션 툴 개발

---

### 무인 매장 통합 관리 시스템

- 매장 내 인원 및 이상상황 탐지
- Detection, Tracking, Camera calibration

#### 사용툴

- Python
- OpenCV, NumPy
- Docker
- Linux

#### 상세 내용

- 입퇴장 감지 알고리즘 개발
  - **입퇴장 감지 정확도 98% 달성**
- Multi - View 카메라 캘리브레이션 및 월드맵 작성
  - 동일 객체 간 글로벌 좌표 동기화
- 트래킹 모델 선정 및 성능 개선
  - **ByteTrack**기반의 자체 알고리즘 개발
- 매장 내 이상상황 탐지 모델 선정
  - **PaDiM**
  - **이상상황 탐지율 93% 달성**

---

### 데이터 익명화

- 얼굴 식별 후 익명화 처리
- Detection

#### 프로젝트 기간: 2022. 12 ~ 2023. 01 (2개월)

#### 사용툴

- Python
- OpenCV
- FastAPI
- AWS EC2
- Linux

#### 상세 내용

- 얼굴 탐지 모델 선정 및 성능 개선
  - **TinaFace**
  - **얼굴 인식률 95% 달성** (**Bird - View 에서는 100% 달성**)
- OpenCV 를 이용한 익명화 처리
- 사내 사용을 위한 자체 익명화 툴 개발
  - 익명화 모델 API 서버 형태로 배포

---

### 연구 장비 사용 로그 자동화

- 약 20평인 연구실 내 인원의 입퇴장 여부 및 연구장비 사용 감지
- Detection, Tracking, Pose estimation, Camera calibration

#### 프로젝트 기간: 2022. 06 ~ 2022. 11 (6개월)

#### 사용툴

- Python
- OpenCV, NumPy
- PyQt5
- PostgreSQL
- AWS EC2, Docker
- Linux

#### 상세 내용

- PyQt5 를 이용하여 60개의 카메라와 40여개의 자체 제작 보드를 조작하는 툴 개발
- StreamLit 으로 상태 확인 웹페이지 제작
- Multi - View 카메라 캘리브레이션 및 월드맵 작성
- 연구원 입퇴장 감지 알고리즘 개발
  - **입퇴장 판단 정확도 98% 달성**
- 장비 사용 알고리즘 개발
  - **장비 사용 정확도 98% 달성**
- 트래킹 모델 선정 및 성능 개선
  - **ByteTrack**기반의 자체 알고리즘 개발
  - **ID Switching 발생률 3% 미만 달성**
- 장비 종류 인식 및 장비 사용 유무 관련 모델 선정 및 성능 개선
  - **Yolov5s**
  - **ViTPose-L**

---

## 포랩 (2021. 11 ~ 2022. 05, 6개월)

### 골프공 궤적 판별 application 개발 (SuperShot)

- 핸드폰 영상을 기반으로 골프공의 궤적, 구질, 순간속도, 최대 높이 예측
- Detection, Tracking, Classification, Prediction

#### 프로젝트 기간: 2021. 11 ~ 2022. 05 (6개월)

#### 사용툴

- Python
- OpenCV, NumPy, Turtle
- AWS S3, Redis
- Windows, Linux

### 상세 내용

- 골프공 객체 탐지 모델 선정 및 성능 개선
  - **Yolo5m**
- 골프공의 기타 정보를 계산하기 위한 자체 알고리즘 개발
  - 영성 내 골프공 축적 계산
  - 순간 속도, 최대 높이, 출발 각도 계산식 정의
- **초기 버젼 iOS / Google PlayMarket 출시 (SuperShot)**

---

## 소이넷 (2021. 06 ~ 2021. 10, 5개월)

### 실내 공원 관제 시스템

- 공원 내 cctv, 수동조작 로봇의 카메라를 통한 화재, 낙상 감지 및 미아 발생 시 미아 얼굴 식별
- Detection, Matching, WebRTC

#### 프로젝트 기간: 2021. 06 ~ 2021. 10 (5개월)

#### 사용툴

- Python, Node.js
- OpenCV, NumPy
- MariaDB
- Windows

#### 상세 내용

- MariaDB 를 이용한 DB 설계 및 구축
- 사람, 화재 감지 관련 모델 선정 및 고도화
  - **Yolov5m**
  - **화재, 사람 탐지율 100% 달성**
- 낙상 감지 관련 자체 알고리즘 개발
  - **낙상 탐지율 100% 달성**
- 미아 판별 관련 모델 선정 및 고도화
  - **RetinaFace**
  - **ArcFace**
  - **미아 판별 성공률 94% 달성**
