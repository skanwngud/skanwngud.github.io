---
title: bool 출력 시 true, false 로 출력하기
author: yjh
math: true
categories: [CPP]
tags: [cpp]
---

`bool a = true;` 를 바로 출력하는 경우 `0`이나 `1`처럼 숫자로 표현 되는 경우가 있었다.

`std::cout` 을 할 때, 출력하려는 변수의 앞 뒤로 `std::boolalpha` 를 추가해주면 `true`나 `false`로 출력이 된다.

```cpp

...
bool a = true;

std::cout << std::boolalpha << a << std::boolalpha << std::endl;
...

```

## Reference

[JArchive 프로그래밍 일기](https://gdlovehush.tistory.com/231)
