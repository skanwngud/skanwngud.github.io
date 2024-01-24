---
title: OpenCV 허프 변환 직선 검출
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, cv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 허프 변환 직선 검출

- 직선 검출은 주로 허프 변환 기법을 사용한다.
- 직선 성분을 찾기 위해 우선 에지를 찾아내고 해당 에지가 일직선상으로 배열 되어있는지를 확인한다.

### 허프 변환 (Hough Transform)

- 2차원 좌표 $xy$에서 직선의 방정식을 파라미터 공간으로 변환하여 직선을 찾아내느 알고리즘이다.
- 2차원 평면상의 직선 방정식은 $y = ax + b$로 나타낼 수 있는데 이는 $b = -ax + y$로 치환 가능하다.
  - 해당 수식은 $ab$ 좌표 공간에서 기울기가 $-x$이고 $y$절편이 $y$인 방정식으로 생각할 수 있다.
  - $xy$ 좌표 공간에서의 직선은 $ab$ 공간에서 한 점으로 표현 되고, $ab$ 공간에서의 직선은 $xy$ 공간에서 한 점으로 표현된다.
- 직선 성분을 찾기 위해선 $xy$ 공간에서 에지로 판단 된 모든 점들을 $ab$ 공간에서 직선으로 표현하고 교차 되는 모든 좌표를 찾아야한다.
  - 교차 좌표를 찾기 위해선 축적 배열을 (accumulation array) 사용한다.
    - $0$으로 초기화 된 2차원 배열에서 직선이 지나가는 위치의 배열의 원소값을 $1$씩 증가시킨다.
    - 교차가 많이 되는 좌표일수록 배열의 원소값이 높아진다.
- $y = ax + b$ 수식으로는 모든 점들을 표현할 수 없으므로 극좌표계 직선방정식인 $x\cos\theta + y\sin\theta = p$를 사용한다.
  - $p$ 는 원점에서 직선까지의 수직 거리를 나타내며 $\theta$ 는 원점에서 직선에 수직선을 내렸을 대 $x$ 축과 이루는 각도를 나타낸다.
  - $xy$ 공간에서 한 점은 $p\theta$ 공간에서 삼각함수 그래프의 형태인 곡선으로 나타나고 $p\theta$ 공간에서의 한 점은 $xy$ 공간에서 직선으로 나타난다.
  - $p$와 $\theta$ 는 $0$부터 $\pi$ 사이의 값을 가지므로 축적배열을 구현할 때 값의 범위를 적당히 조절하는 양자화 (quantization) 을 해야한다.
- `cv::HoughLines(image, lines, rho, theta, threshold, srn=0, stn=0, min_theta=0, max_theta=CV_PI);`
  - `image`: `Canny()` 함수 등을 통해 구한 에지 입력 영상
  - `lines`: `std::vector<cv::Vec2f>` 혹은 `std::vector<cv::Vec3f>` 형태의 출력 영상
    - `cv::Vec2f`: $p$와 $0$의 값을 저장한다.
    - `cv::Vec3f`: $p$와 $0$의 값을 포함한 축적배열에서의 누적값을 저장한다.
  - `rho`: 축적배열에서 $p$ 값의 해상도를 지정한다. 픽셀단위이며 1 지정 시 $p$ 값을 1 픽셀 단위로 설정한다.
  - `theta`: 축적배열에서 $theta$ 값의 해상도를 지정한다. 라디안 단위이며 `CV_PI / 180` 을 지정하면 $theta$ 를 $1^\circ$ 단위로 구분한다.
  - `srn`: 멀티스케일 허프 변환에서 `rho` 해상도를 나누는 값이다. `srn`에 양수를 지정하면 `rho` 해상도와 `rho / srn` 해상도를 이용하고 `srn, stn` 둘 다 $0$이라면 일반 허프 변환을 수행한다.
  - `stn`: 멀티스케일 허프 변환에서 $\theta$ 해상도를 나누는 값이다.

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat edges;
    cv::Canny(src, edges, 50, 150);  // 에지 검출

    std::vector<cv::Vec2f> lines;
    cv::HoughLines(edges, lines, 1, CV_PI / 180, 250);  // 직선 검출

    cv::Mat dst;
    cv::cvtColor(edges, dst, cv::COLOR_GRAY2BGR);

    for (size_t i = 0; i < lines.size(); i++)
    {
        float r = lines[i][0], t = lines[i][1];
        double cos_t = cos(t), sin_t = sin(t);
        double x0 = r * cos_t, y0 = r * sin_t;
        double alpha = 1000;  // 충분히 큰 값으로 넘겨줘야 각 선분의 끝점이 영상 바깥쪽을 향하게 되어 중간에 선이 끊기질 않는다.

        cv::Point pt1(cvRound(x0 + alpha * (-sin_t)), cvRound(y0 + alpha * cos_t));
        cv::Point pt2(cvRound(x0 - alpha * (-sin_t)), cvRound(y0 - alpha * cos_t));

        cv::line(dst, pt1, pt2, cv::Scalar(0, 0, 255), 2, cv::LINE_AA);
    }

    cv::imshow("src", src);
    cv::imshow("dst", dst);

    cv::waitKey(0);
    cv::destroyAllWindows();

    return 0;
}
```

### 확률적 허프 변환 (Probabilistic Hough Transform)

- 직선의 방정식 $p$와 $\theta$ 를 반환하는 것이 아닌 직선의 시작점과 끝점의 좌표를 반환한다.
- `cv::HoughLinesP(image, lines, rho, theta, threshold, minLineLengh=0, maxLineGap=0);`
  - `lines`: `std::vector<cv::Vec4i>` 형태로 저장한다. 선분정보는 `cv::Vec4i` 형태로 저장 되며, `x1, y1, x2, y2` 를 저장한다.
  - `maxLineGap`: 일직선상의 직선이 잡음 등의 영향으로 끊어졌을 때 두 직선을 하나의 직선으로 간주하고자 할 때 사용한다.
