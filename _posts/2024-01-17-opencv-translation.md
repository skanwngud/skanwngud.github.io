---
title: OpenCV 어파인 변환 종류
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cv, opencv, cpp, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 이동 변환 (Translation Transform)

- 입력 영상을 가로 혹은 세로 방향으로 평행 이동 시키는 연산.
- 시프트 (Shift) 연산이라고도 한다.
- $$\begin{cases}
    x' = x + a \\
    y' = y + b \\
    \end{cases}$$

- $a, b$ 는 각각 $x, y$를 얼마만큼 평행 이동 시킬 것인지에 대한 수치이다.
- 이동에 대한 어파인 변환 행렬은 $$\begin{pmatrix}
    1 & 0 &a \\
    0 & 1 & b \\
    \end{pmatrix}$$의 형태이다.
- 어파인 변환 행렬을 구한 뒤 `cv::warpAffine()` 함수를 사용하여 변환을 수행한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src");
    cv::Mat dst1, dst2;

    cv::Mat M1 = cv::Mat_<double>({2, 3}, {1, 0, 50, 0, 1, 50});
    cv::Mat M2 = cv::Mat_<double>({2, 3}, {1, 0, 150, 0, 1, -50});

    cv::warpAffine(src, dst1, M, cv::Size());
    cv::waroAffine(src, dst2, M, cv::Size());

    return 0;
}
```

## 전단 변환 (Shear Transform)

- 직사각형의 영상을 한쪽 방향으로 밀어서 평행사변형 모양으로 변형시킨다.
- 원점은 고정 시킨 뒤 가로세로 방향으로 이동시킨다.
- 입력 영상의 기준 픽셀의 위치에 따라 이동 정도가 달라진다.
- $$\begin{cases}
    x' = x + m_{x}y \\
    y' = m_{y}x + y \\
    \end{cases}$$

- 전단 변환에 대한 어파인 변환 행렬은 $$\begin{pmatrix}
    1 & m_{x} & 0 \\
    m_{y} & 1 & 0 \\
\end{pmatrix}$$이다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src");
    cv::Mat dst;

    cv::Mat M = cv::Mat_<double>({2, 3}, {1, 0.2, 0, 0.2, 1, 0});

    cv::warpAffine(src, dst, M, cv::Size());

    return 0;
}
```

## 대칭 변환 (Flip Transform)

- 입력영상을 좌우 혹은 상하반전을 시키는 변환이다.
- $$\begin{cases}
    x' = w - 1 - x \\
    y' = h - 1 - y \\
    \end{cases}$$

- 좌우반전은 $x$ 축 방향으로 $-1$ 배 크기 변환 후 $x$ 축 방향으로 $w - 1$만큼 이동한다.
- 상하반전은 $y$ 축 방향으로 $-1$배 크기 변환 후 $y$축 방향으로 $h - 1$만큼 이동한다.
- 대칭 변환에 대한 어파인 변환 행렬은 $$\begin{pmatrix}
    -1 & 0 & w - 1 \\
    0 & -1 & h - 1 \\
\end{pmatrix}$$이다.

- 대칭 변환은 어파인 변환 행렬을 구해 `cv::warpAffine()` 함수를 이용해도 되지만 `cv::flip()` 함수를 따로 제공하기도한다.
- `cv::flip(src, dst, flipCode);`
  - `flipCode`: `int`를 받는 상수 열거형이며 양수면 좌우대칭, 0 이면 상하대칭, 음수면 상하좌우대칭을 둘 다 수행한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src");
    cv::Mat dst;

    cv::flip(src, dst, -1);

    return 0;
}
```

## 크기 변환 (Scale Transform)

- 입력 영상의 전체적인 크기를 확대하거나 축소하는 변환이다.
- $$\begin{cases}
    x' = S_xX \\
    y' = S_yY \\
    \end{cases}$$

- 크기 조정에 대한 어파인 변환 행렬은 $$\begin{pmatrix}
    S_x & 0 & 0 \\
    0 & S_y & 0 \\
    \end{pmatrix}$$이다.

- $S$의 값이 0보다 크면 확대, 0보다 작으면 축소된다.
- 어파인 변환 행렬을 통해 리사이징을 할 수도 있지만 `cv::resize()` 함수를 기본으로 제공한다.
- `cv::resize(src, dst, dsize, fx, fy, interpolation);`
  - `dsize`: 출력 영상의 크기를 상수로 명시해준다.
  - `fx, fy`: 출력 영상의 크기를 가로세로의 비율로 지정해준다.
  - `interpolation`: 영상의 확대 및 축소를 진행할 때의 보간법을 명시한다.
    - `INTER_NEAREST`: 속도가 가장 빠르지만 화질의 저하가 심하다.
    - `INTER_LINEAR`: 속도도 빠르고 화질도 충분히 좋기 때문에 `default` 값으로 설정 되어있으며, 실무에서도 많이 사용 된다.
    - `INTER_CUBIC,  INTER_LANCZOS4`: 화질이 우선시 되는 경우 사용한다. 속도는 상대적으로 느리다.
    - `INTER_AREA`: 영상의 축소를 진행 할 때 사용한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src");
    cv::Mat dstUp, dstDown;

    cv::resize(src, dstUp, cv::Size(), 1.2, 1.2, cv::INTER_LINEAR);
    cv::resize(src, dstDown, cv::Size(), 0.8, 0.8, cv::INTER_AREA);

    return 0;
}
```

## 회전 변환 (Rotatation Transform)

- 특정 좌표를 기준으로 영상을 원하는 각도만큼 회전시킨다.
- $$\begin{cases}
    x' = \cos{\theta}x + \sin{\theta}y \\
    y' = -\sin{\theta}x + \cos{\theta}y \\
    \end{cases}$$

- 회전 변환에 대한 어파인 변환 행렬은 $$\begin{pmatrix}
    \cos{\theta} & \sin{\theta} & 0 \\
    -\sin{\theta} & \cos{\theta} & 0 \\
    \end{pmatrix}$$이다.

- `cv::getRotationMatrix2D()` 함수를 이용하면 좀 더 쉽게 어파인 변환 행렬을 얻을 수 있다.
- 위 함수는 또한 원점이 아닌 특정 지점을 기준으로 회전하는 어파인 변환 행렬을 얻을 수 잇다.
- `cv::getRotationMatrix2D(center, angle, scale);`
  - `center`: 회전 기준점
  - `angle`: 회잔 반경을 나타내며 양수는 반시계, 음수는 시게방향으로 회전한다.
- 단순히 $90\degree$ 회전을 하고 싶다면 `cv;:rotate()` 함수를 사용한다.
- `cv::rotate(src, dst, rotateCode);`
  - `rotateCode`: 어느 방향으로 몇 $\degree$만큼 회전 시킬 것인지 명시한다.
    - `ROTATE_90_CLOCKWISE`: $90\degree$만큼 시계 방향으로 회전한다.
    - `ROTATE_90_COUNTERCLOCKWISE`: $90\degree$만큼 반시계 방향으로 회전한다.
    - `ROTATE_180_CLOCKWISE`: $180\degree$만큼 시계 방향으로 회전한다.
    - ...

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src");
    cv::Mat dst;

    cv::Mat M = cv::getRotationMatrix2D(cv::Point(10, 10), 15, 1);
    
    cv::warpAffine(src, dst, M, cv::Size());

    return 0;
}
```
