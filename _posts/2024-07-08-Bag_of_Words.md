---
published : true
layout : post
categories : NLP, AI
title : Bag of Words
tags : [NLP, AI]
date-string : July 08, 2024
---

# Bag of Words

## 다양한 단어 표현 방법
- 국소표현(Local Representation)
  - 해당 단어 그 자체만 봄
  - 특정 값을 매핑하여 단어를 표현
  - 이산표현(Discrete Representation)
- 분산표현(Distributed Representation)
  - 그 단어를 표현하고자 주변을 참고하여 표현
  - 연속표현(Continuous Representation)
- 추가의견
  - 잠재 의미 분석(LSA)
  - 잠재 디리클 할당(LDA)
    - 모두 연속표현, 분산표현은 아님
  - 워드투벡터(Word2Vec)
    - 분산표현

=> 연속표현은 분산표현을 포함하는 더 큰 개념

## Bag of Words
- 단어들 순서는 고려하지 않음
- 단어들의 출현 빈도만 고려하는 텍스트 데이터 수치화
  - 각 단어에 고유한 정수 인덱스 부여
  - 각 인덱스 위치에 단어 토큰의 등장 횟수를 기록한 벡터를 만듬