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


### VectorStore
문서들을 불러왔으면 LLM이 사용자 질문과 비교할 수 있게 벡터화(임베딩)를 시켜야함
VectorStore는 벡터화시킨 문서를 저장해놓은 저장소

```python
from langchain_community.vectorstores import Chroma
from dotenv import load_dotenv
import os
from langchain_openai import OpenAIEmbeddings

load_dotenv()
api = os.getenv('OPENAPI')
os.environ['OPENAI_API_KEY'] = api
embedding_model = OpenAIEmbeddings(model = "text-embedding-3-small")



def get_vectorstore(docs, collection, dir_path):
    vec_store = Chroma.from_documents(
        collection_name = collection,
        documents = docs,
        embedding = embedding_model,
        persist_directory = dir_path
    )
    return vec_store

def add_new_doc(new_docs, vec_db):
    new_vec_db = vec_db.add_documents(new_docs)
    return new_vec_db

def load_vectorstore(dir_path, collection_name):
    persist_db = Chroma(
        persist_directory = dir_path,
        embedding_function = embedding_model,
        collection_name = collection_name
    )
    return persist_db
```

- DocLoader와 마찬가지로 모두 함수형으로 모듈화
    - get_vectorstore라는 함수가 실행되면 임베딩된 문서들이 모여있는 파일이 생성됨
    - 굳이 클래스로 만들 이유가 없다(굳이 객체마다 속성을 부여할 필요가 없음)

- get_vectorstore
    - documents를 바탕으로 벡터저장소를 만드는 모듈
    - 임베딩 모델로 documents를 임베딩한 후 지정된 경로에 파일로 저장

- add_new_doc
    - 기존의 벡터저장소에 새로운 문서를 추가하는 모듈

- load_vectorstore
    - 기존의 벡터저장소를 객체로 불러오는 모듈
    - 후에 나올 retriever를 만들 때 쓰임

#### Chroma를 쓴 이유
- FAISS의 경우 장점이 gpu를 쓸 수 있다는 것이다.
- 하지만 배포할 때 gpu 사용을 상정하지 않고 개발을 해 굳이 FAISS를 쓸 이유가 없다.
    - 정확히는 당장 쓸만한 gpu가 지원되는 것이 아니기 때문

### retriever
검색기는 사용자의 질문과 가장 유사한 문서를 찾기 위한 모듈이다. \
유사도를 측정하기 위해 보통 코사인 유사도 혹은 MMR(Max Marginal Relevance)이 쓰인다. \
이 검색기를 통해 사용자의 질문과 가장 유사한 문서를 찾을 수 있게 된다. \
이는 곧 사용자의 질문에 가장 적절한 답변을 할 수 있게 된다는 뜻.

```python
from langchain_chroma.vectorstores import Chroma
import VectorStore
from langchain_core.retrievers import BaseRetriever
from langchain_community.document_transformers import LongContextReorder

reorder = LongContextReorder()

class Retriever(Chroma):
    def __init__(self, dir_path, collection_name, searched:int):
        super().__init__()
        self.vec_db = VectorStore.load_vectorstore(dir_path, collection_name)
        self.searched = searched
        
    def as_retriever(self):
        vec_db = self.vec_db
        retriever = vec_db.as_retriever(search_kwargs = {'k' : self.searched})
        return retriever
    
    def get_relevant_documents(self, input):
        retriever = self.as_retriever()
        result = retriever.invoke(input)
        reordered = reorder.transform_documents(result)
        return reordered
```

- 기본적으로 Chroma 클래스를 상속받아 구현
    - 벡터스토어를 검색기로 쓰려고 한 것이니 메서드 오버라이딩을 통해 메서드만 수정을 해주며 제작해도 됨

- as_retriever
    - __init__에서 초기화한 vec_db를 retriever로 사용하게 해주는 기능을 수행
    - 이를 통해 retriever를 생성함

- get_relevant_documents
    - VectorstoreRetriever의 get_relevant_documents를 오버라이딩 한 것
    - invoke의 역할을 수행
    - reordered
        - 찾은 문서가 여러개일 때 재정렬을 함
        - 이를 통해 관련도가 낮은 문서는 목록의 중간에 위치, 관련도가 높은 문서는 시작과 끝에 위치하게 함
        - 사용자에게 더 정확한 정보를 제공하기 위해 추가한 기능




[참고자료](https://python.langchain.com/docs/integrations/vectorstores/) \
[참고자료2](https://python.langchain.com/docs/how_to/vectorstore_retriever/) \
[참고자료3](https://python.langchain.com/api_reference/core/vectorstores/langchain_core.vectorstores.base.VectorStoreRetriever.html) \
[참고자료4](https://python.langchain.com/api_reference/community/document_transformers/langchain_community.document_transformers.long_context_reorder.LongContextReorder.html)