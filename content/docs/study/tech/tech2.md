---
date : 2025-04-11
tags: ['2025-04']
categories: ['python, 주식']
bookHidden: true
title: "알고리즘 트레이딩으로 주식 매매 자동화봇 만들기 | 정량적 백테스팅 성과 분석"
bookComments: true
---

# 알고리즘 트레이딩으로 주식 매매 자동화봇 만들기 | 정량적 백테스팅 성과 분석

## 목록

*2024-04-12* ⋯ [전략 백테스팅과 수익률 그래프 그리기](https://yshghid.github.io/docs/study/tech/tech2/#전략-백테스팅과-수익률-그래프-그리기)

*2024-04-12* ⋯ [정량적 백테스팅 성과 분석](https://yshghid.github.io/docs/study/tech/tech3/#정량적-백테스팅-성과-분석)

---

## 전략 백테스팅과 수익률 그래프 그리기

### 1. 개념 

- 로직 설명
  - 삼성전자 일봉 사용
  - 최근 5일 종가 중 당일 종가 가격이 가장 낮고, 20일 이동평균보다 종가가 더 낮은 경우 매수 (여기까지만 있다면? 주가가 무한히 떨어지면 무한 매수하게되므로 실거래시에는 사용하기어려움. 그래서 아래 2개 추가)
  - 단, 현재 보유 종목이 있다면 추가매수 없음
  - 매수 3일차 종가에 매도

- 슬리피지 적용
  - 수수료+세금을 슬리피지에 함께 포함
  - 실제 전략 운용시, 내 물량으로 인해 호가가 바뀌거나 주문속도의 차이로 인해 기대 체결가격과 실제 체결가격간 괴리가 발생하는 등 백테스팅에서 고려하기 어려운 괴리가 존재.
  - 위 케이스들을 고려해 백테스팅 툴을 만들수도 있지만, 고난이도의 작업이기때문에 슬리피지를 넉넉히 적용하는 것으로 어느정도 문제 해결
  - 슬리피지 = 수수료+세금+매매가격차이


### 2. 실습

```python
import pandas as pd
import matplotlib.pyplot as plt

d = pd.read_parquet('005930.parquet')
d['5d_max'] = d.rolling(5)['close'].max()
d['5d_min'] = d.rolling(5)['close'].min()
d['last_1d_close'] = d['close'].shift(1)
d['20d_mean'] = d.rolling(20)['close'].mean()
```

```python
# 종가가 5일 최저가 & 종가가 20일 이동평균 아래
# 지금 진입해있는 포지션이 없을 경우에만 진입
# 매도 조건: 마지막 매수 3일 후 매도

# 파라미터 설정
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가

# 일별 총 포트폴리오 가치
daily_total_value = []

holding_time_passed = 0 # 마지막 매수 후 경과 일수


# for 문으로 하루씩 백테스팅 진행
for idx,data in d.iterrows():
    # 하루 시작
    daily_total_value.append(0)

    # 전략 구현
    # 종가가 5일 최저가이고 종가가 20일 이평 아래인지 여부
    # 현재 매수 가능한 현금이 충분히 있는지
    # 매수 조건에 맞으면 매수 진입
    if (data['close'] < data['20d_mean']) and (data['close'] == data['5d_min']):
        if holding_cash > 1*data['close'] and position == 0:
            position += 1
            holding_cash -= 1 * data['close']
            avg_price = data['close'] # 평단가는 오늘 종가
            holding_time_passed = 0

    # 마지막 매수 3일 후 매도
    if position > 0 and holding_time_passed == 3:
        holding_cash += position * data['close']
        position = 0
        avg_price = 0

    # 파라미터 업데이트
    if position > 0:
        holding_time_passed += 1

    # 하루 마무리
    daily_total_value[-1] = holding_cash + position * data['close']

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)

return1 = daily_total_value.copy()
```
![image](https://github.com/user-attachments/assets/2a3de553-b990-466b-a659-7465787e62c3)


```python
################ 백테스팅 파라미터 ################
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
slippage = 0.004 # 슬리피지
daily_total_value = [] # 일별 총 포트폴리오 가치

################ 전략 파라미터 ################
holding_time_passed = 0 # 마지막 매수 후 경과 일수
```

- 슬리피지 0.4% 반영 (수수료+세금 0.2% 가격 괴리 0.2%)
- 매수 시점에 슬리피지 반영하는게 조금더 보수적임
- 매수 매도 따로 슬리피지 반영하는게 조금더 디테일함

```python
# for 문으로 하루씩 백테스팅 진행
for idx,data in d.iterrows():
    daily_total_value.append(0)

    if (data['close'] < data['20d_mean']) and (data['close'] == data['5d_min']):
        if holding_cash > 1*data['close'] and position == 0:
            position += 1
            holding_cash -= 1 * data['close']
            avg_price = data['close']
            holding_time_passed = 0

    # 마지막 매수 3일 후 매도
    if position > 0 and holding_time_passed == 3:
        holding_cash += position * data['close'] * (1-slippage) # 1,000,000 -> -0.4% -> 996,000
        position = 0
        avg_price = 0

    # 오늘의 마무리
    if position > 0:
        holding_time_passed += 1
    
    daily_total_value[-1] = holding_cash + position * data['close']

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)

return2 = daily_total_value.copy()
```
![image](https://github.com/user-attachments/assets/0d7fbac9-cc66-4358-be43-8c8eacd11a26)

- 삼성전자의 가격이 튈때를 반영했을때 이런 그래프가 나온다. 즉 수익률이 좋지 않다.

```python
plt.figure(figsize=(15,8))
plt.plot(return1,c='k')
plt.plot(return2,c='r')
```

![image](https://github.com/user-attachments/assets/500fa8e8-2acf-4092-a944-cd961c9c1bfc)


- 슬리피지 유무에 따른 수익률 차이. 
- 슬리피지가 없을때는 올라가는 느낌이었는데 수수료, 세금 떼고나니까 빠지는중. 현실적인 요인을 반영하면 좋아보였던 전략도 좋지 않을 수 있다.

```python
################ 백테스팅 파라미터 ################
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
slippage = 0.004 # 슬리피지
daily_total_value = [] # 일별 총 포트폴리오 가치

################ 전략 파라미터 ################
holding_time_passed = 0 # 마지막 매수 후 경과 일수
```

- 슬리피지 0.4% 반영 (수수료+세금 0.2% 가격 괴리 0.2%)
- 매수 시점에 슬리피지 반영하는게 조금더 보수적임
- 매수 매도 따로 슬리피지 반영하는게 조금더 디테일함

```python
# for 문으로 하루씩 백테스팅 진행
for idx,data in d.iterrows():
    daily_total_value.append(0)

    if (data['close'] < data['20d_mean']) and (data['close'] == data['5d_min']):
        if holding_cash > 1*data['close']: #<<여기 수정
            position += 1
            holding_cash -= 1 * data['close']
            avg_price = data['close']
            holding_time_passed = 0

    # 마지막 매수 3일 후 매도
    if position > 0 and holding_time_passed == 3:
        holding_cash += position * data['close'] * (1-slippage) # 1,000,000 -> -0.4% -> 996,000
        position = 0
        avg_price = 0

    # 오늘의 마무리
    if position > 0:
        holding_time_passed += 1
    
    daily_total_value[-1] = holding_cash + position * data['close']

return2 = daily_total_value.copy()

plt.figure(figsize=(15,8))
plt.plot(return1,c='k')
plt.plot(return2,c='r')
```

- 보유 주식수랑 상관없이 무한으로 진입할수있다고 한다면?

![image](https://github.com/user-attachments/assets/c2559c85-f21f-4998-9236-3405809a8942)

- 수익률이 크게 바뀐다. 

> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ 목록](https://yshghid.github.io/docs/study/tech/tech2/#목록)

----

## 정량적 백테스팅 성과 분석

### 1. 개념 

- 전략의 성과를 정확하게 분석하기 위해서는 직관적 분석이 아닌 정량적 분석이 필요하다.
  - 연 수익률, 변동성, 최대 손실, 변동 대비 수익률 등의 지표를 활용한다.
  - 퀀트 트레이딩 회사에선 정량적 분석으로 성과 평가하고 비중 조절하는 것이 당연시 됨.


- 주요 정량평가 지표
  - 연 수익률: 내 전략의 1년 수익률
  - 변동성: 일별 수익률의 표준편차. 수익이 얼마나 일정하게 나는지, 포트폴리오 평가가치가 얼마나 빠르게 움직일 수 있는지.
  - Sharpe 지수
    - 수익률/변동성
    - 한 단위만큼의 변동성을 가질 대 얼마나 수익이 잘 나는지 분석. Sharpe 지수가 높을수록 좋음.
    - 가장 많이 보는 지표로, 그만큼 변동을 줄이고 수익을 높이는것이 중요하단것을 의미.
    - '수익률' 대신 '수익률 - Risk free ratio"를 적용할 수 있음 (Risk free ratio는 보통 채권 수익률)
    ![image](https://github.com/user-attachments/assets/90a6d565-bcaf-4ad3-88da-42aa4f3f66dd)
    - 이 지표를 변형한 Sortino Ratio가 있음.

  - MDD (Maximum DrawDown)
    - 고점 대비 최대 하락폭
    - 전략에서 발생할 수 있었던 최대 손실폭을 확인해, 전략의 최대 리스크 파악
    - Sharpe 지수와 함께 가장 중요하게 보는 지표 중 하나

### 2. 실습





> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ 목록](https://yshghid.github.io/docs/study/tech/tech2/#목록)
