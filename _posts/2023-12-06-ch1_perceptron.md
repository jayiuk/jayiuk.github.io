layout : post
author : jayiuk
tags : ["AI", "theory"]

# 1강 퍼셉트론

## 인공신경망 : 생물학적인 신경망을 모방하여 만든 것 
## -> 신경망에 대한 이해가 필요함

![alt text](https://cdn.aitimes.com/news/photo/202106/138838_139058_1547.jpg)

Dendrite : 이웃뉴런에서 전기신호를 받음.
Synapse : 다른 뉴런과 Dendrite의 연결부위에 있다. 전기 신호의 세기를 재조정
soma(cell body) : Dendrite로부터 받은 전기 신호를 모두 합친다.
Axon : Soma의 전위가 일정 이상이 되면 이웃 뉴런으로 전기신호를 보냄. 시냅스에서 전기 신호의 세기를 재조정해서 다음 뉴런으로 전달.

## 퍼셉트론
 - 신경망을 수학적으로 모델링하여 만든 것.
 - 입력값에 가중치를 곱함.
 - 그걸 합친 결과가 임계값을 넘어가면 1, 그렇지 않으면 0