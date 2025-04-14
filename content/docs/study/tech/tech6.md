---
date : 2025-04-14
tags: ['2025-04']
categories: ['python, 주식']
bookHidden: true
title: "추세매매전략, AI 주가예측전략"
bookComments: true
---

# 추세매매전략, AI 주가예측전략

## 목록

*2025-04-14* ⋯ [보조지표로 만드는 추세매매전략](https://yshghid.github.io/docs/study/tech/tech5/#보조지표로-만드는-추세매매전략)

---

## 보조지표로 만드는 추세매매전략

### 1. 개념 

- RSI: 과열 여부 기반 매매
  - 주식의 가격이 '너무'오를 때 팔고, '너무' 내릴 때 사는 전략
  - '너무'의 정의는? 과매도/과매수를 판별하는 기술적 지표(Technical Indicator)를 통해 데이터를 통한 매매.

- Technical Indicator
  - Trend-Following: 가격 움직임의 추세 및 방향 e.g. Simple Moving Average (SMA)
  - Momentum: 가격 움직임의 강도 e.g. Relative Strength Index (RSI)
  - Volatility: 가격 움직임 및 시장의 변동성 e.g. Bollinger Bands (BB)
  - Volume: 체결 수량과 관련 e.g. Money Flow Index (MFI)
  - etc: ...

- RSI (상대 강도 지수)
  - 대표적인 momentum 기술적 지표로, 매수와 매도의 상대적 강도 측정
  - RSI = RS/(1+RS) * 100
  - Where RS = Average Gain / Average Loss
    - Average Gain = 주어진 기간동안 가격이 전날보다 상승한 날의 상승분의 평균
    - Average Loss = 주어진 기간동안 가격이 전날보다 하락한 날의 하락분의 평균
   
  ex) 최근 10일 동안의 가격이 다음과 같을 때 9일 기간에 대한 RSI 계산
  - 100, 102, 104, 102, 103, 101, 99, 103, 98, 100
  - Average Gain = (102+104+104+103+100)/5 = 102.6
  - Average Loss = (102+101+99+98)/4 = 100
  - RS = 102.6/100 = 1.026
  - ∴ RSI = 1.026 / (1+1.026)*100 = 50.64

- RSI는 0이상 100이하 값을 가지며 높을수록 과매수, 낮을수록 과매도로 해석.
  - 일반적으로 70 이상이면 과매수, 30 이하면 과매도로 해석.

### 2. 알고리즘 모델링

- 아이디어: 매 시점, 다음 두 조건 확인
  - 현금을 가지고 있고 RSI가 낮으면 매수
  - 종목을 보유히고 있고 RSI가 높으면 매도

```plain text
For t in time:
  If ( holding_cash > 0 ) and ( RSI(ws) of t-1 < thres_bid ), then BID
  elif ( hodling_stocks > 0 ) and ( RSI(ws) of t-1 > thres_ask ), them ASK
  ( else, do nothing )
```

- 최적의 알고리즘을 구하는 법
  - 알고리즘을 결정하는 주요 변수
    - ws: RSI 계산에 사용되는 과거 데이터 일자 수
    - thres_bid/thres_ask: 과매도/과매수 기준이 되는 RSI 값
  - 모델 학습 및 선정
    - Train: 주요 변수의 다양한 값 조합 (e.g. 과거 14일(ws=14) 데이터로 RSI를 계산하고, RSI가 30 이하면 매수(thres_bid=30), 70 이상이면 매도(thres_ask=70))에 대해 train 데이터로 알고리즘 성능(e.g. 수익률)을 확인하고, 최적의 조합 확인(최적 알고리즘)
    - Test: 최종 선택된 조합에 대해 test 데이터에 대한 테스트 진행 (성능)

- Package ta를 사용하여 RSI 계산
  - https://technical-analysis-library-in-python.readthedocs.io/en/latest/#
  - pip install ta

### 3. 실습

```python
!pip install ta
```

- ta 설치
  - 금융 시계열 데이터로 RSI 등의 기술적 지표(Technical Indicator)를 생성하는 Python library

```python
data_samsung = pd.read_parquet('../chapter4/005930.parquet').set_index('timestamp')
data_samsung = data_samsung[data_samsung.volume > 0] # 거래량이 없는(e.g. 토요일) 날짜 제외
data_samsung
```
![image](https://github.com/user-attachments/assets/9fa440e6-2c52-466d-ac55-335e1bc1605e)


- 데이터 로드: 3531개 시점.

```python
# 데이터 분리

t1, t2, t3 = '2010', '2020', '2024'
df_train = data_samsung.loc[(data_samsung.index >= t1) & (data_samsung.index < t2)].dropna(axis=0)  
df_test = data_samsung.loc[(data_samsung.index >= t2) & (data_samsung.index < t3)].iloc[:-1]   
print(df_train.shape[0], df_test.shape[0])  
```
```plain text
2463 986
```

- train, test 셋을 만들어주기. 

```python
# ta를 이용하여 rsi 생성
import ta

df_train['RSI'] = ta.momentum.rsi(df_train['close'], window=14)
```

- df_train의 종가를 기준으로 rsi 생성
- window는 14일로 설정
- 첫 14일까지는 RSI가 Nan이다.


```python
# 주가 및 생성된 rsi 값 plotting

import matplotlib.pyplot as plt

rsi_u, rsi_l = 70, 30

fig, axs = plt.subplots(1, 1, figsize=(15, 5))
axs.plot(df_train['RSI'], color='darkgrey', label='RSI')
axs.set_xticks(df_train.index[::300])
axs.legend(loc="upper right")
axs_rsi0 = axs.twinx()
axs_rsi0.plot(df_train['close'], color='black', label='Close')
axs_rsi0.scatter(df_train.loc[df_train['RSI'] > rsi_u].index, df_train.loc[df_train['RSI'] > rsi_u, 'close'], color='red', s=10, label='RSI over 70')
axs_rsi0.scatter(df_train.loc[df_train['RSI'] < rsi_l].index, df_train.loc[df_train['RSI'] < rsi_l, 'close'], color='blue', s=10, label='RSI under 30')
axs_rsi0.legend(loc="lower right")
```
![image](https://github.com/user-attachments/assets/a1ce854f-712f-4eac-9f3e-1574d2a4a222)


- 최대 70, 최소 30으로 설정하고 RSI가 높고 낮은 지점을 시각화. 

> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ 목록](https://yshghid.github.io/docs/study/tech/tech6/#목록)
