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