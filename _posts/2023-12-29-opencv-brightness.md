---
title: OpenCV 밝기 조절
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 영상의 밝기 조절

- 영상의 모든 픽셀에 대해 값을 증가시키면 밝아지고 감소시키면 어두워진다.
- $dst(x, y) = src(x, y) + n \;(단, \;x, \;y 는 \;픽셀의 \;좌표이며 \;n 은 \;밝기 \;조절값)$
- 단, 포화연산 (saturate) 으로 인해 픽셀값은 최소 0, 최대 255 를 넘을 수 없다.
  - 255 를 넘어가는 연산을 하게 되면 255 로 유지, 0 밑으로 내려가는 연산을 하게 되면 0 으로 유지.
  - $dst(x, y) = saturate(src(x, y) + n)$

### OpenCV 기본 연산

- `OpenCV`에선 연산자 오버로딩이 되어있어 이미지 픽셀 연산에 대해선 자동으로 포화연산을 지원한다.
- 이후 나올 다른 방법들에 비해 자체적인 최적화를 통해 속도가 제일 빠르며 가독성 또한 가장 좋다.

```cpp
#include "opencv2/opencv.hpp"

void brightness();

int main(void)
{
    brightness();

    return 0;
}

void brightness()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    if (src.empty())
    {
        std::cerr << "Image Load Failed!" << std::endl;
        return;
    }

    cv::Mat dst(src.rows, src.cols, src.type());  // src 와 동일한 크기의 행렬을 생성한다.
    
    dst = src + 100;  // 밝기 100 증가

    cv::imshow("src", src);
    cv::imshow("dst", dst);
    cv::waitKey();
}
```

- `src` 이미지에서 밝기를 증가시킨 이미지 `dst`를 만든다.
- 별도의 행렬을 만들지 않고 자기 자신의 밝기를 조절하는 경우엔 행렬을 추가로 생성하지 않고 덧셈할당(`+=`) 을 한다.
  - 위 코드의 경우 `src += 100;`을 하면 된다.
- 연산자의 경우 연산자 오버로딩이 되어있기 때문에 포화연산이 자동으로 적용 되어 픽셀이 255 를 넘어가지 않는다.

### 픽셀에 직접 접근하여 밝기 조절

- 특수한 상황에서 `OpenCV`의 함수 등을 사용하지 못하는 경우 픽셀에 직접 접근하여 밝기를 조절해야한다.
- 이 경우엔 포화연산을 직접 구현해야한다.
  - 기본제공하는 포화연산 함수는 `cv::saturate_cast<uchar>(cv::Mat::operator);`의 형태이다.
  - 직접 구현해야하는 경우는 `v = cv::Mat::operator.at<uchar>(i, j) + 100;`일 때, `v > 255? 255 : v < 0? 0 : v;`이다.

```cpp
#include "opencv2/opencv.hpp"

void brightness1();  // 포화연산 함수 사용
void brightness2();  // 포화연산 직접 구현

int main(void)
{
    brightness1();
    brightness2();

    cv::destroyAllWindows();

    return 0;
}

void brightness1()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    if (src.empty())
    {
        std::cerr << "Image Load Failed!" << std::endl;
        return;
    }

    cv::Mat dst(src.rows, src.cols, src.type());

    for (int i = 0; i < src.rows; i++)
    {
        for (int j = 0; j < src.cols; j++)
        {
            dst.at<uchar>(i, j) = cv::saturate_cast<uchar>(src.at<uchar>(i, j) + 100);
        }
    }

    cv::imshow("src1", src);
    cv::imshow("dst1", dst);
    cv::waitKey();
}

void brightness2()
{
    cv::Mat src = cv::imread("path/to/image", cv::IMREAD_GRAYSCALE);

    if (src.empty())
    {
        std::cerr << "Image Load Failed!" << std::endl;
        return;
    }

    cv::Mat dst(src.rows, src.cols, src.type());

    for (int i = 0; i < src.rows; i++)
    {
        for (int j = 0; j < src.cols; j++)
        {
            int v = src.at<uchar>(i, j) + 100;  // src 의 밝기를 100 조절함 값
            dst.at<uchar>(i, j) = v > 255 ? 255 : v > 0 ? 0 : v;
        }
    }

    cv::imshow("src2", src);
    cv::imshow("dst2", dst);
    cv::waitKey();
}
```

### 트랙바를 이용한 밝기 조절

```cpp
#include "opencv2/opencv.hpp"

void brightness();
void on_brightness(int pos, void* userdata);

int main(void)
{

    return 0;
}

void brightness()
{
    cv::Mat src = cv::imread("/path/to/image", cv::IMREAD_GRAYSCALE);

    if (src.empty())
    {
        std::cerr << "Image Load Failed!" << std::endl;
        return;
    }

    cv::namedWindow("dst");
    cv::createTrackbar("Brightness", "dst", 0, 100, on_brightness, (void*)&src);
    on_brightness(0, (void*)&src);

    cv::waitKey();
}

void on_brightness(int pos, void* userdata)
{
    cv::Mat src = *(cv::Mat*)userdata;
    cv::Mat dst = src + pos;

    cv::imshow("dst", dst);
}
```

- `createTrackbar` 함수 뒤에 `on_brightness` 함수를 호출하지 않으면 트랙바를 움직이기 전까지 `dst`가 나오질 않기 때문이다.
