---
published : true
layout : post
categories : NLP, AI
title : Euclidean Distance & Jaccard Similarity
tags : [NLP, AI]
date-string : July 10, 2024
---

# 유클리디안 거리
- 두 점 사이의 거리를 구할때 쓰는 방법 중 하나
- 다차원 공간에서 두 점 사이의 거리를 구하는 법

$$p = (p_1, p_2, \dots, p_n), q = (q_1, q_2, \dots, q_n) \\
  \sqrt{(q_1 - p_1)^2 + (q_2 - p_2)^2 + \dots + (q_n - p_n)^2} \;=\;\sqrt{\sum_{i=1}^n(q_i - p_i)^2}$$

- 식 자체는 피타고라스 정리에서 차원이 추가된 개념
- DTM은 행렬로 구성됨
- 행은 문서의 개수 열은 차원으로 치환 가능
- 유클리디안 거리로 문서 간 유사도 구하기 가능


# 자카드 유사도
- 합집합에서 교집합의 비율
- 0 ~ 1 사이의 값을 가짐

$$J(A, B) \;=\; \frac{|A \cap B|}{|A \cup B|} \;=\; \frac{|A \cap B|}{|A| + |B| - |A \cap B|}$$

