---
date : 2025-07-23
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "TFT #0 연구 방향"
---

# TFT #0 연구 방향

#2025-07-23

---

(#2025-05-31 작성)

#1

사용하고자 하는 데이터는?

- feature
    - Clinical feature (17, float): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY
    - Antibiotics feature (2, str)
        - Treatment (list, str): 투여한 항생제, 결측값일수도있고 2개 이상일수도 있음
        - Strain (str): 환자가 감염된 균주, 1개
    - NEWS (int): 중증도
    - Code (int/str): 환자 등록번호
- time-series
    - 10개 시점 (항생제 투여 기준 D-3 ~ D+6)

TFT input 형식은?

- Observed (18): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY / Strain
- Known (1): Treatment
- Static (1): Code
- Target: NEWS

목적?

- 항생제 투여에 따른 NEWS 예측

문제점1

- ‘Treatment’ 즉 리스트를 feature로 넣으려면 one hot encoding 해야함
- one hot encoding 하면? ‘Treatment’ feature의 차원이 너무 많아짐 항생제가 100종류 이상이라서

문제점1의 solution

- ‘Treatment’ feature를 항생제 리스트 대신 존재 유무 (0,1)로 변경
- ‘Strain’ feature도 항생제랑 관련된 feature이므로 우선 제거

수정된 input

- Observed (17): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY
- Known (1): Treatment
- Static (1): Code
- Target: NEWS

문제점2 데이터 단순화 과정에서 무시된 내용

- 항생제의 균주 특이성
- 항생제는 투여 1일만에 NEWS를 낮출 수도 있고 2일 이상 소요될 수도 있음
- 중복 투여된 항생제가 서로 영향을 줬을 가능성

문제점2의 solution

- 항생제별 균주 특이성 feature 추가
    - 원래 데이터에서 항생제 별로 Sequence를 찾고 투여 후 NEWS가 감소하는 Sequence를 식별 (K means등 clustering 기법을 써도되고 단순히 감소폭을 봐도 되고)
    - Sequence의 투여 첫날 기준 항생제-균주 pair를 찾기
    - Paired_antibiotics feature 추가
- 항생제별 NEWS를 낮추는데 소요시간 feature 추가
    - 항생제 투여 후 NEWS가 일정 수준까지 낮아지는데 소요되는 일수에 따라 유형 A, B, C로 구분
    - Response_time feature로 추가

문제점1,2의 solution 의 효과

- ‘항생제 종류’와 ‘균주’를 제거한 대신에 ‘항생제 종류’와 ‘균주’가 갖는 아래 특성만 (중요하다고 가정하고) 반영시킴
    - 특정 균주 감염된 경우에 NEWS를 일정 수준 감소시킨 이력이 있는지 (0,1)
    - 모든 투여 경우에서 NEWS를 일정 수준 감소시키는데 걸리는 시간이 느린편인지 빠른편인지 (A, B, C)

---

#2

문제점1,2의 solution에서 생각할수있는 이슈 사항

- 추후 항생제 시뮬레이션을 할때도 자체적으로 annotation한 Paired_antibiotics 및 Response_time feature를 넣어줘야 할것인데 우리 데이터상에 적은 antibiotics나 strain인 경우 과대적합일 수도 있고 우리 데이터셋이 없는 antibiotics나 strain에 대해서는 적용하기 어렵다는 문제가 있음
- known feature인 Treatment가 모든 sequence에서 투여 이전에 0인데 이게 TFT 알고리즘에서 불리하게 작용하는 점은 없을까?
- Encoder와 Decoder에 다른 feature가 들어가도 괜찮던데 이걸 최대로 이용할 방법은 없을까?

문제점1,2의 solution를 사용한 결과 모델의 의의

- 17개 임상 feature와 항생제 투여유무 feature에 추가적으로 항생제-균주가 NEWS를 낮추는지 여부, 항생제 효과 소요시간을 반영했을때 NEWS 예측 성능이 올라갔다.
    - 이는 항생제 항목 자체를 넣어주는 원-핫 인코딩을 썻을때보다는 dimension 축소 효과로 인해 예측 성능이 높아진거고
    - 항생제-균주가 NEWS를 낮추는지 여부, 항생제 효과 소요시간을 반영하지 않았을때보다는 항생제의 2가지 특성을 반영했다는 이유로 인해 예측 성능이 높아진 것이며
    - 이런 모델을 통해 6종 항생제 투여로 시뮬레이션 해본 결과 최적의 항생제 탐색에 사용 가능할거같다.
    - 항생제 자체가 갖는 다차원 특성을 medical insight를 토대로 2개로 줄인것에 의의가 있다.

생각

이대로 진행해도 괜찮지만 뭔가 일찍부터 카테고리를 나눠서 수행하는것보다 항생제별로 다 결과를 뽑은 담에 결과를 토대로 역으로 그 카테고리가 나오게 하는게 이쁠거같음

---

#3

이슈사항정리

Q1) 항생제 feature가 D+0 이전에는 결측인 경우가 문제되지 않을까?

A1) TFT의 known feature는 미래 예측을 위한 입력이며, 과거 구간에서는 비어있어도 문제가 되지 않는다고함. Encoder는 과거 임상 수치와 항생제 미투여 상태(0)만 보고 학습하고, Decoder는 항생제 시나리오가 주어졌을 때 그 조건하에 예측을 수행하는것은 TFT 구조 설계상 허용되는 일반적인 상황.

Q2) 항생제 종류가 너무 많은 경우(100종 이상) 직접 one-hot or embedding 사용하면?

A2) 100개 one-hot 인코딩 시 차원이 너무 크고 sparse하여 과적합 유발. Embedding도 너무 많아지면 학습 어렵고 특히 데이터 적으면 성능 저하될수있음.

Q3) multi-hot 임베딩 하면?

A3) 100종 항생제라고 치면 100개 binary feature로 넣어주는건데 구조가 단순하고 해석이 쉽지만 feature 수 많고 sparse하고 상호작용 표현 어려움

Q4) 항생제 군 분류 후 군 정보 feature 쓰면?

A4) beta-lactam계, macrolide계 등으로 10~15종으로 분류한 feature를 넣어주는건데 feature 수 줄고 효과 해석도 나쁘지않음 다른 항생제 종류 쓴 데이터에 시뮬레이션 하기도 괜찮을듯 근데 일반적으로 나누는 분류법이라서 일반적인 결과가 나와버릴수도

Q5) 항생제 임베딩해서 균주, 반응시간과의 상호작용 반영된 latent vector 학습

A5) feature를 가내수공업으로 넣어주는게 아니라 항생제 효과 요약 벡터를 생성하는건데 균주와의 관계, 반응소요시간 등에서 내가 놓칠수있는 부분을 캐치해서 넣어줄수있음. 예를들어 나는 샘플을 보고 NEWS를 3.0 이하로 떨어뜨린 경우가 많으면 “효과적” 아니면 “알수없음"으로 생각하는 알고리즘인데 딥러닝 돌리면 샘플을 보고 “~~~” 하니까 임베딩공간상 이 위치, 이 샘플은 “~~~” 하니까 임베딩공간상 다른 위치 이렇게 할당하는거고 ““에 NEWS를 3.0 이하로 떨어뜨린 경우가 많은지에 대한 비중이 큰지 작은지 없는지는 모르지만 어떤 weight가 줘진상태든 간에 데이터 상 내가생각한 저 기준보다 더 중요한 특성이 있으니가 weight를 덜 줬겠지 라고 생각하는것임. 이 방법은 설명력이 낮을 수 있다.

Q6) Q5 연구는 TFT를 적용한 항생제 연구로서 항생제 투여에 따른 NEWS 예측에 중요한 feature와 그렇지 않은 feature를 자동으로 weight 조절해서 학습하는게 포인트임. 근데 항생제 종류에 따라 중요한 feature가 다를 수도 있지 않나? 이걸 반영하지 않고 도출한 ‘중요한 feature 목록’은 그냥 “항생제"라는 x로 “NEWS"라는 y를 예측할때 일반적으로 이런 feature가 중요하다 선에서 그침. 모조리 넣고 항생제마다 결과를 봣을때 이런이런 feature가 비슷하다고 나온 애들은 확인해보니 이런 공통 특성을 갖더라 이런식으로, 카테고리화는 마무리 단계에 들어가야하지 않나 싶음.

A6) 조건 분기 Decoder를 적용하는 방법이 있는데 더 찾아봐야함

Q7) 중복 투여에 따른 영향을 고려 안해도되나..

A7) 아래 gpt 넣엇을때 추천받은 방법을 일단 수행해보고 결정하기.

> 목적: 본 연구는 “항생제 종류에 따라 NEWS score를 예측"하는 문제를 해결하고자 한다. 이를 위해 기존 Temporal Fusion Transformer(TFT) 구조에 다음 네 가지 기능을 통합한 모델을 제안한다:
> 
> - Multi-path 구조
> - 항생제-균주 상호작용 임베딩
> - 조건부 시나리오 입력
> - 항생제 효과 지연 시간 반영
> 
> 기존 TFT 구조 요약
> 
> - Encoder: 과거 시계열(임상 수치 등) 정보를 인코딩
> - Decoder: 미래 시점 예측 (known feature 사용)
> - GRN + Attention: 중요 변수 선택 및 장기 의존성 반영
> 
> 기존 TFT의 한계 (본 연구 기준)
> 
> 1. 항생제 효과 구분 불가: 항생제 정보를 모델에 제대로 반영하지 못함
> 2. 균주와 항생제 상호작용 무시 특정 항생제가 어떤 균주에 효과적인지 파악 불가
> 3. 약물 반응 지연 미반영 투여 즉시 효과가 나타난다고 가정함
> 4. 조건부 시나리오 예측 불가 같은 환자라도 항생제를 바꾸었을 때의 결과 비교 불가
> 5. 데이터 부족 문제 항생제별로 모델을 나누면 데이터가 부족하고 과적합 발생 가능
> 
> 제안하는 개선 TFT: Multi-path TFT with Antibiotic × Strain Interaction
> 
> [1] Multi-path 구조: 항생제 종류에 따라 Decoder 경로 또는 Attention 흐름을 다르게 설정/Decoder 입력에 항생제 조건을 명시하여 조건부 예측 가능/같은 환자에 대해 항생제 시나리오를 바꿔 결과 비교 가능 [2] 항생제 × 균주 상호작용 임베딩: 항생제 임베딩과 균주 임베딩 간의 상호작용을 모델링 (concat, bilinear 등)/항균력 차이를 자동 학습할 수 있어 특정 조합의 효과를 반영 가능 [3] 조건부 Gated Layer: 항생제와 균주 정보에 따라 경로 가중치를 다르게 부여/특정 조합에 따라 예측 흐름을 다르게 조정 가능 [4] 효과 지연 반영: 항생제마다 효과가 나타나는 시간 차이를 가중치 또는 마스크 형태로 반영/예: Vancomycin은 1일 후, Piperacillin은 2일 후 효과가 나타나는 지연 구조를 학습
> 
> 최종 구조 개요
> 
> - Static Encoder: 항생제 종류, 감염 균주 등 고정 정보 인코딩
> - Encoder (Observed features): 시계열 임상 수치 및 항생제 투여 여부 등
> - Decoder (Known future inputs): 미래 시점의 항생제 종류 및 투여 계획
> - Conditional Gating Layer: 항생제와 균주 정보를 입력으로 받아 예측 경로 가중치 조절
> - Output: 조건에 따른 NEWS score 예측
> 
> 기대 효과
> 
> - 항생제 반응 차이 반영: 항생제-균주 조합에 따른 실제 임상 반응 예측 가능
> - 시나리오 기반 예측: 항생제 변경 시 예후 변화를 시뮬레이션 가능
> - 데이터 손실 방지: 항생제별로 데이터를 분할하지 않아 데이터 효율성 유지
> - 상호작용 내재화: 항생제-균주 관계를 잠재 공간에서 학습 가능
> - 반응 지연 반영: 실제 약물 효과 발생 시점을 반영해 예측 정확도 향상

→ 항생제나 균주에 따라 중요한 feature가 다를 수도 있고 delay 효과가 다를 수도 있음을 반영 가능 (맞나?)

#
