---
title: OpenCV 산술 연산
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 산술 연산

- 영상 및 이미지는 2차원 행렬로 볼 수 있으므로 행렬의 산술 연산이 가능하다.
- 덧셈 및 뺄셈 연산으로 영상을 합성하여 새로운 영상으로 나타낼 수 있다.
  - 곱셈과 나눗셈 연산은 잘 하지 않는다.

### add

- 덧셈 연산.
- $dst(x, \;y) = saturate(src1(x \;y) \;+ \;src2(x, \;y))$
- `cv::add(cv::Mat src1, cv::Mat src2, cv::Mat dst, int dtype=-1);`
- `src1`와 `src2`의 데이터 타입이 동일하다면 행렬 연산을 실시한다.
- 두 입력 영상의 데이터 타입이 다른 경우 한쪽 행렬의 전체 픽셀에 대해 스칼라 덧셈을 실시한다.
- `+` 연산자 오버로딩을 통해 연산이 가능하다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src1 = cv::imread("path/to/src1", cv::IMREAD_GRAYSCALE);
    cv::Mat src2 = cv::imread("path/to/src2", cv::IMREAD_GRAYSCALE);

    cv::Mat dst;

    cv::add(src1, src2, dst);

    cv::imshow("debug", dst);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```

### addWeighted

- 두 행렬의 덧셈 연산 시 가중치를 부여한다.
- $dst(x, \;y) = saturate(\alpha * src1(x, \;y) + \beta * src2(x, \;y))$
- `cv::addWeighted(cv::Mat src1, double alpha, cv::Mat src2, double beta, int gamma, cv::Mat dst, int dtype=-1);`
  - $\gamma$는 가중치 결과에 추가로 더할 값이다.
- $\alpha$와 $\beta$는 각 입력 영상의 가중치를 의미하는 실수이다.
  - $\alpha + \beta = 1$이 보편적이다.
  - $\alpha + \beta > 1$인 경우 두 영상의 평균 밝기보다 높은 밝기의 영상이 출력 된다.
  - $\alpha + \beta < 1$인 경우 두 영상의 평균 밝기보다 낮은 밝기의 영상이 출력 된다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src1 = cv::imread("path/to/src1", cv::IMREAD_GRAYSCALE);
    cv::Mat src2 = cv::imread("path/to/src2", cv::IMREAD_GRAYSCALE);

    cv::Mat dst;

    cv::addWeighted(src1, 0.4, src2, 0.6, 0, dst);

    cv::imshow("debug", dst);
    cv::waitKey();

    cv::dstroyAllWindows();

    return 0;
}
```

### subtract

- 빼기 연산.
- `add`와 동일하나 `subtract`는 뺄셈이므로 `src1`과 `src2`의 순서에 따라 결괏값의 차이가 발생한다.
- 두 입력 영상의 타입이 동일하다면 `-` 연산자로 연산 수행이 가능하다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src1 = cv::imread("path/to/src1", cv::IMREAD_GRAYSCALE);
    cv::Mat src2 = cv::imread("path/to/src2", cv::IMREAD_GRAYSCALE);

    cv::Mat dst1, dst2;

    cv::subtract(src1, src2, dst1);
    cv::subtrack(src2, src1, dst2);

    cv::Mat images;
    cv::hconcat(std::vector<cv::Mat>({dst1, dst2}), images);

    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```

### absdiff

- 차연산.
- 뺄셈 순서에 상관 없이 두 입력 영상의 픽셀차가 큰 영역을 나타내기 위해 사용.
- 두 입력 영상의 차이값의 절댓값을 나타낸다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src1 = cv::imread("path/to/src1", cv::IMREAD_GRAYSCALE);
    cv::Mat src2 = cv::imread("path/to/src2", cv::IMREAD_GRAYSCALE);

    cv::Mat dst;

    cv::absdiff(src1, src2, dst);
    
    cv::imshow("debug", dst);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```

### multiply, divide

- 곱셈, 나눗셈 연산.
- 실무에서 잘 사용하진 않으며 각 픽셀에 대한 연산을 수행한다.
