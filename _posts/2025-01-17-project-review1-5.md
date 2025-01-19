---
published : true
layout : post
categories : NLP, AI, chatbot
title : 빌릿 챗봇 프로젝트 5
tags : [NLP, AI, chatbot]
date-string : January 17, 2025
---

# 빌릿 챗봇 프로젝트 리뷰 5

## agent

챗봇을 구현하기 위해 가장 신경쓴 부분은 제때 필요한 도구가 호출되게 하는 것이었다.\
처음엔 간이대출심사만 바인딩하고 나머지는 체인으로 해결할 생각이었다.\
하지만 이런 방식은 도구 호출 안정성이 떨어지고, 에러가 발생할 수 있으므로 에이전트를 활용하는 것으로 바꾸었다.

### agent 코드

```python
import funcsfortool2
from funcsfortool2 import SimpleScreening, retrieve, fin_retirever
from langchain.agents import AgentExecutor, create_openai_functions_agent
from langchain_core.prompts import ChatPromptTemplate, MessagesPlaceholder
from langchain.tools.render import render_text_description
from langchain_community.chat_message_histories import SQLChatMessageHistory
from sqlalchemy import create_engine
import time
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain.callbacks.base import BaseCallbackHandler


simple = SimpleScreening()
retriever_tool = retrieve()
fin_retriever_tool = fin_retirever()



template2 = """
        당신은 외국인 노동자들을 위한 P2P서비스 빌리잇(Billit)의 ㅈㄴ똑똑한 챗봇 상담사입니다.
        당신은 다음의 도구들을 사용할 수 있습니다.:
        {tools}
        도구의 이름은 {tool_names}입니다.
        History: 저장된 대화내역 입니다. 당신은 이걸 기반으로 대답할 수 있습니다. 자세한 예시는 rule에 있습니다.:{chat_history}
        다음의 rule을 무조건 지키면서 사용자의 질문에 대답하세요.
        rule:
        - Question이 들어오면 [{tool_names}]중 하나의 툴을 선택해 사용하세요
        - 다만 '그것들의 차이가 뭐야?', '내가 물어봤던거 다시 알려줘.'와 같은 상황에선 회원가입을 유도하는 답을 하세요.
        - 질의응답 유형의 Question이 들어오면 local_retriever를 쓰세요.
            - 예시: '자료열람요구권이 뭐야?', '대출 어떻게 받아?', '투자는 어떻게 해?'와 같은 상황에선 local_retriever를 쓰세요.
        - Question의 언어에 맞게 답하세요. 만약 Question이 베트남어면 베트남어로 답하세요. Question이 한국어면 한국어로 답하세요. Question의 언어를 모르면 영어로 답하세요.
        - 대답은 짧고 간단하게 해야합니다.
        - 말 끝에 회원가입을 유도하는 말을 덧붙이세요.
            - 예시 : '자세한 사항을 알고 싶으시면 회원가입을 진행해 주세요.'
"""

class NonLoginAgent():
    def __init__(self, llm):
        self.llm = llm
        self.tools =  [retriever_tool]
        self.prompt = ChatPromptTemplate.from_messages([
            ("system", template2),
            MessagesPlaceholder("chat_history", optional = True),
            ("human", "{input}"),
            MessagesPlaceholder("agent_scratchpad")
        ])
        self.agent = self._get_agent()
    
    def _get_agent(self):
        tool_names = ", ".join([t.name for t in self.tools])
        prompt_fin = self.prompt.partial(tools=render_text_description(self.tools), tool_names=tool_names)
        agent_t = create_openai_functions_agent(llm = self.llm, tools = self.tools, prompt = prompt_fin)
        agent_aex = AgentExecutor(agent = agent_t, tools = self.tools, handle_parsing_errors=True, max_iterations = 15, verbose=False)
        return agent_aex
    
    def answer_to_me(self, query):
        agent = self.agent
        result = agent.invoke({'input' : query})
        return result
    

template = """
        당신은 외국인 노동자들을 위한 P2P서비스 빌리잇(Billit)의 똑똑한 챗봇 상담사입니다.
        당신은 다음의 도구들을 사용할 수 있습니다.:
        {tools}
        도구의 이름은 {tool_names}입니다.
        History: 저장된 대화내역 입니다. 당신은 이걸 기반으로 대답할 수 있습니다. 자세한 예시는 rule에 있습니다.:{chat_history}
        다음의 rule을 무조건 지키면서 사용자의 질문에 대답하세요.
        rule:
        - Question이 들어오면 [{tool_names}]중 하나의 툴을 선택해 사용하세요
        - 다만 '그것들의 차이가 뭐야?', '내가 물어봤던거 다시 알려줘.'와 같은 상황에선 History만 쓰세요.
        - 서비스 이용방법, 대출 및 투자 방법 유형의 Question이 들어오면 local_retriever를 쓰세요.
            - 예시: '대출 받고 싶은데 어떻게 해?', '투자는 어떻게 해?', '이 서비스는 뭐하는 서비스야?', '서비스 이용방법 알려줘'와 같은 상황에선 local_retriever를 쓰세요.
        - 대출심사 유형의 Question이 들어오면 simple_screening을 쓰세요.
            - 예시: '나 대출 가능해?', '나 연봉이 4000인데 대출 가능해?', '나 대출 가능한지 봐줘.'와 같은 상황에서 simple_screening을 쓰세요.
        - 금융 지식 관련 Question에선 financial_vocabulary를 쓰세요
            - 예시 : '자료열람요구권이 뭐야?', '개인신용평가대응권을 못쓰는 상황이 있어?', '인지세가 뭐야?', '인지세는 누가, 얼마나 부담해?'와 같은 질문이 들어오면 financial_vocabulary를 쓰세요.
        - Question의 언어에 맞게 답하세요. 만약 Question이 베트남어면 베트남어로 답하세요. Question이 한국어면 한국어로 답하세요. Question의 언어를 모르면 영어로 답하세요.
"""

class LoginAgent(NonLoginAgent):
    def __init__(self, llm, db_path, user_id):
        super().__init__(llm)
        self.model = llm
        self.prompt = ChatPromptTemplate.from_messages([
            ("system", template),
            MessagesPlaceholder("chat_history", optional = True),
            ("human", "{input}"),
            MessagesPlaceholder("agent_scratchpad")
        ])
        self.db_path = db_path
        self.engine = create_engine(self.db_path)
        self.user_id = user_id
        self.session_id = self._get_conversation_id()
        self.tools = [simple, retriever_tool, fin_retriever_tool]
        self.memory = SQLChatMessageHistory(
            table_name = self.user_id,
            session_id = self.session_id,
            connection = self.engine
        )
        self.agent = self._get_agent()
        self.agent_hist = self._agent_history()
    
    def _get_conversation_id(self):
        user_id = self.user_id
        now = time
        time1 = str(now.localtime().tm_year)
        time2 = str(now.localtime().tm_mon)
        time3 = str(now.localtime().tm_mday)
        conversation_id = user_id + time1 + time2 + time3
        return conversation_id

    
    def load_memory(self):
        return SQLChatMessageHistory(
            table_name = 'history',
            session_id = self._get_conversation_id(),
            connection = self.engine
        )
    

    def _agent_history(self):
        agent = self.agent
        history_agent = RunnableWithMessageHistory(
            agent,
            self.load_memory,
            input_messages_key = 'input',
            history_messages_key = 'chat_history'
        )
        return history_agent
    
    def answer_to_me(self, query):
        agent = self.agent_hist
        memory = self.memory
        result = agent.invoke({'input' : query}, config = {'configurable' : {'session_id' : self.session_id}})
        print("result :", result)
        memory.add_user_message(query)
        memory.add_ai_message(result['output'])
        return result
```

- 에이전트는 크게 두 가지로 나뉘어져 있다.
    - NonLoginAgent 모듈은 로그인하지 않은 사용자에게 아주 기초적인 내용만(사용메뉴얼, 금융어휘) 아주 간단하게 알려준다
    - LoninAgent는 로그인한 사용자에게 간이대출심사, 대화내역 저장 및 대화내역 참고해 대답의 기능을 한다.
- LoginAgent는 NonLoginAgent를 상속받아 구현
    - NonLoginAgent에 들어가는 기능은 LoginAgent에도 똑같이 들어가므로 코드 중복을 방지할 수 있다.
    - NonLoginAgent를 만들 때 LoginAgent를 상속받아 만들면, 간이대출심사 등 로그인 하지 않은 사용자에게 제공하면 안되는 기능이 제공될 수 있다.

#### 프롬프트
프롬프트에서는 tool_names를 알려줘 어떤 도구들이 있는지 알려준다.\
그리고 어떤 상황에서 어떤 도구를 선택해야 하는지를 알려준다.
- 이 과정에서 예시와 함께 알려줘 정확도를 높인다.

또한 질문 언어에 맞게 대답하라는 규칙을 추가했다.
- 외국인 노동자 대상이기 때문에 다국어 지원이 돼야하기 때문.

simple_screening을 쓸 땐 사용자 입력에 따라 파라미터가 입력됨
- 때문에 여기서 바로 실제의 대출심사로는 안넘어감.
- 만약 그럴 경우 정확한 대출 심사에 차질이 생긴다.

#### 메모리
sql을 사용해 대화내역을 저장한다.
- 이 경우 랭체인의 SQLChatMessageHistory 클래스를 사용한다.
- 대화구분을 위한 session_id는 유저 식별 번호(id 혹은 핸드폰번호)에 접속 날짜(연월일)를 붙인 값이다.
    - 이렇게 하면 모델이 대화 내용을 참고해 대답할 때 시간을 줄일 수 있게된다.

또한 에이전트가 대화내용을 참고해 대답할 수 있게 하기 위해 RunnableWithMessageHistory를 써서 에이전트가 대화내용을 참고할 수 있게 한다.

[agent](https://github.com/jayiuk/MLOps_chatbot/blob/main/agentver2.py)