---
title: 행렬 계산
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

## 행렬 연산

- 행렬과 스칼라간의 합, 곱
- 행렬과 행렬간의 합, 곱 (수학적인 곱, 같은 위치의 원소간의 곱)
- 역행렬
- 전치행렬 등

### 역행렬

- 정방형의 행렬 $A$에 대해 $A$의 역행렬 $A^{-1}$과의 곱 $AA^{-1}$은 단위 행렬 $E$가 나옴
- `cv::Mat.inv()`로 역행렬 생성

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    float data[] = {1, 2, 3, 4};

    cv::Mat mat1(2, 2, CV_8UC1, data);  // 행렬 선언 및 초기화
    cv::Mat mat2 = mat1.inv();  // mat1 에 대한 역행렬 생성
    
    std::cout << "mat1:\n" << mat1 << std::endl;
    std::cout << "mat2:\n" << mat2 << std::endl;
    std::cout << "mat1 * mat2:\n" << mat1 * mat2 << std::endl;

    return 0;
}

/*
mat1:
[1, 2;
 3, 4]

mat2:
[-2, 1;
 1.5, -0.5]

mat1 * mat2:
[1, 0;
 0, 1]
*/
```

### 전치행렬

- 행렬의 행과 열의 순서를 바꿈
- `cv::Mat.t()`의 형태로 선언

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    float data[] = {2, 3, 4, 5};

    cv::Mat mat1(2, 2, CV_8UC1, data);
    cv::Mat mat2 = mat1.t();  // 전치 행렬 생성

    std::cout << "mat1:\n" << mat1 << std::endl;
    std::cout << "mat2:\n" << mat2 << std::endl;

    return 0;
}

/*
mat1:
[2, 3;
 4, 5]

mat2:
[2, 4;
 3, 5]
*/
```

### 행렬의 곱

- $A * B$와 `cv::Mat.mul(cv::Mat)`이 존재
- $A * B$는 수학적인 행렬의 곱이고 `mul`함수는 같은 위치의 원소끼리의 곱셈이다

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    float data1[] = {1, 2, 3, 4};
    float data2[] = {2, 3, 4, 5};

    cv::Mat mat1(2, 2, CV_8UC1, data1);
    cv::Mat mat2(2, 2, CV_8UC1, data2);

    std::cout << "mat1 * mat2:\n" << mat1 * mat2 << std::endl;
    std::cout << "mat1.mul(mat2):\n" << mat1.mul(mat2) << std::endl;

    return 0;
}

/*
mat1 * mat2:
[10, 13;
 22, 29]

mat1.mul(mat2):
[2, 6,
 12, 20]
*/

```
