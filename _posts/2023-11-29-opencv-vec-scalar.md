---
title: OpenCV Vec Scalar
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## Vec 클래스

- `OpenCV`에서 지원하는 데이터 타입
- 특정 벡터에 접근할 수 있는 `.val()` 멤버 함수 존재
  - `[]` 연산자 재정의로 `vector.val(int x)`를 `vector[int x]`로 접근 가능함

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    cv::Vec3b p1, p2(0, 0, 255);  // 벡터 선언

    std::cout << p1 << std::endl;  // [0, 0, 0]으로 초기화
    std::cout << p2 << std::endl;  // [0, 0, 255]으로 초기화

    p1.val(0) = 100;  // p1의 0번째 원소값에 100 대입
    p1[1] = 100;  // p1의 1번째 원소값이 100 대입

    std::cout << p1 << std::endl;  // [100, 100, 0]으로 변경

    return 0;
}
```

## Scalar_ 클래스

- 원래 모양은 `Scalar_<typename T>`으로 선언하나 자주 사용하는 타입은 템플릿으로 재정의
- `Scalar` 클래스는 4개 이하의 채널을 갖는 영상 혹은 이미지의 픽셀값을 표현하는 용도로 많이 사용
  - 크기가 4인 `double` 형태의 배열을 갖는 자료형
  - 4개 이하인 채널의 영상값을 표현할 땐 RGBa 값으로 표현

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

void ScalarOp();


int main(void)
{
    ScalarOp();

    return 0;
}


void ScalarOp()
{
    cv::Scalar gray = 128;  // [128, 0, 0, 0] 행렬 생성
    std::cout << "gray: " << gray << std::endl;

    cv::Scalar yellow(0, 255, 255);  // [0, 255, 255, 0] 행렬 생성
    std::cout << "yellow: " << yellow << std::endl;

    cv::Mat img1(256, 256, CV_8UC3, yellow);  // 256 x 256 크기에 yellow 대입해 선언

    for(int i = 0; i < 4; i++)
    {
        std::cout << yellow[i] << std::endl;  // Scalar 클래스도 Vec 클래스처럼 [] 연산자로 접근 가능
    }
}

/*
gray: [128, 0, 0, 0]
yellow: [0, 255, 255, 0]
0
255
255
0
*/
```
