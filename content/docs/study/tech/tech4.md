---
date : 2025-04-12
tags: ['2025-04']
categories: ['python, 주식']
bookHidden: true
title: "백테스팅 #1 (2025년 4월 11일 삼성전자)"
bookComments: true
---

# 백테스팅 #1 (2025년 4월 11일 삼성전자)

#2025-04-13

---

> - 복습삼아!! 주가 데이터를 FinanceDataReader로 가져와서 돌려보았다.
> - 환경은 jupyter notebook이고 python version 3.8이다. 

```python
!python --version
```
```plain text
Python 3.8.19
```

### 1. Install Packages

```python
!pip install plotly
!pip install finance-datareader
```

### 2. Load Data

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import FinanceDataReader as fdr

d = fdr.DataReader('005930', '2010')

d['timestamp'] = d.index.tolist()
d = d.reset_index()
d.columns = [col.lower() for col in d.columns]
d['ticker'] = '005930' 
d = d[['timestamp', 'ticker', 'open', 'high', 'low', 'close', 'volume']]
d
```
![image](https://github.com/user-attachments/assets/1e05f50b-ceb8-4396-a769-6570e8b816c2)

- 삼성전자 주가 데이터를 2010년부터 현재까지로 찍어왓다. 2000년부터 되는데 실습이 2010년부터길래, 내가 제대로하는건지 비교해야대서...
- 실습 데이터는 20240429까지라 3534개 row였는데 나는 2025-04-11까지라 3764개 row를 갖는다.

```python
plt.figure(figsize=(15,8))
plt.plot(d['close'])
```
![image](https://github.com/user-attachments/assets/f5eaa669-7b8d-4ebc-aff0-f2d8010e2774)

- 종가 시각화

![image](https://github.com/user-attachments/assets/bfc202eb-f330-4c5b-8418-eca618e1048c)

- 웃긴게 ㅠㅠ 왼쪽이 실습데이터고 오른쪽이 1년뒤인 오늘 데이터인데 1년만에 주륵주륵 떨어지는 양상을 보임

```python
# 주가 그래프에 매수 타점 표시
plt.figure(figsize=(15,8))
plt.plot(d['close'])
plt.scatter(buy.index,buy['close'],c='r',s=5)

# 최근 300일 매수 타점 표시
d_sample = d.iloc[-300:]
buy_sample = d_sample[(d_sample['close'] == d_sample['5d_min']) & (d_sample['close'] < d_sample['20d_mean'])]
print(buy_sample.shape)

plt.figure(figsize=(15,8))
plt.plot(d_sample['close'])
plt.scatter(buy_sample.index,buy_sample['close'],c='r')
```
![image](https://github.com/user-attachments/assets/37c9208f-6a34-4cb5-83a9-2bcf46ea433a)
![image](https://github.com/user-attachments/assets/27b939b0-5cd0-4237-bfcb-ce684a17e048)

- 최근 300일만 보면 저점에서 열심히 야금야금 매수하는 중인걸 볼수있긴하다.
- 미래에 너무 큰 재앙이 있을뿐 ㅠ

### 3. 기본전략 백테스팅

```python
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
daily_total_value = [] # 일별 총 포트폴리오 가치

for idx,data in d.iterrows():
    # 하루 시작
    daily_total_value.append(0)

    # 매수조건 확인 및 매수
    if data['close'] < data['20d_mean'] and position == 0:
        holding_cash -= 1 * data['close']
        position += 1
        avg_price = data['close'] # 오늘 종가가 평단가가 됨 (보유주식 1개로 해놔서)

    # 매도조건 확인 및 매도
    elif position > 0:
        holding_cash += position * data['close'] # 다음날 종가로 매도
        position = 0
        avg_price = 0

    # 장 마감 후
    daily_total_value[-1] += holding_cash + position * data['close']
    
print(len(daily_total_value))
print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
- 조건은 실습과 동일하게
  - 100만원 자본금
  - 매수: 종가가 20일 이동평균보다 낮고, 현재 보유 주식이 없는 경우 포지션 1개 오늘 종가로 구매
  - 매도: 매수 다음날 종가에 매도
- 결과: 5220원 벌었다. (실습은 7320원..)

![image](https://github.com/user-attachments/assets/1e1c66d7-7337-4f9a-b234-56cbfb181651)


### 4. 슬리피지 백테스팅 및 정량적 평가

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

print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)

return1 = daily_total_value.copy()
```

- 매수: 종가가 5일 최저가 & 종가가 20일 이동평균 아래이면서 보유 주식 0일때 매수
- 매도: 매수 3일후 매도
- 결과: 42880원 벌었다. (실습은 51680원)

![image](https://github.com/user-attachments/assets/4647646d-080f-4651-aa63-a0c8c8405122)

- 여기에 슬리피지 적용하면?

```python
################ 백테스팅 파라미터 ################
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
slippage = 0.004 # 슬리피지
daily_total_value = [] # 일별 총 포트폴리오 가치

################ 전략 파라미터 ################
holding_time_passed = 0 # 마지막 매수 후 경과 일수

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

print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)

return2 = daily_total_value.copy()
```
```plain text
978693
```
![image](https://github.com/user-attachments/assets/372061d0-8c1a-4994-9c72-dee10b9d48a3)

- 매수 매도 조건은 동일한데 슬리피지 적용.
  - 슬리피지 0.4% 반영 (수수료+세금 0.2% 가격 괴리 0.2%)

- 결과: -21307원 (실습은 -5150원)

```python
plt.figure(figsize=(15,8))
plt.plot(return1,c='k')
plt.plot(return2,c='r')
```
![image](https://github.com/user-attachments/assets/6dee5e98-f05d-4439-baf9-ad925ad1f9d1)

- 슬리피지 적용 전후 비교.

- 보유 주식수랑 상관없이 무한으로 진입할수있다고 한다면?

```python
################ 백테스팅 파라미터 ################
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
slippage = 0.004 # 슬리피지
daily_total_value = [] # 일별 총 포트폴리오 가치

################ 전략 파라미터 ################
holding_time_passed = 0 # 마지막 매수 후 경과 일수

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

return3 = daily_total_value.copy()

print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(return2,c='k')
plt.plot(return3,c='r')
```
```plain text
1007436.6799999995
```
- 수익 7436원.

![image](https://github.com/user-attachments/assets/2cde0731-4cdb-470b-911e-158fa7b3c344)

- 왼쪽이 실습, 오른쪽이 현재 데이터인데 실습에서는 보유주식수 제한 없을때가 수익 10만원으로 꽤 높았는데 현재 데이터에서는 그 1년만에 수익 엄청떨어져서 5천원 엔딩이 됏다.

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

print('일 수익률: {:.4f}%, 일 변동성: {:.4f}%'.format((daily_return-1)*100,daily_std))
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
총 수익률: 0.74%
------------------------------------------------
총 백테스팅 기간: 15.06년
연 수익률: 0.05%
------------------------------------------------
일 수익률: 0.0002%, 일 변동성: 0.0023%
Sharpe ratio: 0.01
------------------------------------------------
MDD: -16.28%
```

![image](https://github.com/user-attachments/assets/dbbceb97-11f3-4ffe-9164-43c00f136cdf)


- 위 테스팅의 정량 지표 계산.
- 총 수익률 0.74%인데 중간에 고점 대비 16.28% 떨어진다.
- Sharpe ratio도 실습은 0.23이었는데 현재 데이터는 0.01 나왓다.













