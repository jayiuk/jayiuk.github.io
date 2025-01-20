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
기존 순환 모델은 이전의 은닉층인 $h_{t-1}$ 과 입력값인 t에 대한 함수인 은닉층 $h_t$를 만든다.\
이는 장기 기억 부분에 문제가 생길 수밖에 없다.\
은닉층 $h_t$는 이전 단계인 $h_{t-1}$과 현재 입력인 t만 사용하기 때문이다.\
즉 훈련 데이터간에 배치 제한을 강제해 장기 기억에 대해선 문제가 생기게 된다.\
