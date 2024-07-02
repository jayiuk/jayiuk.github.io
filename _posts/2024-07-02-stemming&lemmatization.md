---
published : true
layout : post
categories : NLP, AI
title : 어간 추출, 표제어 추출
tags : [NLP, AI]
date-string : July 02, 2024
---

# 어간 추출 & 표제어 추출
- 서로 다른 단어라도 하나의 단어로 일반화

## 표제어 추출(lemmatization)
- 표제어 : 기본 사전형 단어
- 다른 형태의 단어 -> 같은 뿌리면 하나의 단어로 바꿈
- 형태학적 파싱을 먼저 진행

### 형태학
- 형태소 : 의미를 가지는 가장 작은 단위
- 형태학 : 형태소로부터 단어를 만들어나가는 학문

### 형태소의 종류
- 어간(stem) : 단어의 의미를 담고 있는 부분
- 접사(affix) : 단어에 추가적인 의미를 주는 부분

### 형태학적 파싱
- 단어를 어간과 접사로 분리하는 작업

### 표제어 추출 예제 코드

```python
from nltk.stem import WordNetLemmatizer
lemmatizer = WordNetLemmatizer()
out = [lemmatizer.lemmatize(word) for word in words]
```

- 표제어 추출을 했을 경우 단어의 형태가 적절히 보존됨
- 표제어 추출기가 본래 단어의 품사를 모를 경우 정확한 결과가 안나옴

## 어간 추출(stemming)
- 어간(stem)을 추출하는 작업
- 어간 추출 후 나오는 단어는 사전에 존재하지 않는 단어일 수 있음
  - 이 작업은 어미를 어림짐작해 자르는 과정일 수 있기 때문

