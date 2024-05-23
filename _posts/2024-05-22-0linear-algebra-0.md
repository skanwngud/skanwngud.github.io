---
title: 선형대수 (1)
author: yjh
math: true
categories: [Math, Linear Algebra]
tags: [math, linear algebra]
---

## 행렬

### 용어정리

- 성분(component): 행렬 안에 배열 된 구성원 (= 항, 원소)
- 행(rows): 행렬의 가로줄
- 열(columns): 행렬의 세로줄
- $m$ x $n$ 행렬: $m$개의 행과 $n$개의 열로 이루어진 행렬
- 주대각선: 행렬의 왼쪽 위에서 오른쪽 아래를 가르는 선

- 대각성분: 주대각선에 걸치는 행과 열의 지표수가 같은 성분

$$A = \begin{pmatrix}
    1 & 2 & 3 \\
    4 & 5 & 6
\end{pmatrix}$$ 인 행렬 $A$가 존재하는 경우, 주대각선의 성분은 $1, 5$이다

- 대각성분을 제외한 다른 성분들이 전부 $0$인 경우 대각행렬이라 부른다
- 영행렬: 모든 성분이 $0$인 행렬
- 전치행렬: 성분 $(a_{ij})$에 대해 $(a_{ji})$ 인 행렬, $A^{T}$로 표기한다
- 대칭행렬: $A\;=A^{T}\;$ 인 행렬 $A$
- 정사각행렬: 행, 열의 개수가 같은 행렬, $m$ x $n$ 인 행렬이 있을 때, $m = n$
- 단위행렬: 모든 대각성분이 $1$이고, 그 외의 성분은 $0$인 정사각행렬

### 행렬의 연산

- 행렬 간 연산에서 덧셈과 뺄셈, 상수배는 교환, 결합 법칙이 성립 가능
- 행렬 간 연산에서 곱셈은 결합법칙만 성립 가능하다
  - 행렬의 곱셈의 경우 두 행렬 $A$, $B$에 대해 $A$ 행렬의 열 갯수와 $B$ 행렬의 행 갯수가 같아야한다

$$A = \begin{pmatrix}
    1 & 0 & 1 \\
    0 & 1 & 0
\end{pmatrix}$$, $$B = \begin{pmatrix}
    1 & 2 \\
    3 & 4 \\
    5 & 6
\end{pmatrix}$$ 이 존재할 때, $AB$의 연산은 $A$ 의 행과 $B$ 의 열 단위로 연산을 진행한다.

$AB = 1\*1 + 0\*3 + 1\*5 + 0\*2 + 1\*4 + 0\*6$

## 행렬식

- 정사각형 행렬 $A$를 하나의 수로써 대응 시키는 특별한 함수 (유의미한 상수)
- $detA = \left\vert A \right\vert$ 로 표현
- $0$ x $0$ -> $det() = 0$
- $1$ x $1$ -> $det(a) = a$
- $2$ x $2$ -> $$det(\begin{pmatrix}
    a & b \\
    c & d
\end{pmatrix}) = ad - bc$$
- $3$ x $3$ -> $$det(\begin{pmatrix}
    a & b & c \\
    d & e & f \\
    g & h & i
\end{pmatrix}) = a\left\vert \begin{matrix}
    e & f \\
    h & i
\end{matrix} \right\vert - b\left\vert \begin{matrix}
    d & f \\
    g & i
\end{matrix} \right\vert + c\left\vert \begin{matrix}
    d & e \\ g & h
\end{matrix} \right\vert$$

### 역행렬

- $A^{-1}$로 표기한다
- 행렬 $A$가 존재할 때, $AA^{-1} = E$ 가 되는 행렬
- 행렬식이 $0$인 경우 역행렬이 존재하지 않는다
$$A^{-1} = \frac{1}{detA}\begin{pmatrix}
    C_{11} & C_{21} & \cdots \\
    C_{12} & C_{22} & \cdots \\
    \vdots & \vdots & \ddots \\
\end{pmatrix}$$ $($ 단, $C_{ij} = (-1)^{i+j}M_{ij}$ $)$ 이다.
