---
title: OpenCV 샤프닝
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cv, cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 샤프닝 (Sharpening)

- 블러링과 반대로 윤곽이 뚜렷하고 선명한 느낌이 나도록한다.
- 영상의 에지 근방에서 픽셀값의 명암비가 커지도록 수정해야 선명한 영상이 나온다.

### 언샤프 마스크 필터 (Unsharp mask filter)

- 샤프닝은 우선 입력 영상을 블러링 처리를 한 뒤 최종적으로 샤프닝 처리를 한다.
- 입력 영상을 블러처리한 뒤 함수값의 증감도를 그래프화 시킨다.
- 함수값이 증가하는 부분에선 음수, 증가가 멈추는 부분에선 양수로 변환이 되는데, 이 그래프를 기존의 입력 영상에 대해 빼기 연산을 실시한다.
- $h(x, y) = f(x, y) + \alpha * g(x, y)$
  - $h(x, y)$는 출력 영상
  - $f(x, y)$는 입력 영상
  - $g(x, y)$는 블러링 처리 후 픽셀의 변화도를 연산한 영상이다.
  - $\alpha$는 샤프닝의 가중치를 의미하며 $1$보다 크면 더욱 날카롭게 $1$보다 작으면 덜 날카롭게 영상을 출력한다.
- `OpenCV`에서 언샤프 마스크 필터 함수를 따로 제공하지는 않으므로 직접 구현해야한다.

```cpp
#include "opencv2/opencv.hpp"

void unsharpMask();

int main(void)
{
    unsharpMask();

    return 0;
}

void unsharpMask()
{
    cv::Mat src = cv::imread("./lenna.bmp", cv::IMREAD_GRAYSCALE);

    std::vector<cv::Mat> images({src});
    for (int sigma = 1; sigma <= 5; sigma++)
    {
        cv::Mat blurred;
        cv::GaussianBlur(src, blurred, cv::Size(), (double)sigma);

        float alpha = 1.f;
        cv::Mat dst = src + alpha * (src - blurred);

        images.push_back(dst);
    }

    cv::Mat result;
    cv::hconcat(images, result);

    cv::imshow("debug", result);
    cv::waitKey();

    cv::destroyAllWindows();
}
```
