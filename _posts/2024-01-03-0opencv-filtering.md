---
title: OpenCV 필터링
author: yjh
math: true
categories: [CPP, OpenCv]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 필터링 (Filtering)

- 영상에서 원하는 정보만을 걸러내는 연산을 수행한다.
- 영상을 부드럽게 처리하거나, 반대로 선명하게 만들거나, 노이즈를 제거하는 등의 연산을 한다.
- 마스크 행렬을 통해 연산을 실시한다.
- `cv::filter2D`라는 기본적인 필터링 함수를 제공한다.
  - `cv::filter2D(cv::Mat src, cv::Mat dst, int ddpeth, cv::Mat Kernel, cv::Point anchor = cv::Point(-1, -1), borderType = BORDER_DEFAULT`
  - `ddepth`: 출력 영상의 깊이, -1 을 지정한 경우 입력 영상과 동일한 깊이로 반환하나.
    - 입력 영상 `src.type()`에 따른 `ddepth` 값.
      - `CV_8U` = `-1, CV_16S, CV_32F, CV_64F`
      - `CV_16S` = `-1, CV_32F, CV_64F`
      - `CV_32F` = `-1, CV_64F`
      - `CV_64F` = `-1`
  - `kernel`: 필터링 커널, 1채널 실수형 행렬.
  - `anchor`: 고정점 좌표. `cv::Point(-1, -1)`인 경우 커널의 중심을 고정점으로 사용한다.
  - `delta`: 필터링 후 추가적으로 더할 값.
- $dst(x, y) = \sum\limits_{i}\sum\limits_{j}kernel(i, j) * src(x + i - anchor.x, y + j - anchor.y) + delta$

## 마스크 (Mask)

- 작은 크기의 행렬이다.
- 필터링의 성격을 정의하는 행렬이며 커널 (kernel), 윈도우 (window) 라고 부르며 마스크 자체를 필터라고 부르기도 한다.
- 마스크 행렬의 원소는 주로 실수로 구성 되며, 직사각형이나 정사각형 등 형태나 크기가 다양하다.
- 마스크 행렬의 원소 총합은 1이 되도록 구성하는 것이 일반적이다.
  - 입력 영상과 출력 영상의 밝기가 동일해지기 때문이다.
  - 총합이 1보다 크면 출력 영상의 평균 밝기가 증가한다. (밝아진다.)
  - 총합이 1보다 작으면 출력 영상의 평균 밝기가 감소한다. (어두워진다.)

### 고정점

- 현재 필터링을 수행하는 마스크의 기준 픽셀의 위치를 나타내며 주로 마스크의 정중앙을 고정점으로 사용한다.

### 수식

- $G(x, y) = m(0, 0)f(x - 1, y -1) + m(1, 0)f(x, y -1) + m(2, 0)f(x + 1, y - 1)  \\ + m(0, 1)f(x - 1, y) + m(1, 1)f(x, y) + m(2, 1)f(x + 1, y)  \\ + m(0, 2)f(x - 1, y + 1) + m(1, 2)f(x, y + 1) + m(2, 2)f(x + 1, y + 1)$
  - `(x, y)` 좌표에서 마스크 연산을 통해 `G(x, y)`를 구했다면 마스크를 한 픽셀 옆으로 움직여 `(x + 1, y)` 에서 다시 마스크 연산을 수정하여 `G(x + 1, y)`에 저장한다.
  - 해당 연산을 영상 전체에 대해 수행하면 필터링이 완료 된다.

## 패딩 (Padding, border)

- 영상의 가장자리 부근에서는 필터링의 연산이 제대로 되지 않으므로 마스크 행렬의 크기만큼의 값을 가지는 가상의 픽셀을 만들어낸다.
- 패딩의 방법으로는 `constant, replicate, reflect, reflect_101, reflect101, default`등이 존재한다.
  - `constant`: 지정한 상수값으로 패딩한다. `000|abcdefg|000`
  - `replicate`: 가장 자리 픽셀 (경계선 픽셀) 의 값으로 패딩한다. `aaa|abcdefg|ggg`
  - `reflect`: 가장자리 픽셀로부터의 패딩 크기까지의 모양으로 패딩한다. `cba|abcdefg|gfe`
  - `reflect_101`: 가장자리 픽셀을 기준으로 패딩 크기까지의 모양으로 패딩한다. `dcb|abcdefg|fed`
  - `reflect101, default`: `reflect_101`과 동일하다.

## 엠보싱 필터 (Embossing filter)

- 픽셀값 변화가 적은 평탄한 영역은 회색으로, 픽셀값의 변화가 큰 경계면은 더 밝거나 어둡게한다.
- 대각선 방향으로 $+1$ 또는 $-1$ 값이 지정 되어있는 `3 x 3` 행렬
  - $\begin{pmatrix}
      -1 & -1 & 0  \\
      -1 & 0 & 1  \\
      0 & 1 & 1  \\
      \end{pmatrix}$
- 해당 마스크를 통해 대각선 방향으로 변화가 크게 되면 픽셀의 결과가 $0$보다 현저히 낮거나 크게 된다.
  - 반대로 변화가 적은 부분은 $0$에 가깝게 수렴 되며 $0$보다 작은 부분은 포화연산이 되기 때문에 전반적으로 어두운 영상이 되며 입체감이 줄어들게 된다.
  - 최종 결과물의 각 픽셀에 $128$을 더해 입체감을 준다.

```cpp
#include "opencv2/opencv.hpp"

void filterEmbossing();

int main(void)
{
  filterEmbossing();

  return 0;
}

void filterEmbossing()
{
  cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

  float data[] = {-1, -1, 0, -1, 0, 1, 0, 1, 1};
  cv::Mat mask(3, 3, CV_32FC1, data);

  cv::Mat dst;
  cv::filter2D(src, dst, -1, mask, cv::Point(-1, -1), 128);

  cv::imshow("src", src);
  cv::imshow("dst", dst);
  cv::waitKey();

  cv::destoryAllWindows();
}
```
