---
title: OpenCV 이진화
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cv, cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 이진화 (Binarization)

- 영상의 각 픽셀을 두 분류로 나누는 작업이다.
- 픽셀값은 0 또는 255가 될 수 있으며, 해당 부류는 사용자가 지정하기에 따라 달라진다.
  - 객체영역과 배경영역 혹은 관심영역과 비관심영역 등.
- 그레이스케일 영상에 대해 이진화를 수행하려면 영상의 픽셀값이 임계값보다 크면 255로, 작으면 0으로 설정한다.
- 임계값은 그레이스케일에서 0 에서 255 사이의 정수를 정할 수 있다.
- 영상 내 픽셀에 대한 이진화의 수식은 다음과 같다.
- $$dst(x, y) = \begin{cases} 255 \;(src(x, y) < \;T \;일 \;때) \\ 0 \;(그 \;외) \end{cases}$$
  - $src, dst$는 입력과 출력영상이며 $T$는 임계값이다.
  - $T$는 사용자 경험에 의해 정하거나 영상의 특성을 분석하여 자동으로 지정할 수 있다.
- 임계값 정의는 이진화를 수행하려는 목적에 따라 달라져야하며, 임계값에 따라 결과가 상이하게 나올 수 있다.

### cv::threshold

- `cv::threshold(src, dst, thresh, maxval, type);`
  - `maxval`: `type`에서 `cv::THRESHOLD_BINARY`, `cv::TRESHOLD_BINARY_INV` 를 사용할 때 영상의 최대값이다.
  - `type`: 임계값 연산 방법. `ThresholdTypes` 열거형 상수를 사용한다.
    - `cv::THRESHOLD_BINARY`, `cv::THRESHOLD_BINARY_INV`, ...
    - `cv::THRESHOLD_OTSU`, `cv::THRESHOLD_TRIANGLE` 은 임계값을 자동으로 결정할 때 사용 된다.
      - 영상의 픽셀값 분ㅍ를 분석 후 임계값을 자동으로 결정하고 결정 된 임계값을 이용해 연산을 수행한다.
      - 주로 논리합 (|) 을 통해 다른 상수들과 결합해 사용한다.

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

void on_threshold(int pos, void* userdata);

int main(int argc, *argv[])
{
    cv::Mat src;

    if (arg < 2)  // 파일 실행 시 입력값을 받지 않는 경우
    {
        src = cv::imread("/path/to/src", cv::IMREAD_GRAYSCALE);
    }
    else  // 파일 실행 시 입력값을 받는 경우
    {
        src = cv::imread(argv[1], cv::IMREAD_GRAYSCALE);
    }

    cv::imshow("src", src);
    
    cv::namedWindow("dst", dst);

    cv::createTrackbar("Threshold", "dst", 0, 255, on_threshold, (void*)&src);
    cv::setTrackbarPos("Threshold", "dst", 128);

    cv::Mat simple = src > 128;  // 연산자 재정의로 인한 간단한 이진화 방법이다.
    cv::imshow("simple", simple);

    cv::waitKey();

    return 0;
}

void on_threshold(int pos, void* userdata)
{
    cv::Mat src = *(cv::Mat*)userdata;

    cv::Mat dst;
    cv::threshold(src, dst, pos, 255, cv::THRESHOLD_BINARY);

    cv::imshow("dst", dst);
}

```

## 적응형 이진화 (Adaptive Binarization)

- 모든 픽셀에 임계값을 적용하는 전역 이진화 (global adaptive) 와 달리 각 픽셀별로 임계값을 조절한다.
  - 전역 이진화는 조명이 균일하지 못하는 등의 영상의 특성에 따라 제대로 된 이진화를 수행할 수 없기 때문이다.
- 적응형 이진화에 대한 수식은 $T(x, y) = \mu(x, y) - C$이다.
  - $\mu(x, y)$는 $(x, y)$ 주변 블록 영역의 픽셀값 평균이고 $C$는 임계값의 크기를 조정하는 상수이다.
  - $\mu(x, y)$는  일반적인 산술평균을 구하거나 가우시안 함수 형태의 가중치를 적용한 가중 평균을 사용한다.
  - $C$는 영상의 특성에 따라 사용자가 결정한다.

### cv::adaptiveThreshold

- `cv::adaptiveThreshold(src, dst, maxValue, adaptiveMethod, thresholdType, blockSize, C);`
  - `maxValue`: 이진화 결과 영상의 최댓값이다.
  - `adaptiveMethod`: 적응형 이진화에서 블록 평균 계산 방법 지정한다.
    - `cv::ADAPTIVE_THRSH_MEAN_C`: `blockSize`x`blockSize` 크기의 주변 영역으로부터의 산술 평균을 구한다.
    - `cv::ADAPTIVE_THRESH_GAUSSIAN_C`: `blcoSize`x`blockSize` 크기의 가우시안 마스크를 사용하여 가우시안 가중 평균을 구한다.
  - `thresholdType`: `cv::THRESH_BINARY`, `cv::THRESH_BINARY_INV` 중 하나를 지정해서 사용한다.
  - `blcoSize`: 임계값 계산시 사용하는 3보다 같거나 큰 홀수를 지정하는 블록 크기이다.
  - `C`: 임계값 조정을 위한 상수이다. 블록 평균에서 `C`를 뺀 값을 임계값으로 사용한다.

```cpp
#include "opencv2/opencv.hpp"

void on_adaptive(int pos, void* userdata);

int main(void)
{
  cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

  cv::Mat dst;
  
  cv::imshow("src", src);

  cv::namedWindow("dst");
  cv::createTrackbar("Block Size", "dst", 0, 200, on_adaptive, (void*)&src);
  cv::setTrackbarPos("Block Size", "dst", 11);

  cv::waitKey();
  cv::destroyAllWindows();

  return 0;
}

void on_adaptive(int pos, void* userdata)
{
  cv::Mat src = *(cv::Mat*)userdata;

  int bsize = pos;
  if (bsize % 2 == 0) bsize--;
  if (bsize < 3) bsize = 3;

  cv::Mat dst;
  cv::adaptiveThreshold(src, dst, 255, cv::ADAPTIVE_THRESH_GAUSSIAN_C, cv::THRESH_BINARY, bsize, 5);

  cv::imshow("dst", dst);

}

```
