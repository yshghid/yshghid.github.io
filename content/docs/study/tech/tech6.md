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


- RSI는 연한 회색으로 시각화, 종가를 검정으로 시각화
- 눈으로 보기에도 빨간색에 팔고, 파란색에 사면 좋을거같음.

- window=14가 좋은지 9가 좋은지 등은 테스트해봐야 아므로 2010-2020년 과거 데이터를 기준으로 테스팅을 해보고 어떤 RSI값, 어떤 window 값을 가질지를 정한 후에 2020-2024에 실제 적용을 한다. 

```python
### 모델 특성 조합 정의

import itertools

rsi_ws_list = [7, 9, 14, 15, 21, 28]
rsi_u_list = [70, 75, 80, 85, 90, 95]
rsi_l_list = [5, 10, 15, 20, 25, 30]

params = list(itertools.product(*[rsi_ws_list, rsi_u_list, rsi_l_list]))
print(len(params))
params
```
```plain text
216
[(7, 70, 5),
 (7, 70, 10),
 (7, 70, 15),
 (7, 70, 20),
...
 (28, 95, 10),
 (28, 95, 15),
 (28, 95, 20),
 (28, 95, 25),
 (28, 95, 30)]
```

- 2010-2019까지의 삼성전자 데이터에 대해 가장 우수한 성능을 갖는 window 크기(ws) 및 과매수/과매도 기준(rsi_u, rsi_l)을 찾기.
- 216개 조합을 통해 최적의 파라미터를 찾는다!

```python
for ws in rsi_ws_list:
    df_train[f'RSI_{ws}'] = ta.momentum.rsi(df_train['close'], window=ws).shift(1) # 전날의 rsi 값
```

- RSI 값은 오늘 값이 사용되면 안되니까 전날 값으로 바꿔준다.

- 세팅은? 보유현금 100만원, 슬리피지 0.0025

```python  
train_results = pd.DataFrame(columns=['ws', 'rsi_u', 'rsi_l', 'return', 'num_of_trades'])

for ws, rsi_u, rsi_l in params:

    ################ 백테스팅 파라미터 #############
    holding_cash = 1_000_000 # 보유 현금
    position = 0 # 현재 보유 포지션
    avg_price = 0 # 평단가
    daily_total_value = [] #일별 총 포트폴리오 가치
    slippage = 0.0025 # 슬리피지 
    ################ 백테스팅 파라미터 #############

    n_trades = 0

    # 한 row 씩 루프
    for idx, data in df_train.iterrows():

        if (np.isnan(data[f'RSI_{ws}'])):
            # 이전 rsi 값이 계산된 경우에만 알고리즘 매매 진행
            continue

        daily_total_value.append(0) # 일별 포트폴리오 가치 List에 새로운 값 추가

        # 매수: 과매도 상황(즉, RSI가 rsi_l 이하일 때)
        if (position == 0) and (data[f'RSI_{ws}'] < rsi_l): 
            # 주식 매수 시의 현금 감소, 포지션 증가, 평단가 변화 계산
            position = int(holding_cash / data['close'])
            holding_cash -= position * data['close']
            avg_price = data['close']
            n_trades += 1 # 매수 시점 개수 카운팅을 통해 전체 거래 횟수를 파악

        # 매도: 과매수 상황(즉, RSi가 rsi_u 이상일 때)
        elif (position > 0) and (data[f'RSI_{ws}'] > rsi_u):   
            holding_cash += (position * data['close']) * (1-slippage) # 포지션 매도 시 세금/수수료를 제한 값만 현금으로 돌아옴
            position = 0
            avg_price = 0

        daily_total_value[-1]+= holding_cash + position* data['close'] # 당일 종료 시점에서 보유 현금 + 주식 평가가치로 총 포트폴리오 가치 계산

    train_results.loc[len(train_results)] = [ws, rsi_u, rsi_l, daily_total_value[-1] / 1_000_000, n_trades]
```

1. 첫 며칠은 RSI값이 존재하지 않으니까 RSI 존재하는 경우에만 매매 진행
2. 과매수(RSI가 rsi_l 이하)일때 매도
3. 과매도(RSI가 rsi_l 이하)일때 매수

```python
# 충분히 거래가 되는(즉, train 기간 동안 거래 횟수가 5 초과인) 조합 중, 가장 성능이 좋은 조합을 찾는다.

train_results.loc[train_results.num_of_trades > 5].sort_values(by='return', ascending=False).iloc[:10]
```

![image](https://github.com/user-attachments/assets/39af35b4-71b0-4b0e-b73d-6f1a8bfd3bb8)

```python
# 최적의 조합
ws, rsi_u, rsi_l, _, _ = train_results.loc[train_results.num_of_trades > 5].sort_values(by='return', ascending=False).iloc[0]

print(ws, rsi_u, rsi_l)
```
```plain text
9.0 85.0 30.0
```

- 최적 조합은? window 9, 과매수조건 85, 과매도조건 30.

```python
# test 기간을 통해 매매 전략 성능 파악 

df_test[f'RSI_{ws}'] = ta.momentum.rsi(df_test['close'], window=ws).shift(1)
    
################ 백테스팅 파라미터 #############
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
daily_total_value = [] #일별 총 포트폴리오 가치
slippage = 0.0025 # 슬리피지 
################ 백테스팅 파라미터 #############


n_trades = 0

# 한 row 씩 루프
for idx, data in df_test.reset_index().iterrows():

    if (np.isnan(data[f'RSI_{ws}'])):
        # 이전 rsi 값이 계산된 경우에만 알고리즘 매매 진행
        continue

    daily_total_value.append(0) # 일별 포트폴리오 가치 List에 새로운 값 추가


    # 매수: 과매도 상황(즉, RSI가 rsi_l 이하일 때)
    if (position == 0) and (data[f'RSI_{ws}'] < rsi_l): 
        # 주식 매수 시의 현금 감소, 포지션 증가, 평단가 변화 계산
        position = int(holding_cash / data['close'])
        holding_cash -= position * data['close']
        avg_price = data['close']
        n_trades += 1 # 매수 시점 개수 카운팅을 통해 전체 거래 횟수를 파악

    # 매도: 과매수 상황(즉, RSi가 rsi_u 이상일 때)
    elif (position > 0) and (data[f'RSI_{ws}'] > rsi_u):   
        holding_cash += (position * data['close']) * (1-slippage) # 포지션 매도 시 세금/수수료를 제한 값만 현금으로 돌아옴
        position = 0
        avg_price = 0

    daily_total_value[-1]+= holding_cash+ position* data['close'] # 당일 종료 시점에서 보유 현금 + 주식 평가가치로 총 포트폴리오 가치 계산

print(daily_total_value[-1])
plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
```python
1585862.5
```

![image](https://github.com/user-attachments/assets/ffbf2816-2151-449b-b0dc-ff641a1a67b6)

- 가장 좋은 조합으로 test data에서 돌려보기.
- 1.5배 정도 기록함!

```python
# 전략 총 수익률 계산
total_return_pct = daily_total_value[-1]/daily_total_value[0]
print('총 수익률: {:.2f}%'.format((total_return_pct-1)*100))

print('------------------------------------------------')

# 1년을 250일로 가정, 연 복리 수익률 계산
total_years = len(daily_total_value)/250
print('총 백테스팅 기간: {:.2f}년'.format(total_years))

import math
annaul_return = math.pow(total_return_pct,1/total_years)

print('연 수익률: {:.2f}%'.format((annaul_return-1)*100))

print('------------------------------------------------')

# Sharpe Ratio
daily_return = math.pow(total_return_pct,1/len(daily_total_value))
daily_std = pd.DataFrame(daily_total_value).pct_change().std()[0]

print('일 수익률: {:.2f}%, 일 변동성: {:.2f}%'.format((daily_return-1)*100,daily_std))
print('Sharpe ratio: {:.2f}'.format(((daily_return-1)/daily_std)*np.sqrt(250)))

print('------------------------------------------------')

# MDD 계산
tv = pd.DataFrame(daily_total_value)
dd = tv/tv.cummax()
print('MDD: {:.2f}%'.format((dd.min()-1)[0]*100))


plt.figure(figsize=(10,5))
plt.plot(dd)
plt.show()

print('------------------------------------------------')
```
```plain text
총 수익률: 58.59%
------------------------------------------------
총 백테스팅 기간: 3.91년
연 수익률: 12.52%
------------------------------------------------
일 수익률: 0.05%, 일 변동성: 0.01%
Sharpe ratio: 0.55
------------------------------------------------
MDD: -36.44%
```
- 4년동안 58%정도 수익률, 1년에 약 12%
- MDD는 -36% 정도인데 코로나여서 변동이 좀 있는편.

- 성과 정량화 뿐만아니라 벤치마크와의 비교가 필요하다.
  - 보통 buy & hold 즉 주식을 사고 계속 가지고있었을때와 비교해야함.
  
```python
# 삼성전자 Buy & Hold의 수익률 계산

bm_daily_total_value = df_test['close'].values/df_test['close'].values[0]

# 전략 총 수익률 계산
total_return_pct = bm_daily_total_value[-1]/bm_daily_total_value[0]
print('총 수익률: {:.2f}%'.format((total_return_pct-1)*100))

print('------------------------------------------------')

# 1년을 250일로 가정, 연 복리 수익률 계산
total_years = len(bm_daily_total_value)/250
print('총 백테스팅 기간: {:.2f}년'.format(total_years))

import math
annaul_return = math.pow(total_return_pct,1/total_years)

print('연 수익률: {:.2f}%'.format((annaul_return-1)*100))

print('------------------------------------------------')

# Sharpe Ratio
daily_return = math.pow(total_return_pct,1/len(bm_daily_total_value))
daily_std = pd.DataFrame(bm_daily_total_value).pct_change().std()[0]

print('일 수익률: {:.2f}%, 일 변동성: {:.2f}%'.format((daily_return-1)*100,daily_std))
print('Sharpe ratio: {:.2f}'.format(((daily_return-1)/daily_std)*np.sqrt(250)))

print('------------------------------------------------')

# MDD 계산
tv = pd.DataFrame(bm_daily_total_value)
dd = tv/tv.cummax()
print('MDD: {:.2f}%'.format((dd.min()-1)[0]*100))


plt.figure(figsize=(10,5))
plt.plot(dd)
plt.show()

print('------------------------------------------------')
```
```plain text
총 수익률: 41.30%
------------------------------------------------
총 백테스팅 기간: 3.94년
연 수익률: 9.16%
------------------------------------------------
일 수익률: 0.04%, 일 변동성: 0.02%
Sharpe ratio: 0.35
------------------------------------------------
MDD: -42.20%
```

![image](https://github.com/user-attachments/assets/04b52951-d393-4215-865f-f4aa29248305)


> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ 목록](https://yshghid.github.io/docs/study/tech/tech6/#목록)
