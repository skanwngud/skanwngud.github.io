---
title: OpenCV 크기 불변 특징점 검출과 기술
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## SIFT (Scale Invariant Feature Transform)

- 크기에 불변한 특징점을 검출할 때 인접한 가우시안 블러링 영상끼리의 차영상을 사용하며 이를 DoG (Difference of Gaussian) 이라고 한다.
- 특징점을 검출하는 기능 뿐만 아니라 특징점 주변의 픽셀값을 이용한 기술자 (descriptor) 계산 방법도 포함한다.
- 기술자는 특징점 주변 영상의 특성을 여러 개의 실수값으로 표현한 것이며 **특징 벡터 (feature vector)** 라고도한다.
- 서로 같은 특징점에서 추출 된 기술자는 실수값 구성이 서로 일치해야한다.
- 기본적으로 특징점 부근 부분 영상으로부터 그래디언트 방향 히스토그램을 추출하여 기술자로 사용한다.
- 특징점 근방으로부터 특징점의 주된 방향 성분을 계산하고 이 방향만큼 회전한 부분 영상으로부터 128개의 빈 (bin) 으로 구성 된 그래디언트 방향 히스토그램을 계산한다.
- 각각의 빈 값은 `float` 자료형을 사용하며 하나의 특징점을 512 바이트 크기의 기술자로 표현한다.
- `SIFT` 를 개선하기 위해 `SURF` (Speed-Up Robust Features), `KAZE` 등이 나왔지만 여전히 메모리 점유율이 높고 속도도 실시간으로 연산이 힘들다는 단점이 존재했다.

### 스케일 스페이스 (Scale Space)

- 영상의 크기 변화에 무관하게 특징점을 추출하기 위해 입력 영상으로부터 구성한다.
- 스케일 스페이스는 영상에 대한 표준편차를 이용한 가우시안 블러링을 적용하여 구성한 영상 집합을 의미한다.
- 가우시안 블러링을 통한 영상 집합을 옥타브 (octave) 라고 부르며, 이후 입력 영상을 가로세로 반으로 줄여가며 여러 옥타브를 구성한다.

## ORB (Oriented FAST and Rotated BRIEF)

- `FAST` 코너 검출 방법을 이용하여 특징점을 추출하나 `ORB` 는 입력 영상의 크기를 점진적으로 축소한 피라미드 영상을 구축하여 특징점을 추출한다.
- 각 특징점에서 주된 방향 성분을 계산하고 방향을 고려한 `BRIEF` 알고리즘으로 이진 기술자를 계산한다.

### BRIEF (Binary Robust Independent Elementary Features)

- 순수하게 특징점 기술자만 생성하는 알고리즘이다.
- 특징점 주변 픽셀 쌍을 미리 정하고 해당 픽셀값 크기를 비교하여 0 혹은 1 로 특징을 기술한다.
  - 두 점 $x$, $y$ 에서의 픽셀값 크기 비교를 위한 $\gamma$ 값은 다음과 같다.
  - $$\gamma(x, y) = \begin{cases} 1 \;\; I(x) < I(y)\;일\;때 \\ 0 \;\; 그\;외 \end{cases}$$
  - 특징점 $p$ 주변 픽셀 $a$, $b$, $c$ 를 미리 정의하고 $\gamma(a, b)$, $\gamma(b, c)$, $\gamma(c, a)$를 구하면 이진수 $110_{(2)}$ 가 나온다.
  - 이진수 $110_{(2)}$ 는 $b$ 가 $a$ 보다 밝고, $c$ 가 $b$ 보다 밝고, $a$ 가 $c$ 보다 어둡다는 정보를 표현한다.

### 이진 기술자 (Binary Descriptor)

- 이진 기술자 (binary descriptor) 로 표현 된 특징점 사이의 거리 계산은 주로 해밍 거리 (hamming distance) 를 이용한다.
- 이진수로 표현 된 두 기술자에서 서로 값이 다른 비트의 개수를 세는 방식으로 계산한다.
- 두 기술자의 비트 단위의 배타적 논리합 (`XOR`) 연산 후, 비트 값이 1인 개수를 세는 방식으로 빠르게 계산 가능하다.

## cv::Ferature2D

- 특징점 관련 클래스는 모두 `cv::Feature2D` 클래스를 상속 받아 만들어진다.
- 해당 클래스는 `detect()`, `compute()`, `detectAndCompute()` 함수를 가상 멤버 함수를 가질 수 있다.
  - 특징점 검출 관련 클래스에 따라 `detect()` 만 할 수 있거나 `compute()` 만 할 수 있는 등의 기능차이가 존재한다.

### cv::ORB::create

- `ORB` 특징점 검출 클래스 객체를 생성한다.
- `cv::ORB::create(nfeatures, scaleFactor, nlevels, edgeThreshold, firstLevel, WTA_K, scoreType, patchSize, fastThreshold);`
  - `nfeatures`: 검출 할 최대 특징점 갯수.
  - `scaleFactor`: 피라미드 생성 비율.
  - `nlevels`: 피라미드 단계 갯수.
  - `edgeThreshold`: 특징을 검출하지 않을 영상 가장 자리 픽셀 크기.
  - `firstLevel`: 항상 0 으로 지정해줘야한다.
  - `WTA_K`: `BRIEF` 기술자 계산 시 사용해야 할 점의 갯수. (2, 3, 4 중 하나를 지정해야한다.)
  - `scoreType`: 특이점 점수 결정 방법이다. (`cv::ORB::HARRIS_SCORE`, `cv::ORB::FAST_SCORE` 중 하나를 지정해줘야한다.)
  - `patchSize`: `BRIEF` 기술자 계산 시 사용할 패치의 크기이다.
  - `fastThreshold`: `FAST` 코너 검출 방법에서 사용 되는 임계값이다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("./path/to/src", cv::IMREAD_GRAYSCALE);

    cv::Ptr<cv::Featured2D> feature = cv::ORB::create();

    std::vector<cv::KeyPoint> keypoints;
    feature->detect(src, keypoints);

    cv::Mat desc;
    feature->compute(src, keypoints, dsec);

    cv::Mat dst;
    cv::drawKeypoints(src, keypoints, dst, cv::Scalar::all(-1), cv::DrawMatchesFlags::DRAW_RICH_KEYPOINTS);

    std::vector<cv::Keypoint> kpts;
    cv::Mat desc2, dst2;
    feature->detectAndCompute(src, cv::Mat(), kpts, desc2);  // detect(), compute() 를 한 번에 수행한다
    cv::drawKeypoints(src, kpts, dst2, cv::Scalar::all(-1), cv::DrawMatchesFlags::DRAW_RICH_KEYPOINTS);

    return 0;
}
```
