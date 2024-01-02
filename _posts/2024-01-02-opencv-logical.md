---
title: OpenCV 논리연산
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 논리 연산

- 픽셀값을 이진수로 표현하여 비트 단위의 논리 연산을 수행한다.
- 논리곱 (`AND`), 논리합 (`OR`), 배타적 논리합 (`XOR`), 부정 (`NOT`) 을 지원한다.
- `AND, OR, XOR`은 두 입력 영상의 각 픽셀에 대한 연산을 실시하지만 `NOT`은 하나의 입력 영상의 전체 픽셀을 일괄적으로 연산한다.
- 연산자 오버로딩을 지원한다.
  - `AND` = `&`
  - `OR` = `|`
  - `XOR` = `^`
  - `NOT` = `~`

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src1 = cv::imread("path/to/src1", cv::IMREAD_GRAYSCALE);
    cv::Mat src2 = cv::imread("path/to/src2", cv::IMREAD_GRAYSCALE);

    cv::Mat dst1, dst2, dst3, dst4;

    cv::bitwise_and(src1, src2, dst1);
    cv::bitwise_or(src1, src2, dst2);
    cv::bitwise_xor(src1, src2, dst3);
    cv::bitwise_not(src1, dst4);

    cv::Mat images;
    cv::hconcat(std::vector<cv::Mat>({dst1, dst2, dst3, dst4}), images);

    cv::imshow("debug", images);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```
