---
published : true
layout : post
categories : NLP, AI
title : Perplexity, PPL
tags : [NLP, AI]
date-string : July 08, 2024
---

# Perplexity, PPL
- 두 모델 A, B가 있다고 가정
- 두 모델의 성능을 비교하는 방법
  - 다 실행시킨 후 결과를 비교
  - 이 방법은 시간이 많이 걸림
- Perplexity : 자신의 성능을 수치화해서 결과를 내놓음

## 언어 모델의 평가 방법(Evaluation metric) : PPL
- Perplexity : 언어 모델을 평가하기 위한 평가 지표
- 문장의 길이로 정규화된 문장 확률의 역수
- 수치가 높으면 성능이 낮음
- 수치가 낮아야 성능이 좋다는 의미

$$PPL(W) = P(w_1, w_2, w_3, \dots, w_N)^{-\frac{1}{N}} = \\ ^N\sqrt{\frac{1}{P(w_1, w_2, \dots, w_N)}}$$

- 체인룰 적용

$$PPL(W) = ^N\sqrt{\frac{1}{P(w_1, w_2, \dots, w_N)}} = \\ ^N\sqrt{\frac{1}{\prod_{i = 1}^N P(wi|w_1, w_2, \dots, w_{i-1})}}$$

- n-gram 적용

$$PPL(W) = ^N\sqrt{\frac{1}{\prod_{i=1}^N P(w_i|w_{i-1})}}$$

## 분기계수
- PPL은 선택할 수 있는 가능한 경우의 수를 의미하는 분기계수
- 언어모델이 특정 시점에서 평균적으로 몇 개의 선택지를 가지고 고민하는지 의미
  - PPL이 낮을수록 모델의 성능이 더 좋다는 의미로 볼 수 있다
- PPL이 10인 경우
  - 테스트 데이터에 대해 다음 단어를 예측하는 시점마다 평균적으로 10개의 단어를 가지고 고민했다는 의미

$$PPL(W) = P(w_1, w_2, \dots. w_N)^{-\frac{1}{N}} = (\frac{1}{10}^N)^{-\frac{1}{N}} = \\ \frac{1}{10}^{-1} = 10$$

### 주의할 점
- PPL값이 낮다 -> 테스트 데이터 상에서 높은 정확도를 보인다
- 사람이 직접 느끼기에 좋은 언어 모델이라는 것을 반드시 의미하진 않음
- 정량적 양이 많고 도메인에 알맞은 동일한 테스트 데이터를 바탕으로 진행해야 신뢰도가 올라감