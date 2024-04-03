---
layout : post
categories : vision
title : EigenPlaces 논문 리뷰
tags : [vision, review]
date-string : April 03, 2024
---

# EigenPlaces:Training Viewpoint Robust Models for Visual Place Recognition review

## Visual Place Recognition
- 이미지의 장소를 외형적인 특징들 만으로 예측하는 것
- 일반적으로 db에 있는 지리 정보가 태깅된 이미지 중에서 가장 비슷한 이미지를 검색하는 것으로 이루어짐
- 다른 관점에서 보여진 장소를 같은 장소로 인식하는 게 주요 문제

## EigenPlaces
- 모델에게 같은 물체의 다른 관점들을 훈련 데이터로 클러스터링하여 제시하는 것
- 