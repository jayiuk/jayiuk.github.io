---
published : true
layout : post
categories : NLP, AI, chatbot
title : 빌릿 챗봇 프로젝트 3
tags : [NLP, AI, chatbot]
date-string : January 9, 2025
---

# 프로젝트 리뷰 1-3

## DocLoader, VectorStore, Retriever

### DocLoader
- 벡터화 시킬 문서를 가져오는 모듈
- 문서에 대한 정보가 담긴 metadata와 문서 내용이 담긴 page_content로 구성되어 있는 Document 형식의 데이터를 반환
- 이 모듈을 구현할 때 chunking을 같이 수행하도록 했음


```python
from langchain_community.document_loaders import PyMuPDFLoader, DirectoryLoader, TextLoader
import re
from langchain.schema import Document

def split(func): # split하는 부분. 여기선 charactersplitter 메서드를 사용하지 않고 직접 구현함.
    def wrapper(*args, **kwargs):
        docs = func(*args, **kwargs)
        splitted_doc_list = []
        doc_list = [doc.page_content for doc in docs]
        meta = [doc.metadata for doc in docs]
        for doc in doc_list:
            con_pattern = r'\n\n'
            c_doc = re.sub(con_pattern, '', doc)
            splitted_doc = re.split(r'[.※·－①②③④⑤]', c_doc)
            splitted_doc_list.append(splitted_doc)
        result_doc = [Document(page_content= "\n\n".join(splitted_doc_list), metadata = meta) for splitted_doc_list, meta in zip(splitted_doc_list, meta)] # split 결과를 다시 Document 형태로 바꾸는 작업
        # split 결과는 리스트로 나와 Document 형태로 바꿔줘야함.
        return result_doc
    return wrapper

@split
def docload(file_path):
    loader = PyMuPDFLoader(file_path)
    pages = loader.load()
    return pages

@split
def dirloader(dir_path):
    loader = DirectoryLoader(dir_path, glob = '*.txt', loader_cls = TextLoader)
    pages = loader.load()
    return pages
```

- docload 함수는 pdf로 되어있는 문서를 로드하는데 쓰임
    - 여기선 사용설명서를 로드하는데 씀
- dirloader는 txt파일을 로드하는데 쓰임
    - 여기선 금융 어휘 문서를 로드하는데 쓰임
- split 데코레이터
    - 문서를 청킹하는데 씀
    - 우선 불러온 문서에서 page_content와 metadata를 분리
    - page_content에서 필요 없는 부분들을 지움
    - .※·－①②③④⑤을 기준으로 문서를 자름
    - 이 작업이 완료된 후 결과값은 splitted_doc_list라는 리스트에 append
    - splitted_doc_list와 metadata를 하나로 합쳐 Document 형태로 반환

- textsplitter 메서드를 안쓴 이유
    - 마침표 등과 같이 문장이 끝나는 지점을 기준으로 문서를 자르는게 좋다고 생각함
    - 단순히 나열된 정보이기 때문에 이런 식으로 해도 큰 문제는 없을거라 생각


