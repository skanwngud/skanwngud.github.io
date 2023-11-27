---
titme: OpenCV 클래스
auhtor: yjh
math: true
categories: [CPP, OpenCV]
tags: [cpp, opencv]
---

## Point\_ 클래스

- 2차원 평면 위에 있는 점의 좌표를 표현하는 템플릿 클래스
- x, y 라는 이름의 멤버 변수를 갖고 있음
- 실제로 어떤 자료형태로 사용할지 정의를 해주어야한다
  - `Point2i` (int 형), `Point2f` (float 형) 등이 존재하며 제일 많이 쓰이는 `int` 형을 `Point` 클래스로 오버라이딩 함
- (0, 0) 으로 초기화

```cpp
#include "opencv2/opencv.hpp"

int main(void){

    cv::Point pt1;          // pt1 = (0, 0)
    pt1.x = 5; pt1.y = 10;  // pt1 = (5, 10)

    cv::Point pt2(10, 20);  // pt2 = (10, 20)

    return 0;
}
```

- 좌표에 대한 다양한 연산 가능
  - Point* 클래스 간 연산, Point* 클래스와 상수 간의 사칙연산, 행렬 연산, `bool` 연산 등

```cpp
cv::Point pt3 = pt1 + pt2;  // pt3 = (15, 30)
cv::Point pt4 = pt2 * 2;    // pt4 = (20, 40)

int d1 = pt1.dot(pt2);      // 250
bool b1 = (pt1 == pt2);     // false
```

## Size\_ 클래스

- 영상이나 사각형 영역의 크기를 표현하는 템플릿 클래스
- width, height 라는 이름의 멤버 변수를 갖고 있음
- `Point_ 클래스` 와 마찬가지로 자료형을 정의해주어야하며 가장 많이 쓰이는 `int` 형을 `Size` 클래스로 오버라이딩
- (0, 0) 으로 초기화

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    cv::Size sz1;                    // sz1 = (0, 0)
    sz1.width = 5; sz1.height = 10;  // sz1 = (5, 10)

    cv::Size sz2(10, 20);            // sz2 = (10, 20)

    return 0;
}
```

- size 에 대한 다양한 연산 가능
  - *영역*을 의미하는 `area` 멤버 함수를 가짐

```cpp
int area = sz1.area();  // area = 5 * 10 = 50
```

## Rect\_ 클래스

- 사각형의 위치와 크기 정보를 표현하는 템플릿 클래스
- x, y, width, height 라는 이름의 멤버 변수를 갖고 있음
  - x, y 는 사각형의 왼쪽 상단의 지점 (시작 부분)
  - width, height 는 x, y 로부터 시작 된 크기 정보
- (0, 0, 0, 0) 으로 초기화
- 마찬가지로 `int` 형을 `Rect` 클래스로 오버라이딩

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    Rect rc1;                  // (0, 0) 으로 시작하는 (0, 0) 크기의 사각형 (유효하지 않음)
    Rect rc2(10, 10, 60, 40);  // (10, 10) 으로 시작하는 (60, 40) 크기의 사각형
    return 0;
}
```

- `Rect_` 클래스의 연산은 `Size_` 와 `Point_` 클래스로 연산이 가능하다
  - `Point_` 로는 `x`, `y`
  - `Size_` 로는 `width`, `height`

```cpp
Rect rc3 = rc1 + Size(50, 40);   // (0, 0) 으로 시작하는 (50, 40) 크기의 사각형
Rect rc4 = rc2 + Point(10, 10);  // (20, 20) 으로 시작하는 (60, 40) 크기의 사각형
```

- `Rect_` 클래스는 `|` 와 `&` 로 논리연산도 수행 가능
  - `|` 의 경우 두 사각형이 서로 교차하는 부분
  - `&` 의 경우 두 사각형을 포함하는 가장 작은 사각형

## RotatedRect 클래스

- 사각형의 위치, 크기 정보와 회전각도를 표현하는 _클래스_
- center, size, angle 이라는 이름의 멤버 변수를 갖고 있음
  - center(x, y), size(widht, height), angle
- 모든 정보를 `float` 를 사용하여 표현
  - `Point2f`, `Size2f`

```cpp
#include "opencv2/opencv.hpp"

int main(void)
{
    RotatedRect rr1(Point2f(40, 30), Size2f(40, 20), 30.f);

    return 0;
}

```

- `RotatedRect` 의 네 꼭지점을 얻고 싶다면 `RoatedRect::points()` 함수 사용
- `RotatedRect` 를 포함하는 최소 크기의 사각형의 좌표는 `RotatedRect::boundingRect()` 함수 사용

```cpp
cv::Point2f pts[4];
rr1.points(pts);

for (int i = 0; i < 4; i++)
{
    std::cout << pts[i] << std::endl;
}

```
