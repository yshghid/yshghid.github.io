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
  - 원래 데이터에서 항생제 별로 Sequence를 찾고 투여 후 NEWS가 감소하는 Sequence를 식별 (K means 등으로)
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

##solution1,2의 상황

- 



```bash

```
