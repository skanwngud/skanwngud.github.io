---
title: OpenCV 에서 제공하는 유용한 함수
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## OpenCV 에서 제공하는 유용한 함수들

### cv::sum()

- 행렬 원소들의 합
- 이미지의 채널 값에 따라 원소가 저장 됨. (B G R A)
  - `channel` 값이 1 (GRAY SCALE) 일 때, 값이 첫 번째 원소에 저장 된다. [N, 0, 0, 0]
  - `channel` 값이 3 (COLOR SCALE) 일 때, 값이 B G R 순서로 저장 된다. [B, G, R, 0]

### cv:: mean()

- `cv::scalar mean = cv::mean(mat);`
- 행렬 원소들의 평균, 마스크 연산 지원
- `cv::sum`과 마찬가지로 채널 값에 따라 원소가 저장 되는 길이가 달라진다.

### cv::minMaxLoc()

- `cv::minMaxLoc(inputArray, &minVal, &maxVal, &minPos, &maxPos);`
- 행렬 원소의 최소 / 최대값 및 좌표 정보 확인, 마스크 연산 지원
- 각 위치에 맞는 변수들의 주소값들을 전달해줘야 한다.
  - 특정 값이 필요하지 않는다면 그 위치에 `0 (NULL)`을 넣는다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("path/to/img", cv::IMREAD_GRAYSCALE);

    double minVal, maxVal;  // 최소, 최대값
    cv::Point minPos, maxPos;  // 최소, 최대값의 위치

    cv::minMaxLoc(img, &minVal, &maxVal, &minPos, &maxPos);
    cv::minMaxLoc(img, 0, 0, &minPos, 0);  // minPos 만 필요한 경우

    std::cout << minVal << std::endl;
    std::cout << maxVal << std::endl;
    std::cout << minPos << std::endl;
    std::cout << maxPos << std::endl;

    return 0;
}
```

### cv::normalize()

- `cv::normalize(inputArray, outputArray, alpha, beta, norm_type, matrix_type);`
- 행렬의 놈 (norm) 값을 정규화하거나 원소값 범위를 특정 범위로 정규화한다.
- `norm_type`의 종류에 따라 동작이 결정 된다.
  - `NORM_INF`, `NORM_L1`, `NNORM_L2`, ...

### cvRound()

- 반올림 함수.
- 실수를 0.5 를 기준으로 반올림한다.
- 소숫점 아래의 값이 정확히 0.5 라면 가장 가까운 짝수로 반환한다.

```cpp
std::cout << cvRound(2.5) << std::endl;  // 2
std::cout << cvRound(2.51) << std::endl;  // 3
std::cout << cvRound(3.4999) << std::endl;  // 3
std::cout << cvRound(3.5) << std::endl;  // 4
std::cout << cvRound(2) << std::endl;  // 2
```

### CV_Assert, CV_DbgAssert

- 조건에 따라 에러를 발생시켜주는 함수.
- `CV_Assert(expr), CV_DbgAssert(expr)`의 형태.
  - `expr`은 표현식, 조건문이며 해당 조건문이 `false`인 경우 에러를 발생시킨다.
  - `CV_Assert`의 경우 릴리스모드, 디버그모드 둘 다 사용 가능하다.
  - `CV_DbgAssert`의 경우 디버그모드에서만 사용 가능하다.

```cpp
CV_Assert(1 == 0);  // 0 (false) 이므로 에러 발생
CV_DbgAssert(1 == 0); // 0 (false) 이므로 에러 발생 (디버그 모드에서만 동작)
```

### cv::TickMeter

- 실행 시간을 측정해주는 함수.
- `cv::TickMeter` 객체를 호출한 뒤 `cv::TickMeter.start()`로 측정 시작, `cv::TickMeter.stop()` 으로 측정 종료한다.
- 시간 측정을 정확히 하기 위해서는 릴리스 모드에서 측정해야한다.
  - 디버그모드에서는 컴파일러 자체의 최적화 등이 동작하지않으며 추가적인 연산이 더 들어간다.
- `getTickCount()` 함수로 컴퓨터 내부에서 수행 되는 클럭의 횟수를 센다. (`int64` 반환)
- `getTickFrequency()` 함수를 통해 클럭 주파수 (초당 클럭 횟수) 를 반환한다.
- `cv::TickMeter.getTimeSec()` 함수를 통해 연산 시간을 초 단위로 환산해서 반환한다.

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

int main(void)
{
    cv::TickMeter tm;  // 시간 측정 연산자 생성

    cv::Mat img = cv::imread("path/to/image", cv::IMREAD_COLOR);
    cv::Mat dst(img.rows, img.cols, img.type());

    tm.start();  // 시작 측정 시작

    for (int i = 0; i < img.rows; i++)
    {
        for (int j = 0; j < img.cols; j++)
        {
            dst.at<uchar>(i, j) = src.at<uchar>(i, j);
        }
    }

    tm.stop()l  // 시간 측정 종료

    std::cout << "time: " << tm.getTimeSec() << "sec." << std::endl;

    return 0;
}
```
