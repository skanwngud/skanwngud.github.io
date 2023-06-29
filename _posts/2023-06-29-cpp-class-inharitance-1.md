---
title: 클래스 상속 1
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 클래스 상속
- cpp 의 상속 예제는 ```class SubClass : public SuperClass``` 의 형식
- 상속한 클래스는 상속 된 클래스의 함수나 변수등에 접근 가능 (only public)
    - private 은 클래스 외부에서의 접근을 막는 것이므로 비록 상속이 되었을지라도 해당 클래스의 외부에서 접근하는 셈이기 때문
    - 아래 예제에서 UnivStudent 는 Person 의 age, name 에 바로 접근 불가능

|Person|UnivStudent|
|--|--|
|상위|하위|
|기초 (base)|유도 (derived)|
|슈퍼 (super)|서브 (sub)|
|부모|자식|

```cpp
#include <iostream>
#include <cstring>

class Person  // 상속 할 super class 정의
{
    private:
        int age;
        char name[50];

    public:
        Person(int myage, const char * myname)
            : age(myage)
        {
            strcpy(name, myname);
        }

        void WhatYourName() const
        {
            std::cout << "My name is " << name << std::endl;
        }

        void HowOldAreYou() const
        {
            std::cout << "I'm " << age << " years old" << std::endl;
        }
};

class UnivStudent : public Person // Person 이란 클래스를 Univstudent 가 public 으로 상속
{
    private:
        char major[50];

    public:
        UnivStudent(const char * myname, int myage, const char * mymajor)  // 생성자
            : Person(myage, myname)  // 상속 한 클래스 초기화
        {
            stcpy(major, mymagor);
        }

        void WhoAreYou() const
        {
            WahtYourName();  // 상속 한 클래스의 함수를 사용 가능
            HowOldAreYou();
            std::cout << "My major is " << major << std::endl;
        }
};

int main(void)
{
    UnivStudent std1("name1", 10, "major1");
    std1.WhoAreYou();

    UnivStudent std2("name2", 11, "major2");
    std2.WhoAreYou();

    return 0;
};

// MY name is name1
// I'm 10 years old
// My major is major1

// My name is name2
// I'm 11 years old
// My major is major2
```

## 상속 클래스 생성자
- 상속 클래스의 생성은 super 클래스의 생성자가 먼저 생성 되고 그 이후 sub 클래스의 생성자가 생성 된다.

## 상속 클래스 소멸자
- 상속 클래스의 소멸은 생성과 반대
- 동일한 구조의 클래스 c1 (super, sub), c2 (super, sub) 가 있을 때
    - c1 super init -> c2 super init -> c1 sub init -> c2 suv init -> c2 sub dest -> c1 sub dest -> c2 super dest -> c1 super dest 순서

## 번외.
### ```strcpy``` 를 사용 할 때 ```new``` 와 ```delete``` 를 사용할 때와 사용하지 않을 때의 차이
- 동적할당을 언제 사용하느냐에 대한 질문.
- ```char name[50];``` 과 ```char * name;``` 의 경우처럼 앞의 경우에는 동적할당이 아니기 때문에 ```new``` 와 ```delete``` 를 사용하지 않으며, 따라서 뒤의 경우에는 함수나 클래스 따위에서 인자를 받아 동적할당을 한다.
```cpp
char name[50];
strcpy(name, myname);

char * name;
name = new char[strlen(myname) + 1];
strcpy(name, myname);
delete[]name;
```