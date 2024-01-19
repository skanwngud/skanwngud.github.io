---
title: OpenCV 마스크 에지 기반 검출
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 마스크 에지 기반 검출

- 영상을 $x$ 축 방향 $(1, 3)$ 혹은 $y$ 축 방향 $(3, 1)$ 방향으로 편미분을 하게 되면 노이즈의 영향으로 결과의 정확도가 떨어질 수 있다.
- 실제 영상에서는 노이즈의 영향을 줄이기 위해 좀 더 큰 크기의 마스크 필터를 사용한다.
- 소벨 필터 (Sobel filter) 마스크를 많이 사용하며, 샤르 필터 (Scharr filter) 마스크도 존재한다.
- 소벨 필터 마스크의 모양은 다음과 같다.
  - $x$축 - $\begin{pmatrix} -1 & 0 & 2 \\ -2 & 0 & 2 \\ -1 & 0 & 1 \\ \end{pmatrix}$
  - $y$축 - $\begin{pmatrix} -1 & -2 & -1 \\ 0 & 0 & 0 \\ 1 & 2 & 1 \\ \end{pmatrix}$
- 위 마스크는 현재 행에 대한 중앙 미분을 2회 수행하고 이전 행과 다음 행에 대해서도 중앙 차분 연산을 1회씩 수행한다.
  - 현재 행에서 두 번의 중앙 차분 연산을 수행하는 것은 현재 행의 중앙 차분 근사에 더 큰 가중치를 주기 위함이다.
  - 현재 행과 이웃 행에서의 픽셀 값의 변화가 유사하다는 점을 이용한 방법으로 노이즈의 영향을 줄인다.

## 소벨 필터 마스크 (Sobel filter mask)

- 기본적으로 `cv::Sboel()` 함수를 제공한다.
- `cv::Sobel(src, dst, deepth, dx, dy, ksize=3, scale=1, delta=0, borderType=BORDER_DEFAULT);`
  - `deepth`: 출력 영상의 채널, $-1$이면 입력 영상과 동일하다.
  - `ksize`: 소벨 커널의 크기이다. ($1$을 지정하면 $(1, 3), (3, 1)$을 사용하고 $3$을 지정하면 $(3, 3)$을 사용한다.)
  - `delta`: 필터링 연산을 수행한 후 추가적으로 더할 값을 지정한다.
  - `dx, dy`: 각 축의 편미분 차수를 의미한다.
- 소벨 필터 마스크의 수식은 다음과 같다.
  - $dst = \frac{ \partial^{xorder + yorder}src }{ \partial^{xorder}\partial^{yorder} }$
- `cv::Sobel()` 함수는 각 축으로의 고차 미분을 계산할 수 있지만 주로 1차 미분을 구하는 용도로 사용한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat dx, dy;

    cv::Sobel(src, dx, -1, 1, 0);  // x 축 방향으로 1차 미분
    cv::Sobel(src, dy, -1, 0, 1);  // y 축 방향으로 1차 미분

    cv::imshow("src", src);
    cv::imshow("x", dx);
    cv::imshow("y", dy);
    cv::waitKey(0);

    cv::destroyAllWindows();

    return 0;
}
```

## 샤르 필터 마스크 (Scharr filter mask)

- 소벨 필터 마스크보다 좀 더 정확한 편미분이 가능하다.
- 기본적으로 `cv::Scharr(src, dst, dx, dy, scale=1, delta=0, borderType=BORDER_DEFAULT);` 함수를 제공한다.
- 샤르 필터 마스크의 모양은 다음과 같다.
  - $x$축 - $\begin{pmatrix} -3 & 0 & 3 \\ -10 & 0 & 10 \\ -3 & 0 & 3 \\ \end{pmatrix}$
  - $y$축 - $\begin{pmatrix} -3 & -10 & -3 \\ 0 & 0 & 0 \\ 3 & 10 & 3 \\ \end{pmatrix}$
- `cv::Sobel()` 함수를 통해 해당 연산을 동일하게 수행 가능하다.
  - `ksize`에 `cv::FILTER_CHARR` 혹은 $-1$을 지정하면 $(3, 3)$ 형태의 `scharr` 마스크를 사용한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat scharr_dst, sobel_dst;
    
    cv::Scharr(src, scharr_dst, -1, 1, 0);  // scharr 함수를 이용해 x 축으로 편미분
    cv::Sobel(src, sobel_dst, -1, 1, 0, cv::FILTER_SCHARR);  // sobel 함수를 이용해 x 축으로 편미분

    cv::imshow("src", src);
    cv::imshow("scharr", scharr_dst);
    cv::imshow("sobel", sobel_dst);
    cv::waitKey(0);

    cv::destroyAllWindows();

    return 0;
}
```

## 그래디언트 크기, 방향 계산

- 각 축으로 편미분 한 결과들을 행렬에 저장 후 두 미분 행렬을 이용해 그래디언트 크기를 계산할 수 있다.

### cv::magnitude()

- 각 축의 방향의 좌표들을 이용하여 벡터의 크기를 계산하는 `magnitude()` 함수를 제공한다.
- `cv::magnitude(x, y, magnitude);`
  - `x, y`; 벡터의 $x, y$좌표를 나타내는 실수 행렬 혹은 벡터이다.
    - `CV_32F` 혹은 `CV_64F`의 깊이를 사용하는 행렬 혹은 벡터여야한다.
  - `magnitude`: 출력 영상이며 수식은 $magnitude(I) = \sqrt{x(T)^2 + y(I)^2}$로 나타낸다.

### cv::phase()

- 마찬가지로 각 축의 방향의 좌표들을 이용하여 벡터의 방향을 계산하는 `phase()` 함수를 제공한다.
- `cv::phase(x, y, angle, angleInDegrees=false);`
  - `angle`: `x`와 같은 크기, 타입을 갖는 출력 영상이며 수식은 $angle(I) = atan 2 \begin{pmatrix} \frac{y(I)}{x(I)} \end{pmatrix}$로 나타낸다.
  - `angleInDegrees`: `true`면 각도 단위, `false`면 라디안 단위로 출력한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat dx, dy;  // 편미분 결과 저장

    cv::Sobel(src, dx, CV_32FC1, 1, 0);  // 편미분
    cv::Sobel(src, dy, CV_32FC1, 0, 1);  // 편미분

    cv::Mat fmag, mag;
    cv::manitude(dx, dy, fmag);
    fmag.convertTo(mag, CV_8UC1);

    cv::Mat edge = mag > 150;

    cv::imshow("mag", mag);
    cv::imshow("edge", edge);
    cv::waitKey(0);

    cv::destroyAllWindows();

    return 0;
}
```
