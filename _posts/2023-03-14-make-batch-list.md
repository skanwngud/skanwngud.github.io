---
title: 배치리스트 만들기
author: yjh
categories: [Python, Snippet]
tags: [python, snippet]
---
## Summary
파일명이나 이미지 등이 들어가있는 리스트와 배치 사이즈를 입력 받아 그 리스트를 배치 단위로 잘라주는 함수이다.

## Source Code
```python
def make_batch_list(self, file_list: List[], batch_size: int) -> List[]:
    result_list = []

    for idx in range(0, len(file_list), batch_size):
        if idx + batch_size < len(file_list):
            result_list.append(file_list[idx:idx+batch_size])
        else:
            result_list.append(file_list[idx:])

    return result_list
```

## Explenation
```python
def make_batch_list(self, file_list: List[], batch_size: int) -> List[]:
```

`input data` 로 잘라 줄 대상이 될 리스트와 잘라 줄 단위인 배치 사이즈를 지정한다.


```python
result_list = []
```

배치 단위로 잘린 결과를 담을 리스트를 선언한다.


```python
for idx in range(0, len(file_list), batch_size):
```

배치 사이즈 단위로 반복문을 수행한다.

(`for in range(a, b, c)` 에서 a 는 start, b 는 end, c 는 step 을 뜻한다.)


```python
if idx + batch_size < len(file_list):
    result_list.append(file_list[idx:idx+batch_size])
```

현재 파일 위치 + 배치 사이즈의 크기가 리스트의 최대 길이를 넘지 않는다면 그 범위만큼 잘라 추가해준다.


```python
else:  # == idx + batch_size > len(file_list)
    result_list.append(file_list[idx:])
```

반대로 현재 파일 위치 + 배치 사이즈의 크기가 최대 길이를 넘었다면 현재 파일 위치에서 리스트의 끝까지 추가한다.


```python
return result_list
```

모든 리스트를 순회해서 배치 단위로 잘라준 리스트를 반환한다.

## Result
```python
test_list = [i for i in range(10)]

batch_list = make_batch_list(test_list, 3)
batch_list2 = make_batch_list(test_list, 4)

print(batch_list)
print(batch_list2)

# 결과
# [[0, 1, 2], [3, 4, 5], [6, 7, 8], [9]]
# [[0, 1, 2, 3], [4, 5, 6, 7], [8, 9]]
```
