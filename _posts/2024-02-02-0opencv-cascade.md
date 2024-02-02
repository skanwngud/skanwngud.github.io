---
title: OpenCV cascade 얼굴 검출
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, book review, opencv]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 캐스케이드 (Cascade) 얼굴 검출

- 영상을 $(24, 24)$ 사이즈로 정규화 한 후, 유사 - 하르 필터 (Harr - like filter) 집합으로부터 특징 정보를 추출하여 얼굴 여부를 판단한다.

### 유사 - 하르 필터

- 흑백 사각형이 서로 붙어있는 형태로 구성 된 필터이다.
  - 흰색 영역의 픽셀값은 모두 더하고 검은색 영역 픽셀값은 모두 빼서 하나의 특징값을 얻을 수 있다.
  - 사람의 정면 얼굴 형태가 전형적으로 밝은 영역과 어두운 영역이 정해져있기 때문에 해당 필터로 구한 특정 값은 얼굴을 판별하는 용도로 사용 가능하다.
- 제시한 $(24, 24)$ 사이즈의 크기에서 필터를 대략 *18만개* 를 생성할 수 있고, 시간 자체가 너무 오래 걸린다는 단점이 있었다.
  - 해당 단점을 에이다부스트 알고리즘과 적분 영상을 이용하여 해결하였다.
  - 필터 연산은 처음 1단계에서 필터 하나를 사용하여 얼굴을 판단하고 얼굴이 아니라고 판단 되면 이후의 필터 계산을 수행하지 않는다.
  - 1단계를 통과 한 경우에는 2단계에서 필터 다섯개를 사용하여 얼굴을 판단하고 그 뒤 해당 연산을 계속 반복한다.

### cv::CascadeClassifier

- `OpenCV`에서는 `cv::CascadeClassifier` 클래스를 제공한다.
- 미리 훈련 된 객체 검출 분류기의 `xml` 파일을 불러오는 기능과 주어진 영상에서 객체를 검출하는 기능으로 이루어져있다.
- 객체 생성은 `cv::CascadeClassifier detector;`로 생성한다.
- 기학습 된 분류기를 불러오기 위해선 `load()` 함수를 사용한다.
  - 객체 생성과 동시에 분류기를 불러올 수 있다.

### detectMultiScale(image, objects, scaleFactor, minNeighbors, flags, minSize, maxSize)

- `image`: 입력 영상이며 3채월 영상이 들어오는 경우 자체적으로 그레이스케일로 변환 후 연산을 수행한다.
- `objects`: 검출 된 객체의 사각형 좌표 정보로 `cv::Rect` 클래스로 반환한다.
- `scaleFactor`: 검색 윈도우의 확대 비율, 1보다 커야한다.
- `minNieghbors`: 검출 영역을 선택하기 위한 최소 검출의 횟수이다.
- `minSize`: 검출한 객체의 최소크기이다.
- `maxSize`: 검출한 객체의 최대크기이다.

> OpenCV 의 최근 버전에서는 더 이상 해당 얼굴 검출기를 지원하지 않는다. 이는 검출기의 성능보다 최근 딥러닝의 성능이 월등히 높아졌기 때문이다.
{: .promt-warning }

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Mat src = cv::imread("path/to/src", cv::IMREAD_COLOR);

    if (src.empty())
    {
        std::cerr << "Image file load is failed." << std::endl;
        return 1;
    }

    cv::CascadeClassifier detect_faces("path/to/face.xml");  // 얼굴 검출기
    cv::CascadeClassifier detect_eyes("path/to/eyes.xml");  // 눈 검출기

    if (detect_faces.empty() || detect_eyes.empty())
    {
        std::cerr << "detector load is failed." << std::endl;
        return 1;
    }

    std::vector<cv::Rect> faces, eyes;  // 얼굴과 눈을 검출한 결과를 담을 벡터
    detect_face.detectMultiScale(src, faces);  // src 파일에서 얼굴을 우선 찾음

    for (cv::Rect face: faces)
    {
        cv::Mat roi = src(face);  // 눈을 찾기 위해선 얼굴을 먼저 찾아낸 후 해당 영역에서 찾으면 훨씬 효율적이다.
        detect_eyes.detectMultiScale(roi, eyes);  // 먼저 찾은 얼굴들을 순회하면서 눈 영역을 찾아낸다.
        
        for (cv::Rect eye: eyes)
        {
            int x, y;
            cv::Rect eyeRoi(x + eye.x, y + eye.y, eye.width, eye.height);  // src 파일에서 눈의 영역이 어딘지 파악
            cv::rectangle(src, eyeRoi, cv::Scalar(0, 0, 255), 2);
        }

        cv::rectangle(src, face, cv::Scalar(0, 255, 0), 2);
    }

    cv::imshow("src", src);
    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
