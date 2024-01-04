---
title: OpenCV 잡음 제거 필터링
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 잡음 (Noise)

- 원본 신호에 추가 된 원치 않은 신호를 의미한다.
- 영상의 경우 영상을 획득하는 과정 중에 많이 발생한다.
  - 광학적 신호를 전기적 신호로 바꾸는 센서에서 주로 발생한다.
- $f(x, y) = s(x, y) + n(x, y)$
  - $f(x, y)$는 출력영상.
  - $s(x, y)$는 센서에서 들어온 입력 영상.
  - $n(x, y)$는 노이즈.

### 잡음 모델

- 잡음이 생성 되는 방식을 의미한다.
- 다양한 모델이 존재하며 가장 대표적인 것은 가우시안 잡음 모델이다.
  - 평균이 $0$인 가우시안 분포를 따르는 잡음을 의미한다.
  - 평균이 $0$이고 표준편차가 $\sigma$인 가우시안 분포는 $x$값이 $-\sigma\leqq{x}\leqq{\sigma}$ 구간에서 전체 데이터의 $67\%$가 존재하며, $-2\sigma\leqq{x}\leqq{2\sigma}$ 구간에서 전체 데이터의 $95\%$가 존재, $-3\sigma\leqq{x}\leqq{3\sigma}$ 구간에서 전체 데이터의 $99.7\%$가 존재한다.
- 표준편차가 작은 가우시안 모델일수록 잡음에 의한 픽셀값의 변화가 적어진다.

### cv::randn(dst, mean, stddev)

- `dst` 행렬을 가우시안 분포를 따르는 난수로 채운다.
- `dst` 행렬은 미리 적절한 타입으로 정의 되어있어야하며, 생성 된 난수는 `dst` 행렬의 자료형에 맞게끔 포화연산 된다.
- 평균이 $0$인 가우시안 잡음을 생성 할 경우 양수와 음수가 섞여있는 난수가 발생 되므로 `CV_32S, CV_32F`처럼 부호가 있는 자료형을 선택한다.

```cpp
#include "opencv2/opencv.hpp"

void noise_gaussian();

int main(void)
{
    noise_gaussian();

    return 0;
}

void noise_gaussian()
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    std::vector<cv::Mat> noised({src});
    for (int stddev = 10; stddev <= 30; stddev+=10)
    {
         cv::Mat noise(src.size(), CV_32SC1);  // 노이즈 생성
         cv::rand(noise, 0, stddev);

         cv::Mat dst;
         cv::add(src, noise, cv::Mat(), CV_8U);  // 노이즈 추가

        noised.push_back(dst);
    }

    cv::Mat images;
    cv::hconcat(noised, images);

    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();
}
```

## 양방향 필터 (Biateral filter)

- 에지 정보는 그대로 유지하면서 잡음만 제거하는 에지 보전 잡음 제거 필터 (edge-preserving noise removal filter).
- 대부분의 영상에는 가우시안 잡음이 포함 되어있으므로 가우시안 필터를 통해 노이즈를 제거한다.
  - 가우시안 필터를 이용한 노이즈 제거는 가우시안 잡음을 효과적으로 제거하지만 그만큼 에지 성분도 감소하게 되면서 영상이 전체적으로 흐릿해진다.
  - 양방향 필터를 이용하게 되면 에지 성분은 유지하되 잡음은 제거할 수 있다.
- $G_p = \frac{1}{W_p}\sum\limits_{q\in{S}}G_{\sigma_s}(\|p - q\|)G_{\sigma_r}(|\int_p - \int_q|)$
  - $\int$는 입력, $G$는 출력이다.
  - $p, q$는 각각 픽셀의 좌표이다.
  - $\int_p, \int_q$는 $p$점과 $q$점간의 입력 영상 픽셀값, $G_p$는 p$점에서의 출력영상 픽셀값이다.
  - $S$는 필터의 크기, $W_p$는 양방향 필터 마스크 합이 1이 되도록하는 정규화 상수이다.
  - 해당 식은 두 개의 가우시안 함수의 곱으로 이루어져있다.
  - $G_{\sigma_s}(\|p - q\|)$ 는 두 점 사이의 거리에 대한 가우시안 함수
  - $G_{\sigma_r}(|\int_p - \int_q|)$ 는 두 점의 픽셀값 차이에 의한 가우시안 함수
    - 두 점의 픽셀 밝기의 차이가 적은 평탄한 지역에선 큰 가중치를 갖고 에지를 사이에 두면 $0$에 가까운 작은 값을 갖게 된다.
- 픽셀값의 차이에 의존적이기 때문에 모든 픽셀에 대해 서로 다른 형태를 갖게 된다.
- 모든 픽셀의 밝기 차이에 의한 고유의 마스크 필터 행렬을 만들어 마스크 연산을 수행해야한다.
  - 일반적인 가우시안 블러링보다 훨씬 많은 연산량을 갖게 된다.
- `cv::biateralFilter(src, dst, d, sigmaColor, sigmaSpace, borderType);`
  - `d`: 필터링에 사용할 이웃 픽셀간의 거리 (양수가 아닌 값을 지정하면 `sigmaSpace`에서 자동으로 계산한다.)
  - `sigmaColor`: 픽셀 값 차이에 따라 차이가 작으면 차이가 큰 주변 픽셀과는 블러링이 되지 않으며 차이가 크면 주변 픽셀과도 블러링 연산을 수행한다.
  - `sigmaSpace`: 일반적인 표준편차 값이다.

```cpp
#include "opencv2/opencv.hpp"

void filter_bialteral();

int main(void)
{
    filter_bialteral();

    return 0;
}

void filter_bialteral()
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat noise(src.size(), CV_32SC1);  // 노이즈 생성
    cv::randn(noise, 0, 5);

    cv::Mat dst1;
    cv::GaussianBlur(src, dst1, cv::Size(), 5);  // 표준편차가 5인 가우시안 블러 연산 처리

    cv::Mat dst2;
    cv::bialteralFilter(src, dst2, -1, 10, 5);  // 색 표준편차가 10 이고 거리 표준편차가 5 인 양방향 필터링 연산 수행

    std::vector<cv::Mat> blurred({src, dst1, dst2});
    
    cv::Mat images;
    cv::hconcat(blurred, images);
    
    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();
}
```

## 미디언 필터 (Median filter)

- 입력 영상에서 자기 자신의 픽셀과 주변 픽셀 값 중에서 중간값을 선택하여 결과 영상 픽셀값으로 설정하는 필터링이다.
- 마스크 행렬과 입력 영상의 픽셀값을 서로 곱한 후 모두 더하는 등의 연산을 수행하지 않는다.
- 주변 픽셀들의 중간값을 선택하기 위해 내부에서 픽셀을 정렬한다.
  - e.g.) $((48, 60, 72), (52, 102, 88), (69, 84, 92))$ 형태인 3x3 형태의 행렬의 중간값인 $72$로 선택해 필터링 연산을 수행한다.
- 잡은 픽셀값이 주변 픽셀값과 차이가 큰 경우에 더욱 효과적이다.
  - **Salt & Pepper** 잡음 등에 효과적이다.
    - 픽셀의 값이 $0$ 또는 $255$으로 변경 되는 형태의 잡음.
- `cv::medianBlur(src, dst, ksize);`
  - `ksize`: 필터 크기, $3$과 같거나 큰 홀수를 지정해야한다.
  - 내부적으로 `BORDER_REPLICATE`를 수행한다.

```cpp
#include "opencv2/opencv.hpp"

void filter_median();

int main(void)
{
    filter_median();

    return 0;
}

void filter_median()
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    // salt & pepper noise
    cv::Mat noise = src.clone();
    int num = (int)(noise.total() * 0.1);

    for (int i = 0; i < num; i++)
    {
        int x = rand() % noise.cols;
        int y = rand() % noise.rows;

        noise.at<uchar>(y, x) = (i % 2) * 255;
    }

    cv::Mat dst;
    cv::medianBlur(noise, dst, 3);  // 필터 크기를 3으로 하는 메디안 필터 연산 수행

    std::vector<cv::Mat> blurred({src, dst});
    cv::Mat images;
    cv::hconcat(bluured, images);

    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();
}
```
