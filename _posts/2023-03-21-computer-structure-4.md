---
title: 컴퓨터 구조 (4)
author: yjh
math: true
categories: [Computer Science]
tags: [cs]
---

## 색을 표현하는 방법
- *R*ed, *G*reen, *B*lue 를 사용하는 (RGB)
- 색은 컬러큐브color cube 라는 이름의 정육면체로 표현할 수 있다.
- (0, 0, 0) 은 검은색, (1, 1, 1) 은 흰색을 표현.
- RGB 은 32비트 (4바이트) 를 사용하는데 4바이트 중 3바이트는 색상을 남은 1바이트는 투명도transparency 를 나타낸다.
- RGBa 는 각 RGB 값에 투명도를 의미하는 알파값 (1 ~ 255) 값을 곱한다.

### 색 인코딩
- 색 인코딩인 16진수를 사용하여 #000000 (검은색) 부터 #FFFFFF (흰색) 까지의 값을 이용한다.
