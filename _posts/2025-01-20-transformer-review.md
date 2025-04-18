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
은닉층 $$h_t$$에는 t와 이전 은닉층인 $$h_{t-1}$$이 들어간다.\
이런 방식으로 계속 진행되면 처음 입력한 값은 점차 희석되게 된다.\
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
- 인코더
    - 인코더는 2가지의 서브레이어로 구성됨
        - multi-head self-attention mechanism, fully connected feed forward network로 구성됨
        - 각 서브레이어들은 residual connection을 적용, 그 후 레이어 정규화
            - 이를 수식으로 쓰면 $$LayerNorm(x + Sublayer(x))$$이다.
            - Sublayer(x)는 그 서브레이어에서 구현된 함수.
            - residual connection을 위해선 모든 차원이 동일해야 함. 그래서 여기선 $$d_{model}=512$$로 맞춤
                - residual connection은 위의 식처럼 서브레이어의 출력(Sublayer(x))과 입력(x)의 차원이 같아야 덧셈이 가능함
                - 그러므로 임베딩 레이어를 포함한 모든 레이어의 출력 차원이 512로 맞춰져있어야됨
- 디코더
    - 기본적으로 인코더와 유사하다. 하지만 차이점은 있다.
        - 디코더는 서브레이어가 하나 더 있다.
            - 인코더의 결과를 포함해 attention을 수행하는 레이어가 하나 더 있음.
        - multi-head self-attention 레이어를 수정해 마스킹을 함.
            - 이 마스킹은 디코더가 i번째를 예측할 때 그 이후의 값을 참고하지 못하도록 함
                - 즉 i번째 예측을 위해 i-1까지만 보게함. i+1부터는 못보게 만들었다.

- Attention
attention함수는 query와 key-value쌍을 아웃풋으로 매핑하는 것으로 표현됨(query, key, value, output 모두 벡터)\
output은 value의 가중합으로 계산되고, value의 가중치는 query와 그에 상응하는 key와의 compatibility function을 통해 계산됨.\
![Image](https://github.com/user-attachments/assets/b66f2dfe-d36c-444f-94a8-ffca65e5b8e0)

    - 이 그림을 보면 scaled dot-product attention에서 Q, K가 연산을 거쳐 나온 결과와 V가 행렬 곱셈을 함.

- Scaled Dot-Product Attention
$$Attention(Q,K,V) = softmax(\frac{QK^T}{\sqrt{d_k}})V$$
    - dk는 Q와 K의 차원
    - 여기서 입력은 dk차원인 Q, K 그리고 dv차원인 V로 구성됨.
    - 쿼리는 Q벡터로, key는 K벡터로, value는 V벡터로 각각 뭉친다.
    - 여기서 dot-product attention을 사용한 이유는 additive attention보다 빠르고 효율적이기 때문
        - additive attention은 가중치 학습이 필요하기 때문
    - $$\sqrt{d_k}$$를 해주는 이유는 특정 값에 대해만 집중되는 것을 방지해주기 위해서다다
        - 차원이 커지면 softmax 함수 특성상 특정 값에 대해서만 치중되는 경우가 생긴다. 이를 방지해주기 위해 스케일링을 해줌.
        - 다른 함수들은 확률분포 생성에 적합하지 않다.

- Multi-Head Attention
$$MultiHead(Q, K, V) = Concat(head_1, \dots , head_h)W^O \\ where\ head_i = Attention(QW^Q_i, KW^K_i, VW^V_i)$$
$$W^Q_i \in R^{d_{model} \times d_k} \, , W^K_i \in R^{d_{model} \times d_k} \, , W^V_i \in R^{d_{model \times d_v}} \, , W^O \in R^{hd_v \times d_{model}}$$

    - 이 수식을 보면 Multi-Head Attention은 attention을 h번한 후 이를 합친 것이다.
    - 이런 방식을 사용하면 한번에 다양한 곳에 어텐션을 수행 가능.
    - 이는 동시에 여러 정보를 취합할 수 있고, 각 토큰의 유사성, 종속성을 동시에 파악할 수 있게 해준다.
    - 또한 이 논문에선 $$d_k = d_v = d_{model} / h = 64 /, (h = 8)$$을 사용
        - 이 방식을 사용하면 각각의 헤드의 차원을 줄여준다. 이는 전체 계산의 비용을 크게 감소해줌.
        - 전체 차원에 대한 single-head attention과 비슷해진다.


- Position-wise Feed-Forward Networks

$$FFN(x) = max(0, xW_1 + b_1)W_2 + b_2$$
$$W_1 \in R^{d_{model} \times d_{ff}} ,\ W_2 \in R^{d_{ff} \times d_{model}}$$

    - 이 서브레이어는 어텐션을 거쳐 나온 행렬을 정제해주는 역할
    - ReLU함수를 써서 비선형성을 추가
    - 이 서브레이어는 각각의 위치마다 동일하게 작용함
    - 이 서브레이어에선 차원이 $$d_{ff}$$ 만큼 늘어났다가 다시 $$d_{model}$$로 차원이 다시 돌아온다.
        - 이런 변형을 통해 비선형성을 추가해 표현력을 높여준다.
        - 이 논문에선 $$d_{ff}$$를 2048로 했다. $$d_{model}$$보다 더 높은 차원을 가진다.

- Embeddings & Softmax
    - 모델에 입력되기 전에 입력 데이터는 벡터로 변환되어야 한다.
        - multi-head attention은 행렬 연산이므로 벡터로 변환되어야함.
    - 이 역할을 해주는 것이 임베딩 모델이다.
    - 또한 디코더 아웃풋을 선형 변환과 소프트맥스 함수를 통해 변환한다.
        - 소프트맥스는 확률분포를 생성해주는 역할을 한다. 이 함수를 쓴다는 것은 확률분포로 바꿔준다고 보면 된다.
        - 이렇게 함으로 다음 토큰 확률을 예측할 수 있게 된다.
    - 인코더, 디코더의 임베딩 레이어는 같은 가중치 행렬을 공유한다.
    - 또한 pre-softmax linear transformation에서도 같은 가중치를 공유한다.
        - pre-softmax linear transformation은 디코더에서 생성한 벡터를 단어로 매핑하는 역할
        - 가중치를 공유하는 이유
            - 유사성 기반 디코더에 일관성 부여
                - 입력 임베딩과 출력 임베딩이 다르다면 같은 단어에 대해서도 유사성이 떨어지게 된다.
                - 인코더가 이해한 것과 디코더가 생성한 것이 서로 연관성이 없어지게 된다.
                - 의미가 일정하게 유지되지 않기 때문에 같은 가중치 행렬을 공유한다.
            - 학습 안정성 및 일반화
                - 같은 가중치를 공유하면 파라미터 수가 줄어든다.
                - 이는 학습 효율성이 올라가고, 오버피팅을 방지할 수 있다.
    
- Positional Encoding
    - 회귀 혹은 합성곱 레이어가 없다.
    - 그러므로 상대적 혹은 절대적 순서, 위치 정보를 따로 주입해야 한다.
    - 그러므로 인코더, 디코더 각각의 밑에 위치 정보를 구해 추가해줄 positional encoding을 추가.
    - positional encoding의 차원은 $$d_{model}$$이다.
        - 그래야 임베딩과 합쳐질 수 있기 때문.
    - 이 논문에선 sin, cos 함수를 사용한다.

    $$PE_{(pos, 2i)} = sin(pos/10000^{2i/d_{model}}) \\
      PE_{(pos, 2i+1)} = cos(pos/10000^{21/d_{model}})$$


    
    - 이 논문에서 세운 가설
        - 위의 식을 사용하면 각 포지셔널 인코딩의 차원은 sinusoid에 대응된다.
            - sinusoid는 정현파
            - 즉 주기함수가 된다는 뜻이다. sin, cos 함수는 -1부터 1까지의 주기함수다.
            - 주기함수라면 다른 위치에서 같은 값을 가질 수 있지만, positional encoding은 기본적으로 벡터이므로 이를 피할 수 있다.
            - 서로 다른 주기의 함수를 사용하면 위치 표현에서의 다양성이 생긴다.
                - 만약 첫번째 차원의 값들의 차이가 크지 않다면 두번째 차원에 다른 값을 채워 넣는다.
                - 이때 첫번째 차원의 값을 sin함수를 썼을 때 또 동일한 sin함수를 쓰면 벡터들 간의 차이가 작은건 변함이 없다
                - 이 때 cosine 함수를 쓰면 벡터들 간의 차이를 키울 수 있다.
                - 만약 cos함수를 썼을 때도 벡터들간의 차이가 크지 않다면 주기를 늘린다.
                - 이 과정을 반복하면 충분히 다른 인코딩 값을 가지게 된다.
                - 이런 방식으로 위치 표현의 다양성을 확보
        - 파장은 $$2pi$$부터 $$10000 \cdot 2pi$$ 까지 등비수열을 형성한다.
            - 이는 주기가 지수적으로 증가하게 된다.
                - 등차수열은 주기가 너무 촘촘하거나 너무 퍼지게 된다.
                - 등비수열은 지수적으로 증가하게돼 짧은 주기부터 긴 주기까지 파악 가능
        - 이렇게되면 모델은 상대적인 위치를 더 잘 학습하게 된다.
            - 위치간 차이를 함수로 파악 가능
            - 주기적 패턴 덕분에 상대적인 거리, 반복 구조를 쉽게 파악할 수 있게 된다.
            - 등비수열 주기는 다양한 스케일에서 상대 위치 인식을 가능하게 한다.
            - 즉 다양한 주기를 사용하는건 위치 표현의 다양성을 확보하기 위함이고, 등비수열 주기를 사용하는건 그 다양성을 효과적으로 확보하기 위함이다.

