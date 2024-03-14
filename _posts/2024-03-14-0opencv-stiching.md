---
title: 영상 이어 붙이기
author: yjh
math: true
categories: [Computer Vision, OpenCV]
tags: [cpp, cv, opencv, book review]
---

> 해당 포스팅은 *OpenCv 4로 배우는 컴퓨터 비전과 머신러닝 (황선규 저)*를 보고 공부하며 개인적인 용도를 위해 정리한 글이다.

## 영상 이어 붙이기 (Image Stiching)

- 여러 장의 영상을 서로 이어 붙여 하나의 큰 영상을 만드는 기법이다.
- 파노라마 영상 (panorama image) 라고 부른다.
- 입력으로 사용하는 영상은 서로 일정 비율 이상으로 겹치는 영역이 존재해야하며, 서로 같은 위치를 분간할 수 있도록 유효한 특징점이 많이 있어야한다.
- 특징점을 검출하고 서로 매칭을 수행하여 호모그래피를 구하고 영상의 밝기를 보정하는 블렌딩 (blending) 처리로 마무리한다.

## cv::Sticher

- `mode`: 이어 붙이기 방식. `cv::Sticher::PANORAMA` 혹은 `cv::Sticher::SCANS` 를 지정한다.
  - 기본값은 `PANORAMA` 이므로 생략 가능하며, 스캐너에서 스캔한 이미지의 경우 `SCANS` 를 지정한다.
  - `PANORAMA` 는 영상이 서로 호모그래피 관계에 있다 생각하고 `SCANS` 는 어파인 관계에 있다고 간주한다.

```cpp
#include "opencv2/opencv.hpp"
#include <iostream>

int main(int argc, char *argv[])
{
    if (argc < 3)  // 최소 3장의 이미지가 입력으로 들어오지 않으면 에러 발생
    {
        std::cerr << "Usage: stiching.exe <image_file 1>, <image_file 2>, <image_file 3>, ..." << std::endl;
        return -1;
    }

    std::vector<cv::Mat> imgs;  // 입력 영상을 저장할 벡터 선언
    for (int i = 1; i < argc; i++)
    {
        cv::Mat img = cv::imread(argv[i]);  // 입력 받은 경로의 이미지들을 로드

        if (img.empty())
        {
            std::cerr << "Image load is failed" << std::endl;
            return - 1;
        }

        imgs.push_back(img);
    }

    cv::Ptr<cv::Stitcher> sticher = cv::Stitcher::create();  // 객체 생성

    cv::Mat dst;
    cv::Stitcher::Status status = sticher->stitch(imgs, dst);  // 입력 영상을 받아 이어붙인다

    if (status != cv::Stitcher::Status::OK)  // 실패하면 에러 발생 후 종료
    {
        std::cerr << "Error on Stiching!" << std::endl;
        return -1;
    }

    cv::imwrite("result.jpg", dst);
    cv::imshow("dst", dst);

    cv::waitKey();
    cv::destroyAllWindows();

    return 0;
}
```
