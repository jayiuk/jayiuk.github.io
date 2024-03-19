---
layout : post
categories : python, project
title : 파이썬 표준 라이브러리
tags : [python, project]
date-string : March 12, 2024
---


# 파이썬 표준 라이브러리

## encoding, decoding
- encoding : 코드화 = 암호화 = 부호화
- decoding : 역코드화 = 복호화
```python
a = "Hello"
type(a)
pring(a)
b = a.encode('utf-8')
type(b)
print(b)
```
```python
a = '한글'
b = a.encode('utf-8')
print(b)
b.decode('utf-8')
```
 - utf로 인코딩, 아스키로 디코딩은 안됨
 - 기호, 이모지 출력 가능
```python
print("Learning : ", "\U0001F40D)
```

```python
 #-*- coding: ascii -*-
 #-*- coding: utf-8 -*-
```
 - 어떤 방식으로 인코딩 되는지 알려주는것
## 클로저
- 함수 안의 함수를 결과로 반환할 때 그 내부함수를 클로저라고 함
- 콜백함수, 함수의 순차적 실행, 데코레이터
```python
def mul3(n):
    return n*3
mul3(3)
def mul5(n):
    return n*5
mul5(3)
class Mul:
    def __init__(self, m):
        self.m = m
    def mul(self, n):
        return self.m*n
if __name__ = "__main__":
    multi3 = Mul(3)
    multi5 = Mul(5)
class Mul:
    def __init__(self, m):
        self.m = m
    def __call__(self, n):
        return self.m*n
if __name__ = "__main__":
    mul3 = Mul(3)
    mul5 = Mul(5)

def mul(m):
    def wrapper(n):
        return m*n
    return wrapper
if __name__ == "__main__":
    mul3 = mul(3)
    mul5 = mul(5)
    print(mul3(10))
    print(mul5(10))
```

## 데코레이터
- 함수를 꾸며주는 함수
- @를 이용한 어노테이션
- 반복되는 작업을 여러 함수에 적용
- 기존 함수를 수정하지 않고 추가 기능 구현

```python
import time
def fun1(a, b):
    start = time.time()
    print("함수가 실행")
    val = a + b
    end = time.time()
    print("함수 실행 시간 :  %f초"%(end-start))
    return val
if __name__ = "__main__":
    result = fun1(1, 2)
    print(result)
def fun2(a, b):
    start = time.time()
    print("함수가 실행")
    val = a * b
    end = time.time()
    print("함수 실행 시간 :  %f초"%(end-start))
    return val

def fun1(a, b):
    val = a + b
    return val
def fun2(a, b):
    val = a * b
    return val

def elapsed(func):
    def wrapper(a, b):
        print('start')
        start = time.time()
        result = func(a, b)
        end = time.time()
        print("함수 실행 시간 :  %f초"%(end-start))
        return result
    return wrapper
if __name__ == "__main__":
    deco1 = elapsed(fun1)
    result = deco1(1, 2)

@elapsed
def fun1(a, b):
    val = a + b
    return val
@elapsed
def fun2(a, b):
    val = a * b
    return val
```

## 이터레이터
- 어떤 집합에서 값을 차례대로 꺼낼 수 있는 객체
- for문을 순회할 수 있는 객체

```python
for a in [1, 2, 3]:
    print(a)
a = [1, 2, 3]
iterator = iter(a)
type(iterator)
next(iterator)
next(iterator)
next(iterator)
next(iterator)#이건 에러. 꺼낼 수 있는 객체 없음
for a in iterator:#앞에 넥스트를 실행한 후에 실행하면 출력X 이터레이터는 한바퀴만 돔
    print(a)
```
## 제너레이터
- 이터레이터를 생성해주는 함수
- yield

```python
def generator():
    yield 'a'
    yield 'b'
    yield 'c'
g = generator()
type(g)
next(g)
next(g)
next(g)
next(g) #에러
def client_count(total_client):
    n = 1
    for num in range(total_client):
        print(f'{n}번째 고객님 입장하세요')
        n += 1
        yield
mygen = client_count(100)
next(mygen)
next(mygen)
```
## 변수 타입 어노테이션
- 파이썬은 int a = 1; 이런 식으로 안짜도 됨
```python
a = int(1)
type(a)
a = str("1")
type(a)
```
- 파이썬에서 타입 어노테이션 하는 방법
```python
a: int = 10
a = 10
```
- 파이썬은 동적 프로그래밍 언어
- 실행 도중 변수 타입을 동적으로 변경 가능
- 자바, C: 정적 프로그래밍 언어-> 컴파일 시 변수 타입이 결정
- 단점1 : 프로젝트가 커지면 해석이 어려워짐
- 단점2 : 느림
```python
def add(a: int, b: int) -> int:
    return a + b
add(1, 2)
add("1", "2") #이래도 에러가 안난다. 대신 결과가 문자열로 나온다
add.__annotations__ #어노테이션 확인
```
## str()과 repr()함수
 - str()과 repr()은 모두 객체를 문자열로 만환하는 함수
 - str은 비공식적인 문자열을 출력
 - repr은 공식적인 문자열을 출력(시스템에서 인식 가능)
 - str은 사용자가 보기 쉽도록, repr은 문자열로 다시 생성
```python
a = 123
str(a)
a = 123
repr(a)

a = "Hello World"
str(a)
a = "Hello World"
repr(a)

import datetime
a = datetime.datetime(2022, 1, 1)
str(a)
a = datetime.datetime(2022, 1, 1)
repr(a)
a = "Hello World"
b = str(a)
eval(b) #에러. 시스템 인식 못함
b = repr(a)
eval(b) #에러 안남. 시스템 인식 가능

a = datetime(2022, 1, 1)
b = str(a)
type(a)
eval(b) #에러 발생
b = repr(a)
eval(b) #객체 자체를 온전히 표현하는 문자열. 에러 안남
```
## 라이브러리 다루기
```bash
pip list
```
- 내가 설치한 라이브러리 목록 볼 수 있음
```bash
pip install pandas
```
- 패키지 설치
```bash
pip uninstall pandas
```
- 패키지 삭제
```bash
pip install pandas==3.0.1
```
 - 버전 설정 설치
```bash
pip install --upgrade pandas
```
- 업그레이드
```bash
pip freeze > requirements.txt
```
- 내가 현재 설치돼있는 버전 정보를 requirements.txt로 저장
```bash
pip install -r requirements.txt
```
- requirements.txt에 있는대로 패키지 설치
```python
import base64#인코딩 방식
string = "집가고싶다"
encoded = base64.b64encode(string)#str은 안됨. 에러가남
bstring = string.encode('ascii')
print(bstring)
encoded = base64.b64encode(bstring)
print(encoded)
decoded = base64.decodebytes(encoded)
print(decoded)
decoded_str = decoded.decode('ascii')
print(decoded_str)
```

```python
path = 'C:/'
from PIL import Image
img = Image.open(path)
img
#바이너리 파일 읽기
with open(path, 'rb') as img:
    image = img.read()
#비트 정보 확인
from bitstring import BitArray
input_str = '0xff'
c = BitArray(hex=input_str)
c.bin
#base64인코딩
with open(path, 'rb') as img:
    data = img.read()
    encoded = base64.b64encode(data)
    print(encoded)
decoded = base64.decodebytes(encoded)
print(decoded)
file = "/content/decoded.png"
with open(file, 'wb') as file:
    file.write(decoded)
```

```python
import textwrap
text = '아주아주긴문자열'
print(text)
textwrap.shorten(text, width=200)#말줄여줌
textwrap.shorten(text, width = 100, placeholder = '...이하줄임')
wrapped_text = textwrap.wrap(text, width = 40)
print(wrapped_text)
print('\n'.join(wrapped_text))
filled_text = textwrap.fill(text, width = 40)
print(filled_text)
```

```python
import re
#정규표현식 regular expression.
#특정 문자열 추출, 변환
words = re.findall(r'\w+', text)#정규표현식으로 모든 단어를 찾음
print(words)
regax = r'0\d{1, 2}[ -]?\d{3, 4}[ -]\d{3, 4}'
phone = re.findall(regax, contact)
print("\n", join(phone))
pat = re.compile(r'0\d{1, 2}[ -]?\d{3, 4}[ -]\d{3, 4}')
print(pat.sub("***-****-****", contact))
```

```python
#빈도수 확인
counter = collections.Counter(words)
print(counter)
print(counter.most_commons(5))
```

```python
import gensim
#파이썬 3.8, gensim = 3.7.3
#gensim : 파이썬 머신러닝 라이브러리
#Word2Vec : 단어를 벡터로 환산해줌
model = gensim.models.Word2Vec.load('ko/ko.bin')
model.wv.most_similar("뉴스")#뉴스와 비슷한 단어를 찾음
model.wv.similarity('자동차', '강아지')#유사도 계산
#문서 요약
import pandas as pd
from gensim.summarization.summarizer import summarize
import numpy as np
df = pd.read_csv("path")
summarize(df.loc[0, 'passage'])
summarize(df.loc[0, 'passage'], ratio = 0.4)
df['extract'] = df.passage.apply(lambda x: summarize(x, ratio = 0.4))
#파일 객체 = open(파일 이름, 파일 열기 모드)
#파일 객체.close()
#r : 읽기모드
#w : 쓰기모드
#a : 추가모드
f = open("new_file.txt", 'w')
f.close()
```