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
- 원소가 2개인 벡터 : $$R^2$$

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
- $$x \in R^4$$ \, $$y \in R^2$$
  - 이런 식으로 나타낼 수 있다
- x와 z는 엄밀히 다른 벡터
  - 방향이 다르기 때문

### 파이썬에서의 벡터
- 벡터의 차원은 벡터가 가진 원소의 수
- 파이썬에서 벡터 또는 행렬의 차원 : 객체를 출력하는데 사용되는 기하학적 차원의 수
  - numpy에서 벡터는 2차원 배열로 간주
  - 특정 방향이 없는 수 나열 : 파이썬에서는 1차원 배열
  - 수학적 차원읜 파이썬에서 벡터의 길이 또는 모양

```
선형대수학에서 벡터에 아무런 표시가 없는 경우 : 열벡터라고 가정함
행벡터 : $$W^T$$
- T : 전치 연산
```

- 파이썬에서 벡터는 리스트 타입으로 표현 가능
  - 가장 간단함
  - 잘 사용되지 않는 방법
    - 선형대수 연산은 파이썬 리스트에 대해 잘 동작하지 않기 때문

- 넘파이 배열로 생성하는 것이 좋음

#### 파이썬에서 벡터를 생성하는 네 가지 방법
```python
import numpy as np
aslist = [1, 2, 3]
asarray = np.array([1, 2, 3]) #1차원 배열
rowv = np.array([[1, 2, 3]]) #행
colv = np.array([[1], [2], [3]]) #열
```

- asarray : 단순 1차원 넘파이배열
- rowv : 행방향 벡터
- colv : 열방향 벡터

## 벡터의 기하학적 해석
- 벡터의 대수학적 해석 : 순서대로 나열된 수 목록
- 벡터의 기하학적 해석 : 특정 길이(크기)와 방향(각도)을 가진 직선
  - 벡터의 두 점 : 꼬리(시작하는 곳), 머리(끝나는 곳)이라 부름
  - 머리는 화살표가 달려 있다
- 기준 위치 : 원점에서 시작한 벡터

## 벡터 연산
### 덧셈
- 두 벡터의 덧셈은 서로 대응되는 원소끼리 더함
$$\begin {pmatrix}  
  4 \\               
  5 \\              
  6                  
  \end {pmatrix} +\begin{pmatrix}
                   10 \\
                   20 \\
                   30   
                   \end {pmatrix} = \begin {pmatrix}
                                      14 \\
                                      25 \\
                                      36
                                      \end {pmatrix}$$

- 벡터의 덧셈은 동일한 차원을 갖는 벡터끼리만 가능
- 뺄셈도 같은 원리

```python
v = np.array([4, 5, 6])
w = np.array([10, 20, 30])
u = np.array([0, 3, 6, 9])

vw = v + w
uw = u + w #이 코드는 오류 발생. 차원이 다르기 때문
```

- 방향이 다른 경우

$$\begin {pmatrix}
  4 \\
  5 \\
  6
  \end {pmatrix} + \begin {pmatrix}
                    10 & 20 & 30
                    \end {pmatrix}$$

```python
rv = np.array([[4], [5], [6]])
cv = np.array([[10, 20, 30]])
rvcv = rv + cv
print(rvcv)
```

![코드실행결과](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/73f3cd71-05d0-4ce4-a19a-5224de038442)

- 벡터 방향이 다른 경우 브로드캐스팅 연산 수행
- 일반적인 덧셈 연산과는 다름
- 벡터 방향은 중요함

### 벡터의 덧셈과 뺄셈의 기하학적 해석
![vector_plus](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/2b5465c9-8003-4b40-b439-f3414eaceaef)

![vector_plus2](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/142f7da7-07f0-4819-a2d1-464bfcb7749b)

![vector_minus](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/3808c437-14e5-4c2f-9b07-0e101e06fc6c)

- 두 벡터를 기하학적으로 더할 때 한 벡터의 꼬리와 다른 벡터의 머리를 연결
- 더한 결과 : 첫 번째 벡터의 고리와 두 번째 벡터의 머리를 이은 선
- 벡터의 뺄셈
  - 벡터의 꼬리들을 같은 좌표에 둠
  - 뺀 결과의 벡터는 두 번째 벡터의 머리에서 첫 번째 벡터의 머리로 가는 선
  - 직교벡터 분해의 기초

### 스칼라-벡터 곱셈
- 스칼라 : 벡터나 행렬에 포함된 숫자가 아닌 수 그 자체
- $$\alpha, \lambda $$ 와 같은그리스어 소문자로 나타냄
- 스칼라 벡터 곱셈
$$\lambda = 4 \, , W = \begin {pmatrix}
                        9 \\
                        4 \\
                        1
                        \end {pmatrix}
  \lambda W = \begin {pmatrix}
              36 \\
              16 \\
              4
              \end {pmatrix} $$

```
영벡터 : 모든 원소가 0. 볼드체 0으로 표시
선형대수학에서 특수한 벡터
영벡터를 사용해 문제를 푸는 건 '자명한 해'라 제외함
```

- 스칼라-벡터 곱에선 데이터타입이 중요함

```python
s = 2
a = [3, 4, 5]
b = np.array(a)
print(a*s)
print(b*s)
```
![scalarXvector](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/ab485e6e-b027-410e-a589-07a24b7fc153)

- 리스트에 스칼라를 곱하면 리스트를 스칼라 만큼 반복
- 넘파이 배열이라면 원소별로 곱셈

### 스칼라-벡터 덧셈
- 선형대수학에선 불가능
- 파이썬에선 가능
  - 스칼라-벡터 곱셈과 유사함

```python
s = 2
v = np.array([3, 6])
print(s + v)
```
![scalar+vector](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/0d6fd8d7-b9b8-4c67-b594-4d485111cd14)

### 스칼라-벡터 곱셈의 기하학적 해석

![scalar-vector](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/a04ba29a-afb3-42dd-884e-fc73733711c0)

![scaler-vector(neg)](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/7944b09a-e4e7-4aab-8f13-96fd26dd0eca)

- 스칼라가 0보다 작으면 방향이 바뀜
  - 벡터는 원점을 통과해서 양방향의 무한대로 가는 무한히 긴 선을 가리킨다는 해석도 있음
  - 이 의미에서 회전된 벡터는 여전히 동일한 무한한선
    - 음의 스칼라가 방향을 바꾼 것이 아님
- 벡터 덧셈과 스칼라-벡터 곱셈을 이용해 벡터의 평균 구하기 가능

```python
s = np.array([1, 4, 5, 6])
v = np.array([2, 5, 10, 9])
sv = s + v
avgsv = sv * (1/2)
print(avgsv)
```

![avgvector](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/a7182bec-fecd-48be-bf9e-84552344bacf)

### 전치
- 열벡터를 행벡터로 또는 그 반대로 변환

$$m_{i, j}^T = m_{j, i}$$

$$v^{TT} = v$$

### 파이썬에서 벡터 브로드캐스팅
- 브로드캐스팅 연산은 현대 컴퓨터 기반 선형대수학에서만 존재
- 브로드캐스팅 : 한 벡터를 다른 벡터의 각 원소로 연산을 여러 번 반복하는 것

---

$$\begin {pmatrix}
  1 & 1
  \end {pmatrix} + 
  \begin {pmatrix}
  10 & 20
  \end {pmatrix}\\

  \begin {pmatrix}
  2 & 2
  \end {pmatrix} + 
  \begin {pmatrix}
  10 & 20
  \end {pmatrix}\\
  \begin {pmatrix}
  3 & 3
  \end {pmatrix} + 
  \begin{pmatrix}
  10 & 20
  \end {pmatrix}$$

- 이 식의 경우 벡터 [1, 2, 3]의 전치와 [10, 20]의 패턴을 모은 다음 덧셈을 브로드캐스팅하면 하나의 식으로 간결하게 구현 가능

```python
s = np.array([[1, 2, 3]]).T
v = np.array([[10, 20]])
print(s + v)
```
![vector_broadcasting](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/24ba5807-268f-4dc8-8579-cd8e2377cdf8)

- 만약 v가 열벡터고 s가 행벡터면 3X2행렬이 아닌 2X3행렬이 나옴

## 벡터 크기와 단위벡터
- 벡터의 크기(기하학적 길이 or 노름(norm)) : 벡터의 꼬리부터 머리까지의 거리
- 표준 유클리드 거리 공식으로 구함
$$\parallel v \parallel = \sqrt{\sum_{i = 1}^n v_i^2}$$

- 노름은 벡터 양 옆에 이중 수직 막대로 표시
- 단위벡터 : 기하학적 길이(크기)가 1인 벡터
  - 여러 응용 분야에서 사용
    - 직교행렬, 회전행렬, 고유벡터, 특이벡터
  - $$\parallel v \parallel = 1$$
    - 단위벡터 정의
- 

#### 이론에서의 선형대수학과 코드에서 선형대수학 사이 용어 차이
- 수학 이론에서
  - 벡터의 차원 : 벡터의 원소 수
  - 길이 : 기하학적 거리
- 코드에서
  - 파이썬 len() : 배열의 차원 반환
  - np.norm() : 기하학적 길이(크기) 반환

```python
v = np.array([1, 2, 3, 7, 8, 9])
v_dim = len(v)
v_mag = np.linalg.norm(v)
```
![diff](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/05d35bd5-865b-4227-b501-0054f7b3e3bf)

## 벡터-내적
- 표기방법
$$a^Ta$$
  - 일반적인 표기법
- 다른 방법
$$a \cdot b$$
$$<a, b>$$

- 내적은 하나의 숫자로 두 벡터 사이의 관계를 나타냄
- 계산 방법
  - 두 벡터에서 대응되는 원소끼리 곱한 후 모든 결과를 더함

$$\delta = \sum_{i = 1}^n a_ib_i$$

- 동일한 차원의 두 벡터 사이에서만 성립

- 파이썬에서 내적 구현

```python
v = np.array([1, 2, 3, 4])
w = np.array([5, 6, 7, 8])
np.dot(v, w)
```
<img width="175" alt="npdot" src="https://github.com/user-attachments/assets/d54f68d9-e4df-4914-8260-3d9292c125be">

- 벡터에 스칼라를 곱하면 내적도 그만큼 커짐
<img width="107" alt="scalarXvectordot" src="https://github.com/user-attachments/assets/d840e9aa-f880-4f57-9bec-9e3c00ec979d">

$$\sigma v^T w$$

### 내적의 의미
- 두 벡터 사이의 유사성 또는 매핑
  - 서로 관련이 클 수록(유사성이 클 수록) 내적은 커짐
  - 피어슨 상관계수 : 두 변수 사이의 정규화된 내적

### 내적의 분배 법칙
- 내적에 분배 법칙 적용 가능
$$a^T(b+c) = a^Tb + a^Tc$$

- 위의 식을 파이썬 코드로 구현

```python
a = np.array([0, 1, 2])
b = np.array([3, 5, 8])
c = np.array([13, 21, 34])

res1 = np.dot(a, b+c)
res2 = np.dot(a, b) + np.dot(a, c)
```

<img width="107" alt="dotplus" src="https://github.com/user-attachments/assets/ed8de754-259b-46fd-9bc2-04713561557f">

