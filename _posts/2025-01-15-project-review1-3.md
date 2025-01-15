---
published : true
layout : post
categories : NLP, AI, chatbot
title : 빌릿 챗봇 프로젝트 4
tags : [NLP, AI, chatbot]
date-string : January 9, 2025
---

# 챗봇 프로젝트 리뷰 4

## getdti, funcsfortool

getdti는 dti라는 컬럼을 구하기 위한 모듈.\
여기서 dti를 구한 후 머신러닝 모델에 입력값으로 쓰인다.\
funcsfortool은 챗봇이 쓸 툴을 모아놓은 모듈.\
챗봇은 필요에 따라 여기 있는 툴을 활용해서 정확한 답변을 생성하게 된다.

### getdti

```python
import pymysql

conn = pymysql.connect(host = 'localhost', port = 3306, user = 'root', password='1234', db = 'mydata')

def _get_data(user_pn): # dti를 구할 때 필요한 데이터를 불러오는 함수.
    mortgage_debt = 0
    mortgage_repayment = 0
    installment = 0
    mortgage_term = 0
    cursor = conn.cursor()
    cursor.execute(f'SELECT mortgage_debt, mortgage_repayment, installment, mortgage_term FROM mydata.user_log WHERE user_pn="{user_pn}"')
    row = cursor.fetchall()
    row0 = row[0]
    mortgage_debt = row0[0]
    mortgage_debt = float(mortgage_debt)
    mortgage_repayment = row0[1]
    mortgage_repayment = float(mortgage_repayment)
    installment = row0[2]
    mortgage_term = row0[3]
    conn.close()
    return mortgage_debt, mortgage_repayment, installment, mortgage_term


def calculate_dti(user_pn, income):
    mortgage_debt, mortgage_repayment, installment, mortgage_term = _get_data(user_pn)
    dti = ((((mortgage_debt/mortgage_term) + mortgage_repayment) + installment) / income) * 100 # 실제 dti를 구하는 부분.
    return dti
```

- DB에 저장된 데이터들을 바탕으로 dti를 계산하는 방법
    - 원래는 마이데이터를 활용해야 하지만 마이데이터는 활용할 수 없어 임시로 가상의 데이터를 만든 후 사용
- DB에 저장된 데이터는 사용자의 핸드폰번호를 사용하여 불러와진다.
    - 사용자 편의를 위해 이런 방식을 사용.

#### _get_data
- 우선 pymysql 콘솔 생성
- 그 후 생성된 콘솔로 커서를 생성
- 생성된 커서를 이용해 user_pn을 키로 써서 mortgage_debt, mortgage_repayment, installment, mortgage_term을 불러옴.
    - 이렇게 하면 유저에 맞는 데이터가 불러와진다.
- 불러온 데이터를 모델 입력 형식에 맞게 바꿔준다.
    - 이는 처음에 DB를 만들 때 고려를 못해서 임시로 해둠. 지금은 의미가 없기는 하다.


#### calculate_dti
- 불러온 데이터를 이용해 dti를 계산하는 함수
- dti를 계산한 후 계산된 dti를 반환
- 이렇게 해서 모델의 입력값으로 사용


### funcsfortool
```python
'''
agent가 쓸 툴들을 구현해놓은 모듈
SimpleScreening은 간이대출심사 툴
retrieve는 서비스 사용법 알려주는 툴을 위한 모듈
fin_retirever는 금융 어휘 사전 툴을 위한 모듈

SimpleScreening 모듈은 getdti 모듈을 통해 dti를 구함
그 후 loan_amnt, dti, issue_d_peroid, annual_income을 간이대출심사 모델에 입력
그 결과값을 통해 대출 가능 여부 확인

retrieve는 서비스 사용방법 질의응답 툴을 위한 모듈
agentic rag 개념을 활용해 구현

fin_retirever는 간단한 금융 어휘를 알려주는 툴을 위한 모듈
이 역시 agentic rag 개념을 활용해 구현현
'''

from langchain_core.tools import BaseTool
import pickle
from pydantic import BaseModel, Field
import VectorStore
from retriever import Retriever
from langchain.tools.retriever import create_retriever_tool
from typing import Optional, Type
from langchain.callbacks.manager import CallbackManagerForToolRun
import pandas as pd
import getdti
from flask import request

import requests


dirpath = './MLOps_chatbot'
collection = 'testdb'
vec_db = VectorStore.load_vectorstore(dir_path = dirpath, collection_name = collection)
ret = Retriever(dirpath, collection, searched = 2)
agent_retirever = ret.as_retriever()

dir2 = './fin_vocab'
collection2 = 'findb'
vec_db2 = VectorStore.load_vectorstore(dir_path = dir2, collection_name = collection2)
ret2 = Retriever(dir2, collection2, searched = 2)
agent_retirever2 = ret2.as_retriever()


ml = pickle.load(open('ml_for_tool.pkl', 'rb')) #간이대출심사 모델

class ScreeningInput(BaseModel):
   annual_income:int = Field(..., description = "사용자가 입력한 질문에 있는 연봉. 혹은 사용자가 입력한 질문에 있는 소득.")
   period:int = Field(..., description = "사용자가 입력한 질문에 있는 마지막 대출 후 경과한 햇수. 단위는 무조건 년으로 받음.")
   loan_amount:int = Field(..., description = "사용자가 입력한 질문에 있는 대출 희망 금액.")

class SimpleScreening(BaseTool):
    name = 'simple_screening'
    description = """
    주어진 머신러닝 모델을 이용해 간단한 대출심사를 진행.
    대출 심사 요청이 왔을때만 실행.
    예시
    - 나 대출 가능해?
    - 나 대출 가능한지 봐줘.
    이런 질문들이 들어왔을 때 파라미터를 입력받은 후 이 도구를 사용합니다.
    누락된 파라미터가 있을 땐 체인 종료 후 사용자에게 누락된 파라미터를 요청하세요.
    TypeError시 사용자에게 다시 입력 받으세요.
    """
    args_schema : Type[BaseModel] = ScreeningInput


    def _run(self, annual_income:int, period:int, loan_amount:int, run_manager: Optional[CallbackManagerForToolRun] = None):
        pn_data = request.get_json() #getdti 모듈을 통해 dti를 구하려면 사용자의 전화번호가 필요함. 이 경우 세션이 아니라 로컬 스토리지를 통해 로그인 정보를 확인해서 서버에 전화번호를 요청함.
        user_pn = pn_data['user_pn'] 
        dti = getdti.calculate_dti(user_pn, annual_income) #사용자 전화번호는 DB에서 dti를 구할 때 필요한 데이터들을 찾을 때 씀.
        input_data = pd.DataFrame([{'loan_amnt' : loan_amount, 'dti' : dti, 'issue_d_period' : period, 'annual_inc' : annual_income}])
        prediction = ml.predict(input_data)
        screening_result = ''
        if prediction[0] == 0:
            screening_result = '대출이 가능합니다.'
        elif prediction[0] == 1:
            screening_result = '대출이 가능하지만 금리가 조금 더 높을 수 있어요.'
        elif prediction[0] == 2:
            screening_result = '아쉽지만 탈락입니다.'
        return screening_result

def retrieve():
    retriever_tool = create_retriever_tool(
        agent_retirever,
        name = 'local_retriever',
        description = '''서비스 사용법, 대출 방법, 투자 방법에 관한 문서들을 검색할 수 있는 검색기 도구입니다. 
        서비스 사용법, 대출 방법, 투자 방법에 대한 질문이 들어오면 사용합니다.
        예시
        - 나 대출 받는 방법이 궁금해.
        - 투자를 하고싶은데 어떻게 해.
        - Billit 어떻게 써?
        - 이 서비스는 뭐하는 서비스야?
        이런 질문에는 이 local retriever를 써 관련된 문서를 찾은 후 그 문서를 기반으로 답하세요.'''
    )
    return retriever_tool

def fin_retirever():
    retriever_tool = create_retriever_tool(
        agent_retirever2,
        name = 'financial_vocabulary',
        description = '''금융지식에 관한 문서들을 검색할 수 있는 검색기 도구입니다.
        금융지식 관련 질문이 들어오면 사용합니다.
        예시
        - 자료열람요구권이 뭐야?
        - 개인신용평가대응권이 뭐야?
        - 인지세가 뭐야?
        - 개인신용평가대응권을 못쓰는 상황이 어떻게 돼?
        - 인지세는 누가, 얼마나 부담해?
    '''
    )
    return retriever_tool
```

- 에이전트가 쓸 툴을 모아놓은 모듈
- 이렇게 하면 툴들을 더 효과적으로 관리할 수 있음
- 만약 툴을 추가하거나 수정하고 싶으면 여기서 수정만 하면 됨

#### SimpleScreening
- 간이 대출심사를 진행할 때 쓰는 툴
- ScreeningInput에선 pydantic을 사용해 입력값을 받는다.
    - 여기서 description을 써서 이 입력값이 무엇인지 알 수 있게 함.
- SimpleScreening에선 ScreeningInput으로 받은 입력값을 가져옴
- 서버에 user_pn(전화번호)를 요청함
    - 여기선 로그인 관리를 세션이 아니라 로컬 스토리지를 활용해서 함.
    - 그래서 세션을 통해 가져오는건 안됨
- getdti모듈을 활용해 dti를 계산
- dti를 불러왔으면 입력된 값과 dti를 머신러닝 모델에 입력
- 결과에 따라 대출 가능 여부 알려준다

#### retrieve
- 서비스 사용메뉴얼 질의응답에 쓰인다.
- crete_retriever_tool 메서드를 사용해 retriever를 툴로 활용
- description에서 어떤 상황에 이 툴이 쓰이는지 예시와 함께 정의

#### fin_retirever
- 이름에 오타가 있긴 함
- 이 툴은 금융어휘 질의응답에 사용
- retrieve 모듈과 똑같은 방식으로 만듬
- 다만 retriever에 사용된 벡터DB가 다름
- 이 방식으로 서로 다른 retriever를 사용할 수 있게 됨
- 또한 벡터DB를 분리해 검색 시간과 정확도를 높일 수 있게됨


[getdti](https://github.com/jayiuk/MLOps_chatbot/blob/main/getdti.py)
[funcsfortool2](https://github.com/jayiuk/MLOps_chatbot/blob/main/funcsfortool2.py)
