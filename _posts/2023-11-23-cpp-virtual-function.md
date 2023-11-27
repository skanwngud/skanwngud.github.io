---
titme: 가상 함수
auhtor: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 가상 함수의 필요성 (상속관계에서의 오버라이딩)

```cpp
#include <iostream>


class Base
{
    public:
        void BaseFunc() { std::cout << "Base Function" << std::endl; }
};

class Derived : public Base
{
    public:
        void DerivedFunc() { std::cout << "Derived Function" << std::endl; }
};


int main(void)
{
    Base *bptr = new Derived();  // Compile OK
    bptr->DerivedFunc();  // Compiple Error

    return 0;
}
```

- C++ 컴파일러는 포인터 연산의 가능성 여부를 판단할 때, 포인터의 자료형을 기준으로 판단하지 실제 가리키는 객체의 자료형을 기준으로 판단하지 않는다.
- `Dervied` 는 `Base` 의 유도클래스이기 때문에 객체포인터로 `Dervied` 를 가리켜도 `Base` 에 접근 가능하다.
- 그러나 `Base` 형태의 자료형으로 선언한 객체포인터이기 때문에 `Dervied` 내의 멤버함수는 접근할 수 없다.

```cpp
#include <iostream>


class First
{
    public:
        void MyFunc() { std::cout << "FirstFunc" << std::endl; }
};

class Second : public First
{
    public:
        void MyFunc() { std::cout << "SecondFunc" << std::endl; }
};

class Third : public Second
{
    public:
        void MyFunc() { std::cout << "ThirdFunc" << std::endl; }
};


int main(void)
{
    Third *tptr = new Third();
    Second *sptr = tptr;
    First *fptr = sptr;

    fptr->MyFunc();
    sptr->MyFunc();
    tptr->MyFunc();

    delete tprt;

    return 0;
}

// FirstFunc
// SecondFunc
// ThirdFunc
```

- 각 형태에 맞는 객체의 함수들을 호출 (함수의 상속관계에 상관 없다)

## 가상함수 (Virtual Function)

- 함수 앞에 `virtual` 로 선언
- 최상위에서 상속 받는 클래스에서 `virtual` 로 선언하면 상속하는 자식 클래스들도 자동으로 가상함수화 됨
  - 그러나 가독성 등을 위해 상속하는 클래스에서도 `virtual`로 선언하는 것을 권장한다

```cpp
#include <iostream>


class First
{
    public:
        virtual void MyFunc() { std::cout << "FirstFunc" << std::endl; }
};

class Second : public Fisrt
{
    public:
        virtual void MyFunc() { std::cout << "SecondFunc" << std::end; }
};

class Third : public Second
{
    public:
        virtual void MyFunc() { std::cout << "ThirdFunc" << std::end; }
};


int main(void)
{
    Third *tptr = new Third();
    Second *sptr = tptr;
    First *fptr = sptr;

    fptr->MyFunc();
    sptr->MyFunc();
    tptr->MyFunc();

    delete tptr;

    return 0;
}

// ThirdFunc
// ThirdFunc
// ThirdFunc
```

- `virtual` 로 선언하게 되면 가상함수화 된다
- 가상함수화 되면 포인터 변수가 자료형을 따지지 않고 실제 가리키는 객체를 참조하여 호출

### 순수 가상함수와 추상클래스 (abstract class)

- 순수 가상함수는 **몸체가 선언 되지 않은 함수**를 의미한다
- `virtual void Func() = 0;`의 형태로 표현
- 추상 클래스는 하나 이상의 순수 가상함수가 정의 된 클래스를 의미한다
  - 객체생성이 불가능한 클래스이다

### 가상 소멸자 (virtual destructor)

- `virtual`로 선언 된 소멸자를 가상 소멸자라고 한다
- 오버라이딩 된 함수들의 소멸자들도 순서대로 모두 호출 되어야하기 때문에 최상위 클래스의 소멸자에 `virtual`로 선언해준다
