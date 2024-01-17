---
title: OpenCV 행렬 복사
author: yjh
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 복사 생성자를 통한 행렬 복사

- 복사 생정자 `=` 를 통한 행렬 복사
- `cv::Mat img2` 라는 객체를 생성 후 `cv::Mat img` 객체를 참조
-`img` 를 참조하는 얕은 복사이기 때문에 `img` 변화에 영향을 받음

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Mat img2 = img;

    return 0;
}
```

## clone() 함수를 통한 행렬 복사

- 픽셀 데이터를 공유하는 것이 아닌 메모리 공간을 새로 할당 (깊은 복사)
- 자기 자신과 동일한 `cv::Mat` 객체를 완전히 새로 만들어 반환

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Mat img2 = img.clone();

    return 0;
}
```

## copyTo() 함수를 통한 행렬 복사

- 픽셀 데이터를 공유하는 것이 아닌 메모리 공간을 새로 할당 (깊은 복사)
- 함수 이름에서 알 수 있듯이 원형이 되는 함수를 변수로 받는 객체로 복사한다

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Mat img2;  // 빈 객체 생성
    img.copyTo(img2);  // 빈 객체에 img를 복사

    return 0;
}
```