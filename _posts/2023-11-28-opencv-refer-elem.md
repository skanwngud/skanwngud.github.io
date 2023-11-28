---
title: OpenCV 픽셀 참조
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

## cv::Mat::at()

- 가장 직관적이며 행과 열을 나타내는 두 개의 정수를 인자로 받아 해당 위치의 픽셀값을 참조
- 템플릿으로 재정의해야하므로 항상 자료형을 정의해야함
- RGB를 갖는 3채널의 자료형은 `cv::Vec3b`이다

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Vec3b pix = img.at<cv::Vec3b>(100, 100);

    // 행렬을 순환해가며 픽셀 값 참조하기
    for(int i = 0; i < img.rows; i ++)
    {
        for(int j = 0; j < img.cols; j++)
        {
            std::cout << "at pixel value(" << i << ", " << j << "): " << img.at<cv::Vec3b>(i, j) << std::endl;
        }
    }  // i, j 값을 통해 행렬 전체를 순환해가며 픽셀값을 반환한다

    return 0;
}
```

## cv::Mat::ptr()

- 특정 행의 첫 번째 원소의 *주소값*을 반환
- 해당 함수는 행 단위로 행렬을 순환한다
- `cv::Mat::at()`과 마찬가지로 3채널의 자료형은 `cv::Vec3b`이다.
  - 1채널을 갖는 (그레이스케일) 행렬은 `uchar`.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Vec3b *ptr = img.ptr<cv::Vec3b>(100);  // 행값만 입력하면 해당 행 전체 중 첫 번째 픽셀값의 주소값만 반환
    cv::Vec3b ptr100 = ptr[100];  // 100번째 행의 100번째 열의 픽셀값

    // 행렬을 순환해가며 픽셀 값 참조하기
    for(i = 0; i < img.rows; i++)
    {
        cv::Vec3b *ptr = img.ptr<cv::Vec3b>(i);  // 행 지정
        for(int j = 0; j < img.cols; j++)
        {
            std::cout << "ptr pixel value(" << i << ", " << j << "): " << ptr[j] << std::endl;
        }
    }

    return 0;
}
```

> 행 단위의 접근은 ptr이 훨씬 빠르지만 임의의 픽셀 접근은 at이 더 편리하다.

## cv::MatIterator_

- `cv::Mat::at()`과 `cv::Mat::ptr()`은 행렬의 범위를 벗어나면 에러 발생
- 이를 방지하기 위해 `OpenCv`에선 반복자`iterator`를 지원
- 행렬의 `begin`과 `end`를 통해 접근하므로 범위를 벗어날 염려가 없음
- 행렬 전체를 순차적으로 순회하기 때문에 `ptr`보단 느리고 `at`보단 자유도가 낮아 사용성이 높지 않음

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    for(cv::MatIterator_<cv::Vec3b> i = img.begin<cv::Vec3b>(); i != img.end<cv::Vec3b>(); ++i)
    {
        std::cout << "iter pixel value: " << *i << std::endl;
    }

    return 0;
}
```

- `i`가 `begin`에서 시작해 `end`가 아닐 때까지 행렬 전체를 순환한다
