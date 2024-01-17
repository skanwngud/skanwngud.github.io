---
title: OpenCV 투시 변환
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, cv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 투시 변환 (Perspective Transform)

- 직선성과 평행관계가 유지 되는 어파인 변환보다 훨씬 자유로운 기하학적인 변환 방법이다.
- 직사각형 형태의 영상을 임의의 볼록 사각형 형태로 변환할 수 있다.
- 4개의 점의 이동관계로부터 여덟개의 방정식을 얻을 수 있으며, 해당 방정식으로부투 투시 변환을 표현하는 파라미터 정보를 얻을 수 있다.
- 투시 변환 행렬은 $(3, 3)$ 크기의 실수 행렬로 표현한다.
- $$\begin{pmatrix} wx' \\ wy' \\ w \\ \end{pmatrix}
    = M_{p}\begin{pmatrix} x \\ y \\ 1 \\ \end{pmatrix}
    = \begin{pmatrix} p_{11} & p_{12} & p_{13} \\ p_{21} & p_{22} & p_{23} \\ p_{31} & p_{32} & p_{33} \\ \end{pmatrix}
    \begin{pmatrix} x \\ y \\ 1 \\ \end{pmatrix}$$
- 위 행렬에서 입력좌표와 출력좌표를 $(x, y, 1), (wx', wy', w)$ 의 형태로 표현한 것을 동차 좌표계 (homogeneous coordintates)라고 한다.
- $w$는 결과 영상의 좌표를 표현 할 때 사용 되는 상수이며, $w = p_{31}x + p_{32}y + p_{33}$의 형태로 계산된다.
- $$\begin{cases}
    x' = \frac{p_{11}x \;+ \;p_{12}y \;+ \;p_{13}}{p_{31}x \;+ \;p_{32}y \;+ \;p_{33}} \\
    y' = \frac{p_{21}x \;+ \;p_{22}y \;+ \;p_{23}}{p_{31}x \;+ \;p_{32}y \;+ \;p_{33}} \\
    \end{cases}$$

### cv::getPerspectiveTransform

- 투시 변환 행렬을 구해주는 함수를 기본 제공한다.
- 4개의 점과 이 점들이 이동한 결과 영상의 좌표 4개를 입력 받아 $(3, 3)$ 크기의 투시 변환 행렬을 반환한다.
- `cv::getPerspectiveTransform(src, dst, m);`
- `src`와 `dst` 는 `cv::Point2f`가 담긴 배열이나 `std::vecotr<cv::Point2f>`를 전달한다.

### cv::warpPerspective

- 투시 변환 결과 영상을 반환한다.
- `cv::warpPerspective(src, dst, m, size, flags, borderMode, borderValue);`

```cpp
// 입력 영상에서 마우스로 4개의 점을 찍은 뒤 투시 변환을 진행하는 코드

#include "opencv2/opencv.hpp"
#include <iostream>

cv::Mat src;
cv::Point2f srcQuad[4], dstQuad[4];

void on_mouse(int event, int x, int y, int flags, void* userdata);

int main(void)
{
    src = cv::imread("path/to/src");

    cv::namedWindow("src");
    cv::setMouseCallback("src", on_mouse);

    cv::imshow("src", src);
    cv::waitKey();

    return 0;
}

void on_mouse(int event, int x, int y, int flags, void*)
{
    static int cnt = 0;

    if (event == cv::EVENT_LBUTTONDOWN)
    {
        if (cnt < 4)
        {
            srcQuad[cnt++] = cv::Point2f(x, y);

            cv::circle(src, cv::Point(x, y), 5, cv::Scalar(0, 0, 255), -1);
            cv::imshow("src", src);
            
            if (cnt == 4)
            {
                int w = 200, h = 300;

                dstQuad[0] = cv::Point2f(0, 0);
                dstQuad[1] = cv::Point2f(w - 1, 0);
                dstQuad[2] = cv::Point2f(w - 1, h - 1);
                dstQuad[3] = cv::Point2f(0, h - 1);

                cv::Mat pers = cv::getPerspectiveTransform(srcQuad, dstQuad);

                cv::Mat dst;
                cv::warpPerspective(src, dst, pers, cv::Size(w, h));

                cv::imshow("dst", dst);
            }
        }
    }
}
```

### cv::perspectiveTransform

- `src`이 투시 변환을 통해 `dst`의 어느 지점으로 이동했는지 알려주는 함수이다.
- `cv::perspectiveTransform(src, dst, m);`
  - `src`: `std::vector<cv::Point2f>`
  - `dst`: `std::vector<cv::Point2f>`
  - `m`: $(3, 3)$ 혹은 $(4, 4)$ 실수형 행렬이다.

```cpp
...

    std::vector<cv::Point2f> s, d;
    
    for (int i = 0; i < 4; i++)
    {
        s.push_back(srcPts[i]);
    }

    cv::perspectiveTransform(s, d, pers);

...
```
