---
title: OpenCV 특징점 매칭
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, cv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## OpenCV 특징점 매칭

- 두 영상에서 추출한 특징점 기술사를 비교하여 서로 비슷한 특징점을 찾는 작업이다.
- 크기 불변 특징점으로부터 구한 기술자를 매칭하면 크기와 회전에 강인한 영상 매칭을 수행할 수 있다.

## cv::DMatch 클래스

- 한 장의 영상에서 추출한 특징점고 다른 한 장의 영상, 또는 여러 영상에서 추출한 특징점 사이의 매칭정보를 표현한다.

### cv::DMatch 클래스의 멤버변수

- `cv::DMatch::queryIdx`: 질의 기술자 번호.
- `cv::DMatch::trainIDx`: 훈련 기술자 번호.
- `cv::DMatch::imgIdx`: 훈련 영상 번호. 여러장의 영상을 훈련 영상으로 솔정한 경우에 사용 된다.
- `cv::DMatch::distance`
  - 두 기술자 사이의 거리를 나타낸다. 매칭의 척도이며, 유사할수록 0에 가깝다.
  - 거리 계산 방식은 유클리드 (euclidean distance) 거리를 주로 사용한다.
    - 이진 기술자끼리 비교하는 경우에는 해밍 거리를 사용한다.

## 매칭 클래스 종류

### cv::DMatch::BFMatcher

- `DescriptorMatcher` 클래스를 상속 받아 만들어진 클래스이다.
- 전수 조사 (Brute-force) 매칭을 수행한다.
- 질의 기술자 집합에 있는 모든 기술자와 훈련 기술자 집합에 있는 모든 기술자 사이의 거리를 계산하고, 거리가 가장 작은 기술자를 찾아 매칭한다.
- 매우 직관적인 매칭 결정 방법이지만 특징점의 갯수가 늘어날수록 계산 횟수가 급격하게 늘어난다.
  - e.g.) 첫 번째 영상에 $1,000$ 개의 특징점이 있고 두 번째 영상에 $2,000$ 개의 특징점이 있다면 전체 $2,000,000$ 번의 연산을 수행해야한다.

### cv::DMatch::BFMatcher::create

- `cv::Ptr<cv::DescriptorMatcher> matcher = cv::BFMatcher::create();` 의 형태로 객체를 생성한다.
- `normType`: 기술자 거리 측정 방식, `NORM_L1`, `NORM_L2`, `NORM_HAMMING`, `NORM_HAMMING2` 중 하나를 지정한다.
- `crossCheck`: `true` 인 경우 `i`번째 질의 기술자와 가장 유사한 훈련 기술자가 `j`이고 `j`번째 훈련 기술자와 가장 유사한게 `i`인 경우에만 매칭한다.

### cv::DMatch::FlannBasedMatcher

- Flann (Fast Library approximate nearest neighbors).
- 근사화 된 최근방 이웃 (ANN, Approximate Nearest Neighbor) 알고리즘을 빠르게 구현한 라이브러리이다.
- 해당 클래스는 `Flann` 라이브러리를 이용하여 빠르게 매칭을 수행하는 클래스이다.
  - 근사화 된 거리 계산 방법을 사용하므로 가장 거리가 작은 특징점은 찾을 수 없지만, 대신 매우 빠르게 동작한다.
- `L2 Norm` 거리 측정 방식을 사용하므로 해밍거리를 사용하는 이진 기술자에 대해서는 사용할 수 없다.

### cv::DMatch:FlannBasedMachter::create

- 입력 값은 없고 반환 값만 존재한다.

### cv::Descriptor::match

- `queryDescriptors`: 질의 기술자 지합
- `trainDescriptors`: 훈련 기술자 집합
- `matches`: 매칭 결과
- `mask`
  - 서로 매칭 가능한 질의 기술자와 훈련 기술자를 지정할 때 사용한다.
  - 행 개수는 질의 기술자 개수, 열의 개수는 훈련 기술자의 개수와 같아야한다.
  - `mask.at<cv::uchar>(i, j)` 의 값이 0 이면 `queryDescriptor[i]` 는 `trainDescriptor[j]`로 매칭 될 수 없다./
  - `cv::noArray()` 를 지정하면 모든 가능한 매칭을 찾는다.

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("./path/to/src", cv::IMREAD_GRAYSCALE);
    cv::Mat ref = cv::imread("./path/to/ref", cv::IMREAD_GRAYSCALE);

    if (src.empty() || ref.empty())
    {
        std::cerr << "file load is failed." << std::endl;
        return -1;
    }

    cv::Ptr<cv::Feature2D> feature = cv::ORB::create();  // 특징점을 찾기 위해 추출기 생성

    std::vector<cv::KeyPoint> kpts1, kpts2;  // 특징점 (keypoint) 를 담을 변수 선언

    cv::Mat desc1, desc2;
    feature->detectAndCompute(src, cv::Mat(), kpts1, desc1);
    feature->detectAndCompute(ref, cv::Mat(), kpts2, desc2);

    cv::Ptr<cv::DescriptorMatcher> matcher = cv::BFMatcher::create(cv::NORM_HAMMING);

    std::vector<cv::DMatch> matches;
    matcher->match(desc1, desc2, matches);

    // 좋은 매칭점을 찾기 위한 distance 정렬 및 상위 매칭 결과만 저장
    std::sort(matches.begin(), matches.end());  // matches 내부의 distance 를 정렬
    std::vector<cv::DMatch> good_matches(matches.begin(), matches.begin() + 50);  // 가장 매칭이 잘 된 상위 50개만 저장

    cv::Mat dst;
    cv::drawMatches(src, kpts1, ref, kpts2, matches, dst);

    cv::Mat dst2;
    cv::drawMatches(src, kpts1, ref, kpts2, good_matches, dst2);

    cv::imshow("dst", dst);
    cv::imshow("dst2", dst2);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}
```
