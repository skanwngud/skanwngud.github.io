---
title: OpenCV 비디오 다루기
author: yjh
categoris: [CPP, OpenCV]
tags: [cpp, opencv]
---

## Video Capture

- 영상 파일을 불러오거나 장치에 연결 되어있는 카메라에서 이미지 (프레임) 을 받아옴
- 영상 파일을 불러올 때엔 `cv::VideoCapture cap("./path/to/file");`을
- 장치에 연결 된 카메라에서 프레임을 불러올 때에는 `cv::VideoCapture cap(0);`을 입력한다.
  - `0`은 장치에 연결 된 0번째 카메라라는 의미이며, 연결 된 장치가 늘어날 수록 `index` 도 늘어난다.
- `cv::VideoCapture` 클래스를 초기화하기 위한 방법은 두 가지.
  - 선언과 동시에 초기화
  - 선언만 한 뒤 `.open()` 함수로 초기화
- `.isOpend()` 함수로 현재 영상이나 카메라가 열려있는지 확인 가능
- `.read(frame)` 함수로 미리 설정 된 `frame`이란 `cv::Mat` 변수에 불러온 이미지 대입 가능
  - `cv::VideoCapture::operator >> frame` 으로 `>>` 연산자를 통해 대입 가능
- 비디오 캡처에 대한 모든 작업이 완료 되었으면 `release()` 함수를 이용해 메모리에서 제거한다.

### 영상 정보 얻기

- `.get(flag)` 함수로 영상에 대한 정보를 얻을 수 있음
  - `cv::CAP_PROP_FPS`; 영상의 fps를 얻음
  - `cv::CAP_PROP_FRAME_WIDTH`; 영상의 width 를 얻을 수 있음
- `.set(flag)` 함수로 영상에 대한 정보값을 세팅할 수 있음

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
    cv::Mat frame;

    cv::VideoCapture cap(0);  // 선언과 동시에 초기화
    // cv::VideoCapture cap();
    // cap.open(0);  // 선언 후 open 함수를 통한 초기화

    if(!cap.isOpened())
    {
        std::cerr << "the video has not ready.";
        return -1;  // 영상이 열리지 않았으면 종료
    }

    while(cap.isOpened())
    {
        cap >> frame;
        cap.read(frame);  // 미리 선언 한 frame 에 read 한 이미지 대입

        std::cout << "Frame size is " << cap.get(cv::CAP_PROP_FRAME_WIDTH) << ", " << cap.get(cv::CAP_PROP_FRAME_HEIGHT) << std::endl;

        if(!frame.rows > 0)
        {
            std::cout << "video is over." << std::endl;
            break;
        }

        cv::imshow("debug", frame);
        if(cv::waitKey(cap.get(cv::CAP_PROP_FPS)) == 27)
        {
            break;
        }
    }

    cv::destroyAllWindows();
    cv::release();

    return 0;
}
```

## Video Writer

- 이미지나 영상의 프레임을 영상의 형태로 저장하는 클래스
- `cv::VideoWriter::fourcc(codec name(4 char));`로 저장할 비디오의 코덱 형식을 지정
  - `int` 형식의 4개의 숫자
- `cv::VideoWriter wrt;` 선언을 통해 `VideoWriter` 객체를 선언하고 `wrt.open();`을 통해 초기화한다.
  - `wrt.open("path/to/file_name", fourcc, fps, size);`
  - `cv::Size(width, height)`
- 프레임을 저장할 땐 `write()` 함수를 사용하거나 재정의 된 `<<` 연산자를 이용한다.
- 모든 처리가 끝나면 마찬가지로 `release()` 함수를 실행한다.

```cpp
#include "opencv2/opencv.hpp"

#include <iostream>

int main(void)
{
  cv::Mat frame;
  cv::Mat outFrame;

  cv::VideoCapture cap("./path/to/video");  // 프레임을 캡처 할 카메라, 혹은 영상 파일

  cv::VideoWriter wrt;
  int fourcc = cv::VideoWriter::fourcc('X', 'V', 'I', 'D');  // 코덱 정의
  wrt.open("./path/to/save/file", fourcc, cap.get(cv::CAP_PROP_FPS), cv::Size(cap.get(cv::CAP_PROP_FRAME_WIDTH), cap.get(cv::CAP_PROP_FRAME_HEIGHT)));

  while(cap.isOpened())
  {
    cap >> frame;

    if(!cap.rows > 0)
    {
      break;
    }

    wrt << frame;  // 저장
    // wrt.write(frame);  으로도 지정 가능
  }

  wrt.release();
  cap.release();

  return 0;
}
```
