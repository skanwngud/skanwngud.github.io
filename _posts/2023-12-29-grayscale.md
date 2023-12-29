---
title: OpenCV 그레이스케일
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## Grayscale

- 컬러값인 `RGB`를 갖지 않고 밝기값만 갖고 있는 $1$채널 이미지.
- 이미지 연산 등에서 주로 그레이스케일을 사용하는 이유는 컬러스케일에 비해 채널값이 하나이므로 메모리를 ${1} / {3}$ 만 사용하기 때문에 훨씬 경제적이다.

### 이미지를 불러올 때 처음부터 그레이스케일로 로드

- `cv::imread` 함수를 사용할 때 플래그를 `cv::IMREAD_GRAYSCALE`로 지정해주면 된다.

```cpp
cv::Mat grayScaleImage = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);
```

### CV_8UC1 타입으로 생성

- 최초 행렬 생성시 `CV_8UC1` 타입으로 생성하면 그레이스케일로 생성 된다.

```cpp
cv::Mat grayScaleImage(480, 640, CV_8UC1);
```

### cv::cvtColor 함수를 통해 그레이스케일로 변환

- 이미 컬러스케일로 로드 되었거나 생성 된 이미지 등은 `cv::cvtColor` 함수를 통해 그레이스케일로 변경 할 수 있다.
  - 함수의 플래그는 `cv::COLOR_BGR2GRAY`로 지정한다.

```cpp
cv::Mat colorScaleImage = cv::imread("path/to/image", cv::IMREAD_COLOR);  // 컬로스케일로 로드
cv::Mat grayScaleImage;

cv::cvtColor(colorScaleImage, grayScalImage, cv::COLOR_BGR2GRAY);
```

### 그레이스케일 여부 확인

- 이미지의 타입을 통해 그레이스케일인지 확인할 수 있다.
  - `cv::Mat::operator.type()` 는 타입을 반환하며 해당 타입이 `CV_8UC1`인지 확인하면 된다.

```cpp
cv::Mat colorScaleImage = cv::imread("path/to/image", cv::IMREAD_COLOR);
cv::Mat grayScaleImage = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

std::cout << (colorScaleImage.type() == CV_8UC1) << std::endl;
std::cout << (grayScaleImage.type() == CV_8UC1) << std::endl;

// 0 (false)
// 1 (true)
```
