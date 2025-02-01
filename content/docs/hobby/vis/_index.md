---
weight: 19
bookComments: false
title: "♡✧。"
---

# ♡✧。

진전의 가시화 


### 2월 1주(1.29-2.2)

1.29

<ADsP - 1과목 데이터의 이해>

1.30

<구글 BERT의 정석 - 트랜스포머 입문> [(link)](https://yshghid.github.io/docs/study/cs/cs15/)

트랜스포머의 인코더.

- 셀프 어텐션은 입력 단어 사이 연관성을 어텐션 점수로 계산하는 것.
  - 입력 단어를 Q, K, V벡터로 변환하고, Q와 K벡터의 내적을 구하고, 소프트맥스를 적용하여 확률로 변환하고, V벡터를 이 확률로 가중합하여 최종 어텐션 점수를 얻는다.
- 단어 간의 관계는 여러 종류가 있는데 1개의 헤드를 사용하면 1가지 관계만 학습될것임. 멀티 헤드 어텐션을 수행해서 다양한 관계 패턴을 동시에 학습한다. 관계의 종류로는 문법적 관계(cat-sat은 주어-동사), 의미적 관계(cat-mat은 동물-사물이 놓인곳), 위치적 관계(on-mat)가 있다.
  - 각 헤드에서 나온 결과를 병합하고 최종 선형 변환해서 하나의 벡터를 얻는다.
 
- 단어의 임베딩 벡터 + sin/cos으로 계산된 위치 인코딩 = 최종 입력 벡터.
  - 이 작업을 Residual Connection라고 함.
- Residual Connection하고 Layer Normalization 해준다. 그래야 기울기 소실이나 기울기 폭발이 일어나지 않는다.
- 위 입력 벡터를 FFN이 받아서 선현 변환 -> ReLU 활성화 함수 적용 -> 선형 변환 -> 출력.

트랜스포머의 디코더.

- 


<구글 BERT의 정석 - BERT 입문> [(link)](https://yshghid.github.io/docs/study/cs/cs16/)


1.31

<구글 BERT의 정석 - BERT의 파생 모델> [(link)](https://yshghid.github.io/docs/study/cs/cs17/)

<ADsP - 2과목 데이터 분석 기획>

2.1

2.2


---

## 아카이브


2월 1주(2.1-2.2) [>>](https://yshghid.github.io/docs/hobby/vis/#2%ec%9b%94-1%ec%a3%bc21-22)

---




