---
published : true
layout : post
categories : NLP, AI, langchain
title : 랭체인 CH.1 랭체인 기초
tags : [NLP, AI, langchain]
date-string : August 12, 2024
---

# 랭체인 기초
## 랭체인이란
- LLM을 사용하는 어플리케이션 개발을 위한 오픈소스 프레임워크
- LLM 추상화를 통해 LLM어플리케이션 개발을 간소화해줌
  - 컴퓨터를 쓴다고 작동 원리를 꼭 알아야 하는게 아니듯 언어 모델에 대한 이해가 떨어져도 언어 모델을 써서 개발을 하게 해줌


## 랭체인 기본 프레임워크
- 개발
  - 랭체인의 오픈소스 빌딩 블록, 컴포넌트, 서드파티 통합 등을 사용하여 어플리케이션 제작
  - langchain : 체인, 에이전트, 및 검색 전략
  - langchain-core : 기본 추상화 및 랭체인 표현 언어
  - langchain-community : 서드파티 통합(langchain-openai 등)

- 테스트
  - LangSmith를 사용하여 체인을 검사, 모니터링 및 평가

- 배포
  - LangGraph Cloud를 사용하여 LangGraph 애플리케이션을 API와 Assistant로 전환
  - LangGraph : 그래프의 에지와 노드로 단계를 모델링. LLM으로 stateful 멀티 액터 애플리케이션을 구축(에이전트 여러개를 하나의 서비스로로)
  - LangServe : 체인을 REST API 형태로 배포

## 랭체인 주요 기능
- 다양한 데이터 소스와의 통합
  - 랭체인을 사용하면 다양한 데이터 소스와 LLM의 통합을 쉽게 할 수 있다.
    - 다양한 파일(PDF, csv, txt 등등)에서 데이터를 불러와 벡터스토어로 사용 가능
    - 외부 API 연동 또한 지원
      - 예시

      ```python
      from langchain_community.utilites import RequestWrapper
      requests_wrapper = RequestsWrapper()
      response = requests_wrapper.get("https://api.just.example.empty.com/data")
      ```

- 유연한 프롬프팅 및 컨텍스트 관리
  - 랭체인은 프롬프팅, 컨텍스트 관리 도구 제공한다(ChatPromptTemplate).
  - LLM이 사용자 질문에 대해 더욱 정확하고 맞춤형 응답을 생성하게 해준다.
    - few-shot 러닝 방식을 사용하여 모델이 언제 어떻게 어떤 대답을 생성해야할 지 지정 가능
    - ReAct, CoT 등의 방식을 이용하여 더욱 정교한 추론이 가능하게 할 수 있다.
- 파인튜닝 및 커스터마이징
  - 랭체인을 사용하면 파인튜닝도 가능하다.
    - LangSmithRunChatLoader로 채팅 세션을 불러와 convert_messages_for_finetuning로 학습용으로 전환한 후 파인튜닝 시키는 것이 가능하다.
    - [참고링크](https://python.langchain.com/docs/integrations/chat_loaders/langsmith_llm_runs/)
    - 또한 같은 원리로 특정 산업군 혹은 분야에 대한 데이터로 파인튜닝 하는 것이 가능하다.
  - 다양한 모델을 쉽게 교체하거나 조정 가능.
    - 애플리케이션 요구에 맞게 수정이 가능하다.

- 데이터 반응형 애플리케이션 구축.
  - 랭체인으로 실시간 데이터 반영 애플리케이션 구축이 가능하다.
  - websocket, kafka, rest api 연동 등을 활용해서 데이터를 불러와 이벤트 트리거를 활용해 특정 이벤트 발생 시 데이터 처리. 그 후 랭체인의 CallbackHandler로 실시간 로그 기록 및 데이터 스트리밍 처리를 통해 데이터 반응형 애플리케이션 구축이 가능하다.




[참고자료1](https://python.langchain.com/docs/introduction/) \
[참고자료2](https://www.samsungsds.com/kr/insights/what-is-langchain.html) \
[참고자료3](https://www.samsungsds.com/kr/insights/the-concept-of-langchain.html) \
[참고자료4](https://python.langchain.com/docs/integrations/chat_loaders/langsmith_llm_runs/)