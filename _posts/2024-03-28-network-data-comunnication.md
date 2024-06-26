---
layout : post
categories : network
title : 컴퓨터 네트워크 데이터 통신 리뷰
tags : [network]
date-string : March 28, 2024
---

# 데이터 링크 제어 (Data Link Control)

## DLC 서비스
### 프레임 짜기
- 비트들을 프레임으로 만들고 각 프레임은 다른 프레임과 구분
- 프레임 길이
  - 고정 길이 프레임
  - 가변 길이 프레임
    - 주로 LAN에서 사용

### 문자 중심 프레임 짜기
- 전달되는 데이터는 ASCII와 같은 부호화 시스템의 8비트 문자
- 시작과 마지막에 8비트 플래그를 추가
- 바이트 채우기(stuffing)와 빼기(unstuffing)
  - 정보 내에 플래그로 사용되는 패턴이 있을 경우 이를 플래그로 오인하지 않게 하기 위한 것
  - 바이트 채우기는 텍스트에 플래그나 ESC 문자가 있을 때 여분의 1바이트를 추가

### 비트 중심 프레임 짜기
- 프레임의 데이터 부분을 전부 bit 열로 인식
- '01111110' 8비트 패턴의 플래그 사용
- 비트 채우기(stuffing)와 빼기(unstuffing)
  - 수신자는 데이터 속의 '01111110'을 플래그로 오인하지 않도록 하는 것
  - 0뒤에 연속하는 5개의 1이 있게 되면 0을 추가로 채우는 과정

# 오류의 유형
- 단일 비트 오류(single-bit error)
  - 데이터 단위 중 오직 하나의 비트만이 1 -> 0, 0->1로 변경되는 오류를 의미
- 폭주 오류(Burst Error)
  - 데이터 단위에서 2개 이상의 연속적인 비트들의 오류

# 검출 대 정정
- 오류 검출 : 오류가 있는지 여부만을 알아내는것
- 오류 정정 : 정확하게 몇 비트가 잘못되었는지 알아야 하며, 더욱이 어디가 잘못되었는지를 알아야 하는 것
- 오류 정정은 검출보다 어려움

# 전진 오류 정정 대 재전송
- 전진 오류 정정 : 수신자가 메시지의 중복 비트를 이용하여 메시지의 원래 데이터를 알아내서 오류를 제거하는 과정
- 재전송 : 오류 발생을 검출하여 송신자에게 다시 보내줄 것을 요청하는 것

# 순환 코드
## 순환 중복 검사
- 오류를 정정하기 위한 순환 코드를 생성
- LAN이나 WAN에서 많이 사용


# 임의 접근
## 임의 접근 방식
- 경쟁 방식
- 어떤 지국도 다른 지국보다 우월하지 않으며, 다른 지국에 대해 제어권 행사 X
- 전송할 데이터가 있는 지국은 전송할지 말지를 결정하기 위해 프로토콜에서 정해진 절차를 따름
- 지국이 전송하기 위한 예정된 시간 없음. 전송은 지국 사이에서 임의로 이뤄짐
- 어느 지국이 다음 번에 전송해야 하는지 아무 규칙이 없음. 지국들은 매체에 전송하기 위해 서로 경쟁
- 2개 이상의 지국이 동시에 전송하려고 하면 접근에 대한 충돌이 발생하며 프레임은 손상, 변형될 수 있음

## 에너지 준위
- 0준위 : 휴지 상태
- 정상 : 채널을 확보하여 프레임 전송
- 비정상 : 충돌 발생, 정상 준위의 2배