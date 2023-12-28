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
