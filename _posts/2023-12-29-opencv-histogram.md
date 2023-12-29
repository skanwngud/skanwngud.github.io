---
title: OpenCV 히스토그램
author: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 히스토그램

- 픽셀값의 분포를 나타낸다.
- 히스토그램 그래프의 가로축은 빈(bin) 이라 부른다.
  - 그레이스케일의 경우 픽셀이 나타낼 수 있는 0 ~ 255 까지 총 256 개의 bin 이 존재한다.
  - bin 의 갯수와 픽셀값의 범위가 항상 일치해야하는 것만은 아니다.
    - bin 이 작을수록 더욱 대략적인 픽셀값의 분포를 나타내며, 그 반대는 좀 더 세밀한 픽셀값의 분포를 나타낸다.

### calcHist

- 영상의 히스토그램을 분석하는 함수.
  - 단일 영상, 여러장의 영상
  - 그레이스케일, 컬러스케일
  - bin 의 갯수 지정 가능
- `void calcHist(images, nimages, channels, mask, hist, dims, histSize, ranges, uniform = true, accumulate = false);`
  - `images`: 입력 영상의 배열 또는 영상의 주소 (배열인 경우 모든 이미지의 크기와 채널이 동일해야함)
  - `nimages`: 입력 영상의 개수
  - `chnnels`: 히스토그램을 구할 채널을 나타내는 정수형 배열
  - `mask`: 마스크 영상. 입력 영상과 같은 크기의 배열이며, 원소값이 0 이 아닌 좌표의 값들만 계산에 사용 된다. `cv::Mat()`이나 `cv::noArray()`로 무시할 수 있다.
  - `hist`: 출력 히스토그램 `CV_32FC1`를 사용하는 행렬
  - `dims`: 출력 히스토그램 차원 수
  - `histSize`: 각 차원의 히스토그램 배열 크기 (각 차원의 bin 개수)
  - `ranges`: 각 차원의 히스토그램 범위.
    - `uniform = true`(등간격) 인 경우 `ranges[i]`는 각 차원의 최소값과 최대값으로 구성 된 배열이며 [최소값, 최대값)을 나타냄.
    - `uniform = false`(비등간격) 인 경우 `ranges[i]`는 각각의 구역을 나타내는 histSize[i] + 1 개의 원소로 구성 된 배열.
  - `uniform`: bin 간격이 균등한지 나타내는 플래그
  - `accumulate`: 누적 여부 플래그. `true`면 `hist`를 초기화하지 않고 누적하여 계산한다.

```cpp
#include "opencv2/opencv.hpp"

cv::Mat calcGrayHist(cv::Mat &img);
cv::Mat getGrayHist(cv::Mat &hist);

int main(void)
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    cv::Mat hist = calcGrayHist(src);
    cv::Mat histImage = getGrayHist(hist);

    cv::imshow("src", src);
    cv::imshow("srcHist", srcHist);

    cv::waitKey();

    return 0;
}

// 히스토그램 계산 함수
cv::Mat calcGrayHist(cv::Mat & img)
{
    CV_Assert(img.type() == CV_8UC1);

    cv::Mat hist;

    int channels[] = { 0 };
    int dims = 1;
    const int histSize[] = { 256 };
    float graylevel[] = {0, 256 };
    const float* ranges[] = { graylevel };

    cv::calcHist(&img, 1, channels, cv::noArray(), hist, dims, histSize, ranges);

    return hist;
}

// 히스토그램 출력 함수
cv::Mat getGrayHist(cv::Mat &hist)
{
    CV_Assert(hist.type() == CV_32FC1);
    CV_Assert(hist.size() == cv::Size(1, 256));

    double histMax;
    cv::minMaxLoc(hist, 0, &histMax);  // 최소값은 제외, 최대값은 &histMax 에 저장

    cv::Mat imgHist(100, 256, CV_8UC1, cv::Scalar(255));

    for (int i = 0; i < 256; i++)
    {
        // 최대값은 길이가 100인 직선을 긋는다.
        cv::line(imgHist, cv::Point(i, 100), cv::Point(i, 100 - cvRound(hist.at<float>(i, 0) * 100 / histMax)), cv::Scalar(0));
    }

}
```

### 히스토그램 스트레칭 (Histogram Stretching)

- 영상의 픽셀분포를 전 구간에 걸쳐 나타내도록하는 선형 변환 기법이다.
- 명암비가 낮은 이미지는 히스토그램이 특정 구간에 밀집 되어있는데, 이를 길게 늘리듯이 펼치는 방법이다.
- 히스토그램 스트레칭을 하면 전반적인 명암비가 높아지므로 *대체로* 품질이 좋아진다.
- $dst(x,\;y) = (\frac{src(x, \;y) - G_{min}}{G_{max} - G_{min}}) * 255 \;(단, \;G_{max}, \;G_{min}은 \;픽셀 \;최대최소값)$
  - 해당식은 $G_{min}$값은 0으로 $G_{max}$값은 255로 늘리면서 히스토그램을 늘려준다.
- 히스토그램 스트레칭에 대한 함수는 따로 제공하지 않으므로 직접 구현해야한다.

```cpp
#include "opencv2/opencv.hpp"

cv::Mat calcGrayHist(cv::Mat &src);  // 위 코드와 동일
cv::Mat getGrayHist(cv::Mat &hist);  // 위 코드와 동일

void histogramStretching();

int main(void)
{
    histogramStretching();

    return 0;
}

void histogramStretching()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    double gmin, gmax;  // 최소, 최대값
    cv::minMaxLoc(src, &gmin, &gmax);

    cv::Mat dst = ((src - gmin) * 255 / (gmax - gmin));

    cv::imshow("src", src);
    cv::imshow("srcHist", getGrayHist(calcGrayHist(src)));

    cv::imshow("dst", dst);
    cv::imshow("dstHist", getGrayHist(calcGrayHist(dst)));

    cv::waitKey();
}
```

### 히스토그램 평탄화 (Histogram Equalize)

- `cv::equalizeHist(src, dst);`의 형태이며, 그레이스케일만 입력 받는다.
- 스트레칭과 더불어 픽셀값 분포를 고르게하는 작업.
- 특정 영역에 뭉쳐있는 픽셀값들을 고르게 분포시켜준다.
- 누적 히스토그램 $H(g)$을 이용하여 평탄화한다.
  - $H(g) = \sum\limits_{0\leqq{i}<g}h(i)$
    - 위 수식의 값은 그레이스케일 픽셀의 최대값인 255 를 훨씬 넘으므로 정규화를 해야한다.
    - $dst(x, \;y) = round(H(src(x, \;y)) * \frac{L_{max}}{N}) \;(단, \;L_{max}는 \;영상이 \;가질 \;수 \;있는 \;최대 \;밝기이며, \;N은 \;픽셀의 \;갯수)$

```cpp
#include "opencv2/opencv.hpp"

cv::Mat calcGrayHist(cv::Mat &src);
cv::Mat getGrayHist(cv::Mat &hist);

void histogramEqualize();

int main(void)
{
    histogramEqualize();

    return 0;
}

void histogramEqualize()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    cv::Mat dst;
    cv::equalize(src, dst);

    cv::imshow("src", src);
    cv::imshow("srcHist", getGrayHist(calcGrayHist(src)));

    cv::imshow("dst", dst);
    cv::imshow("dstHist", getGrayHist(calcGrayHist(src)));

    cv::waitKey();
}
```
