---
published : true
layout : post
categories : ML, AI, project
title : 따릉이 고장 예측 구현 part.1
tags : [ML, project]
date-string : June 25, 2024
---
# 따릉이 고장 예측 모델 구현(1) - 기획 및 분석 단계

## 기획 배경

- 사실 학교 과제 때문에 시작한 프로젝트이긴 함
- 따릉이 관리 인력이 부족해 고장난 자전거가 제때 수리되지 않는다는 기사를 본 적이 있음
- 또한 따릉이 고장 신고를 하지 않는다면 고장이 났을 때 제때 수리하기 힘들다
- 만약 고장 예측을 해 미리 확인이 가능하면 인력 소모가 줄어들거라 예상

## 사용 데이터

- [일별 공공자전거 이용 내역 2023년](https://data.seoul.go.kr/dataList/OA-15246/F/1/datasetView.do)
- [자전거 고장신고 내역 2023년](https://data.seoul.go.kr/dataList/OA-15644/F/1/datasetView.do)

## 데이터 분석

### 데이터 불러오기

- 일별 공공자전거 이용 내역의 경우 월 단위로 저장된 데이터
- 우선 12개 다 불러와서 concat하는 방식으로 하나의 데이터 생성
- 그 후 대여 일자를 기준으로 그룹화
  - 각 날짜마다 이용건수, 이용시간, 이동거리 합산
  - 대여일자는 datetime으로 데이터타입 변경
- 고장신고 내역도 2023년 데이터는 3개로 쪼개진 데이터
  - 위와 같은 방식으로 concat진행
- 날짜를 기준으로 그룹화
  - 각 날짜마다 고장난 횟수 합산
- 그 후 둘의 데이터를 날짜를 기준으로 merge
- 위의 과정을 거쳐 데이터를 준비

### 기초 데이터 분석

- 이 단계에선 우선 Null값이 있는지 확인
  - 확인했을 때 없다면 평균 등의 대푯값을 확인해 채우기 혹은 그 부분은 버리기 둘 중 하나를 진행해야함
- 없는걸 확인 후 상관계수 확인
  - 이용시간, 이동거리, 이용횟수, 고장횟수 간의 상관계수가 기본적으로 선형이 나와야 그 다음으로 진행 가능

![bicycle_corr](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/5f7c5d7d-f8fc-4f3c-867a-757c6b8ee3d7)

- 이용시간, 이동거리, 이용횟수 와 고장횟수 간의 상관관계는 각각 0.78, 0.78, 0.87이 나와 어느정도 양의 관계가 있다고 판단
- 이용건수와 이동거리간의 상관계수는 0.95정도 나온다. 이용건수와 이용시간의 상관계수도 마찬가지로 0.95가 나온다. 이용시간과 이동거리의 상관계수는 0.99가 나온다.
- 이 세가지 변수는 서로 간의 반례(이용시간이 많은데 이동거리가 적은 경우)를 만들지 않는다고 볼 수 있다.
- 대여소번호의 경우 우연의 일치로 봐야한다. 이는 임의로 정해진 숫자이기 때문이다. 고장이 많은 곳일수록 대여소번호 숫자가 올라가지 않기 때문이다.

### 라벨링

- 위험도를 직관적으로 나타내기 위해 티어를 나누기로함
- 고려해야 할 변수가 많아 대푯값을 기준으로 나누기엔 힘들다고 판단
- 사이킷런의 KMeans를 사용해 클러스터링
  - 비지도 학습이고, 비슷한 특성을 지닌 데이터끼리 k개의 군집을 만들기 때문에 이번 프로젝트에 적합하다고 판단
  - kmeans로 클러스터링 한 후 결과를 시각화한 후 A, B, C, D로 나눌 예정

### 시각화 결과

![bbc_clustering_viz_sum](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/67a0be31-5eb3-4a38-93d9-f09638dd5eaa)
![bbc_clustering_viz_mean](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/4a667727-896b-4a60-9109-6a648616b1f6)
![bbc_clustering_viz_median](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/7291e85c-666b-49b8-9481-9b6695a64dee)
![bbc_clustering_viz_max](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/9802a91a-c5c6-43f6-8e7b-d6ff7fff3e34)

- 위의 사진들은 클러스터링을 한 후 각 그룹별로 시각화한 모습이다
- 순서대로 총합, 평균, 중앙값, 최댓값을 대푯값으로 한 후 시각화한 것이다
- 모두 0, 3, 1, 2의 순서로 높은 걸 알 수 있다

![bbc_clustering_viz_break](https://github.com/jayiuk/jayiuk.github.io/assets/58243784/34e752d8-1fbe-4f7d-8303-3160eaa51f43)

- 위 사진은 고장 횟수를 시각화한 결과다
- 위 결과 역시 0, 3, 1, 2의 순서로 나타난다
- 즉 위험도는 0, 3, 1, 2의 순서로 나타내면 될 것이다
