---
title: OpenCV 행렬 변환
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 행렬의 데이터 타입 변경

- `cv::Mat.convertTo(cv::Mat, type)`의 형태
- 행렬 전체의 데이터타입을 변환시킨다

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    float data[] = {1, 2, 3, 4};

    cv::Mat mat1(2, 2, CV_32FC1, data);  // 32bit float 타입의 행렬 생성
    cv::Mat mat2;  // 빈 행렬 생성

    mat1.convertTo(mat2, CV_8UC1);  // 32bit float 타입의 행렬 mat1 을 8bit uchar 타입의 행렬로 변환후 mat2 에 복사

    return 0;
}
```

## 행렬의 모양 변경

- `cv::Mat.reshape(channel, rows)`의 형태
- 행렬의 행을 기준으로 모양을 변경
  - `channel`의 값이 0 이면 채널값은 변경 안 함
  - `rows`의 값이 0 이면 행 값은 변경 안 함
- `reshape` 함수의 반환행렬의 원소값을 변경하면 원본 행렬의 원소값도 변경
  - 참조를 하기 때문

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    unchar data[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12};

    cv::Mat mat1(3, 4, CV_8UC1, data);  // 3 x 4 형태의 행렬 생성
    cv::Mat mat2 = mat1.reshape(0, 1);  // 채널변경 없이 행이 1 개짜리인 행렬 반환 (1 x 12)

    mat2.at<uchar>(3) = 100;  // reshape 된 행렬의 3번째 요소를 100으로 변환

    std::cout << "mat1:\n" << mat1 << std::endl;  // 원본행렬의 3번째 요소도 100으로 변경
    std::cout << "mat2:\n" << mat2 << std::endl;

    return 0;
}

/*
mat1:
[1, 2, 3, 100;
 5, 6, 7, 8;
 9, 10, 11, 12]

mat2:
[1, 2, 3, 100, 5, 6, 7, 8, 9, 10, 11, 12]
*/
```

## 행렬의 행 개수 변경

- `cv::Mat.resize()`의 형태
- 행렬의 마지막 행 기준으로 행 혹은 행렬 추가
  - 기존 행렬의 행 개수보다 함수의 인자값이 크다면 행 추가
  - 기존 행렬의 행 개수보다 함수의 인자값이 작다면 행 삭제
- 행 추가의 경우 초기값 지정 가능

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    uchar data[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12};
    
    cv::Mat mat1(3, 4, CV_8UC1, data);  // 3 x 4 행렬 생성

    mat1.resize(5, 100);  // 5 x 4 행렬로 리사이징하며 추가 되는 행렬의 초기값은 100으로 지정
    mat1.resize(2, 100);  // 2 x 4 행렬로 리사이징하며 삭제 되는 행렬은 초기값이 필요 없으므로 100은 무시

    return 0;
}
```

## push_back, pop_back

- `cv::Mat.push_back()`, `cv::Mat.pop_back()`의 형태
- `push_back`은 마지막 행에 행 혹은 행렬을 추가
  - `*this`의 열 크기, 데이터 타입이 같아야함
- `pop_back`은 마지막 행 기준 행 혹은 행렬 삭제
  - `*this`의 행 크기보다 작아야함

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    uchar data[] = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12};

    cv::Mat mat(3, 4, CV_8UC1, data);

    mat.push_back(cv::Mat(1, 4, CV_8UC1));  // 1 x 4 행렬 (행 벡터) 추가
    mat.push_back(cv::Mat(2, 4, CV_8UC1));  // 2 x 4 행렬 추가

    mat.pop_back(1);  // 마지막 행 기준 1 x 4 행렬 (행 벡터) 삭제
    mat.pop_back(2);  // 마지막 행 기준 2 x 4 행렬 삭제
    
    return 0;
}
```
