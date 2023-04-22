---
title: 복사생성자
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 복사생성자
### cpp 의 초기화
```cpp
int num = 5;
int &ref = num;

int num(5);
int &ref(num);
```

- 위 코드의 윗부분과 아랫부분은 결과적으로 동일.

```cpp
class SoSimple
{
    private:
        int num1;
        int num2;
    public:
        SoSimple(int n1, int n2): num1(n1), num2(n2)
        { }
        void ShowSimpleData()
        {
            cout << num1 << endl;
            cout << num2 << endl;
        }
};

int main(void)
{
    SoSimple sim1(15, 20);
    SoSimple sim2 = sim1;

    sim2.ShowSimpleData();
    
    return 0;
}
```

- ```Sosimple sim2=sim1;``` 은 초기화를 연상시킨다.
- 객체 sim2 를 새로 생성하면서 sim1 과 sim2 간의 멤버 대 멤버간의 복사가 일어난다.

### SoSimple sim2(sim1);
- SoSimple 객체를 생성.
- 객체의 이름은 sim2 라고 지정.
- sim1 을 인자로 받을 수 있는 생성자의 호출을 통해 객체생성을 완료.

```cpp
#include <iostream>
using namespace std;

class SoSimple
{
private:
	int num1;
	int num2;
public:
	SoSimple(int n1, int n2)
		: num1(n1), num2(n2)
	{
		// empty
	}

	SoSimple(SoSimple &copy)
		: num1(copy.num1), num2(copy.num2)
	{
		cout << "Called SoSimple(SoSimple &copy)" << endl;
	}
	void ShowSimpleData()
	{
		cout << num1 << endl;
		cout << num2 << endl;
	}
};

int main(void)
{
	SoSimple sim1(15, 30);
	cout << "생성 및 초기화 직전" << endl;
	SoSimple sim2 = sim1;
	cout << "생성 및 초기화 직후" << endl;
	sim2.ShowSimpleData();

	return 0;
}

//생성 및 초기화 직전
//Called SoSimple(SoSimple &copy)
//생성 및 초기화 직후
//15
//30
```
- 해당 코드에서 ```SoSimple(SoSimple &copy) ... ``` 부분이 복사생성자.
- 해당 부분은 ```SoSimple(const SoSimple &copy) ... ``` 로 변경을 해주는 것이 좋다.
    - 복사는 원본을 변경해서는 안 되므로 ```const``` 를 붙여준다.

- 복사생성자에도 디폴트 복사 생성자가 존재하기 때문에 따로 명시해두지 않아도 멤버 대 멤버의 복사를 진행가능하다.

### 복사 생성자의 묵시적 호출을 허용하지 않기.
- ```explicit``` 를 복사생성자 앞에 적어두면 된다.
```cpp
explicit SoSimple(const SoSimple &copy)
    : num1(copy.num1), num2(copy.num2)
{
    // empty
}
```
- ```explicit``` 을 사용하면 대입연산자를 이용한 객체의 생성 및 초기화는 불가능해진다.
- 묵시적 변환이 자주 발생하게 되면 코드의 해석이 힘들어질 수도 있기 때문에 코드의 명확성을 이용해 적절히 사용한다.

```cpp
class AAA
{
    private:
        int num;
    public:
        AAA(int n): num(n) { }
};

...

AAA obj=3;  /// AAA obj(3);


class AAA
{
    private:
        int num;
    public:
        explicit AAA(int n): num(n) { }
};

...

AAA obj(3);  // 대입연산자는 불가
```

## 복사 생성자의 호출 시점
- 기존에 생성 된 객체를 이용해 새로운 개체를 초기화하는 경우.
- Call - By - Value 방식의 함수 호출 과정에서 객체를 인자로 전달하는 경우.
- 객체를 반환하되, 참조형으로 반환하지 않는 경우.