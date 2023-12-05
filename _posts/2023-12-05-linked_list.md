layout : post
author : jayiuk
tags : ["linked_list"]

# 연결 리스트
---
## 리스트
 - 데이터에 순서를 매겨 늘어놓은 자료구조
 - 구조가 단순한 리스트로 선형 리스트 또는 연결 리스트가 있음
 - 스택과 큐도 리스트 자료구조에 속함
   - 파이썬의 리스트 자료형과는 다름

## 연결 리스트
 - 노드
   - 연결 리스트의 각각의 원소
   - 데이터와 뒤쪽 노드를 참조하는 포인터로 구성되어 있음
   - 머리노드 : 연결리스트 맨 앞에 있는 원소
   - 꼬리노드 : 연결리스트 맨 뒤에 있는 노드
   - 앞쪽노드 : 어떤 노드의 바로 앞에 있는 노드
   - 뒤쪽노드 : 어떤 노드의 바로 뒤에 있는 노드

## 배열로 연결 리스트 만들기
 - 어떤 그룹의 회원 정보 데이터를 나타내는 튜플
   - int형 회원번호, str형 이름, str형 전화번호
   - list형 배열인 data의 원소는 7개, data[5:]는 등록되지 않은 상태
 - 뒤쪽 노드 꺼내기
   - 인덱스 값을 1씩 증가시켜 원소에 접근
 - 노드의 삽입과 삭제
   - 회원번호가 55인 회원을 추가하려면 추가한 원소 이후의 모든 원소가 하나씩 이동해야함
   - 삭제하는 경우에도 배열 안의 일부 원소를 모두 이동해야함
   - 데이터를 삽입, 삭제함에 따라 데이터를 옮겨야 해서 효율적이지 않음

## 포인터를 이용한 연결 리스트
 - 데이터를 삽입할 때 노드용 인스턴스를 생성하고, 데이터를 삭제할 때 노드용 인스턴스를 소멸
 - Node 클래스
   - 데이터용 필드 data와 별도로 자신과 같은 클래스형의 인스턴스를 참조하기 위한 참조용 필드 next로 구성(data : 데이터에 대한 참조, next : 노드에 대한 참조(꼬리노드의 next는 None))
   - 자기 참조형 구조 : 자신과 같은 형의 인스턴스를 참조하는 필드가 있는 구조

## 노드 클래스
 - 필드
   - data : 데이터
   - next : 뒤쪽 포인터

## 파이썬의 리스트 자료형
 - 연결 리스트는 임의의 위치에 원소를 삽입하거나 삭제할 때 빠르게 수행할 수 있다는 장점, 메모리와 속도에 대해서는 배열보다 효율이 떨어짐
 - 파이썬의 리스트 자료형은 연결 리스트의 자료구조가 아님
   - 모든 원소를 메모리에 연속적으로 배치하는 배열로 내부적으로 구현되어 있음

```python
from __future__ import annotations
from typing import Any, Type
class Node:
    def __init__(self, data = None, next = None):
        self.data = data
        self.next = next
class LinkedList:
    def __init__(self) -> None:
        self.no = 0
        self.head = None
        self.current = None
    def __len__(self) -> int:
        return self.no
    def search(self, data:Any) -> int:
        cnt = 0
        ptr = self.head
        while ptr is not None:
            if ptr.data == data:
                self.current = ptr
                return cnt
            cnt += 1
            ptr = ptr.next
        return -1
    def __contains__(self, data : Any) -> bool:
        return self.search(data) >= 0
    def add_first(self, data : Any) -> None:
        ptr = self.head
        self.head = self.current = Node(data, ptr)
        self.no += 1
    def add_last(self, data : Any):
        if self.head is None:
            self.add_first(data)
        else:
            ptr = self.head
            while ptr.next is not None:
                ptr = ptr.next
            ptr.next = self.current = Node(data, None)
            self.no += 1
    def remove_first(self) -> None:
        if self.head is not None:
            self.head = self.current = self.head.next
            self.no -= 1
    def remove_last(self):
        if self.head is not None:
            if self.head.next is None:
                self.remove_first()
            else:
                ptr = self.head
                while ptr.next is not None:
                    pre = ptr
                    ptr = ptr.next
                pre.next = None
                self.current = pre
                self.no -= 1
    def remove(self, p : None) -> None:
        if self.head is not None:
            if p is self.head():
                self.remove_first()
            else:
                ptr = self.head
                while ptr.next is not p:
                    ptr = ptr.next
                    if ptr is None:
                        return
                ptr.next = p.next
                self.current = ptr
                self.no -= 1
    def remoce_current_node(self) -> None:
        self.remove(self.current)
    def clear(self) -> None:
        while self.head is not None:
            self.remove_first()
        self.current = None
        self.no = 0
    def next(self) -> bool:
        if self.current is None or self.current.next is None:
            return False
        else:
            self.current = self.current.next
        return True
    def print_current_node(self) -> None:
        if self.current is None:
            print("주목 노드 존재 X")
        else:
            print(self.current.data)
    def print(self) -> None:
        ptr = self.head
        while ptr is not None:
            print(ptr.data)
            ptr = ptr.next
    def __iter__(self) -> LinkedListIterator:
        return LinkedListIterator(self.head)
class LinkedListIterator:
    def __init__(self, head : Node):
        self.current = head
    def __iter__(self) -> LinkedListIterator:
        return self
    def __next__(self) -> Any:
        if self.current is None:
            raise StopIteration
        else:
            data = self.current.data
            self.current = self.current.next
            return data
```  
---
## 연결 리스트의 길이 구하기
 - 빈 연결 리스트
   - 연결 리스트가 비어있을 때 head값은 None
 - 노드가 1개인 연결 리스트
   - 헤드가 참조하는 노드 A는 머리노드이자 꼬리노드
   - head가 참조하는 뒤쪽 포인터의 값이 헤드
 - 노드가 2개인 연결 리스트
   - head가 머리 노드 A를 가리키며, 노드 A의 뒤쪽 포인터 넥스트가 꼬리 노드 B를 가리킴
 - 꼬리 노드 여부 판단
   - Node형인 변수 p가 리스트에 있는 노드를 참조할 때, p의 next 필드가 None이면 p는 꼬리노드

## 이터러블 객체와 이터레이터의 구현
 - 이터러블 객체
   - 원소를 1개씩 꺼내는 구조의 객체
   - str형 문자열, list형 리스트, tuple형 튜플

 ---
## 커서를 이용한 연결리스트
### 포인터를 이용한 연결리스트의 한계
 - 포인터를 이용한 연결리스트는 노드를 삽입, 삭제할 때 데이터를 이동하지 않고 처리
   - 노드를 삽입, 삭제할 때마다 내부에서 노드용 인스턴스를 생성하고 소멸함
 - 메모리를 확보하고 해제하는데 많은 비용이 소모
### 커서를 이용한 연결리스트
 - 프로그램 실행 중 데이터 개수가 크게 변하지 않거나 데이터 최대 개수를 예측할 수 있는 경우엔 배열 안의 원소를 사용하여 효율적으로 운용함
 - 커서
   - int형 정숫값인 인덱스로 나타낸 포인터
   - 노드 B의 뒤쪽 커서 3은 뒤쪽 노드가 인덱스 3의 위치에 있는 노드 C라는 의미
 - 노드의 삽입과 삭제에 따른 원소의 이동이 처음부터 불필요함
 - 맨앞에 노드 G를 삽입하는 과정
   - 배열 안에서 가장 끝쪽에 있는 인덱스 위치에 저장
      - 연결 리스트 상에서 맨 끝이 아님
      - 배열에서 물리적인 이치 관계와 연결 리스트의 논리적인 순서 관계가 일치하지 않음
   - head를 1에서 6으로 업데이트 하고 노드 G의 뒤쪽 커서를 1로 설정함
### 삭제된 노드 관리
 - 연결 리스트에서 A, B, C, D로 연결된 상태
 - 맨 앞에 새로운 노드 E삽입
   - 인덱스 4의 위치에 노드 E 저장(4번째 레코드)
 - 3번째 레코드 노드 B를 삭제
 - 삭제를 여러번 하면 배열 안에 빈 레코드가 많이 생김

 ### 프리 리스트
  - 삭제된 레코드 그룹을 관리할 때 사용하는 자료구조
  - 원래 데이터의 순서를 관리하는 연결리스트와 결합하여 구현
  - 노드 클래스 Node에 추가된 필드
    - dnext : 프리 리스트의 뒤쪽 노드를 참조하는 커서
  - 연결 리스트 ArrayLinkedList에 추가된 필드
    - deleted : 프리 리스트의 머리 노드를 참조하는 커서
    - max : 배열에서 맨 끝 쪽에 저장되는 레코드 번호
  - 연결 리스트에 원소 삽입 시, 프리 리스트가 비어있지 않으면 프리 리스트의 머리 노드에 원소를 저장하고, 비어 있으면 배열에서 맨 끝에 저장
  - 연결 리스트에서 노드 삭제 시, 삭제한 레코드 인덱스를 프리 리스트의 머리 노드로 등록
  
  ---
# 파이썬의 논리 연산자
 - 많은 프로그래밍 언어에서 논리 연산자는 평가 결과로 True, False 등의 불리언 값을 생성
 - 파이썬의 논리 연산자는 다른 프로그래밍 언어와 다르게 동작함
 - x and y : x를 평가하여 거짓이면 그 값을 생성, 그렇지 않으면 y를 평가하여 그 값을 생성
 - x or y : x를 평가하여 참이면 그 값을 생성, 그렇지 않으면 y를 평가하여 그 값을 생성
 - not x : x가 참이면 False, 거짓이면 True 생성
   - and연산자와 or연산자가 생성하는 값은 가장 마지막에 평가한 식의 결과값
     - 5 or 3의 평가 결과값은 5
     - 0 or 3의 평가 결과값은 3
   - and연산자와 or연산자는 논리 연산의 평가 결과가 왼쪽 피연산자의 평가 결과만으로 확정되는 경우, 오른쪽 피연산자의 평가를 생략하는 단축 평가를 수행

---

# 원형리스트
 - 연결리스트의 꼬리 노드가 다시 머리 노드를 가리키는 모양
 - 고리 모양으로 늘어선 데이터를 표현하는데 알맞은 자료구조
 - 원형 리스트의 꼬리 노드의 뒤쪽 포인터가 None이 아닌 머리 노드의 포인터 값이 됨

---

# 이중 연결 리스트
 - 연결 리스트의 가장 큰 단점은 뒤쪽 노드를 찾기 쉬운 반면, 앞쪽 노드를 찾기 어렵다는 것
 - 이중 연결 리스트의 각 노드는 뒤쪽 노드에 대한 포인터와 앞쪽 노드에 대한 포인터가 주어짐
   - data : 데이터 참조
   - prev : 앞쪽 노드 참조
   - next : 뒤쪽 노드 참조

---

# 원형 이중 연결 리스트
 - 원형리스트와 이중 연결 리스트를 결합
 - 원형 이중 연결 리스트에서 개별 노드의 형은 이중 연결 리스트와 동일

---

self.prev = prev or self
self.next = next or self
 - prev 또는 next로 전달받은 인자가 None이 아닌(참)경우 prev 또는 next를 대입하여 참조
 - prev 또는 next로 전달받은 인자가 None(거짓)인 경우 self를 대입하여 자신의 인스턴스 참조

 add() : 주목 노드 바로 뒤에 노드를 삽입하는 매서드
  - 연결 리스트와 달리 더미 노드가 있어, 빈 리스트에 삽입이나 리스트 맨 앞에 삽입 처리하는 과정 불필요
add_first() : 머리에 노드를 삽입하는 경우
add_last() : 꼬리에 노드를 삽입하는 경우

reversed()
 - 앞쪽으로 스캔하기 위한 이터레이터 반환

self.current.next = self.current.next.prev = node
 - C언어, 자바등에서 '='은 대입 연산자로 오른쪽 결합 연산자(정상 동작)
   - self.current.next.prev = node
   - self.current.next = self.current.next.prev
 - 파이썬에서 '='은 연산자가 아닌 대입 명령문으로 연속 대입의 경우 다음과 같이 동잦(오동작)
   - self.current.next = node
   - self.current.next.prev = node