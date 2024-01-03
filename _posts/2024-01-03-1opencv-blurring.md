---
title: OpenCV 블러링
author: yjh
math: true
categories: [CPP, OpenCv]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 블러링 (Blurring)

- 영상을 부드럽게 만드는 기법. 스무딩 (smoothing) 이라고도 한다.
- 인접한 픽셀간 픽셀값의 변화가 크지 않은 경우 부드러운 느낌을 받을수 있다.
- 거친 입력 영상을 부드럽게 하거나 노이즈를 제거하는 전처리 과정으로 사용할 수 있다.
- `OpenCV` 에서는 `cv::blur`(평균값 필터) 와 `cv::GaussianBlur`(가우시안 필터) 함수를 제공한다.

### 평균값 필터 (Mean filter)

- 블러링 필터 중에서 간단하고 구현하기 쉬운 필터이다.
- 입력 영상에서 특정 픽셀과 주변 픽셀들의 산술 평균을 결과 영상 픽셀값으로 설정한다.
- 급격한 변화가 줄어들어 에지가 무뎌지고 잡음의 영향이 크게 사라지는 효과가 있다.
  - 너무 과도하게 사용하면 경계가 흐릿해지고 사물의 구분이 어려워지는 단점이 존재한다.
- 마스크 크기가 커지면 커질수록 영상은 더 부드러워지지만 그만큼 연산량이 크게 증가하게 된다.
- 보통 정사각행렬을 사용한다.

```cpp
#include "opencv2/opencv.hpp"

void blurMean();

int main(void)
{

    return 0;
}

void blurMean()
{
    cv::Mat src = cv::imreaD("path/to/src", cv::IMREAD_GRAYSCALE);

    std::vector<cv::Mat> blurred({src});

    for (int ksize = 3; ksize <= 5; ksize+=2)
    {
        cv::Mat dst;
        cv::blur(src, dst, cv::Size(ksize, ksize));

        blurred.push_back(dst);
    }

    cv::Mat images;
    cv::hconcat(blurred, images);

    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();
}
```

### 가우시안 필터

- 평균값 필터보다 자연스러운 블러링 기법이다.
- 가우시안 분포 함수를 근사하여 생성한 필터를 마스크를 사용한다.
  - 가우시안 분포는 평균과 표준 편차에 따라 분포모양이 결정 되지만 영상의 경우 평균이 $0$인 가우시안 분포 함수를 사용한다.
  - 또한 영상은 2차원 행렬이므로 평균이 $(0, 0)$이므로 $x$ 축과 $y$ 축 방향의 표준편차가 각각 $\sigma_{x}$, $\sigma_{y}$ 인 2차원 가우시안 분포 함수를 사용한다.
- $G_{\sigma_{x}\sigma_{y}} = \frac{1 * e^{\frac{-x^2}{2\sigma_{x}^{2}} + \frac{y^2}{2\sigma_{}y^{2}}}}{2\pi\sigma_{x}\sigma_{y}}$
  - 헤딩 식의 경우 한 픽셀 당 $81$번의 연산이 이루어지기 때문에 연산량이 굉장히 많다.
  - $G_{\sigma_{x}}, G_{\sigma_{y}}$ 의 형태로 나누게 되면 $18$번의 연산만 수행하면 되므로 훨씬 경제적이다.
    - $G_{x}$ 연산 후 $(G_{x})^{T}$ 으로 연산한다.
- 평균이 $0$이고 표준편차가 $\sigma$인 경우에 $x$의 값은 대부분 $-4\sigma$ ~ $4\sigma$에 분포하기 때문에 크기는 보통 $8\sigma + 1$로 결정한다.
- `cv::GaussianBlur(cv::Mat src, cv::Mat dst, cv::Size ksize, double sigmaX, double sigmaY, int borderType)`
  - `ksize`: `cv::Size()`로 주어 자동으로 마스크의 크기를 결정해주는 편이 좋다.
  - `sigmaY`: $0$ 혹은 지정하지 않으면 `y`축으로 `sigmaX` 값과 동일한 값을 가지는 표준 편차를 사용한다.

```cpp
#include "opencv2/opencv.hpp"

void blurGaussian();

int main(void)
{
    blurGaussian();

    return 0;
}

void blurGaussian()
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    std::vecotr<cv::Mat> blurred({src});
    for (int sigma = 1; sigma <= 5; sigma++)
    {
        cv::Mat dst;
        cv::GaussianBlur(src, dst, cv::Size(), (double)sigma);

        blurred.push_back(dst);
    }

    cv::Mat images;
    cv::hconcat(blurred, images);

    cv::imshow("debug", blurred);
    cv::waitKey();

    cv::destroyAllWindows();
}
```
