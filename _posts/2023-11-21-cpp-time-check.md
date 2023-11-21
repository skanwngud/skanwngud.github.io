---
title: CPP 에서 수행 시간 체크하기
author: yjh
categories: [CPP]
tags: [cpp]
---

## clock 라이브러리 사용

- `clock()` 는 최대 `ms` 단위까지 보여줄 수 있다.
- 반환형은 `clock_t`.
- 현재 시간을 반환할 때는 `clock()` 을 사용한다.
- 수행시간을 체크한뒤, 그 시간을 초로 환산하고 싶다면 `CLOCK_PER_SEC` 으로 나눠주면 된다.

```cpp
#include <iostream>
#include <time.h>

#include "opencv2/core.hpp"
#include "opencv2/imgproc.hpp"


int main(void)
{
    clock_t start, end;
    double duration;

    start = clock();

    cv::Mat img = cv::Mat::zeros(640, 360, CV_8UC3);
    cv::rectangle(img, cv::Point(30, 100), cv::Point(100, 200), cv::Scalar(0, 255, 0), 2);

    end = clock();
    duration = (double)(end - start);

    std::cout << ((duration) / CLOCKS_PER_SEC) << std::endl;

    return 0;
}
```

## time 라이브러리 사용

- `time()` 은 최대 `초단위(s)` 까지 보여줄 수 있다.
- 반환형은 `time_t`.
- 현재 시간을 반환할 땐 `time(NULL);` 을 사용한다.

```cpp
#include <iostream>
#include <time.h>

#include "opencv2/core.hpp"
#include "opencv2/imgproc.hpp"


int main(void)
{
    time_t start, end;
    double duration;

    start = time(NULL);

    cv::Mat img = cv::Mat::zeros(640, 360, CV_8UC3);
    cv::rectangle(img, cv::Point(30, 100), cv::Point(100, 200), cv::Scalar(0, 255, 0), 2);

    end = time(NULL);
    duration = (double)(end - start);

    std::cout << duration << std::endl;

    return 0;
}
```

## Reference

- [펭귄과 컴퓨터](https://scarlettb.tistory.com/5)
