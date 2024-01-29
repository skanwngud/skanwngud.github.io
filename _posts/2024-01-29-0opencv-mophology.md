---
title: OpenCV 모폴로지 연산
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 모폴로지 (Mophology) 연산

- 모폴로지는 형태 또는 모양에 관한 학문을 의미하며, 영상 내부 객체의 형태와 구조를 분석하고 처리하는 기법이다.
- 그레이스케일 영상과 이진 영상 둘 다 가능하지만 주로 이진화 된 영상에서 객체의 모양을 변형하는 용도로 사용한다.

## 구조 요소 (Structuring Element)

- 모폴로지 연산을 하기 위해선 우선 구조 요소를 정의한다.
- 필터링에서 사용 되는 마스크처럼 모폴로지 연산의 동작을 결정하는 작은 크기의 행렬이다.
- 다양한 크기와 모양으로 정의할 수 있으며, 필요에 따라 원하는 구조 요소를 선택하여 사용할 수 있지만 대부분의 연산에서는 $(3, 3)$ 정방향 구조 요소를 사용한다.
- 각각의 구조 요소 행렬은 모폴로지 연산 결과가 저장 될 위치가 존재하며, 고정점 (anchor point) 라고 한다.
  - 고정점은 일반적으로 중심점을 사용한다.

### cv::getStructuringElement

- `cv::getStructuringElement(shape, ksize, anchor = cv::Point(-1, -1));`
  - `shape`: 구조 요소의 모양
    - `cv::MORPH_RECT`: 사각형 모양의 구조 요소.
    - `cv::MORPH_CROSS`: 십자가 모양의 구조 요소.
    - `cv::MORPH_ELLIPSE`: 타원 모양의 구조요소, 지정한 구조 요소 크기의 사각형에 내접하는 타원을 이용한다.
  - `ksize`: 구조 요소의 크기.
  - `anchor`: `cv::NORM_CROSS` 모양의 구조요소에서 십자가 중심 좌표이다. `cv::Point(-1, -1)`을 지정하면 십자가 중심 좌표로 설정한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat dst;
    dst = cv::getStructuringElement(cv::MORPH_RECT, cv::Size(3, 3));

    return 0;
}
```

## 침식 (Erode) 과 팽창 (Dilate)

### 침식

- 객체 영역의 외각을 골고루 깎아내는 연산이다.
- 전체적으로 객체 영역은 축소 되고 배경은 확대 된다.
- 전체 영상에 대해 스캔하면서 구조 요소가 객체 영역 내부에 완전히 포함 되는 경우 고정점 위치 픽셀을 $255$로 설정한다.

### cv::erode

- `cv::erode(src, dst, kernel, anchor = cv::Point(-1, -1), iterations = 1, borderType = cv::BORDER_CONSTANT, borderValue = morphologyDefaultBorderValue());`
  - `kernel`: 구조 요소. 만약 `cv::Mat()` 혹은 `cv::noArray()` 를 지정하면 $(3, 3)$ 사각형 구조 요소를 사용한다.
    - 그 외의 구조요소는 `cv::getStructuringElement()` 함수를 통해 만들 수 있다.
  - `anchor`: 고정점 위치이다. `cv::Point(-1, -1)` 을 지정하면 정중앙을 고정점으로 설정한다.
  - `iterations`: 반복 횟수이다.

### 팽창

- 객체 영역의 외곽을 확대하는 연산이다.
- 객체 영역은 확대 되고 배경은 축소 된다.
- 전체 영상에 대해 스캔하면서 구조 요소가 객체 영역에 한 픽셀이라도 만나는 경우 고정점 위치 픽셀을 $255$로 설정한다.

### cv::dilate

- `cv::dilate(src, dst, kernel, anchor, iterations, borderType, borderValue);`
  - `cv::erode()`와 완전히 동일하다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat bin;
    cv::threshold(src, bin, 0, 255, cv::THRESH_BINARY | cv::THRESH_OTSU);

    cv::Mat erode, dilate;
    
    cv::erode(src, erode, cv::Mat());
    cv::dilate(src, dilate, cv::Mat());

    cv::imshow("src", src);
    cv::imshoe("erode", erode);
    cv::imshow("dilate", dilate);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}

```

## 열기 (Open) 와 닫기 (Close)

### 열기

- 침식 연산 수행 후 팽창 연산을 수행한다.
- 1, 2짜리 픽셀 영역이 제거 된 후 팽창이 수행 되므로 작은 크기의 객체가 효과적으로 제거 된다.

### 닫기

- 팽창 연산 수행 후 침식 연산을 수행한다.
- 팽창 연산을 먼저 수행하기 때문에 객체 내부의 작은 구멍들을 제거하게 된다.

### cv::mophologyEx

- `cv::mophologyEx(src, dst, op, kernel, anchor, iterations, borderType, borderValye);`
  - `op`: 모폴로지 연산 타입 (MorphologyTypes 열거형 상수 생성)
    - `cv::MORPH_ERODE`, `cv::MORPH_DILATE`, `cv::MORPH_OPEN`, ...
    - `cv::MORPH_GRADIENT`: 팽창 (`cv::dilate(src, element)`) 수행 후 침식 (`cv::erode(src, element)`) 수행한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat bin;
    cv::threshold(src, bin, 0, 255, cv::THRESH_BINARY | cv::THRESH_OTSU);

    cv::Mat open, close;

    cv::mophologyEx(bin, open, cv::MORPH_OPEN, cv::Mat());
    cv::mophologyEx(bin, close, cv::MORPH_CLOSE, cv::Mat());

    cv::imshow("src", src);
    cv::imshow("open", open);
    cv::imshow("close", close);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
