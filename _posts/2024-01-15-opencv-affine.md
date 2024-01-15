---
title:
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 영상의 기하학적인 변환

- 영상을 구성하는 픽셀의 배치 구조를 변경함으로써 전체 영상의 모양을 바꾸는 작업이다.
- 픽셀의 밝기, 명암비, 필터링 조작과 달리 픽셀의 값은 유지한채 위치만 변경시킨다.
- $$\begin{cases}
    x' = f_1(x, y) \\
    y' = f_2(x, y) \\
\end{cases}$$

## 어파인 변환 (Affine Transform)

- 영상을 평행 이동시키거나 회전, 크기 변환 등을 통해 만들 수 있는 변환을 통칭한다.
- 직선간의 길이 비율과 평행관계가 그대로 유지 되며 직선은 그대로 직선을 나타낸다.
- $$\begin{cases}
    x' = f_1(x, y) = ax + by + c \\
    y' = f_2(x, y) = dx + ey + f \\
\end{cases}$$
- 어파인 변환 행렬을 통해 어파인 변환을 수행한다.
- `cv::Mat cv::warpAffine(src, dst, M, dsize, flags, borderMode, borderValue);`로 변환을 수행한다.

### 어파인 변환 행렬

- 위의 식을 행렬 변환 시킨 행렬.
- $$\begin{pmatrix}
    (a & b & c) \\
    (d & e & f) \\
\end{pmatrix}$$의 2, 3 모양의 행렬로 변환 가능하다.
- 어파인 변환 행렬을 구하기 위해선 두 행렬 간의 세 점의 이동 관계를 알아야한다.
- `cv::Mat cv::getAffineTransform(src[], dst[]);`
- `cv::Mat cv::getAffineTransform(src, dst);`
  - 위의 두 함수 중 하나를 사용하여 변환 행렬을 구한다.
  - `src[]`들은 `cv::Point2f` 형태
  - `src`들은 `cv::Mat`의 행태이다.

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

void affine_transform();

int main(void)
{
    affine_transform();

    return 0;
}

void affine_transform()
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Point2f srcPts[3], dstPts[3];  // 3 점을 갖는 cv::Point2f 행렬 선언
    srcPts[0] = (0, 0);
    srcPts[1] = (src.cols - 1, 0);
    srcPts[2] = (src.cols - 1, src.rows - 1);

    dstPts[0] = (50, 50);
    dstPts[1] = (src.cols - 100, 100);
    dstPts[2] = (src.cols - 50, src.rows - 50);

    cv::Mat M = cv::getAffineTransform(srcPts, dstPts);  // affine transform matrix 를 얻는다.

    cv::Mat dst;
    cv::warpAffine(src, dst, M, cv::Size());  // affine 변환

    cv::imshow("src", src);
    cv::imshow("dst", dst);
    cv::waitKey(0);

    cv::destroyAllWindows();
}
```

### cv::transform

- 행렬 `src`의 점이 행렬 `dst`의 어느 위치로 이동했는지 알려주는 함수이다.
- `cv::Mat cv::transform(src, dst, M);`
  - `src`: `std::vector<cv::Point2f>`의 형태
  - `dst`: `std::vector<cv::Point2f>`의 형태
  - `M`: `cv::Mat`의 형태의 어파인 변환 행렬이다.

```cpp
...

    std::vector<cv::Point2f> s = {srcPts[0], srcPts[1], srcPts[2]};
    std::vector<cv::Point2f> d;

    cv::transform(s, d, M);
...
```
