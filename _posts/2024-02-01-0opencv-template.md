---
title: OpenCV 템플릿 매칭
author: yjh
categories: [Computer Vision, OpenCV]
tags: [cv, opencv, cpp, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 템플릿 매칭 (Template Matching)

- 테플릿은 찾고자하는 대상이 되는 작은 크기의 영상을 의미한다.
- 템플릿 매칭은 작은 크기의 템플릿 영상을 입력 영상 전체 영역에 대해 이동하면서 가장 비슷한 위치를 수치적으로 찾아내는 방식이다.
- 템플릿 영상을 입력 영상 전체 영역에 대해 이동하면서 템플릿 영상과 입력 영상 부분 영상과의 유사도 (similarity) 혹은 비유사도 (dissmilarity) 를 계산한다.
- 유사도가 높은 경우 영상 위치에서 값이 크게 나타나고, 반대로 비유사도를 계산할 경우에는 비슷한 부분에서 값이 작게 나타난다.

### cv::matchTemplate

- `cv::matchTemplate(image, templ, result, method, mask=cv::noArray());`
  - `templ`: 템플릿 영상. 입력 영상보다 크기가 같거나 작아야하며, 타입이 같아야한다.
  - `result`: 출력 영상. `CV_32FC1` 타입이다.
    - 입력 영상의 크기가 `W, H`이고 템플릿의 크기가 `w, h` 이면 출력 영상의 크기는 `W - w + 1, H - h + 1`이 된다.
  - `method`: 템플릿 매칭 비교 방법 (TemplateMatchModes) 열거형 상수 중 하나를 지정한다.
    - `cv::TM_SQDIFF`: 제곱차 (squard difference) 매칭 방법을 의미한다. 두 영상이 완벽하게 일치하면 0, 일치하지 않으면 0 보다 큰 양수이다.
    - `cv::TM_CCORR`: 상관관계 (correlation) 매칭 방법을 의미한다. 두 영상이 유사하면 큰 양수를, 아니면 작은 값을 반환한다.
    - `cv::TM_CCOEFF`: 상관계수 (correlation coefficient) 매칭 방법을 의미한다. 두 영상을 미리 평균 밝기로 보정 후 매칭을 수행한다.
    - `cv::TM_SQDIFF_NORMED, TM_CCORR_NORMED, TM_CCOEFF_NORMED`: 위의 각 방법들을 정규화한다.
  - `mask`: 찾고자하는 템플릿의 마스크 영상이다. `mask`는 `tmpl`과 같은 크기, 타입이어야하며 `TM_SQDIFF`, `TM_CCORR_NORMED` 에서만 지원한다.
- 여러 매칭 방법 중 정규화 된 상관계수 매칭 방법 (`cv::TM_CCOEFF_NORMED`) 이 효과가 가장 좋은 것으로 알려져있다.
  - 성능이 가장 좋지만 계산수식이 복잡하기 때문에 많은 연산을 필요로한다.
- 제곱차 매칭의 경우 최소값의 위치를, 나머지 방법에선 최댓값의 위치가 매칭이 가장 잘 된 영역을 의미한다.
  - `cv::minMaxLoc()` 함수로 최대최소값의 위치를 알 수 있다.

```cpp
#include "opencv2/opencv.hpp"

void template_matching(cv::Mat ref, cv::Mat tmpl);

int main(void)
{
    cv::Mat ref = cv::imread("path/to/ref", cv::IMREAD_COLOR);
    cv::Mat tmpl = cv::imread("path/to/tmp;", cv::IMREAD_COLOR);

    template_matching(ref, tmpl);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}

void template_matching(cv::Mat ref, cv::Mat tmpl)
{
    // 임의로 밝기 조절
    ref += cv::Scalar(50, 50, 50);

    // 임의로 노이즈 추가
    cv::Mat noise(ref.size(), CV_32SC3);
    cv::radn(noise, 0, 10);
    cv::add(ref, noise, ref, cv::Mat(), CV_8UC3);

    // 템플릿 매칭
    cv::Mat res, res_norm;
    cv::matchTemplate(ref, tmpl, res, cv::TM_CCOEFF_NORMED);
    cv::normalize(res, res_norm, 0, 255, cv::NORM_MINMAX, CV_8U);

    // 최대값, 위치
    double maxv;
    cv::Point maxloc;

    cv::minMaxLoc(res, 0, &maxv, 0, &maxloc);
    std::cout << "maxv: " << maxv << std::endl;

    cv::rectangle(ref, cv::Rect(maxloc.x, maxloc.y, tmpl.cols, tmpl.rows), cv::Scalar(0, 0, 255), 2);

    cv::imshow("tmpl", tmpl);
    cv::imshow("res_norm", res_norm);
    cv::imshow("ref", ref);
}
```
