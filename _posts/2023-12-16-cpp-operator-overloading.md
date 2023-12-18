---
title: 연산자 오버로딩
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 연산자 오버로딩

- `+, -, *, /, +=, -=, ==, !=, ...` 등 숫자등을 연산하는 연산자 함수의 오버로딩이 가능하다.
- 해당 함수들을 오버로딩하는 것으로 객체간의 오버로딩도 가능하게 된다.

### 연산자 오버로딩의 형태

- `operator` 함수로 오버로딩
  - `operator+`, `operator-` ...

### 멤버변수로만 오버로딩 가능한 연산자

- 연산자 오버로딩은 전역함수, 멤버함수로 오버로딩이 가능한데, 이때 멤버함수로만 오버로딩 할 수 있는 연산자가 존재한다.
  - 객체를 대상으로 진행해야 의미가 있기 때문이다.
  - `=`(대입)
  - `()`(함수 호출)
  - `[]`(배열 접근 (인덱스))
  - `->`(멤버 접근을 위한 포인터)

### 오버로딩 할 수 없는 연산자

- 모든 연산자를 오버로딩할 수 있는 것은 아니다.
  - `.`(멤버 접근), `.*`(멤버 포인터)
  - `::`(범위 지정)
  - `?:`(조건 연산자 (3항 연산자))
  - `sizeof`(바이트 단위 크기 계산), `typeid`(RTTI 관련 연산자)
  - `static_cast`, `dynamic_cast`, `const_cast`, `reinterpret_cast` (형변환 연산자들)

```cpp
#include <iostream>

class Point
{
    private:
        int xpos, ypos;

    public:
        Point(int x = 0, int y = 0)
            : xpos(x), ypos(y)
        { }

        void ShowPosition()
        {
            std::cout << xpos << ", " << ypos << std::endl;
        }

        Point operator+=(const Point &pos)
        {
            xpos += pos.xpos;
            ypos += pos.ypos;

            return *this;
        }

        Point operator-=(const Point &pos)
        {
            xpos -= pos.xpos;
            ypos -= pos.ypos;

            return *this;
        }

        friend Point operator-(const Point &pos1, const Point &pos2);
        friend bool operator==(const Point &pos1, const Point &pos2);
        friend bool operator!=(const Point &pos1, const Point &pos2);
};

Point operator-(const Point &pos1, const Point &pos2)
{
    int x, y;
    x = pos1.xpos - pos2.xpos;
    y = pos1.ypos - pos2.ypos;

    return Point(x, y);
}

bool operator==(const Point &pos1, const Point &pos2)
{
    if (pos1.xpos == pos2.xpos && pos1.ypos == pos2.ypos)
    {
        return true;
    }

    return false;
}

bool operator!=(const Point &pos1, const Point &pos2)
{
    if (!(operator==(pos1, pos2)))
    {
        return true;
    }

    return false;
}

int main(void)
{
    Point pos1(3, 4);
    Point pos2(10, 20);
    Point pos3(3, 4);

    std::cout << (pos1 == pos2) << std::endl;
    std::cout << (pos1 == pos3) << std::endl;
    std::cout << (pos2 == pos3) << std::endl;

    std::cout << (pos1 != pos2) << std::endl;
    std::cout << (pos1 != pos3) << std::endl;
    std::cout << (pos2 != pos3) << std::endl;

    pos1.ShowPosition();

    pos1 += pos2;
    pos1.ShowPosition();

    pos2.ShowPosition();

    pos2 -= pos3;
    pos2.ShowPosition();

    Point pos4 = pos1 - pos2;
    Point pos5 = pos1 - pos3;

    pos4.ShowPosition();
    pos5.ShowPosition();

    return 0;
}
```

- `-, ==, !=, +=, -=` 등을 오버로딩.
- 연산자 함수를 오버로딩함으로써 `Point` 객체간의 연산 및 비교가 가능해졌다.

## 단항 연산자의 오버로딩

- 피연산자가 두 개 이상인 이항 연산자와 피연산자가 한 개인 단항 연산자의 가장 큰 차이점은 피연산자의 개수.
- 매개변수의 개수에서 두 연산자 사이의 차이가 발생한다.

### 증가, 감소 연산자

- 대표적인 단항 연산자.
- 1 증가 연산자인 `++` 와 1 감소 연산자인 `--`.
- 멤버함수로써의 연산자 오버로딩은 위 코드를 기준으로 `pos1.operator++()`
  - 전달할 인자가 없는 이유는 단항 연산자를 오버로딩했기 때문이다.
  - 하나있는 피연산자의 멤버함수를 호출하는 형태이기 때문.
- 전역함수로써의 연산자 오버로딩은 `operator++(pos1)`이 된다.
  - 피연산자가 모두 인자로 전달 되므로 인자로 객체를 전달해줘야한다.

```cpp
// ++, --
#include <iostream>

class Point
{
    private:
        int xpos, ypos;

    public:
        Point(int x = 0, int y = 0)
            : xpos(x), ypos(y)
        {   }

        void ShowPosition() const
        {
            std::cout << "[" << xpos << ", " << ypos << "]" << std::endl;
        }

        Point& operator++()  // 멤버함수이므로 인자 전달 안 함
        {
            xpos += 1;
            ypos += 1;

            return *this;  // 자기 자신을 반환
        }

        friend Point &operator--(Point &ref);  // 전역함수이므로 인자 전달
};

Point& operator--(Point &ref)
{
    ref.xpos -= 1;
    ref.ypos -= 1;

    return ref;
}

int main(void)
{
    Point pos(1, 2);
    ++pos;
    pos.ShowPosition();
    --pos;
    pos.ShowPosition();

    ++(++pos); // ++(pos.operator++) -> (pos의 참조값).operator++(); 의 형태
    pos.ShowPosition();

    --(--pos); // operator-- 연산자는 &ref 를 인자로 받고 참조형으로 다시 반환하므로 operator--(pos의 참조값); 의 형태가 된다.
    pos.ShowPosition();

    return 0;
}
```

```cpp
// 부호연산자 - 를 멤버함수로 오버로딩
// 비트연산자 ~ 를 전역함수로 오버로딩 (xpos엔 y가 ypos엔 x가 들어가는 식으로)

#include <iostream>

class Point
{
    private:
        int xpos, ypos;

    public:
        Point(int x = 0, int y = 0)
            :xpos(x), ypos(y)
        {    }

        void ShowPosition() const
        {
            std::cout << "[" << xpos << ", " << ypos << "]" << std::endl;
        }

        Point operator-()
        {
            Point pos(-xpos, -ypos);

            return pos;
        }

        friend Point operator~(const Point &);
};

Point operator~(const Point &ref)
{
    Point pos(ref.ypos, ref.xpos);

    return pos;
}

int main(void)
{
    Point pos1(3, 4);
    Point pos2 = -pos1;

    pos1.ShowPosition();
    pos2.ShowPosition();

    pos2 = ~pos1;

    pos1.ShowPosition();
    pos2.ShowPosition();

    return 0;
}
```

### 전위 증가와 후의 증가의 구분

- 후위증가의 경우 `pos.operatr++(int)`의 형태로 정의한다.
- `int`는 후위연산을 구분하기 위한 목적일 뿐 데이터를 전달하지는 않는다.
- 연산자 오버로딩을 할 때에 반환형은 `const`로 한다.
  - 이는 임시로 생성하여 반환하는 객체를 `const` 형태로 하겠다는 것.
  - 임시로 생성 되는 객체는 `pos` 객체에서 저장 된 값의 변경은 하지 않겠다는 것이다.
  - `const` 로 선언 된 객체는 `const`로 선언 된 함수만 사용 가능하기 때문.

```cpp
#include <iostream>

class Point
{
    private:
        int xpos, ypos;

    public:
        Point(int x = 0, int y = 0)
            : xpos(x), ypos(y)
        { }

        void ShowPosition() const
        {
            std::cout << xpos << ", " << ypos << std::endl;
        }

        Point& operator++()  // 전위 증가
        {
            xpos += 1;
            ypos += 1;

            return *this;
        }

        const Point operator++(int)  // 후위 증가
        {
            const Point retobj(xpos, ypos); // const Point retobj(*this);
            xpos += 1;  // 위 쪽의 새로운 객체를 만든 뒤 연산
            ypos += 1;

            return retobj;
        }

        friend Point& operator--(Point &ref);  // 전역 함수 전위 감소
        friend const Point operator--(Point &ref, int);  // 전역 함수 후위 감소
};

Point& operator--(Point &ref)
{
    ref.xpos -= 1;
    ref.ypos -= 1;

    return ref;
}

const Point operator--(Point &ref, int)
// 반환형이 const 인 이유는 함수의 반환으로 생성 되는 임시 객체를 const 로 생성하겠단 의미
// 저장 된 값의 변경을 허용하지 않겠다는 의미이며 const 로 선언 되지 않은 함수의 호출은 허용하지 않음
{
    const Point retobj(ref.xpos, ref.ypos);

    ref.xpos -= 1;
    ref.ypos -= 1;

    return retobj;
}

int main(void)
{
    Point pos(3, 5);
    Point cpy;

    cpy = pos--;
    cpy.ShowPosition();
    pos.ShowPosition();

    cpy = pos++;
    cpy.ShowPosition();
    pos.ShowPosition();

    return 0;
}
```
