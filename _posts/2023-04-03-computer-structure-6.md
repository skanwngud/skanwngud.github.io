---
title: 컴퓨터 구조 (6)
author: yjh
math: true
categories: [Computer Science]
tags: [cs]
---

## 전역 변수와 지역 변수
### 전역변수
- 함수 외부에서 선언한 변수
- 함수 내부에서도 접근 가능
    - 변수 호출은 가능하나 변수를 수정하기 위해선 ```global``` 이란 명령어가 필요

```python
global_var = 10

def func():
    global global_var
    global_var = 20
    
    return global_var

print(global_var)

# 20
```

### 지역변수
- 특정 지역에서만 접근 가능
    - 특정 지역; 함수 내부, 반복문 내부, 조건문 내부 등
- 지역변수는 함수의 호출이 끝나거나 반복문이나 조건문이 종료 되는 순간 같이 사라진다


## 값에 의한 전달, 참조에 의한 전달
### 값에 의한 전달 (call by value)
- 변수가 값에 의해 전달 되었으므로 함수 내부에서 변경 되지 않음.

```cpp
#include <iostream>
using namespace std;

void change_val(int x, int value)
{
    x = value;
    cout << "x: " << x << "in change_value" << endl;
}

int main(void)
{
    int x = 30;

    change_value(x, 20);
    cout << "x: " << x << "in main" << endl;
    return 0;
}
```
- 값에 의한 전달은 변수의 값만 복사해오는 것이기 때문에 스택 프레임 구조상 함수의 호출이 끝나면 그대로 사라져버린다.
- `main` 함수에서 선언 된 `x` 의 값은 `change_val` 함수에 값만 복사가 되어 들어가게 되므로 `change_val` 함수 내부에서 변경 된 값은 함수의 호출이 끝남과 동시에 사라진다.


### 참조에 의한 전달 (call by reference)
- 변수가 값이 아닌 참조에 의해 전달 되었으므로 함수 내부에서 변경 가능.

```cpp
#include <iostream>
using namespace std;

void change_val(int *x, int value)
{
    *x = value;
    cout << "x: " << x << "in change_val" << endl;
}

int main(void)
{
    int x = 10;
    change_val(&x, 20);

    cout << "x: " << x << "in main" << endl;
    return 0;
}
```
- 참조에 의한 전달은 변수의 메모리 주소를 전달하기 때문에 함수 내부에서도 변경이 가능하다.
- `main` 함수에서 선언 된 `x` 는 `change_val` 이란 함수에 포인터 형태로 전달 되었으므로 값 자체가 아닌 변수 자체의 메모리 주소가 전달 되었다.
- 즉, 스택 프레임에서 `change_val` 의 호출이 끝나도 이미 함수 내에서 `x` 의 메모리 주소의 값 자체를 바꿨기 때문에 변경사항이 유지 된다.

## 객체 참조에 의한 전달 (call by object reference)
- 파이썬은 c계열 언어와 다르게 변수를 메모리 공간에 직접 값을 저장하는 것이 아닌 값 객체를 가르킨다.
```python
def change_val(x, value):
    x = value
    print(f"x: {x} in change_val)

if __name__ == "__main__":
    x = 10
    change_val(x, 20)
    print(f"x: {x} in main")
```

- 함수 내부에서만 `x` 의 값이 바뀌고 `main` 에서는 `x`의 값이 변경 되지 않는다.

## 람다 함수
- 이름이 없는 함수.
- 다음 행으로 넘어가면 다시 사용할 수 없다.
- 람다 함수에는 `return` 과 같은 반환이 없으며 몸체에는 반드시 식이 들어가야한다.