---
title: OpenCV 라벨링
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, book review, opencv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 레이블링 (Labeling)

- 각 객체 영역에 대해 고유의 번호를 지정한다.
- 이진화 처리 된 영상에 대해 각 객체의 위치, 크기, 모양을 분석할 때 사용한다.
- 객체 픽셀 집합에 고유 번호를 매기는 작업으로 연결 된 구성요소 라벨링 (connected components labeling) 이라고도 한다.
- 일반적으로 배경과 객체가 분리 되어있는 이진화 영상에서 연산을 수행한다.
  - 검은색은 배경, 흰색 (검은색이 아닌 색) 은 객체르 인식한다.
- 특정픽셀과 이웃한 픽셀의 연결 관계는 두 가지 방식으로 정의가 가능하다.
  - 상하좌우로 붙어있는 픽셀끼리 연결 되어있다고 정의하는 4방향 연결성 (4-way connectivity)
  - 상하좌우, 대각선으로 붙어있는 픽셀기리 연결 되어있다고 정의하는 8방향 연결성 (8-way connectivity)
- 이진 영상에 라벨링을 수행하면 고유의 번호가 매겨진 2차원 정수 행렬이 만들어지며 이를 라벨맵 (label map) 이라고한다.
- `OpenCV` 에서는 `3.0.0` 버젼부터 라벨링 함수를 제공한다.

### cv::connectedComponents

- `cv::connectedComponents(image, labels, connectivity = 8, ltype=CV_32S);`
  - `connectivity`: 연결성을 정의한다. 4방향, 8방향으로 정의 가능하다.
  - `ltype`: 레이블 갯수이다. 반환값이 `N` 이면 `0` 부터 `N-1`까지의 라벨 번호가 존재하며 `0`번은 배경을 나타내므로 실제 객체 갯수는 `N-1`rodlek.

### cv::connectedComponentsWithStats

- `cv::connectedComponentsWithStats(image, labels, stats, centroids, connectivity = 8, ltype = CV_32S);`
  - `stats`: 라벨 영역에 대한 통계정보를 갖고있는 $(N, 5)$ 행렬이며, `CV_32S` 타입이다.
    - 행의 갯수 `N`은 라벨링 갯수와 동일하다.
    - 각 열은 라벨을 둘러싸고 있는 라벨 영역의 시작점인 `x`, `y` 와 `width`, `height`, `픽셀 갯수` 를 의미한다.
  - `centroids`: 각 라벨 영역의 무게중심을 의마하며, `CV_64F` 타입이다.

```cpp
#include "opencv2/opencv.hpp"

void labeling(cv::Mat src);

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    labeling(src);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}

void labeling(cv::Mat src)
{
    cv::Mat bin;
    cv::threshold(src, bin, 0, 255, cv::THRESH_BINARY | cv::THRESH_OTSU);  // 영상 이진화

    cv::Mat labels, stats, centroids;
    int cnt = cv::connectedComponentsWithStats(bin, labels, stats, centroids);  // 정의 된 각 행렬에 결과값 넣고 라벨 갯수 반환

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);  // 그림을 그려줄 행렬

    for (int i = 0; i < cnt; i++)
    {
        int *p = stats.ptr<int>(i);  // stats 의 각 행을 순회

        if (p[4] < 20) continue;  // 라벨 영역이 20픽셀 미만이면 노이즈로 간주

        double *c = centroids.ptr<double>(i);  // centroids 의 각 행을 순회

        cv::rectangle(dst, cv::Rect(p[0], p[1], p[2], p[3]), cv::Scalar(0, 255, 0), 2);
        cv::line(dst, cv::Point(cvRound(c[0]), cvRound(c[1])), cv::Point(cvRound(c[0]), cvRound(c[1])), cv::Scalar(0, 0, 255), 2, cv::LINE_AA);
    }

    cv::imshow("dst", dst);
}
```