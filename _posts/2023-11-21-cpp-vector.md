---
title: Vector 의 최소, 최대, 평균값 구하기
author: yjh
categoris: [CPP]
tags: [cpp]
---

## 최소값

- `*std::min_element(vector.begin(), vector.end());`

## 최대값

- `*std::max_element(vector.begin(), vector.end());`

## 평균값

- `<numeric>` 라이브러리 추가
- `std::accumulate(vector.begin(), vector.end(), 0.0) / vector.size();`

## 소스코드

```cpp
#include <vector>
#include <numeric>

int main(void)
{
    std::vector<int> vec;  // int vector 선언

    for(int i = 0; i < 5; i++)
    {
        vec.push_back(i);  // 반복문을 거치며 vector 에 push
    }

    int minimum = *std::min_element(vec.begin(), vec.end());  // vector 의 최소값 반환
    int maximum = *std::max_element(vec.begin(), vec.end());  // vector 의 최대값 반환
    double average = std::accumulate(vec.begin(), vec.end(), 0.0) / vec.size();  // vector 의 평균값 반환

    return 0;
}
```

## Reference

- [멈춤보다 천천히라도](https://webnautes.tistory.com/1555)
- [stackoverflow](https://codereview.stackexchange.com/questions/31152/simple-class-exercise-min-max-and-average-of-vector-items)
