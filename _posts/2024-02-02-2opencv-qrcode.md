---
title: OpenCV QRCode 검출
author: yjh
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## QRCode

- 코드의 세 모서리에 포함 된 흑백 정사각형의 패턴을 찾아 코드 전체 영역 위치를 알아내야한다.
- 정사각형 형태로 투시 변환 후 코드 내부에 포함 된 격자 무늬를 해석하여 문자열을 추측해야한다.

### cv::QRCodeDetector

### cv::QRCodeDetector.detect

- `cv::QRCodeDetector.detect(img, points);` 함수로 QRCode 의 영역을 찾는다.
  - `points`: `cv::Point` 혹은 `cv::Point2f` 타입을 반환한다.

### cv::QRCodeDetector.decode

- `cv::QRCodeDetector.decode(img, points, straight_qrcode);` 함수로 코드에 저장 된 문자열을 추출한다.
  - `points`: `detect()` 함수에 반환 된 `cv::Rect` 객체가 들어간다.
  - `straight_qrcode`: 정사각형의 코드의 영상을 반환하며, 타입은 `CV_8UC1`이다.
- 해당 함수를 통해 영상에서 문자열을 추출하지 못하면 빈 문자열을 반환한다.

### cv::QRCodeDetector.detectAndDecode

- `cv::QRCodeDetector.detectAndDecode(img, points, straight_qrcode);` 함수로 위의 함수 두개를 동시에 수행한다.
  - 추출한 문자열만 반환 받고 싶다면 `points`와 `straight_qrcode`는 `cv::Mat()` 혹은 `cv::noArray()`로 지정한다. (디폴트 값)

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_GRAYSCALE);

    std::vector<cv::Point> pts;

    cv::QRCodeDetector qr;
    cv::String str = qr.detectAndDecode(src, pts);

    if (str.empty())
    {
        std::cerr << "qr code is not readable" << std::endl;
        return 1;
    }

    cv::polylines(src, pts, true, cv::Scalar(0, 0, 255), 2);
    cv::putText(src, str, cv::Point(10, 30), cv::FONT_HERSHEY_SIMPLEX, 2, cv::Scalar(0, 0, 255), 2);

    cv::imshow("src", src);
    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
