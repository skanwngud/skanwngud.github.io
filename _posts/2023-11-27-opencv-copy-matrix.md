---
title: OpenCV 행렬 복사
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

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

- 

## copyTo() 함수를 통한 행렬 복사
