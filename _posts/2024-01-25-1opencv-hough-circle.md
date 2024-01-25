---
title: OpenCV 허프 변환 원 검출
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 허프 변환 원 검출

- 중심좌표가 $(a, b)$이고 반지름이 $r$인 원의 방정식은 $(x - a)^2 + (y - b)^2 = r^2$ 로 표현가능하다.
  - 해당 식은 파라미터가 3개이므로 허프변환을 그대로 사용하기 위해선 3차원 공간에 축적배열을 정의해야한다.
  - 3차원 공간에 축적배열을 정의하여 연산하게 되면 메모리 및 연산시간이 너무 많이 소요되므로 허프 그래디언트 (hough gradient) 방식을 대신 사용한다.

### 허프 그래디언트 (Hough Gradient)

- 우선 영상에 존재하는 모든 원의 중심 좌표를 찾는다.
- 검출 된 원의 중심으로부터 원에 적합한 반지름을 구하게 된다.
- 원의 중심좌표를 구하는 과정에서 축적 배열이 사용 된다.
  - 허프 그래디언트에서는 파라미터 공간이 아닌 입력 영상과 동일한 `x, y` 좌표에서 2차원 배열로 만든다.
  - 입력 영상의 모든 에지 픽셀에 대해 그래디언트를 구하고 그래디언트 방향을 따르는 직선상의 축적 배열 값을 1씩 증가시킨다.
- 원주상의 모든 점에 대해 그래디언트 방향의 직선을 그리고 직선상의 축적 배열 값을 증가시키면 결과적으로 원의 중심에서 축적 배열 값이 크게 나타난다.
- 원의 중심을 찾은 후에는 다양한 반지름의 원에 대해 원주상에 충분히 많은 에지 픽셀이 존재하는지 확인하여 적절한 반지름을 선택한다.

### cv::HoughCircle

- `cv::HoughCircle(image, circles, method, dp, minDist, param1=100, param2=100, minRadius=0, maxRadius=0);`
  - `image`: `cv::HoughLines()`, `cv::HoughLinesP()`와 달리 원본 영상의 그레이스케일 영상을 입력 영상을 받는다.
    - 그레이스케일의 원본 영상을 받으면 내부에서 `cv::Sobel()`, `cv::Canny()` 함수를 통해 에지를 검출한다.
  - `circles`: `cv::Vec3f` 혹은 `cv::Vec4f` 타입의 `std::vector` 를 출력 영상으로 보낸다.
    - `cv::Vec3f`: 중심좌표 $a$, $b$와 반지름 $r$을 저장한다.
    - `cv::Vec4f`: 중심좌표 $a$, $b$와 반지름 $r$ 뿐만 아닌 축적배열 값도 저장한다.
  - `method`: `cv::HOUGH_GRADIENT` 만 지정 가능하다.
  - `dp`: 축적배열의 크기이다. 1을 지정하면 입력 영상과 동일하고 2는 입력 영상의 가로세로를 2로 나눈 축적배열을 사용한다.
  - `minDist`: 인접한 원의 최소거리를 지정한다. `minDist` 보다 적은 거리의 두 원이 존재하는 경우 둘 중 하나만 검출한다.
  - `param1`: 캐니 에지 검출기를 이용할 때의 높은 임계값을 설정한다. 낮은 임계값은 해당 값의 절반값을 사용한다.
  - `param2`: 축적배열에서 원의 중심을 찾을 때 사용하는 임계값이다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Mat blurred;
    cv::Blur(src, blurred, cv::Size(3, 3));  // 노이즈 감소를 위한 블러처리

    std::vector<cv::Vec3f> circles;
    cv::HoughCircles(blurred, circles, cv::HOUGH_GRADIENT, 1, 50, 150, 30);  // 원 검출

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);

    for (cv::Vec3f c: circles)
    {
        cv::Point center(cvRound(c[0]), cvRound(c[1]));
        int radius = cvRound(c[2]);

        cv::circle(dst, center, radius, cv::Scalr(0, 0, 255), 2, cv::LINE_AA);
    }

    cv::imshow("src", src);
    cv::imshow("dst", dst);

    cv::waitKey(0);
    cv::destroyAllWindows();

    return 0;
}
```
