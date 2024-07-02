---
published : true
layout : post
categories : AI, linear algebra
title : 선형대수 공부 ch.1 벡터
tags : [AI, linear algebra]
date-string : July 02, 2024
---

# 선형대수학 공부 ch.1 벡터
- 자료 : 개발자를 위한 실전 선형대수학(Practical Linear Algebra for Data Science)

## 벡터
- 수를 순서대로 나열한 것
### 벡터의 특징
- 차원 : 벡터의 차원은 벡터가 가진 원소의 수
- 방향 : 벡터의 방향은 벡터가 열 방향 인지 행 방향인지를 나타냄
$$R^N$$
- 벡터의 차원을 나타낼 때 사용
  - R : 실수(C는 복소수)
  - N : 차원
- 원소가 2개인 벡터 : $R^2$

$$X = 
    \begin{pmatrix}
    1 \\
    4 \\
    5 \\
    6 
    \end{pmatrix}
  
  y = 
  \begin{pmatrix}
  .3 \\
  -7 
  \end{pmatrix}
  z = 
  \begin{pmatrix}
  1 & 4 & 5 & 6
  \end{pmatrix}$$

- x는 4차원 열벡터, y는 2차원 열벡터, z는 4차원 행벡터
- $x \in R^4$\,$y \in R^2$
  - 이런 식으로 나타낼 수 있다
- x와 z는 엄밀히 다른 벡터
  - 방향이 다르기 때문

### 파이썬에서의 벡터
- 벡터의 차원은 벡터가 가진 원소의 수
- 파이썬에서 벡터 또는 행렬의 차원 : 객체를 출력하는데 사용되는 기하학적 차원의 수
  - numpy에서 벡터는 2차원 배열로 간주
  - 특정 방향이 없는 수 나열 : 파이썬에서는 1차원 배열
  - 수학적 차원읜 파이썬에서 벡터의 길이 또는 모양

