---
title: OpenCV 마스크 연산
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

## 마스크 연산

- 영상 (프레임), 이미지 등의 일부 영역에 대해서만 특정 연산을 수행하는 것.

- 주로 영상을 그레이스케일로 변환한 뒤 연산한다.

## setTo

- `operator::Mat.setTo(마스크 값, 마스크 행렬);`의 형태

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("lenna.bmp", cv::IMREAD_COLOR);
    cv::Mat mask = cv::imread("mask_smile.bmp", cv::IMREAD_GRAYSCALE);

    src.setTo(cv::Scalar(0, 255, 255), mask);

    cv::imshow("debug", src);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```

![lenna](/assets/img/lenna.bmp)

![mask_smile](/assets/img/mask_smile.bmp)

![lenna_result](/assets/img/lenna_result.jpg)

- `lenna.bmp`에 `mask_smile.bmp` 영역을 합성한다.

## copyTo

- 복사 할 행렬만 전달하면 단순 행렬 복사한다.
- 복사 할 행렬과 마스크 연산을 입력으로 받으면 마스크 연산을 수행한다.
- 마스크 영역의 행렬값들을 `dst` 행렬로 복사한다.
  - 마스크 영역에 있는 값들은 $0$ 혹은 $255$ 이며 `copyTo`를 통해 $0$이 아닌 값들만 `dst` 행렬로 복사한다.
  - $0$이 아닌 위치의 값들만 복사 되었기 때문에 해당 영역의 그람만 합성 된다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("airplane.bmp", cv::IMREAD_COLOR);
    cv::Mat mask = cv::imread("mask_plane.bmp", cv::IMREAD_GRAYSCALE);
    cv::dst = cv::imread("field.bmp", cv::IMREAD_COLOR);

    src.copyTo(dst, mask);

    cv::imshow("debug", dst);
    cv::waitKey();

    cv::destroyAllWindows();
}
```

![airplane](/assets/img/airplane.bmp)

![mask_airplane](/assets/img/mask_plane.bmp)

![field](/assets/img/field.bmp)

![airplane_result](/assets/img/airplane_result.jpg)

- `airplane.bmp`의 비행기 영역을 지정 ($0$이 아닌 값의 위치)하여 마스크를 생성한 뒤 해당 영역의 행렬값만 복사하여 `field.bmp`에 합성한다.
- `field.bmp`에 `airplane.bmp`의 비행기 영역의 값들이 복사 되어 합성 됨.