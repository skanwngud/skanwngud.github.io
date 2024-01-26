---
title: OpenCV 컬러 영상 히스토그램
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cv, cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 컬러 히스토그램 평탄화

- 기존의 함수 `cv::equalization()` 을 통해 히스토그램 평탄화가 가능하지만 해당 함수는 그레이스케일만 입력을 받는다.
- 컬러 영상의 히스토그램 평탄화를 하기 위해선 직접 함수를 구현해야한다.
  - `BGR` 스케일을 바로 사용하기 보단 색상과 밝기 정보 등이 따로 분리가 되어있는 `YCrCb` 스케일을 활용한다.
  - 밝기 영역 `Y`를 따로 분리해서 히스토그램 평탄화를 진행해야 입력 영상의 색상 정보가 달라지지 않는다.
  - `BGR` 스케일로 평탄화를 하는 경우 각 채널별로 다른 평탄화 훔수가 적용 되기 때문에 특정 채널의 값이 크게 변할 수 있다.

```cpp
#include "opencv2/opencv.hpp"

cv::Mat bgrHistogram(cv::Mat src);
cv::Mat yccHistogram(cv::Mat src);

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_COLOR);

    cv::Mat bgr = bgrHistogram(src);
    cv::Mat ycc = yccHistogram(src);

    cv::imshow("src", src);
    cv::imshow("bgr", bgr);
    cv::imshow("ycc", ycc);

    cv::waitKey();
    cv::destoryAllWindows();

    return 0;
}

cv::Mat bgrHistogram(cv::Mat src)
{
    // BGR 컬러스케일에서 히스토그램 평탄화
    std::vector<cv::Mat> mv;
    cv::split(src, mv);

    std::vecotr<cv::Mat> hists;
    for (cv::Mat m: mv)  // B G R 로 나눈 후 각각 히스토그램 평탄화를 진행 후 다시 합친다.
    {
        cv::Mat hist;
        cv::equalization(m, hist);

        hists.push_back(hist);
    }

    cv::Mat dst;
    cv::merge(hists, dst);

    return dst;
}

cv::Mat yccHistogram(src::Mat src)
{
    // YCrCb 컬러스케일에서 히스토그램 평탄화
    cv::Mat srcYCrCb;
    cv::cvtColor(src, srcYCrCb, cv::COLOR_BGR2YCrCb);

    std::vector<cv::Mat> mv;
    cv::split(srcYCrCb, mv); // Y Cr Cb 로 나눈 후 명도인 Y 만 히스토그램 평탄화를 진행한다.

    cv::equalization(mv[0], mv[0]);

    cv::Mat dst;
    cv::merge(dst, mv);

    cv::cvtColor(dst, dst, cv::COLOR_YCrCb2BGR);

    return dst;
}
```

## 색상 범위 지정

- 색상 영역을 구분할 때에는 `BGR` 보다 `HSV` 처럼 색상 정보가 따로 존재하는 스케일이 더 유리하다.
  - 녹색의 경우 `H` 값이 60 근방으로 표현 되므로 `H` 값이 60과 가까운지 조사하여 녹색을 찾으면 된다.

### cv::inRange

- `cv::inRange(src, lowerb, upperb, dst);`
  - `lowerb`: 색상의 하한값이다. 주로 `cv::Mat`, `cv::Scalar` 타입이 들어온다.
  - `upperb`: 색상의 상한값이다. 주로 `cv::Mat`, `cv::Scalar` 타입이 들어온다.
    - `lowerb`, `upperb` 에 `cv::Mat` 이 들어오면 각 픽셀에 대한 상한, 하한값을 지정할 수 있으며 `cv::Scalar` 의 경우 전체 영상에 대해 값을 지정한다.
  - `dst`: 출력 마스크 영상이다. 입력 영상과 크기가 같고 타입은 `CV_8UC1`이다.
    - 해당 픽셀값이 색상 범위에 포함 되면 흰색, 그렇지 않으면 검은색으로 채워진 마스크 영상을 출력한다.
- 그레이스케일, 컬러스케일 둘 다 사용 가능하다.
- $$dst(x, y) = \begin{cases} 255\; (lowerb(x, y) \leq src(x, y) \leq upperb(x, y) \; 일 \; 때) \\
    0 \; (그 \;외)\end{cases}$$
- 채널의 값이 두 개 이상이라면 한 픽셀에 대해 모든 채널이 만족하는 경우만 픽셀값을 255로 설정한다.

```cpp
#include "opencv2/opencv.hpp"

int lower_hue, upper_hue;
cv::Mat src, src_hsv, mask;
cv::Point pt;

void select_color(int event, int x, int y, int flags, void*);
void on_hue_change(int, void*);

int main(void)
{
    cv::cvtColor(src, src_hsv, cv::COLOR_BGR2HSV);
    
    cv::namedWindow("src");

    while (1)
    {
        cv::imshow("src", src);

        cv::setMouseCallback("src", select_color);

        cv::namedWindow("mask");
        cv::createTrackbar("Lower Hue", "mask", &lower_hue, 179, on_hue_change);
        cv::createTrackbar("Upper Hue", "mask", &upper_hue, 179, on_hue_change);

        on_hue_change(0, 0);

        cv::waitKey();
    }

    return 0;
}

void select_color(int event, int x, int y, int flags, void*)
// 마우스로 클릭으로 색상을 지정한다
{
    switch (event)
    {
        case cv::EVENT_LBUTTONDOWN:
            pt = cv::Point(x, y);
            break;

        case cv::EVENT_LBUTOONUP:
            lower_hue = (int)(src_hsv.at<cv::Vec3b>(x, y)[0]) - 10;
            upper_hue = (int)(src_hsv.at<cv::Vec3b>(x, y)[0]) + 10;
            break;

        default:
            break;
    }
}

void on_hue_change(int, void*)
{
    cv::Scalar lowerb(lower_hue, 100, 0);  // 채도는 원색을 얻기 위해 100 ~ 255 사이의 값을 취한다.
    cv::Scalar upperb(upper_hue, 255, 255);  // 명도에 상관 없이 색상만 신경 쓰기 위해 0 ~ 255 사이의 값을 취한다.
    
    cv::inRange(src_hsv, lowerb, upperb, mask);

    cv::imshow("mask", mask);
}
```

## 히스토그램 역투영

- 주어진 히스토그램 영역과 일치하는 픽셀을 찾아내는 기법을 히스토그램 역투영 (histogram backprojection) 이라고한다.
- `cv::inRange()` 함수의 경우 원색을 위주로 잘 찾아내지만 사람의 피부처럼 미세한 변화가 있거나 값을 수치적으로 지정하기 어려운 경우에는 적합하지 않다.
- 입력 영상에서 찾고자하는 객체의 기준 영상을 미리 가지고 있다면 컬러 히스토그램 정보를 이용해 비슷한 색상 영역을 찾을 수 있다.

### cv::calcBackProject

- `cv::calcBackProject(images, nimages, channels, hist, backProject, ranges, scale=1, uniform=true);`
  - `images`: 입력 영상의 배열 혹은 영상의 주소이다.
  - `nimages`: 입력 영상의 개수이다.
  - `channels`: 역투영 계산시 사용할 채널 번호 배열이다.
  - `hist`: 입력 히스토그램이다.
  - `backProject`: 입력 영상과 같은 크기, 같은 채널을 갖는 출력 히스토그램 역투영 영상이다.
  - `ranges`: 각 차원의 히스토그램 빈 범위를 나타내는 배열이다.
  - `scale`: 역투영 값에 추가벅으로 곱할 값이다.
  - `uniform`: 빈의 간격이 균등한지를 나타내는 플래그이다.
- 해당 함수를 살펴보면 `cv::calcHist()` 함수와 파라미터를 거의 동일하게 사용함을 알 수 있는데, 실제로도 각 함수끼리의 영상 및 데이터들을 공유한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    // 미리 저장한 마스크 파일로 레퍼런스 영상의 피부색 영역의 히스토그램 계산
    cv::Mat ref, ref_ycrcb, mask;  // 레퍼런스 영상, 색공간을 변경한 레퍼런스 영상을 저장할 배열, 마스크 배열

    ref = cv::imread("/path/to/src", cv::IMREAD_COLOR);
    cv::cvtColor(ref, ref_ycrcb, cv::COLOR_BGR2YCrCb);

    mask = cv::imread("/path/to/mask", cv::IMREAD_COLOR);  // 사람의 피부색을 미리 지정한 마스크 파일이다.

    cv::Mat hist;
    int channels[] = {1, 2};
    int cr_bins = 128; int cb_bins = 128;
    int histSize[] = {cr_bins, cb_bins};

    float cr_ranges[] = {0, 256};
    float cb_ranges[] = {0, 256};

    const float* ranges[] = {cr_ranges, cb_ranges}

    cv::calcHist(&ref_ycrcb, 1, channels, mask, hist, 2, histSize, ranges);  // 히스토그램 계산

    // 위에서 구한 히스토그램으로 소스 영상의 피부색 영역 추출
    cv::Mat src, src_ycrcb;  // 소스 영상, 색공간을 변경한 소스 영상을 저장 할 배열

    src = cv::imread("/path/to/src", cv::IMREAD_COLOR)
    cv::cvtColor(src, src_ycrcb, cv::COLOR_BGR2YCrCb);

    cv::Mat backProj;
    cv::calcBackProject(&src_ycrcb, 1, channels, hist, backProj, ranges, 1, true);  // 역투영 계산

    cv::imshow("src", src);
    cv::imshow("backProj", backProj);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
