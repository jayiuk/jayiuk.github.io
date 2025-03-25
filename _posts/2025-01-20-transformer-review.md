---
published : true
layout : post
categories : NLP, AI, chatbot
title : Transformer 논문 리뷰
tags : [NLP, AI, thesis]
date-string : January 20, 2025
---

# Attention Is All You Need
이 논문은 transformer 구조의 모델에 대한 논문이다.\
transformer 구조를 공부하면서 이 논문을 리뷰한 이유는 이 논문이 transformer를 소개하는 첫번째 논문이기 때문이다.\
[아카이브](https://arxiv.org/abs/1706.03762)\
단지 첫번째라 선정한 것이 아니라 이 논문을 기초로 발전을 했기 때문에 이 논문을 리뷰하면서 공부하기로 결정했다.

# 논문 리뷰

## Abstract
여기선 이전의 모델과 이 논문에서 소개하는 transformer 모델의 차이와 장점, 성과를 대략적으로 설명한다.\
이전의 모델들은 복잡환 순환 구조 혹은 컨볼루션 구조의 인코더, 디코더 구조였다.\
여기에 더 성능이 높은 모델은 attention 메카니즘을 적용하기도 했다.\
하지만 이 논문은 오직 attention 메카니즘으로만 모델을 만들었고 이 구조를 Transformer라고 했다.\
여기서 중요한 점은 attention만으로 모델링을 했다는 것이다.\
이는 기존에 사용되던 순환 및 합성곱 신경망을 사용하지 않았다는 것이다.\
쉽게 말해 seq2seq모델에서 신경망 부분을 뺀 것이다.\
그럼에도 성능은 기존의 모델보다 좋게 나왔다.(영어-독일어 번역)

## Introduction
Introduction에선 기존의 순환신경망을 이용한 모델의 단점과 이를 해결한 Transformer에 대한 소개를 담고있다.\
기존 순환 모델은 이전의 은닉층인 $$h_{t-1}$$ 과 입력값인 t에 대한 함수인 은닉층 $$h_t$$를 만든다.\
이는 장기 기억 부분에 문제가 생길 수밖에 없다.\
은닉층 $$h_t$$는 이전 단계인 $$h_{t-1}$$과 현재 입력인 t만 사용하기 때문이다.\
이는 처음 입력한 값에 대한 희석이 생기게 된다.\
결국 장기기억에 대한 문제가 생기게 된다는 것이다.\
기존의 모델은 이 문제를 해결하기 위해 factorization 기법과 조건부 계산은 문제를 해결했다.\
하지만 순차적인 계산을 강제하는 문제는 해결하지 못했다.
- factorization tricks는 행렬 분해를 사용한 LSTM 기법이다.
    - [factorization](https://arxiv.org/abs/1703.10722) 에 따르면 기존의 LSTMP셀 계산의 주요한 부분은 아핀 변환된 T를 계산하는 것이다.
    - affine transformT는 $$T = W * [x_t, h_{t-1}] + b$$ 으로 계산된다. 여기서 W는 $$4n \times 2p$$인 행렬이다.
    - 즉 T에 대한 계산은 행렬 W에 대한 곱셉을 포함하기 때문
    - Factorized LSTM(F-LSTM)은 W를 $$W \approx W_1 * W_2$$가 성립하는 두 행렬로 나눈다.
        - $$W_1 = 2p \times r, W_2 = r \times 4n (r < p <= n)$$
    - 이런 원리를 통해 기존의 LSTM보다 더 적은 파라미터로 모델링을 할 수 있게 된다.

어텐션 메카니즘은 인풋 혹은 아웃풋에서 거리(순환신경망은 순서대로 입력되고 출력이 됨.)에 대해 덜 신경쓰게 해줬다.\
그러나 일부를 제외하면 기존 순환신경망의 보조 역할만 했다.\
그래서 오직 어텐션으로 모델링을 했다.\
이게 Introduction의 내용이다.\

(결국 LLM은 학습한 데이터를 바탕으로 입력과 가장 유사한 매트릭스를 생성하는 것.)
## Background

이 챕터는 이전 연구들의 한계점에 대해 다루고 있다.\
이전 연구들과는 합성곱 신경망 혹은 RNN을 쓰지 않고 오직 self-attention을 사용해서만 모델링을 했다는것.

## Model Architecture
![attention](https://github.com/user-attachments/assets/083d3233-91ff-43f9-8647-bdc19f14edd8)

- 왼쪽이 인코더, 오른쪽이 디코더다.