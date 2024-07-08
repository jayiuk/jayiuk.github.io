---
published : true
layout : post
categories : NLP, AI
title : N-gram 언어 모델
tags : [NLP, AI]
date-string : July 08, 2024
---

# N-gram 언어 모델
- SLM의 일종
  - 카운트에 기반한 통계적 접근
- 일부 단어만 고려
  - 일부 단어 : N-gram중의 N

## 코퍼스에서 카운트하지 못하는 경우의 감소
- SLM의 한계 : 훈련 코퍼스에 확률을 계산하고 싶은 단어나 문장이 없을 수 있다
- 확률을 계산하고 싶은 문장의 길이와 코퍼스에 그 문장이 존재할 확률은 반비례
- =>카운트 하지 못할 수 있다
- 단어들을 줄이면 카운트 가능해짐

$$P(is|An\; adorable\; little\; boy\;) \approx P(is|boy)$$

- 앞 단어 중 임의의 개수만 포함해서 카운트 -> 근사
- 갖고 있는 코퍼스에서 해당 단어의 시퀀스를 카운트할 확률이 높아짐

## N-gram
- 임의의 개수를 정하기 위해 사용함
- n개의 연속적인 단어 나열
- 갖고 있는 코퍼스 중에서 n개의 단어 뭉치를 끊어 하나의 토큰으로 간주
  - unigrams : an, adorable, little, boy, is, spreading, smiles
  - bigrams : an adorable, adorable little, little boy, boy is, is spreading, spreading smiles
  - trigrams : an adorable little, adorable little boy, little boy is, boy is spreading, is spreading smiles
  - 4-grams : an adorable little boy, adorable little boy is, little boy is spreading, boy is spreading smiles

- n-gram을 통한 언어 모델에선 다음 단어 예측을 오직 n-1개의 단어에만 의존
  - 'An adorable little boy is spreading' 다음에 나올 단어 예측
  - 4-grams 이용
  - 'boy is spreading' 부분만 사용

$$P(w|boy\; is\; spreading) = \frac{count(boy\; is\; spreading\; w)}{count(boy\; is\; spreading)}$$

## N-gram Language Model의 한계
- 희소 문제
  - 일부 단어만 보는 것은 코퍼스에서 카운트 할 수 있는 확률을 높임
  - 여전히 n-gram에 대한 희소 문제 존재
- n을 선택하는 것은 trade-off 문제
  - n을 정하는 것은 trade-off 존재함
  - n을 키우면 대부분 언어 모델의 성능을 높일 수 있음
    - 너무 커지면 모델 크기도 커짐
    - 또한 n-gram을 카운트 할 수 있는 확률도 적어짐
    - n이 작아지면 근사의 정확도는 현실의 확률 분포와 멀어짐
    - n은 최대 5를 넘게 잡지 말라고 권장

## 도메인에 맞는 코퍼스의 수집
- 분야에 따라 사용되는 어휘가 다름
- 이에 따른 어휘 빈도수도 도메인에 따라 달라짐
- 도메인에 맞는 코퍼스 수집 필요

## 인공신경망을 이용한 언어 모델
- n-gram 모델의 한계를 극복하기 위한 여러 일반화 방법들이 존재
- 해결 못함
- 결국엔 인공 신경망을 이용한 언어 모델 등장