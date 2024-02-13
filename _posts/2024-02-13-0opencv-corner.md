---
title: OpenCV 코너 검출
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cv, cpp, opencv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 코너 (Corner)

- 에지의 방향이 급격하게 변하는 부분으로, 삼각형의 꼭지점이나 연필 심처럼 뾰족하게 튀어나온 부분 등이 그 예이다.
- 에지나 직선 성분 등의 다른 지역 특징에 비해 분별력이 높고, 영상 전 영역에 분포하여 영상 분석에서 유용하게 사용 된다.
- 코너처럼 한 점의 형태로 표현할 수 있는 특징 (feature) 을 특징점 (feature point) 이라고하며, 특징점은 키포인트 (keypoint) 혹은 관심점 (inspect point) 라고도 부른다.

## 코너 검출

- 코너 검출에는 두 가지의 방법이 유명하다.
- 하나는 고전적이지만 수학적 분석이 의미있는 해리스 코너 (Harris corner) 검출이고, 다른 하나는 FAST 코너 검출이다.
- 특징점을 검출할 때 템플릿 매칭은 객체의 위치를 찾는데에는 효과적이지만, 영상의 크기가 바뀌거나 회전하면 제대로 동작을 안하는 단점이 존재한다.
- 코너를 이용한 특징점 검출의 경우에는 기하학적인 변환이 있어도 특징점을 효과적으로 검출할 수 있다.

### 특징 (feature)

- 평균 밝기, 히스토그램, 에지, 직선 성분, 코너 등
- 영상 전체에서 얻을 수 있는 특징을 제외한 특징을 지역 특징 (local feature) 라고 한다.

## 해리스 코너 검출

- 영상의 특징 위치 $(x, y)$ 에서 $\triangle x$와 $\triangle y$만큼 떨어진 픽셀과의 밝기 차이를 다음 수식으로 표현한다.
- $E(\triangle x, \triangle y) = \sum\limits_{x, y}w(x, y)[I(x + \triangle x, \;y + \triangle y) - I(x, y)]^{2}$
- $w(x, y)$는 균일한 값을 가지는 사각형 윈도우 또는 가우시안 형태의 가중치를 갖는 윈도우이다.
- $E(\triangle x, \triangle y)$가 모든 방향으로 값이 크게 나타난다면 점 $x, y$는 코너라고 간주한다.
  - 모든 방향으로 값이 크게 나타나는지 판별하기 위해 테일러 급수, 고윳값 분석 등의 수학적 기법을 적용한다.
  - $R = Det(M) - k\cdot Tr(M)^{2}$
  - $Det$ 은 행렬식 (determinant) 를, $Tr(M)$ 는 대각합 (trace) 를 의미하며 행렬 $M$ 은
  - $$M = \sum\limits_{x, y}w(x, y)\begin{bmatrix} I_x, I_x & I_x, I_y \\ I_x, I_y & I_y, I_y \end{bmatrix}$$
  - $I_x, I_y$ 는 각 축의 방향으로 편미분한 결과이다.
  - 코너 응답 상수에서 상수 $k$ 는 보통 $0.04 ~ 0.06$ 사이의 값을 사용한다.
- 해리스에 의해 정의 된 함수 $R$ 은 입력 영상 각각의 픽셀에서 정의 되는 실수값이며 이 값을 분석하여 코너, 에지, 평탄한 지역을 판별한다.
- $R$ 이 $0$ 보다 충분히 크다보면 코너라고 판단, $0$ 에 가까우면 평탄한 영역, $0$ 보다 작으면 에지로 판단한다.

## cv::cornerHarris

- `cv::cornerHarris(src, dst, blockSize, ksize, k, borderType = BORDER_DEFAULT);`
  - `blockSize`: 행렬 연산에 사용 할 이웃 픽셀 크기이다. 픽셀의 주변으로 `(blockSize, blockSize)` 윈도우를 설정하여 행렬 $M$ 을 계산한다.
  - `ksize`: 소벨 연산자를 위한 커널 크기를 지정한다.
  - `k`: 해리스 코너 검출 상수이다.
- 해당 함수를 통해 나온 결과는 하나의 코너 위치에서 사용자 지정 임계값보다 큰 픽셀이 여러개 발생할 수 있으므로 비최대 억제 (non-maximum suppression, NMS) 를 사용한다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);
    
    cv::Mat harris;  // 해리스 코너 검출 응답 함수
    cv::cornerHarris(src, harris, 3, 3, 0.04);

    cv::Mat harris_norm;  // 해리스 코너 응답 함수의 분포
    cv::normalize(harris, harris_norm, 0, 255, cv::NORM_MINMAX, CV_8U);

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);

    for (int i = 1; i < harris.rows < i++)
    {
        for (int j = 1; j < harris.cols < j++)
        {
            if (harris_norm.at<uchar>(i, j) > 120)
            {
                if (harris.at<uchar>(i, j) > harris.at<uchar>(i - 1, j) &&
                    harris.at<uchar>(i, j) > harris.at<uchar>(i + 1, j) &&
                    harris.at<uchar>(i, j) > harris.at<uchar>(i, j - 1) &&
                    harris.at<uchar>(i, j) > harris.at<uchar>(i, j + 1))
                    {
                        cv::circle(dst, cv::Point(j, i), 5, cv::Scalar(0, 0, 255), 2);
                    }
            }
        }
    }

    cv::imshow("src", src);
    cv::imshow("dst", dst);
    cv::imshow("norm", harris_norm);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```

## FAST (Features from Accelerated Segment Test) 코너 검출

- 단순 픽셀값 비교를 이용한 코너 검출 방법이다.
- 영상의 모든 픽셀에서 픽셀을 둘러싸고 있는 16개의 주변 픽셀과 밝기를 비교 후 코너 여부를 판별한다.
- 점 $p$가 존재할 때, $p$ 를 둘러싼 16개의 픽셀 중 9개 이상의 연속 된 픽셀이 충분히 밝거나 어둡다면 코너로 정의한다.
- `cv::FAST(image, keypoints, threshold, nonMaxSupperssion = true);`
  - `image`: `CV_8UC1` 타입의 그레이스케일 영상만 입력 가능하다.
  - `keypoints`: `std::vector<cv::Keypoint>&` 타입의 출력 영상이다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    std::vector<cv::KeyPoint> keypoints;
    cv::FAST(src, keypoints, 60, true);

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);

    for (cv::KeyPoint kp: keypoints)
    {
        cv::Point pt(cvRound(kp.pt.x), cvRound(kp.pt.y));
        cv::circle(dst, pt, 5, cv::Scalar(0, 0, 255), 2);
    }

    cv::imshow("src", src);
    cv::imshow("dst", dst);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
