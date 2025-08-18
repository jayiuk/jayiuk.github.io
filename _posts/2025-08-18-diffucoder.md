---
published : true
layout : post
categories : LLM, AI, LoRA
title : DiffuCoder 논문 리뷰
tags : [LLM, AI, diffusion]
date-string : August 18, 2025
---

# DIFFUCODER

## abstract
- dLLM : Diffusion Large Language Model
    - 디코더를 기존의 방식이 아닌 디퓨전 방식을 사용
- dLLM은 Auto Regressive(AR, 기존 방식) LLM에 대한 설득력 있는 대체재
    - 전체 순서를 조작가능
    - 특히 코드 생성에 유용
- 그러나 현재 코딩에서의 dLLM의 훈련과 추론 메커니즘은 여전히 충분히 연구되지 않음
- dLLM의 디코딩에서의 미해결 문제를 풀고 코딩에서의 잠재력을 펼치기 위해 dLLM의 디노이징 과정과 강화학습 방법들을 체계적으로 조사
### 논문에서 쓴 모델과 훈련 데이터 양
- DiffuCoder -> 7B
- 130B 코드 토큰 학습
- 이를 실험체로 디코딩 방식을 분석, 기존 AR 모델과의 차이를 밝혀냄
    - dLLM이 semi AR 방식 없이 어떻게 생성을 결정할 수 있는지
    - temperature를 올리면 토큰 선택 뿐만 아니라 생성 명령도 다양화 하는지
### RL
- 토큰 로그 가능도(토큰이 선택될 가능성에서 로그를 씌운 것)의 분산을 줄이고 훈련 효율성을 평가하고 유지하기 위해 _coupled-GRPO_ 사용
    - 훈련에 사용되는 완성된 문장들에 상보적인(상호보완적인) 마스크를 씌우는 새로운 샘플링 기법
