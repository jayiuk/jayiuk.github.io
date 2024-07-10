---
published : true
layout : post
categories : NLP, AI
title : Cosine Similarity
tags : [NLP, AI]
date-string : July 10, 2024
---

# Cosine Similarity
- 두 벡터 간의 코사인 각도를 이용하여 구할 수 있는 두 벡터의 유사도
- 두 벡터의 방향이 완전히 동일하면 1
- 서로 직교하면 0
- 180도 반대면 -1
- 1에 가까울수록 유사함

![cosine_similarity](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/ae2a2993-5be7-4636-ab96-888015a47b8c)

$$similarity \;=\; cos(\theta) \;=\; \frac{A \cdot B}{\parallel A \parallel \parallel B \parallel} = \\ \frac{\sum_{i = 1}^n A_i \times B_i}{\sqrt{\sum_{i=1}^n (A_i)^2}\times \sqrt{\sum_{i=1}^n (B_i)^2}} $$

[예제는 여기](https://github.com/jayiuk/NLP_Practice/blob/main/basic_nlp_cosine_similarity.ipynb)