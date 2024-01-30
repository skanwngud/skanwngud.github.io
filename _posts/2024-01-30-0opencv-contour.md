---
title: OpenCV 외곽선
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 외곽선 (Contour) 검출

- 외곽선은 객체 영역 픽셀 중에서 배경 영역과 인접한 일련의 픽셀을 의미한다.
- 검은색 배경 안에 있는 흰색 객체 영역에서 가장 최외곽에 있는 픽셀을 찾아 이곽선으로 정의한다.
- 흰색 객체 영역 안에 홀 (hole) 이 존재한다면 홀을 둘러싸고 있는 객체 픽셀들도 외곽선으로 검출할 수 있다.
  - 객체의 외곽선은 바깥쪽과 안쪽의 외곽선으로 구분할 수 있다.
- `OpenCV`의 외곽선 검출은 객체의 외곽선 픽셀 좌표를 모두 추출하여 *계층정보*와 함께 반환한다.
- 객체 하나의 외곽선은 여러개의 점으로 구성 되어있으므로 `std::vector<cv::Point>` 으로 저장 가능하다.
- 하나의 입력 영상에서 객체는 여러개로 구성 될 수 있으므로 최종적으로 외곽선은 `std::vector<std::vector<cv::Point>>`로 저장한다.

### cv::findContours

- `cv::findContours(image, contours, hierachy, mode, method, offset = cv::Point());`
- `cv::findContours(image, contours, mode, method, offset = cv::Point());`
  - `image`: 이진화 처리 된 입력 영상이다. 8비트 1채널이어야하며, 0이 아닌 픽셀을 객체 취급한다. `mode`가 `cv::RETR_CCOMP`이면 `CV_32SC1` 타입을 지정한다.
  - `contours`: 검출 된 외곽선 정보이다. `std::vector<std::vector<cv::Point>>` 타입을 지정한다.
  - `hierachy`: 외곽선 계층 정보이다. `cv::vector<cv::Vec4i>` 타입을 지정하며, `mode`가 특정 상수일 때에만 입력을 받는다.
    - `i` 번째 외곽선에 대해 `hierachy[i][0]`은 다음 외곽선 번호, `...[i][1]`은 이전 외곽선 번호, `...[i][2]`은 자식 외곽선 번호, `...[i][3]`은 부모 이곽선 번호가 저장 된다.
      - 계층 구조에서 해당 외곽선이 존재하지 않는다면 해당 배열에 `-1`이 저장 된다.
  - `mode`: 외곽선 검출 모드이다. `RetrievalModes` 열거형 상수를 지정한다.
    - `cv::RETR_EXTERNAL`: 객체 바깥쪽의 외곽선만 검출한다. 계층 구조는 구성하지 않는다.
    - `cv::RETR_LIST`: 바깥쪽과 안쪽 외곽선을 모두 검색한다. 계층 구조는 구성하지 않는다.
    - `cv::RETR_CCOMP`: 모든 외곽선을 검출하고 2단계 계층 구조를 구성한다.
    - `cv::RETR_TREE`: 모든 외곽선을 검출하고 전체 계층 구조를 구성한다.
  - `method`: 외곽선 근사화 방이다. `ContourApproximationModes` 열거형 상수를 지정한다.
    - `cv::CHAIN_APPROX_NONE`: 모든 외곽선 점들의 좌표를 저장한다.
    - `cv::CHAIN_APPROX_SIMPLE`: 외곽선 중 수평, 수직, 대각선 성분은 끝점만 저장한다.
    - `cv::CHAIN_APPROX_TC89_L1`:  `Teh & Chin L1` 근사화를 적용한다. 저장 되는 외곽선의 수가 많이 줄어들고 외곽선의 모양이 변할 수 있다.
    - `cv::CHAIN_APPROX_TC89_KCOS`: `Teh & Chin K Cos` 근사화를 적용한다. 저장 되는 외곽선의 수가 많이 줄어들고 외곽선의 모양이 변할 수 있다.
  - `offset`: 이동 변위를 지정하는 외곽선 점 좌표의 오프셋이다.

### cv::drawContours

- `cv::drawContours(image, contours, contourIdx, color, thickness, lineType, hierachy, maxLevel, offset);`
- 생성한 외곽선을 그려주는 함수이다.
- 전체 외곽선을 한 번에 그릴 수도 있고 하나의 외곽선 번호를 선택해 그릴 수도 있다.
- 계층 정보를 지정하는 경우 자식 외곽선도 함께 그릴 수 있다.

```cpp
#include "opencv2/opencv.hpp"

void contours_basic(cv::Mat src);
void contours_hier(cv::Mat src);

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    if (src.empty())
    {
        std::cerr << "source image file is not Opend!" << std::endl;
        return 1;
    }

    contours_basic(src);
    contours_hier(src);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}

void contours_basic(cv::Mat src)
{
    cv:Mat bin;
    cv::threshold(src, bin, 0, 255, cv::THRESH_BINARY);

    std::vector<std::vector<cv::Point>> contours;

    cv::findContours(bin, contours, cv::RETR_LIST, cv::CHAIN_APPROX_NONE);

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);

    for (int i = 0; i < contours.size(); i++)
    {
        cv::Scalar c(std::rand() & 255, std::rand() & 255, std::rand() & 255);
        cv::drawContours(dst, contours, i, c, 2);
    }

    cv::imshow("basic", dst);
}

void contours_hier(cv::Mat src)
{
    cv::Mat bin;
    cv::threshold(src, bin, 0, 255, cv::THRESH_BINARY);

    std::vector<std::vector<cv::Point>> contours;
    std::vector<cv::Vec4i> hier;

    cv::findContours(src, contours, hier, cv::RETR_CCOMP, cv::CHAIN_APPROX_SIMPLE);

    cv::Mat dst;
    cv::cvtColor(src, dst, cv::COLOR_GRAY2BGR);

    for (int i = 0; i >= 0; i = hier[i][0])
    // hier[i][0] 은 다음 외곽선의 계층 정보를 갖고 있으며, 갖고 있지 않은 경우 -1 이 되면서 반복문이 종료 된다
    {
        cv::Scalar c(std::rand() & 255, std::rand() & 255, std::rand() & 255);
        cv::drawContours(dst, contours, i, c, -1, cv::LINE_8, hier);
    }

    cv::imshow("hierchy", dst);
}
```

## 외곽선 처리 함수

### cv::boundingRect

- `cv::boundingRect(points);`
- 외곽선들을 감싸는 가장 작은 크기의 사각형 (bounding box) 를 구하는 함수이다.
- 특정 객체의 바운딩 박스는 `cv::connectComponentsWithStats()` 함수를 통해 구할 수 있지만, 이미 외곽선을 알고 있는 경우 `cv::boundingRect()`가 더 효율적이다.

### cv::minAreaRect

- `cv::minAreaRect(points);`
- 외곽선 혹은 점들을 감싸는 최소 크기의 회전 된 사각형 (rotate bounding box) 을 구하는 함수이다.
- 가장 작은 면적의 사각형 정보를 `cv::RotateRect` 클래스로 반환한다.

### cv::minEnclosingCircle

- `cv::minEnclosingCircle(points, center, radius);`
- 외곽선 혹은 최소 크기의 원을 구하는 함수이다.

### cv::arcLength

- `cv::arcLength(curve, closed);`
- 임의의 곡선을 형성하는 점들의 집합을 가지고 있을 때, 해당 곡선의 길이를 구할 때 사용하는 함수이다.
  - `curve`: `std::vector<cv::Point>` 혹은 `std::vector<cv::Point2f>` 자료형을 전달한다.
  - `closed`: `true`인 경우 입력곡선의 시작점과 끝점이 연결 되어있는 폐곡선이라고 간주한다.

### cv::contourArea

- `cv::contourArea(contour, oriented);`
- 임의의 외곽선 정보를 가지고 있을 때, 외곽선이 감싸는 영역의 면적을 알고 싶을 때 사용한다.
  - `oriented`: 진행방향 정보의 사용 여부이다.
    - `true` 일 때, 곡선의 진행 방향 (시계 혹은 반시계 방향) 에 따라 면적의 부호가 달라진다.
    - `false` 일 때, 면적의 절댓값을 반환한다.

### cv::approxPolyDP

- 더글라스-포이커 (Douglass - Peucker) 알고리즘을 사용하여 곡선 또는 다각형을 단순화시킨다.
- 입력 외곽선에서 가장 멀리 떨어져있는 두 점을 찾아 직선으로 연결하고 해당 직선에서 가장 멀리 떨어진 외곽선 상의 점을 찾아 근사화 점으로 추가한다.
- 위 작업을 반복하다가 새로 추가할 외곽선 상의 점과 근사화에 의한 직선과의 수직거리가 `epsilon` 보다 작으면 근사화를 멈춘다.
- `cv::approxPolyDP(curve, approxCurve, epsilon, closed);`
  - `curve`: 2차원 점들의 좌표로 이루어진 입력 영상이다.
  - `approxCurve`: 근사화 된 점들의 좌표로 이루어진 출력 영상이다.
  - `epsilon`: 근사화 정밀도 파라미터. 입력 곡선과 근사화 된 곡선까지의 최대 거리를 지정한다.
    - 보통 입력 외곽선 또는 곡선 길이의 일정 비율로 지정한다.
    - 일반적으로 `cv::arcLength(curve, true) * 0.02`로 한다.
  - `closed`: 폐곡선 여부. `true`면 폐곡선으로 간주한다.

```cpp
#include "opencv2/opencv.hpp"

void setLabel(cv::Mat &img, const std::vector<cv::Point> &pts, const cv::String &label);

int main(void)
{
    cv::Mat img = cv::imread("path/to/img", cv::IMREAD_COLOR);

    cv::Mat gray, bin;  // 이진화 수행을 위한 행렬
    cv::cvtColor(img, gray, cv::COLOR_BGR2GRAY);  // 이진화 하기 위한 그레이스케일 변환
    cv::threshold(gray, bin, 200, 255, cv::THRESH_BINARY_INV | cv::THRESH_OTSU);  // 이진화

    std::vector<std::vector<cv::Point>> &contours;
    cv::findContours(bin, contours, cv::RETR_EXTERNAL, cv::CHAIN_APPROX_NONE);  // 외곽선 검출

    for (std::vector<cv::Point> &pts: contours)
    {
        if (cv::contoursArea(pts) < 400) continue;

        std::vector<cv::Point> approx;
        cv::approxPolyDP(pts, approx, cv::arcLength(pts, true) * 0.02, true);  // 근사화

        int vtc = (int)approx.size();  // 근사화 된 외곽선이 점 몇개로 표현 될 수 있는지

        if (vtc == 3)
        {
            setLabel(img, pts, "TRI");
        }
        else if (vtc == 4)
        {
            steLabel(img, pts, "RECT");
        }
        else if (vtc > 4)
        {
            double len = cv::arcLength(pts, true);
            double area = cv::contourArea(pts);
            double ratio = 4. * CV_PI * rea / (len * len);

            if (ratio > 0.8)
            {
                setLabel(img, pts, "CIR");
            }
        }
    }

    cv::imshow("img", img);

    cv::waitKey();

    return 0;
}

void setLabel(cv::Mat &img, const std::vector<cv::Point> &pts, const cv::String &label)
{
    cv::Rect rc = cv::boundingRect(pts);
    cv::rectangle(img, rc, cv::Scalar(0, 0, 255), 1);
    cv::putText(img, label, rc.tl(), cv::FONT_HERSHEY_PLAIN, 1, cv::Scalar(0, 0, 255));
}

```
