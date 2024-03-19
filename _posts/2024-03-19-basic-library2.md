---
layout : post
categories : python, project
title : 파이썬 표준 라이브러리2
tags : [python, project]
date-string : March 19, 2024
---

# 파이썬 표준 라이브러리 2

## 데이터베이스 라이브러리로 연결

### SQLITE
- SQLITE 라는 데이터베이스를 사용하는데 필요한 파이썬 표준 라이브러리
- 가벼움. 개발용이나 소규모 프로젝트에 어울림

```python
# SQLITE3
# https://sqlitebrowser.org/dl/

import sqlite3
sqlite3.version #sqlite3라는 라이브러리 버전
sqlite3.sqlite_version #이 라이브러리에서 사용하는 sqlite 버전

conn = sqlite3.connect('test.db')
#커서 생성
c = conn.cursor()
#쿼리문 작성
query = '''CREATE TABLE test (ID INTEGER PRIMARY KEY, PRODUCT_NAME TEXT, PRICE INTEGER)'''
c.execute(query)
#데이터 조회
#fetchone(), fetchmany(), fetchall()
#SELECT문으로 조회한 다음 원하는만큼 fetch
import sqlite3
import pandas as pd
conn = sqlite3.connect("***.db")
c = conn.cursor()
c.execute('''SELECT * FROM sqlite_master WHERE type = "table"''')
pd.DataFrame(c.fetchall()) #전부 다 불러오기
pd.DataFrame(c.fetchone()) #하나만 가져오기
pd.DataFrame(c.fetchmany(size=3)) #지정한 개수만큼 가져와짐
c.description #db에 어느 키가 있는지 확인
conn.close() #연결해제
```

```python
conn = sqlite3.connect('test.db')
c = conn.cursor()
query = '''CREATE TABLE test (ID INTEGER PRIMARY KEY, PRODUCT_NAME TEXT, PRICE INTEGER)'''
c.execute("INSERT INTO test VALUES(1, '모자', 15000)")
conn.commit()
c.execute("INSERT INTO test(ID, PRICE, PRODUCT_NAME) VALUES(?, ?, ?)", (4, 550000, '블라우스'))
c.execute("UPDATE test SET PRICE = :price WHERE ID = :id", {"price" : 55000, "id":6})
conn.commit()
c.execute("DELECT FROM test WHERE ID = ?", (8,))
conn.commit()
c.execute("DELECT FROM test")
conn.commit()
```

데이터베이스 백업

```python
c.executemany("INSERT OR REPLACE INTO test(ID, PRODUCT_NAME, PRICE) VALUES(?, ?, ?)", product_list)
conn.commit()
for line in conn.iterdump(): #쿼리문 출력
    print(line)
with conn:
    with open('backup.sql', 'w')as f:
        for line in conn.iterdump():
            f.write('%\n' % line)
        print('Completed')
c.executescript(sql_script)
conn.commit()
```

### tkinter
블로그 데이터를 관리한다고 가정
DB GUI가 있음
GUI에는 버튼, 텍스트 창 등등이 있다
GUI에 생성하면 DB에 저장
DB에 있는 정보를 GUI에 띄움
GUI에서 삭제를 누르면 DB에서 사라짐

```python
# ID 숫자,  subject 문자열, content 문자열, date 문자열
import sqlite3
conn = sqlite3.connect('blog.db')
c = conn.cursor()
c.execute('''CREATE TABLE blog (id integer PRIMARY KEY, subject text, content text, date text)''')

# tkinter
#파이썬 GUI 프로그램 만드는 대표적인 라이브러리
# PyQT5 - 외장라이브러리, 좀 더 멋진 프로그램 만들기 가능, 기능들도 많음
# 간단한 수준에서 필요한 기능이 모두 있음 tkinter

from tkinter import *
root = Tk() #창을 하나 만들어놓고 실행
root.geometry('300x200') #창 크기 설정
label = Label(root, text = 'Hello World!')
label.pack()
root.mainloop()
```

리스트 박스 ListBox
- 사용자가 선택할 수 있는 목록

라벨 Label
- 한 행짜리 문자열 출력

엔트리 Entry 
- 사용자가 입력할 수 있는 한 행짜리 입력 창

텍스트 Text
- 사용자가 입력할 수 있는 여러 행짜리 입력 창

버튼 Button
 - 버튼

```python
for i in ['첫 번째 요소', '두번째 요소', '세번째요쇼']:
    listbox.insert(END, i)

listbox.bind('<<ListboxSelect>>', event_for_listbox)
root.mainloop()

entry = Entry(root)
entry.insert(0, "데이터를 입력해 주세요")
entry.bind("<Return>", event_for_entry)
entry.pack()
root.mainloop()

data = '''첫번째 데이터 
두번째 데이터 
세번째 데이터'''

text = Text(root)
text.insert(1.0, data)
text.pack()
root.mainloop()

def btn_click(event):
    print(f"버튼이 클릭되었습니다!")
b1 = Button(root, text = '1')
b1.bind('<Button-1>', btn_click)
b1.pack()
root.mainloop()
```

Pack
- 부모 컴포넌트에 자동 패킹 - 불필요한 공간 없앰

Place
- 절대좌표로 위치시킴 - 창 크기와 무관하게 고정

Grid
- 테이블 레이아웃으로 배치 - 엑셀처럼

askyesno
- 경고팝업창

showerror
- 잘못 입력했을 때 창 띄워주는 팝업

refresh
- 필요할 때마다 함수 호출. 새로고침

### exe 실행파일 만들기
- pyinstaller 외장 라이브러리 설치 필요
- 다른 사람이 내 파이썬 프로그램 쓰려면 파이썬, 외장 라이브러리, 프로그램 전달, 프로그램 사용법 설명 다 필요함
- 실행파일을 만들면 이런 수고가 없어짐

```bash
pip install pyinstaller
```

