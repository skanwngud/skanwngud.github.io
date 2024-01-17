---
title: OpenCV 마우스 이벤트
author: yjh
categories: [Computer Vision, OpenCV]
tgs: [cpp, opencv, book review, cv]
---

> 해당 포스팅은 *OpenCV 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 마우스 이벤트

- 마우스 콜백함수를 정의한 뒤 `cv::setMouseCallback()` 함수에 전달한다.
- 마우스 이벤트에는 왼쪽, 오른족, 가운데 버튼 등을 지정할 수 있고 업, 다운, 클릭, 드래그 등이 있다.
- `cv::setMouseCallback(window name, call-back function);`

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

cv::Mat img;  // 전역 변수로 이미지 선언
cv::Point pt;  // 전역 변수로 좌표를 담을 변수 선언

void onMouse(int event, int x, int y, int flags, void*);  // 마우스 이벤트 콜백 함수 선언

int main(void)
{
    img = cv::imread("./path/to/image");

    cv::namedWindow("debug");  // 창을 출력할 윈도우 이름 지정
    cv::setMouseCallback("debug", onMouse);  // 마우스 좌표를 입력 받을 윈도우, 입력 받은 좌표와 플래그로 어떤 일을 할지에 대한 이벤트 처리 함수 전달

    cv::imshow("debug", img);
    cv::waitKey();

    cv::destroyAllWindows();

    return 0;
}

void onMouse(int event, int x, int y, int flags, void*)  // 함수 정의
{
    switch(event)
    {
        case cv::EVENT_LUBTTONDOWN:  // 왼버튼 누르기
            pt = cv::Point(x, y);  // 함수로부터 전달 받은 좌표값 지정
            break;

        case cv::EVENT_LBUTTONUP:  // 왼버튼 떼기
            break;

        case cv::EVENT_MOUSEMOVE:  // 마우스 드래그 (움직이기)
            if(flags &cv::EVENT_FLAG_LBUTTON)  // 마우스의 왼쪽 버튼의 이벤트가 발생한 경우
            {
                cv::line(img, pt, cv::Point(x, y), cv::Scalar(0, 255, 0), 2);  // 직전에 저장 되어있던 좌표와 현재 좌표를 연결하는 직선을 그림
                cv::imshow("debug", img);

                pt = cv::Point(x, y);  // 움직이며 발생하는 좌표들로 계속 초기화
            }

            break;

        default:
            break;
    }
}
```
