---
title: 호모그래피와 영상 매칭
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 호모그래피

- [호모그래피 관련 포스팅](https://skanwngud.github.io/posts/epipolar/)
- 3차원 공간상의 평면을 서로 다른 시점에서 바라봤을 때 획득하는 영상 사이의 관계를 의미한다.
- 수학적으로 하나의 평면을 다른 평면으로 투시 변환 (perspective transform) 하는 것과 같은 관계이다.
  - 실제 연산 과정에서 투시 변환과 같이 때문에 $3x3$ 실수 행렬로 표현 가능하다.
  - 네 개의 대응 되는 점의 좌표 이동 정보가 있으면 호모그래피 행렬을 구할 수 있다.
    - 특징점 매칭 정보로부터 호모그래피를 구하는 경우엔 4개의 점보다 훨씬 많은 점을 받기 대문에 투시 변환 시 에러가 최소가 되는 행렬을 구해야한다.

### cv::findHomography

- 두 평면 위에 있는 점들을 투영변환하는 $3x3$ 호모그래피 행렬을 반환한다.
- 원본 평면상의 점 좌표를 $(x_i, y_i)$로 표현하고 목표 평면상의 점 좌표를 $(x_{i}^{'}), y_{i}^{'}$ 로 표현한 경우,
$$

$$
- `srcPoints`: 원본 평면상의 점 좌표. `CV_32FC2`, `cv::Mat`, `std::vector<cv::Point2f>` 타입이 들어온다.
- `dstPoints`: 목표 평면상의 점 좌표. `srcPoints` 와 동일한 타입의 객체들이 들어온다.
- `method`: 행렬 계산 방법을 지정한다.
  - `0`: 모든 점을 사용하는 일반적인 방법이다. 최소자승법.
  - `LMEDS`: 최소 메디안 제곱 (least-median of squares) 방법이다.
  - `RANSAC`: `RANSAC` 방법이다.
  - `RHO`: `PROSAC` 방법이다.
- `ransacReorojThreshold`: 최대 허용 재투영 에러. 이 값 이내로 특징점이 재투영 되는 경우에만 정상치로 간주한다. `RANSAC` 과 `RHO` 에서만 사용한다.
- `mask`: 호모그래피 계산에 사용 된 점들을 알려주는 출력 마스크 행렬이다. `LMEDS`와 `RANSAC` 에서만 사용한다.
- `maxIters`: `RANSAC` 최대 반복 횟수이다.
- `confidence`: 신뢰도 레벨이다. 0 에서 1 사이의 실수를 지정한다.

### RANSAC (RANdom SAmple Consensus)

- 이상치가 포함 된 입력 데이터로부터 수학적 모델 파라미터를 효과적으로 결정하는 알고리즘이다.

1. 해당 알고리즘으로 호모그래피를 계산하는 경우, 다수의 특징점 매칭 정보로부터 4개의 대응점을 임의로 추출한다.
2. 이 정보를 이용하여 $3x3$ 행렬을 계산하고 나머지 특징점 매칭 쌍 중에서 현재 구한 호모그래피 행렬에 부합 되는 매칭 쌍 개수를 센다.
3. 그 후 다시 임의로 네 개의 대응점을추출하고 호모그래피 행렬 계산과 해당 호모그래피에 부합 되는 매칭 쌍 개수를 세는 작업을 반복한다.
4. 이 작업을 반복 시행 후 가장 많은 매칭 쌍의 지지를 받은 호모그래피 행렬을 최종 호모그래피 행렬로 결정한다.
