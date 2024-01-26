---
title: OpenCV 컬러스케일
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 컬러 영상 픽셀 참조

- `cv::imread("/path/to/src", cv::IMREAD_COLOR);` 함수로 `BGR` 형식으로 불러온다.
- 각 컬러는 0 부터 255 까지, `uchar` 형태를 사용하며, 컬러스케일에서 하나의 픽셀은 기본적으로 `BGR` 값을 가지므로 `cv::Vec3b` 형을 가진다.
  - `cv::Vec3b` 는 크기가 3 인 `uchar` 의 배열이며 크기도 `3 bytes` 이다.
- 픽셀참조는 그레이스케일과 마찬가지로 `cv::Mat::at()` 함수 혹은 `cv::Mat::ptr()` 함수를 사용한다.

### cv::Mat.at

- 템플릿으로 정의 되어있으므로 `cv::Vec3b` 으로 명시해준다.
- `cv::Vec3b& pix = cv::Mat::at<cv::Vec3b>(i, j);` 에서 $i, j$ 픽셀의 값을 반환하는데, 각 배열의 $0, 1, 2$ 번째 값은 `B, G, R`을 나타낸다.

### cv::Mat.ptr

- `at()` 함수와 마찬가지로 `cv::Vec3b` 으로 명시해준다.
- `cv::Vec3b* ptr = cv::Mat.ptr<cv::Vec3b>(i);` 에서 $i$번째 행의 $j$ 번째 열은 `ptr[j]`로 표현할 수 있는데, `ptr[j][0]` 등으로 접근할 수 있다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("/path/to/src", cv::IMREAD_COLOR);
    cv::Mat dst(src.rows, src.cols, src.type());  // 입력 영상과 동일한 크기, 타입의 행렬 생성

    for (int i = 0; i < src.cols; i++)
    {
        for (int j = 0; j < src.rows; j++)
        {
            cv::Vec3b &pix = src.at<cv::Vec3b>(i, j);

            dst[0] = 255 - pix[0];  // 색상 반전
            dst[1] = 255 - pix[1];
            dst[2] = 255 - pix[2];
        }
    }

    cv::Mat dst2 = cv::Scalar(255, 255, 255) - src;  // 색상 반전은 주로 이 방법을 사용한다.

    cv::imshow("src", src);
    cv::imshow("dst", dst);

    cv::waitKey(0);
    cV::destroyAllWindows();

    return 0;
}
```

## 색 공간 변환

- 실생활에서는 `BGR` 혹은 `RGB` 공간이라고 부르는 타입을 많이 사용하지만 영상처리에서는 `HSV`, `YCrCb` 등이 더 유리하다.

### cv::cvtColor

- `cv::cvtColor(src, dst, code, dstCn = 0);`
  - `code`: 색공간 변환 코드. `ColorConversionCodes` 열거형 상수 중 하나를 지정한다.
    - `cv::COLOR_BGR2GRAY`, `cv::COLOR_BGR2HSV`, ...
    - `COLOR_BGR2GRAY`: `BGR` 을 `GRAY` 로 변환한다. 공식은 $Y = 0.299R + 0.587G + 0.114B$이다.
    - `COLOR_GRAY2BGR`: `GRAYSCALE`을 `BGR`로 변환한다. 공식은 $R = G, B = Y$이다.
    - 그 외에 여러 코드가 있고 여러 계산식이 존재한다.

### HSV

- `H`: 색상 (hue)
- `S`: 채도 (saturation)
- `V`: 명도 (value)
- `BGR` 에서 `HSV` 로 변환할 때 `H` 는 0 부터 179 사이의 정수, `S` 와 `V` 는 0 부터 255 사이의 정수로 표현한다.
- 색상 값은 주로 $0^\circ$부터 $360^\circ$ 로 표현하지만 `uchar` 자료형은 256 이상은 표현할 수 없으므로 각도를 2로 나눈 값을 `H` 에 저장한다.
- 함수의 입력 영상이 0 부터 1 사이의 값으로 정규화 된 `CV_32FC3` 라면 `H` 는 0부터 360 사이의 실수로, `S` 와 `V` 는 0부터 1 사이의 실수값으로 표현 된다.

### YCrCb

- `Y`: 휘도 (luminance)
- `Cr, Cb`: 색상 혹은 색차 (chrominance)
- `BGR` 에서 `YCrCb` 로 변환할 때 `Y` 는 그레이스케일 계산 공식과 완전 동일하다.
- `CrCb` 는 색상 정보만 갖고 밝기 정보는 갖지 않으므로 그레이스케일 정보와 색상 정보로 분리하여 처리 할 유용하다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_COLOR);

    cv::Mat dst;
    cv::Mat dst2;

    cv::cvtColor(src, dst, cv::COLOR_BGR2GRAY);  // 그레이스케일로 변환
    cv::cvtColor(src, dst2, cv::COLOR_BGR2HSV);  // HSV 스케일로 변환

    cv::imshow("src", src);
    cv::imshow("dst", dst);
    cv::imshow("dst", dst2);  // cv::imshow 는 BGR 컬러스케일을 상정하므로 HSV, YUV 등은 부적합하다.

    cv::waitKey(0);
    cv::destroyAllWindows();

    return 0;
}

```

### cv::split()

- 색상 채널을 나눈다.
- `cv::split(src, mv);`
  - `mv`: 분리 된 1채널 행렬을 저장 할 `std::vector<cv::Mat>`

### cv::merge()

- 분리 된 색상 채널을 합친다.
- `cv::merge(mv, dst);`
  - `mv`: 분리 된 색상채널들이 담겨있는 `std::vector<cv::Mat>`
  - `dst`: 출력 영상

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_COLOR);

    std::vector<cv::Mat> mv;

    cv::split(src, mv);  // BGR 을 각 채널로 나눈 행렬
    
    cv::Mat b = mv[0];  // 각 채널 영상을 저장
    cv::Mat g = mv[1];
    cv::Mat r = mv[2];

    cv::Mat dst;
    cv::merge(mv, dst);  // 나눈 행렬들을 다시 합침

    cv::Mat bgr;
    cv::hconcat(mv, bgr);

    cv::imshow("src", src);
    cv::imshow("dst", dst);
    cv::imshow("bgr", bgr);

    cv::waitKey(0);
    cv::destroyAllWindows();

    return 0;
}

```
