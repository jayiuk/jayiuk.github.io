---
published : true
layout : post
categories : DB, sql
title : 데이터 모델링
tags : [DB, sql]
date-string : April 25, 2024
---

# 데이터 모델링

## 정의
- 일상 생활에서 자주 사용되는 모델링의 정의
  - 우리가 살고 있는 3차원의 현실 세계를 단순화하여 표현
  - 현실 세계를 추상화하여 그 구조를 표현
  - 현실 세계에 존재하는 사물이나 사건에 관한 관점 및 양상을 연관된 주체를 위하여 명확하게 하는것

### 핵심
- 복잡한 것을 단순화시켜 표현 -> 명확하게 함

## 특징
- 추상화 : 복잡한 것 -> 일정한 형식에 맞게 표현
- 단순화 : 서로가 약속한 규약을 준수하는 표기법이나 언어로 표현
  - 모두가 알아들을 수 있게 한다는 의미

- 명확화
  - 명확하게 기술
  - 애매모호함 제거 -> 여러 사람이 이해하기 쉽게

### 모델링이란 복잡한 현실 세계를 추상화, 단순화 명확화하기 위해 일정한 표기법으로 모델을 표현하는 기법

## 모델링의 3가지 관점
- 데이터 관점(Data, What) : 비즈니스와 관련된 데이터는 무엇인지, 데이터 간의 관계는 무엇인지에 대한 관점
- 프로세스 관점(Process, How) : 해당 일로 인해 어떤 일이 일어나는지에 대한 관점
- 상관 관점(Data, Process) : 위의 두 관점 간 서로 어떤 영향을 받는지에 대한 관점

## 데이터 모델링의 정의
- 현실 세계의 비즈니스를 IT 시스템으로 구현하기 위해 데이터 관점으로 업무를 분석하는 기법
- 현실 세계의 비즈니스를 약속된 표기법으로 표현하는 과정
- DB를 구축하기 위한 분석 및 설계의 과정

### 대부분의 IT 시스템은 관계형 데이터베이스(RDBMS, Relational DataBase Management System) 기반으로 구축
### 데이터 모델링을 통해 정의된 데이터 모델을 기반으로 물리적인 DB가 구축
### SQL문을 활용 -> 데이터가 INSERT 입력, UPDATE 수정, DELETE 삭제, SELECT 조회

## 데이터 모델의 기능
- 가시화 : IT 시스템의 모습을 가시화
- 명세화 : IT시스템의 구조와 발생하는 동작을 명세화
- 구조화된 틀 제공 : IT 시스템을 구현하는데 필요한 구조화된 틀 제공
- 문서화 : IT 시스템 구축 시 산출물로 사용되는 문서 제공
- 다양한 관점 제공 : 다른 영역의 세부사항을 숨김 -> 다양한 영역에 집중할 수 있는 관점 제공
- 상세 수준의 표현 방법 제공 : 원하는 목표에 따라 구체화된 상세 수준의 표현 방법을 제공

## 데이터 모델의 중요성
- 파급효과(Leverage) : 데이터 설계 과정에서 비효율적인 데이터 설계, 업무 요건을 충족하지 못하는 데이터 설계 -> 개발/테스트/오픈/운영의 전 과정에서 엄청난 비용 발생 가능
- 복잡한 정보 요구사항의 간결한 표현(Conciseness) : 좋은 데이터 모델 설계 -> IT 시스템에서 구현해야 할 정보 요구사항을 명확하고 간결하게 표현 가능
- 데이터 품질(Data Quality)
  - 데이터 모델의 잘못된 설계로 인해 데이터 중복, 비유연성, 비일관성 발생 가능
  - 데이터 중복, 비유연성, 비일관성으로 인해 데이터 품질 저하될 수 있다.

## 데이터 모델링의 3단계 진행
- 현실 세계  -개념 데이터 모델링(추상적)-> 개념적 구조 -논리 데이터 모델링-> 논리적 구조 -물리 데이터 모델링(구체적)-> 물리 구조(데이터베이스)

### 데이터 모델링의 3단계
- 개념적 데이터 모델링
  - IT 시스템에서 구현하고자 하는 대상에 대해 포괄적 수준의 데이터 모델링을 진행
  - 전사적 데이터 모델링 시 많이 사용하는 단계

- 논리적 데이터 모델링
  - IT 시스템에서 구현하고자 하는 비즈니스를 만족하기 위한 기본키, 속성, 관계, 외래키 등을 정확하게 표현하는 단계

- 물리적 데이터 모델링
  - 논리 데이터 모델을 기반으로 실제 물리 DB 구축을 위해 성능, 저장공간 등의 물리적인 특성을 고려하여 설계하는 단계