---
title: OpenCV 트랙바 생성
author: yjh
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 트랙바 생성

- 트랙바 관련 콜백 함수`cv::createTrackbar`를 `cv::setTrackbarPos` 함수에 전달한다.
- `cv::createTrackbar(trackbar name, window name, pos, level, call back function, image);`
  - `pos`는 트랙바가 어디서 시작할지를 지정한다.
- 콜백 함수 내에서는 `cv::getTrackbarPos` 함수를 통해 트랙바가 위치한 윈도우에서 값을 받아온다.

```cpp
#include "opencv2/opencv.hpp"

void on_level_change(int pose, void* userdata);  // 콜백 함수 선언

cv::Mat img = cv::Mat::zeros(400, 600, CV_8UC1);  // 전역변수로 선언

int pos = 0;  // 시작점

int main(void)
{
    cv::namedWindow("debug");  // 이미지를 출력 할 윈도우 설정
    cv::createTrackbar("level", "debug", &pos, 16, on_level_change, (void*)&img);

    cv::setTrackbarPos("level", "debug", 2);

    return 0;
}

void on_level_change(int pose, void* userdata)
{
    cv::Mat img = *(cv::Mat*)usedata;

    img.setTo(pose * 16);  // 트랙바가 1단계씩 커질 때마다 프레임 전체 값에 += 16
    
    cv::imshow("debug", img);

    pose = cv::getTrackbarPos("level", "debug");
}

```