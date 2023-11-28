---
title: OpenCV 행렬 범위 참조
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

## 이미지 crop 하기

- `cv::Mat` 객체에 `cv::Rect` 등으로 범위를 지정해줘 참조한다
- 단순히 픽셀 데이터를 참조하는 얕은 복사이므로 새로운 객체를 생성하고 싶다면 `clone()`을 먼저 사용 후 참조한다

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Mat roi = img(cv::Rect(100, 100, 200, 200));  // x, y, width, height
    roi = ~roi;  // 해당 영역을 반전시키면 img 도 색상 반전이 됨 (얕은 복사이기 때문)

    cv::Mat img2 = cv::imread("./path/to/image");

    cv::Mat roi2 = img(cv::Rect(100, 100, 200, 200)).clone();  // clone() 을 통해 객체 복사
    roi2 = ~roi2;  // 해당 영역의 색상을 반전시켜도 img2 에는 아무런 영향이 없음 (깊은 복사로 객체를 복사했기 때문)

    return 0;
}
```

## 지정한 행, 열의 범위 반환

- `cv::Mat.rowRange(int x, int y);`로 행 범위 지정
- `cv::Mat.colRange(int x, int y);`로 열 범위 지정

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img = cv::imread("./path/to/image");

    cv::Mat rows = img.rowRange(100, 100);
    cv::Mat cols = img.colRange(100, 100);

    std::cout << "rows: " << rows << std::endl << std::cout << "cols: " << cols << std::endl;

    return 0;
}

// 각 범위에 해당하는 픽셀의 데이터 값들이 출력 된다 (BRG) 값
```
