+++
title = "101개의 쿠키그릇 문제"
menu = "main"
categories = ["Study", "Bayes-statistics"]
+++

## 들어가며

101개의 쿠키그릇 문제는 아래와 같다. 이를 pmf 객체를 사용해서 답을 내보자! pmf 객체는 사건과 그 사건이 발생할 확률을 나타내는 데이터 구조이다. 

> **쿠키 문제**
> - 101개의 그릇에는 바닐라, 초콜릿 두 종류의 쿠키가 존재한다. 그릇 0에는 바닐라 쿠키가 0% 들어 있다. 그릇 1에는 바닐라 쿠키가 1% 들어 있다.
> - 이런 식으로 진행하여 그릇 99에는 바닐라 쿠키가 99개 들어 있고, 그릇 100에는 바닐라 쿠키가 100개 들어 있다.
> - 무작위로 한 그릇을 선택하고 무작위로 쿠키를 하나 선택했을 때 바닐라 쿠키였다.  그 쿠키가 그릇 n에서 나왔을 확률은 얼마인가?

## 1. Pmf 클래스의 기본 사용법

### 1.1 Pmf 객체 생성하기

```python
from empiricaldist import Pmf

coin = Pmf()
coin['heads'] = 1/2
coin['tails'] = 1/2
```

pmf 객체는 각 사건과 그 사건이 발생할 확률을 나타내는 데이터 구조이다. "동전 던지기"라는 문제에서 "앞면"이라는 사건이 발생할 확률에 대한 데이터 구조를 coin이라는 이름으로 생성해줘보겠다.
coin이라는 Pmf 객체를 생성한 후, 'heads'와 'tails'에 각각 0.5의 확률을 할당해주었다.

```python
die = Pmf.from_seq([1, 2, 3, 4, 5, 6])
```

이 코드는 주사위를 굴리는 경우를 나타낸다. 위의 "동전 던지기"와 같이 6개의 사건에 대해 각각 1/6의 확률을 할당해줄수도 있을것이다. 

Pmf.from_seq 메소드를 사용하여 1에서 6까지의 숫자에 동일한 확률을 할당해준 die라는 Pmf 객체를 생성하였다.

### 1.2 Pmf 객체 활용하기

특정 문제에서 특정 사건이 발생할 확률을 나타내는 데이터 구조인 pmf를 생성해보았다. 해당 pmf 객체는 어떻게 활용할수있을까?

문자열 'Mississippi'에서 's' 문자가 나타날 확률은 다음과 같이 구할수있다.

```python
letters = Pmf.from_seq(list('Mississippi'))
letters['s']
```
```bash
0.36363636363636365
```

각 문자의 발생 빈도를 기반으로 letters라는 Pmf 객체를 생성해서 's'의 발생 확률을 구했다. 문자열 'Mississippi'에서 's' 문자가 나타날 확률은 약 36% 이다.

```python
try:
    letters['t']
except KeyError as e:
    print(type(e))
```
```bash
<class 'KeyError'>
```

동일한 방법으로 't'의 발생확률을 구해보았다. 't'는 'Mississippi' 문자열에 존재하지 않기 때문에, letters['t']를 호출하면 KeyError가 발생한다.

```python
letters('s')
```
```bash
0.36363636363636365
```

pmf 객체는 함수처럼 사용할수도 있다. letters라는 Pmf 객체가 있을때 's'의 발생 확률을 구했다.

```python
letters('t')
```
```bash
0
```

동일한 방법으로 't'의 발생확률을 구해보았다. letters['t']를 호출하면 KeyError가 발생했으나 letters('t')를 호출하면 KeyError가 발생하지 않고 대신 0이 반환된다.

즉, 두 방법은 존재하지 않는 항목을 조회할 때 발생하는 예외를 다루는 방식이 다르다. 

```python
die([1, 4, 7])
```

pmf 객체를 함수처럼 사용하는것의 또다른 특징은 die(1)과 같이 하나의 값뿐만 아니라 여러 값을 배열이나 리스트로 제공하여 여러 값의 확률을 한 번에 조회할 수 있다는 점이다. 
예를 들어 die([1, 4, 7])을 호출하면 주사위 값 1, 4, 7의 확률이 각각 반환된다. 이때 7은 존재하지 않으므로 0이 반환된다. 

```bash
array([0.16666667, 0.16666667, 0.        ])
```

## 2. 베이즈 정리의 적용

### 2.1 베이즈 정리의 적용: 쿠키 문제

```python
from empiricaldist import Pmf

prior = Pmf.from_seq(['Bowl 1', 'Bowl 2'])
```

"두 개의 그릇에서 쿠키 뽑기" 문제에서 각 그릇에 동일한 확률을 할당해준 prior라는 pmf 객체를 생성해주었다. 이 pmf 객체는 어떻게 활용할수있을까?

두개의 그릇 중 첫번째 그릇에서 쿠키를 뽑을 확률은 다음과 같다. 

```python
prior('Bowl 1')
```
```bash
0.5
```

여기서 정보를 추가해서, 첫번째 그릇에는 바닐라쿠키 30개와 초코쿠키 10개, 두번째 그릇에는 바닐라쿠키 20개와 초코쿠키 20개가 들어있는 상태다. 그렇다면 **바닐라** 쿠키를 뽑았을때 둘 중 첫번째 그릇에서 뽑았을 확률은 어떻게 계산할까?

```python
likelihood_vanilla = [0.75, 0.5]
posterior = prior * likelihood_vanilla
posterior.normalize()
```

prior는 각 그릇에 동일한 확률을 할당해준 pmf 객체였고 즉 각 그릇이 선택될 사전 확률이다. 

likelihood_vanilla는 바닐라 쿠키를 선택할 확률이다. 

사후 확률 posterior는 사전 확률과 우도를 곱한 후 정규화하여 계산된다. 각 그릇에 동일한 확률을 할당해준 prior 객체를 바닐라 쿠키를 선택할 확률을 사용하여 posterior 객체로 갱신해주었다. 

따라서 답은 아래와 같다. 

```python
posterior('Bowl1')
```
```bash
0.6
```

### 2.2 베이즈 정리의 적용: 101개의 쿠키 그릇 문제

쿠키그릇이 2개에서 101개가 되었다. 위의 2개의 쿠키그릇에서는 바닐라 쿠키의 비율이 각각 0.75, 0.5였다. 101개의 쿠키그릇에서는 비율을 0.00~1.00까지로 설정해줄것이다.

먼저 prior는 101개 그릇에 동일한 확률을 할당해준 pmf 객체다. 즉, 101개의 각 그릇이 선택될 사전 확률은 모두 같다.


```python
hypos = np.arange(101)
prior = Pmf(1, hypos)
prior.normalize()
```

likelihood_vanilla는 바닐라 쿠키를 선택할 확률이다. 0.00~1.00까지 101개의 확률값이 들어있다.


```python
likelihood_vanilla = hypos / 100
```

위 정의에 따르면 첫번째 그릇은 바닐라쿠키를 뽑을 확률이 0.00, 두번째 그릇은 0.01, ..., 101번째 그릇은 1.00이 될것이다.

```python
posterior1 = prior * likelihood_vanilla
posterior1.normalize()
```

prior는 각 그릇을 선택할 확률에 동일한 값을 할당해준 pmf 객체였다. 사전확률 prior에 likelihood_vanilla를 곱해서 **바닐라쿠키를 뽑았을때** 각 그릇을 선택할 확률 posterior1로 갱신해주었다. 
normalize()는 이 값을 정규화하여 확률 분포가 다시 1이 되도록 해준다.

첫번째 바닐라 쿠키를 본 후의 사전 확률과 사후 확률을 시각화해보면 다음과 같다.

```python
prior.plot(label='prior', color='C5')
posterior1.plot(label='posterior', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after one vanilla cookie')
```

![image](https://github.com/user-attachments/assets/b0d8476a-e452-4517-9518-66dac8dfd02b)


두번째 바닐라 쿠키를 본후의 사후확률도 동일하게 계산할수있다. **두번째 바닐라쿠키를 뽑았을때** 각 그릇을 선택할 확률 posterior2로 갱신해주면 된다.

```python
posterior2 = posterior1 * likelihood_vanilla
posterior2.normalize()
```

시각화해보면 다음과 같다. 

```python
posterior2.plot(label='posterior2', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after two vanilla cookies')
```

![image](https://github.com/user-attachments/assets/06ee619c-70da-4975-b9e5-5866a9dde45e)


세번째로 초콜릿쿠키를 본경우 사후확률은 다음과 같이 계산된다.

```python
likelihood_chocolate = 1 - likelihood_vanilla
posterior3 = posterior2 * likelihood_chocolate
posterior3.normalize()
```

시각화해보면 다음과 같다.

```python
posterior3.plot(label='posterior3', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after 2 vanilla, 1 chocolate')
```

![image](https://github.com/user-attachments/assets/e9517b3c-2535-4132-92a9-116b64131ded)


60과 80번 그릇 사이에서 최대 확률이 나온다. 실제로 선택되었을 확률이 가장 높은 그릇은 다음으로 확인 가능하다.

```python
posterior3.idxmax()
posterior3.max_prob()
```
```bash
67
67
```

베이즈 정리를 사용하여 "쿠키 문제"를 해결해보았다. 사전 확률을 설정하고, 새로운 데이터(바닐라와 초콜릿 쿠키)를 관찰할 때마다 확률을 업데이트하면서, 최종적으로 가장 가능성 높은 결과를 찾는다.
이 과정에서 시각화를 통해 각 단계에서 확률이 어떻게 변화하는지도 확인할수 있었다.

전체 코드는 아래와 같다.

```python
from empiricaldist import Pmf

coin = Pmf()
coin['heads'] = 1/2
coin['tails'] = 1/2

die = Pmf.from_seq([1,2,3,4,5,6])

letters = Pmf.from_seq(list('Mississippi'))
letters['s']
try:
    letters['t']
except KeyError as e:
    print(type(e))
letters('s')
letters('t')

die([1,4,7])

prior = Pmf.from_seq(['Bowl 1', 'Bowl 2'])
likelihood_vanilla = [0.75, 0.5]
posterior = prior * likelihood_vanilla
posterior.normalize()

hypos = np.arange(101)
prior = Pmf(1, hypos)
prior.normalize()

likelihood_vanilla = hypos/100
posterior1 = prior * likelihood_vanilla
posterior1.normalize()
prior.plot(label='prior', color='C5')
posterior1.plot(label='posterior', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after one vanilla cookie')

posterior2 = posterior1 * likelihood_vanilla
posterior2.normalize()
posterior2.plot(label='posterior2', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after two vanilla cookies')

likelihood_chocolate = 1 - likelihood_vanilla
posterior3 = posterior2 * likelihood_chocolate
posterior3.normalize()
posterior3.plot(label='posterior3', color='C4')
decorate(xlabel='Bowl #', ylabel='PMF', title='Posterior after 2 vanilla, 1 chocolate')

posterior3.idxmax()
posterior3.max_prob()
```
