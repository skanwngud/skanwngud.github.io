---
title: 클래스 상속 2
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## Protected
- Private > Protected > Public 순으로 정보은닉성 강화
- 일반적인 상황에선 private 와 protected 모두 클래스 외부에서 접근 불가
- 상속 된 상황이라면 sub class 에서 super class 의 protected 멤버 변수에는 접근 가능
    - 유도 클래스 (Derived class) 에게만 제한적으로 접근 허용

## 세 가지 형태의 상속
### Public
- ```class Derived : public Base```
- public 보다 접근 범위가 넓은 멤버들을 public 으로 변경시켜 상속한다는 의미.
- 하지만 public 이 가장 넓은 범위의 접근을 허용하므로 사실상 private 을 제외한 나머지는 그대로 상속한다.

### Protected
- ```class Dervied : protected Base```
- Protected 보다 접근 범위가 넓은 멤버는 protected 로 변경시켜 상속한다는 의미.
    - Protected 보다 넓은 접근 범위의 멤버는 public 이기 때문에 protected 상속을 한 Derived 클래스의 public 멤버는 protected 로 변경 된다.

```cpp
#include <iostream>
using namespace std;

class Base
{
private:
	int num1;

protected:
	int num2;

public:
	int num3;

	Base() : num1(1), num2(2), num3(3)
	{
		// empty
	}
};

class Derived : protected Base {};  // empty

int main(void)
{
	Derived drv;
	cout << drv.num3 << endl;  // Derived class 에서 public -> protected 가 되므로 외부에서 접근 불가

	return 0;
}
```

### Private
- ```class Derived : private Base```
- protected 와 마찬가지로 private 보다 넓은 접근 범위의 멤버들을 전부 private 으로 변경.
- 하지만 이 경우엔 모든 멤버들이 접근불가가 되기 때문에 사실상 의미 없는 상속이 되어버린다.

<i>cpp 에서는 public 이외의 상속은 다중상속과 같은 경우를 제외하면 잘 없다.</i>