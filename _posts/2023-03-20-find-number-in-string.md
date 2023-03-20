---
title: 숫자와 문자가 혼합 된 문자열에서 숫자만 찾기
author: yjh
math: true
caetegories: [Python, Snippet]
tags: [python, snippet]
---
## Summary
`a_1, 100_a` 등과 같은 문자 + 숫자 조합의 문자열에서 숫자 정보만 반환한다.

## Source Code
```python
import re

int(''.join(re.findall('\d', string)))
```

## Explanation
```python
import re
```

`re` 라이브러리를 임포트해준다.

`re` 라이브러니는 `정규화` 관련 함수들이 들어있는 라이브러리이다.

짧은 한 줄의 코드를 굳이 구분을 짓자면 `re.findall('\d', string)`, `''.join()`, `int()` 이렇게 3개로 나눌 수 있는데,

```python
re.findall('\d', string)
```

이 코드는 `string` 이라는 문자열 안에 있는 숫자 (`'\d'`) 를 전부 찾아내 리스트 형태로 반환한다.

```python
''.join()
```

괄호 안에 있는 `iterable` 한 객체들을 빈칸없이 전부 모아서 합쳐준다.

```python
int()
```

`str` 타입인 숫자만 들어있는 문자열을 `int` 타입으로 바꿔준다.

나는 주로 해당 코드를 `sorted()` 함수와 엮어 문자와 숫자가 결합 된 파일명들의 리스트들을 정렬하는 데에 사용한다.

## Result
```python
import re

test_list = ['a_1', 'a_2', 'a_10', '11_a', '2a_', '100', 'a_200']

sorted_list_1 = sorted(test_list)
sorted_list_2 = sorted(test_list, key=lambda x: int(''.join(re.findall('\d', x))))

print(sorted_list_1)
print(sorted_list_2)

# 결과
# ['100', '11_a', '2a_', 'a_1', 'a_10', 'a_2', 'a_200']
# ['a_1', 'a_2', '2a_', 'a_10', '11_a', '100', 'a_200']
```