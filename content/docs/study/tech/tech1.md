---
date : 2025-03-31
tags: ['2025-03']
categories: ['python, 주식']
bookHidden: true
title: "전략 백테스팅"
bookComments: true
---

# 전략 백테스팅

## 목록

*2025-03-31* ⋯ [전략 백테스팅, 매매 시그널](https://yshghid.github.io/docs/study/tech/tech1/#전략-백테스팅-매매-시그널)

*2025-03-31* ⋯ [기초 백테스팅 모델 개발](https://yshghid.github.io/docs/study/tech/tech1/#기초-백테스팅-모델-개발)

---

## 전략 백테스팅, 매매 시그널

### 1. 개념
- 백테스팅(Backtesting): 과거 데이터로부터 내 전략의 예상 수익과 리스크를 정량적으로 평가하는 테스트 방법. \
  ![image](https://github.com/user-attachments/assets/a81db9db-92e9-4d24-a69a-e064ed94b383)

- 매매 시그널
  - 알고리즘으로 계산한 매수/매도 타점
  - 몇 주를 살지, 매매 가능한 시점인지(거래정지 등)도 고려

- 백테스팅과 매매 시그널의 관계
  - 매매 시그널대로 매매할 때, 각 시점별 수억률 그래프를 그려보는 것이 백테스팅

### 2. 실습

- 매매 시그널 생성 실습
  - 데이터: 삼성전자 일봉 데이터
  - 전략: 전일 종가가 최근 5일 종가 중 가장 낮다면 종가 매수, 마지막 매수 5일 후 전량 종가 매도.
  - 매수/매도 날짜, 가격을 계산하기.

```python
import pandas as pd
import matplotlib.pyplot as plt

dirpath = '/data/home/ysh980101/2504/Bin2'
d = pd.read_parquet(f'{dirpath}/005930.parquet')
d
```
```plain text
	timestamp	ticker	open	high	low	close	volume
0	20100104	005930	16060.0	16180.0	16000.0	16180.0	11963550.0
1	20100105	005930	16520.0	16580.0	16300.0	16440.0	27960950.0
2	20100106	005930	16580.0	16820.0	16520.0	16820.0	22987750.0
3	20100107	005930	16820.0	16820.0	16260.0	16260.0	22161850.0
4	20100108	005930	16400.0	16420.0	16120.0	16420.0	14789900.0
...	...	...	...	...	...	...	...
3529	20240423	005930	76400.0	76800.0	75500.0	75500.0	18717699.0
3530	20240424	005930	77500.0	78800.0	77200.0	78600.0	22166150.0
3531	20240425	005930	77300.0	77500.0	76300.0	76300.0	15549134.0
3532	20240426	005930	77800.0	77900.0	76500.0	76700.0	12755629.0
3533	20240429	005930	77400.0	77600.0	76200.0	76700.0	14664474.0
3534 rows × 7 columns
```

- 컬럼: open(시가), high(고가), low(저가), close(종가), volume(물량)
- 일별 데이터

```python
# 오늘 포함 과거 5일 종가 중 최고값
d['5d_max'] = d.rolling(5)['close'].max()

# 오늘 포함 과거 5일 종가 중 최저값
d['5d_min'] = d.rolling(5)['close'].min() 

# 전일 종가
d['last_1d_close'] = d['close'].shift(1) 

# 20일 이동평균
d['20d_mean'] = d.rolling(20)['close'].mean()
```

- 5d_max의 경우 앞의 5개가 없는 시점(첫 4일)은 NaN
- 20d_mean의 경우 앞의 19개가 NaN

```python
# 종가가 5일 최저가이고, 20일 이동평균보다 낮은 시점만 뽑기
buy = d[(d['close'] == d['5d_min']) & (d['close'] < d['20d_mean'])]
buy
```
```plain text
	timestamp	ticker	open	high	low	close	volume	5d_max	5d_min	last_1d_close	20d_mean
19	20100129	005930	16000.0	16020.0	15600.0	15680.0	22864250.0	16840.0	15680.0	16160.0	16402.0
20	20100201	005930	15680.0	15700.0	15300.0	15540.0	25052100.0	16300.0	15540.0	15680.0	16370.0
21	20100202	005930	15800.0	15800.0	15400.0	15440.0	19690150.0	16160.0	15440.0	15540.0	16320.0
24	20100205	005930	15160.0	15220.0	14940.0	15000.0	25751700.0	15540.0	15000.0	15520.0	16148.0
25	20100208	005930	14940.0	15080.0	14820.0	14960.0	21980400.0	15540.0	14960.0	15000.0	16099.0
...	...	...	...	...	...	...	...	...	...	...	...
3524	20240416	005930	81200.0	81300.0	79400.0	80000.0	31949845.0	84100.0	80000.0	82200.0	81400.0
3525	20240417	005930	80700.0	80800.0	78900.0	78900.0	22611631.0	84100.0	78900.0	80000.0	81705.0
3527	20240419	005930	78300.0	78700.0	76300.0	77600.0	31317563.0	82200.0	77600.0	79600.0	81755.0
3528	20240422	005930	77400.0	77500.0	75100.0	76100.0	30469477.0	80000.0	76100.0	77600.0	81615.0
3529	20240423	005930	76400.0	76800.0	75500.0	75500.0	18717699.0	79600.0	75500.0	76100.0	81480.0
723 rows × 11 columns
```

- 매매 시그널 뽑기: 오늘 종가가 최근 5일 종가의 최솟값보다 작으면서, 20일의 일평균보다 낮은 경우만 뽑기
- 총 723개 시점 

```python
# plt.plot을 활용해 주가 그래프 출력

plt.figure(figsize=(15,8))
plt.plot(d['close'])
```
![image](https://github.com/user-attachments/assets/6960f4ab-5d93-4d51-b298-16924b814573)

- 삼성전자 일별 종가 시각화

```python
# 주가 그래프에 매수 타점 표시

plt.figure(figsize=(15,8))
plt.plot(d['close'])
plt.scatter(buy.index,buy['close'],c='r')
```
![image](https://github.com/user-attachments/assets/034236bc-321e-419c-8545-a16a30e58607)


- buy(매수 시그널)에서 종가 타점 표시
  - 시각화를 보면 내려가는부분만 사는 느낌..
  - 3500일 대신 최근 300일만 시각화하면?

```python
# 최근 300일만 뽑아 매수 타점 표시

d_sample = d.iloc[-300:]
buy_sample = d_sample[(d_sample['close'] == d_sample['5d_min']) & (d_sample['close'] < d_sample['20d_mean'])] # 매수 시그널
print(buy_sample.shape)

plt.figure(figsize=(15,8))
plt.plot(d_sample['close'])
plt.scatter(buy_sample.index,buy_sample['close'],c='r')
```
```plain text
(63, 11)
```
![image](https://github.com/user-attachments/assets/9c1e9ebf-1f6c-4d5a-9dea-c08c078a2787)

- 총 63개의 매수 타점
- 떨어질때 계속 매수하다가 올라갈때는 안사고있음. 빨간색에서 매수하고 5일 후에 팔기. 

> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ top](https://yshghid.github.io/docs/study/tech/tech1/#%ec%95%8c%ea%b3%a0%eb%a6%ac%ec%a6%98-%ed%8a%b8%eb%a0%88%ec%9d%b4%eb%94%a9%ec%9c%bc%eb%a1%9c-%ec%a3%bc%ec%8b%9d-%eb%a7%a4%eb%a7%a4-%ec%9e%90%eb%8f%99%ed%99%94%eb%b4%87-%eb%a7%8c%eb%93%a4%ea%b8%b0--%ec%a0%84%eb%9e%b5-%eb%b0%b1%ed%85%8c%ec%8a%a4%ed%8c%85)

---


## 기초 백테스팅 모델 개발

### 1. 개념

- 상용 백테스팅 툴: Tradingview, Zipline, Backtesting.py 등

- 백테스팅 모듈 기초 변수
  - 단일 종목 백테스팅 모듈
  - 1일씩 이동하며 각 시점의 보유현금/보유종목/수익률 등을 기록하는 구조
  - 4개 변수 사용

- 변수 정보
  - holding cash: 보유 현금
  - position: 현재 보유 주식 수
  - Avg_price: 평단가
  - slippage: 슬리피지(세금+수수료+백테스팅과 실거래간 체결가격 차이)

### 2. 실습

```python
import pandas as pd
import matplotlib.pyplot as plt

d = pd.read_parquet('005930.parquet')
d['5d_max'] = d.rolling(5)['close'].max() # 오늘 포함 과거 5일 종가 중 최고값
d['5d_min'] = d.rolling(5)['close'].min() # 오늘 포함 과거 5일 종가 중 최저값
d['last_1d_close'] = d['close'].shift(1) # 전일 종가
d['20d_mean'] = d.rolling(20)['close'].mean() # 20일 이동평균

buy = d[(d['close'] == d['5d_min']) & (d['close'] < d['20d_mean'])]
```
- 가장 옛날 시점이 첫 줄에 가있는지 확인

```python
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
daily_total_value = [] # 일별 총 포트폴리오 가치

for data in d.iterrows():
    print(data)
    break
```
```plain text
(0, timestamp          20100104
ticker               005930
open                16060.0
high                16180.0
low                 16000.0
close               16180.0
volume           11963550.0
5d_max                  NaN
5d_min                  NaN
last_1d_close           NaN
20d_mean                NaN
Name: 0, dtype: object)
```

- 파라미터 설정
  - 보유 현금: 100만원
  - 보유중인 삼성전자 주식 수: 0
  - 평단가: 0 (보유중인 주식 없음)
  - 일별 수익률 저장할 리스트: daily_total_value

- d.iterrows()하면 (idx, row) 튜플이 나온다. 


```python
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
```

- 로직 설정
  - 종가가 20일 이동평균보다 낮고, 현재 보유 주식이 없는 경우에만 포지션 1개 오늘 종가로 구매
  - 무조건 다음날 종가에 청산

- 오늘 시점의 포트폴리오 총평가가치 입력: 보유현금 + 가진 포지션 + 당일 종가 

```python
print(len(daily_total_value))
print(daily_total_value[-1])
```
```plain text
3534
1007320.0
```

- 7320원 번거...맞나 ㅋㅋ

```python
plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
![image](https://github.com/user-attachments/assets/ee0508bb-8c3d-4dd2-be65-af162228c0f7)

- 일별 수익률 시각화 결과 수익률이 좋지는 않다.

cf) 40일 평균으로 해보기

```python
# 40일 이동평균
d['40d_mean'] = d.rolling(40)['close'].mean()

holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
daily_total_value = [] # 일별 총 포트폴리오 가치

for idx,data in d.iterrows():
    daily_total_value.append(0)

    if data['close'] < data['40d_mean'] and position == 0:
        holding_cash -= 1 * data['close']
        position += 1
        avg_price = data['close']
    elif position > 0:
        holding_cash += position * data['close']
        position = 0
        avg_price = 0

    daily_total_value[-1] += holding_cash + position * data['close']
    
print(len(daily_total_value))
print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
```plain text
3534
1016070.0
```
![image](https://github.com/user-attachments/assets/7746a2a7-efc1-40c0-b18b-a07501ad7738)

- 16070원으로 오름..^^

cf2) if문 오류 나면?

```python
holding_cash = 1_000_000 # 보유 현금
position = 0 # 현재 보유 포지션
avg_price = 0 # 평단가
daily_total_value = [] # 일별 총 포트폴리오 가치

for idx,data in d.iterrows():
    daily_total_value.append(0)

    if data['close'] < data['20d_mean'] and position == 0:
        holding_cash -= 1 * data['close']
        position += 1
        avg_price = data['close']
    if position > 0:                    #<<여기 오류라면??
        holding_cash += position * data['close']
        position = 0
        avg_price = 0

    daily_total_value[-1] += holding_cash + position * data['close']
    
print(len(daily_total_value))
print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
```plain text
3534
1000000.0
```
![image](https://github.com/user-attachments/assets/d1acbe0c-914d-40c9-a3a2-4aee773ef28f)

- 매수하자마자 매도하는게 된다. 

cf3) 매수 다음날 대신 매수 5일차 종가에 매도

```python
holding_cash = 1_000_000
position = 0
avg_price = 0
buy_day = None  # 매수한 날짜의 인덱스를 저장
daily_total_value = []

for idx, data in d.iterrows():
    # 하루 시작
    daily_total_value.append(0)

    # 매수조건 확인 및 매수 (포지션 없는 경우)
    if position == 0 and data['close'] < data['20d_mean']:
        holding_cash -= data['close']
        position = 1
        avg_price = data['close']
        buy_day = idx  # 매수일 저장

    # 보유 중이고, 매수 후 5일이 지난 경우 매도
    elif position > 0 and (idx - buy_day) == 4:
        holding_cash += position * data['close']
        position = 0
        avg_price = 0
        buy_day = None

    # 장 마감 후 평가가치 기록
    daily_total_value[-1] += holding_cash + position * data['close']

print(daily_total_value[-1])

plt.figure(figsize=(15,8))
plt.plot(daily_total_value)
```
```plain text
1070830.0
```

![image](https://github.com/user-attachments/assets/8099de57-4ad4-4224-b61f-9c8a8d32bc1f)

- 7마넌으로 올랐다!

> 강의 링크 https://www.inflearn.com/course/%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EC%A3%BC%EC%8B%9D%EB%A7%A4%EB%A7%A4%EB%B4%87-%EC%9E%90%EB%8F%99%EC%82%AC%EB%83%A5

[⏶ top](https://yshghid.github.io/docs/study/tech/tech1/#%ec%95%8c%ea%b3%a0%eb%a6%ac%ec%a6%98-%ed%8a%b8%eb%a0%88%ec%9d%b4%eb%94%a9%ec%9c%bc%eb%a1%9c-%ec%a3%bc%ec%8b%9d-%eb%a7%a4%eb%a7%a4-%ec%9e%90%eb%8f%99%ed%99%94%eb%b4%87-%eb%a7%8c%eb%93%a4%ea%b8%b0--%ec%a0%84%eb%9e%b5-%eb%b0%b1%ed%85%8c%ec%8a%a4%ed%8c%85)
