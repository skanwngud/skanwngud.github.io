---
title: OpenCV 행렬 생성
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 행렬 생성

### 행렬 초기화

- ```cv::Mat img(int height, int width, string type)```로 초기화하여 생성

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img(360, 640, CV_8UC3);

    cv::imshow("debug", img);
    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```

- 높이 360, 길이 640, RGB 3채널을 갖는 행렬 생성

### cv::Mat::create

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img2;
    img2.cv::Mat::create(cv::Size(640, 360), CV_8UC3);

    cv::imshow("debug", img2);
    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}

```

### 그 외

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat img3 = cv::Mat::zeros(360, 640, CV_8UC3);
    cv::Mat img4 = cv::Mat::ones(360, 640, CV_8UC3);
    cv::Mat img5 = cv::Mat::eyes(360, 640, CV_8UC3);

    return 0;
}
```

- `cv::Mat::zeros(int height, int width, flags)`: 0 으로 초기화 된 행렬 생성
- `cv::Mat::ones(int height, int width, flags)`: 1 로 초기화 된 행렬 생성
- `cv::Mat::eye(int height, int width, flags)`: 단위 행렬 (A x E = A 를 만족하는 행렬)
