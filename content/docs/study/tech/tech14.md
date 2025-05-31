![image](https://github.com/user-attachments/assets/27e075d2-2702-4f43-ac99-6bce349551c5)---
date : 2025-05-31
tags: ['2025-05']
categories: ['DL']
bookHidden: true
title: "TFT 연구 방향"
---

# TFT 연구 방향

#2025-05-31

---


#1

##사용하고자 하는 데이터는?

feature
- Clinical feature (17, float): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY
- Antibiotics feature (2, str)
  - Treatment (list, str): 투여한 항생제, 결측값일수도있고 2개 이상일수도 있음
  - Strain (str): 환자가 감염된 균주, 1개
- NEWS (int): 중증도
- Code (int/str): 환자 등록번호

time-series
- 10개 시점 (항생제 투여 기준 D-3 ~ D+6)


##TFT input 형식은?

- Observed (18): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY / Strain
- Known (1): Treatment
- Static (1): Code
- Target: NEWS

(목적: 항생제 투여에 따른 NEWS 예측)

##문제점1

- 'Treatment' 즉 리스트를 feature로 넣으려면 one hot encoding 해야함
- one hot encoding 하면? 'Treatment' feature의 차원이 너무 많아짐 항생제가 100종류 이상이라서

##solution1

- 'Treatment' feature를 항생제 리스트 대신 존재 유무 (0,1)로 변경
- 'Strain' feature도 항생제랑 관련된 feature이므로 우선 제거


##수정된 input

- Observed (17): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY 
- Known (1): Treatment
- Static (1): Code
- Target: NEWS

##데이터 단순화 과정에서 무시된 내용 (문제점2)

- 항생제의 균주 특이성
- 항생제는 투여 1일만에 NEWS를 낮출 수도 있고 2일 이상 소요될 수도 있음
- 중복 투여된 항생제가 서로 영향을 줬을 가능성

##solution2

- 항생제별 균주 특이성 feature 추가
  - 원래 데이터에서 항생제 별로 Sequence를 찾고 투여 후 NEWS가 감소하는 Sequence를 식별 (K means등 clustering 기법을 써도되고 단순히 감소폭을 봐도 되고)
  - Sequence의 투여 첫날 기준 항생제-균주 pair를 찾기
  - Paired_antibiotics feature 추가

- 항생제별 NEWS를 낮추는데 소요시간 feature 추가
  - 항생제 투여 후 NEWS가 일정 수준까지 낮아지는데 소요되는 일수에 따라 유형 A, B, C로 구분
  - Response_time feature로 추가


##solution1,2의 효과

- '항생제 종류'와 '균주'를 제거한 대신에 '항생제 종류'가 갖는 아래 특성만 (중요하다고 가정하고) 반영시킴
  - 특정 균주 감염된 경우에 NEWS를 일정 수준 감소시킨 이력이 있는지 (0,1)
  - 모든 투여 경우에서 NEWS를 일정 수준 감소시키는데 걸리는 시간이 느린편인지 빠른편인지 (A, B, C)

#2

##solution1,2에서 생각할수있는 이슈 사항

- 추후 항생제 시뮬레이션을 할때도 자체적으로 annotation한 Paired_antibiotics 및 Response_time feature를 넣어줘야 할것인데 우리 데이터상에 적은 antibiotics나 strain인 경우 과대적합일 수도 있고 우리 데이터셋이 없는 antibiotics나 strain에 대해서는 적용하기 어렵다는 문제가 있음
- known feature인 Treatment가 모든 sequence에서 투여 이전에 0인데 이게 TFT 알고리즘에서 불리하게 작용하는 점은 없을까?
- Encoder와 Decoder에 다른 feature가 들어가도 괜찮던데 이걸 최대로 이용할 방법은 없을까?

##solution1,2를 사용한 결과 모델의 의의

- 17개 임상 feature와 항생제 투여유무 feature에 추가적으로 항생제-균주가 NEWS를 낮추는지 여부, 항생제 효과 소요시간을 반영했을때 NEWS 예측 성능이 올라갔다.
  - 이는 항생제 항목 자체를 넣어주는 원-핫 인코딩을 썻을때보다는 dimension 축소 효과로 인해 예측 성능이 높아진거고
  - 항생제-균주가 NEWS를 낮추는지 여부, 항생제 효과 소요시간을 반영하지 않았을때보다는 항생제의 2가지 특성을 반영했다는 이유로 인해 예측 성능이 높아진 것이며
  - 이런 모델을 통해 6종 항생제 투여로 시뮬레이션 해본 결과 최적의 항생제 탐색에 사용 가능할거같다.
  - 항생제 자체가 갖는 다차원 특성을 medical insight를 토대로 2개로 줄인것에 의의가 있다.

##생각

이대로 진행해도 괜찮지만 뭔가 일찍부터 카테고리를 나눠서 수행하는것보다 항생제별로 다 결과를 뽑은 담에 결과를 토대로 역으로 그 카테고리가 나오게 하는게 이쁠거같음

```bash

```
