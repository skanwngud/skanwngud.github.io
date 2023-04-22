---
title: 복사생성자 (2)
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

## 복사 생성자 기타
### 메모리 공간의 할당과 초기화가 동시에 일어나는 상황
- ```int num1=num2;``` 의 경우 num1 이라는 이름의 메모리 공간을 할당과 동시에 num2 에 저장 된 값으로 초기화.
```cpp
int SimpleFunc(int n)
{
    ...
}

int main(void)
{
    int num = 10;
    SimpleFunc(num);  // 호출 되는 순간 n 이 할당 됨과 동시에 초기화
}
```

- 위 코드의 경우 매개변수 n 이 할당 됨과 동시에 객체가 초기화 된다.

```cpp
using namespace std;

int SimpleFunc(int n)
{
    ...
    return n;  // 반환하는 순간 메모리 공간이 할당 되면서 동시에 초기화
}

int main(void)
{
    int num = 10;
    cout << SimpleFunc(num) << endl;  // return 값이 별도의 메모리 공간에 저장이 되어있으므로 cout 으로 출력 가능

    return 0;
}
```

- 위 코드의 경우 ```return``` 값이 별도의 메모리 공간에 할당 되고 이 공간에 반환 값이 저장 된다 (반환 값으로 초기화)
- 위 3가지 경우는 객체를 대상으로 할 때에도 마찬가지.
