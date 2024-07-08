---
published : true
layout : post
categories : NLP, AI
title : 통계적 언어 모델
tags : [NLP, AI]
date-string : July 08, 2024
---

# 통계적 언어 모델(Statistical Language Model, SLM)
- 언어모델의 전통적인 접근 방법

## 조건부 확률
$$P(B|A) = P(A, B)/P(A)$$
- 연쇄 법칙
$$P(x_1, x_2, x_3, \dots, x_n) = P(x_1)P(x_2|x_1)P(x_3|x_1, x_2)\dots P(x_n|x_1, x_2, \dots, x_{n-1})$$

## 문장에 대한 확률
- 각 단어는 문맥으로 인해 이전 단어의 영향을 받아 나온 단어
- 모든 단어로부터 하나의 문장이 완성됨

_문장의 확률을 구함 -> 조건부 확률 사용_

$$P(w_1, w_2, \dots, w_n) = \prod_{n=1}^n P(w_n|w_1, \dots, w_{n-1})$$

### 실제 문장에 적용
- 문장 'An adorable little boy is spreading smiles'의 확률

$$P(An\; adorable\; little\; boy\; is\; spreading\; smiles) = P(An) \times P(adorable|An) \times P(little|An\; adorable) \times P(boy|An\; adorable\; little) \times P(is|An\; adorable\; little\; boy) \times P(spreading|An\; adorable\; little\; boy\; is) \times P(smiles|An\; adorable\; little\; boy\; is\; spreading)$$

## 카운트 기반의 접근
- 문장의 확률을 구함 -> 다음 단어에 대한 예측 확률을 곱함
- SLM이 이전 단어로부터 다음 단어에 대한 확률을 구하는 법
  - 카운트에 기반하여 확률 계산

- An adorable little boy가 나왔을 때, is가 나올 확률 구하는 예제

$$P(is|An\; adorable\; little\; boy) = \frac{count(An\; adorable\; little\; boy\; is)}{count(An\; adorable\; little\; boy)}$$

- 학습 코퍼스 데이터에서 An adorable little boy가 100번 등장, 그 다음에 is가 30번 등장
$$P(is|An\; adorable\; little\; boy) = 30\%$$

## 카운트 기반의 한계 - 희소문제(Sparsity Problem)
- 언어모델 : 실제로 사용되는 언어의 확률분포를 근사 모델링
  - 실제로 An adorable little boy 다음에 is가 나올 확률 존재
- 언어모델의 목표 : 이 현실에서의 확률 분포에 근사하는 것
- 카운트 기반 : 방대한 양의 데이터 필요(확률분포에 근사하기 위해)
  - 만약 An adorable little boy is라는 데이터가 없다면
  $$P(is|An\; adorable\; little\; boy)$$
  의 값을 구하지 못함

- 희소문제 : 충분한 양의 데이터를 관측하지 못하여 언어 모델이 제대로 성능을 내지 못하는 문제
  - 실제로는 존재하는 말이지만 단지 학습시키지 못했다는 이유로 언어 모델이 예측을 못함