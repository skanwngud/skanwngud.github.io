---
title: OpenCV 명암비 조절
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 영상의 명암비 조절

- 영상에서 밝은 영역과 어두운 영역 간의 차이를 명암비라고 한다.
- 명암비가 높은 영상은 어두운 영역과 밝은 영역의 차이가 크기 때문에 사물의 분간이 쉽고, 그 반대는 영상 전체가 흐릿하게 보이기 때문에 사물의 분간이 어렵다.
  - 명암비가 크다고 무조건 좋은 것은 아니다. 반대로 명암비가 너무 크게 되면 디테일한 부분들도 다 날아갈 수 있다.
- 영상의 밝기 조절은 영상 전체에 덧셈 연산을 함으로써 균일하게 값이 증가하고 감소했지만, 명암비는 곱셈 연산으로 큰 값은 크게, 작은 값은 작게 증가한다.
- $dst(x, y) = saturate(src(x, y) * a) \;(단, \;x, \;y 는 \;픽셀의 \;좌표이며 \;a 는 \;명암비 \;조절값)$
  - 실수 $a$에 대해 $a > 1$인 경우 명암비 증가, $a < 1$인 경우 명암비 감소
  - e.g.) 입력 영상에 대해 픽셀값이 60, 100 인 경우 기존 픽셀값의 차이는 40.
    - 픽셀값을 전체 2배를 하면 값은 120, 100 이 되며 픽셀값의 차이는 80 이 된다. (명암비 증가)
    - 반대로 픽셀값을 반으로 줄이면 30, 50 으로 픽셀값의 차이는 20 이 된다. (명암비 감소)

### 영상에 단순 곱셈 연산으로 명암비 조절

- 단순히 영상에 실수를 곱하는 경우 영상 전체의 값이 증가하거나 감소하게 된다.
- $a$의 값이 1 이상인 경우 픽셀값이 증가하게 되는데, 포화연산으로 최대값은 255를 넘을 수 없으므로 대부분의 픽셀이 255 값으로 수렴할 수도 있다.
- 따라서, 이와 같이 단순한 방법으로 명암비를 조절하는 것은 실무에서는 거의 사용하지 않는다.

```cpp
#include "opencv2/opencv.hpp"

void contrast();

int main(void)
{
    contrast();

    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}

void contrast()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    float a = 2.f;  // 명암비 조절값
    cv::Mat dst = a * src;  // 전체 픽셀값을 2배해준다.

    cv::imshow("src", src);
    cv::imshow("dst", dst);
}
```

### 명암비 조절의 효과를 증대시키기

- 명암비의 효과를 증대시키기 위해선 일괄적으로 실수를 곱해 명암비를 조절하는 것이 아닌, 밝은 곳은 밝게, 어두운 곳은 어둡게 만들어야한다.
- 밝고 어두운 기준을 어떻게 설정하느냐에 따라 명암비의 효과에 따른 영상의 품질이 크게 달라질 수 있다.
  - 기준은 그레이스케일의 절반값인 128이 될 수도 있고 전체 영상의 평균 픽셀값이 될 수도 있다.
- 픽셀값이 기준보다 높으면 밝게, 낮으면 어둡게 만드는 방식을 이용한다.
- $dst(x, \;y) = satureate(src(x, \;y) + (src(x, \;y) - b)) \;* \;a \;(단, \;a는 -1 보다 \;크거나 \;같은 \;실수이며, \;b는 \;픽셀값의 \;기준)$
  - 해당 식에서 $-1 \leqq a < 0$인 경우 픽셀값이 감소하고 $a > 0$인 경우 픽셀값 증가

```cpp
#include "opencv2/opencv.hpp"

void contrast();

int main(void)
{
    contrast();

    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}

void contrast()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    float alpha = 2.f;
    cv::Mat dst = (src + (src - 128)) * alpha;  // 픽셀값 128 을 기준으로 높으면 값 증가, 낮으면 감소

    cv::imshow("src", src);
    cv::imshow("dst", dst);
}
```
