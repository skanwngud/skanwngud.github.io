---
title: 선형대수 (2)
author: yjh
math: true
categories: [Math, Linear Algebra]
tags: [math, linear algebra]
---

## 벡터와 좌표계

### 평면벡터

- $R^{2}$에서 크기 (스칼라, 변화량) 와 방향의 의미를 모두 포함하는 표현도구
- 평면에서 특정 벡터가 존재할 때, 벡터의 시점과 종점은 중요하지 않다
- 원점 $O$와 점 $A(a_{1}, a_{2})$, $B(b_{1}, b_{2})$가 있을 때, 원점에서 시작하는 벡터 $\vec{OA}$ 와 $\vec{OB}$ 로 표현한다

### 공간벡터

- $R^{3}$에서 크기와 방향의 의미를 모두 포함하는 표현도구
- 평면 ($x, y$) 에서 $z$ 성분이 추가 되었다고 생각하면 편하다

### n차원 벡터

- $R^{n}$ 상의 벡터 $v = (v_1, v_2, \cdots, v_n) = \vec{AB} = (b_1 - a_1, b_2 - a_2, \cdots, b_n - a_n)$
- 영벡터: $0$, $O$, $\vec{O}$ 로 표현하며 모든 성분의 값이 $0$인 벡터를 의미
  - $v = (0, 0, \cdots, 0)$
- 두 벡터간 동일한 위치의 성분들의 값이 일차한다면 두 벡터는 같다고 표현한다
  - $v = (v_1, v_2, \cdots, v_n), w = (w_1, w_2, \cdots, w_n) \rightarrow v_1 = w_1, v_2 = w_2, \cdots, v_n = w_n$

## 벡터의 연산

- 벡터 간 덧셈과 뺄셈은 각 성분간의 연산으로 이루어진다
  - $\vec{v}$와 $\vec{w}$가 있을 때, $v + w = (v_1 + w_1, v_2 + w_2, \cdots, v_n + w_n), v - w = (v_1 - w_1, v_2 - w_2, \cdots, v_n - w_n)$
- 벡터의 실수배는 각 성분에 실수를 연산시키면 된다

### 노름 (Norm)

- 벡터의 크기 혹은 길이라고 하지만, n차원으로 확장되면서 단순 길이로 표현하기는 힘들다
- $\lVert v \rVert = \sqrt{v_1^2, v_2^2, \cdots, v_n^2}$
- 노름이 $1$인 벡터를 단위벡터라고 하며, 어떤 벡터를 단위벡터로 만드는 행위를 **정규화**라고 한다
  - $\frac{v}{\lVert v \rVert} = \hat{v}$
- $e_1 = (1, 0, 0, \cdots, 0), e_2 = (0, 1, 0, \cdots, 0)$ 등을 표준단위벡터라고 말한다
  - 벡터 $\vec{v}$가 있을 때, $\vec{v} = (v_1e_1 + v_2e_2 + \cdots + v_ne_n) = (v_1, 0, 0, \cdots, 0), (0, v_2, 0, \cdots, 0), \cdots, (0, 0, 0, \cdots, v_n)$으로 표현 가능하다

### 선형결합 (Linear Combination)

- $R^n$의 벡터 $w$가 임의의 실수 $k_1, k_2, \cdots, k_n$ 에 대하여 $w = k_1v_1 + k_2v_2 + \cdots + k_nv_n$ 의 형태로 쓰여진다면 $w$ 를 $v_1, v_2, \cdots, v_n$ 의 선형 결합이라고 한다

### 스칼라곱

- 두 벡터간의 곱셈 중 크기에 대해서만 연산한다
- 한 벡터가 다른 벡터의 방향에 대해 가한 힘에 의해 변화 된 스칼라 혹은 크기를 의미한다
- 점곱 (dot product), 내적 (inner product) 라고 한다
- $v \cdot w = \lVert v \rVert \lVert w \rVert \cos\theta = v_1w_1 + v_2w_2 + \cdots + v_nw_n$ ($\theta$는 $v, w$의 끼인각이다)

### 벡터곱

- $R^3$ 상에서 방향은 두 벡터에 동시에 수직이고 크기는 두 벡터의 평행사변형 면적인 벡터
- 가위곱 (cross product) 라고 한다
- $$v \times w = \begin{pmatrix}
    \left\vert \begin{matrix}
        v_2 & v_3 \\
        w_2 & w_3
    \end{matrix} \right\vert, -\left\vert\begin{matrix}
        v_1 & v_3 \\
        w_1 & w_3
    \end{matrix}\right\vert, \left\vert\begin{matrix}
        v_1 & v_2 \\
        w_1 & w_2
    \end{matrix}\right\vert\end{pmatrix}$$ 이며, 두 벡터를 하나의 행렬로 만든 후 행렬식으로 연산할 수도 있다
