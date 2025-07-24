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

### 0. 연구 개요

목적: 항생제 종류에 따라 NEWS score를 예측

모델: Temporal Fusion Transformer(TFT) 


### 1. 데이터 구성 및 TFT 입력 형식

1. 데이터 종류
- Clinical feature (17개, float): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, PULSE, SPO2, O2_APPLY
- Antibiotics feature (2개, 문자열)
- Treatment: 투여 항생제 목록 (복수 가능, 결측 가능)
- Strain: 감염 균주 (1개)
- NEWS: 중증도
- Code: 환자 식별자

2. 시계열 범위: 항생제 투여 기준 D-3부터 D+6까지 10일

3. TFT 입력 구조
- Observed: 임상지표 17개 + Strain
- Known: Treatment
- Static: Code
- Target: NEWS

### 2. 현재 문제점 및 해결책

문제 1: Treatment feature 차원 과다 -> One-hot encoding 시 항생제 종류가 100종 이상이라 차원이 너무 커짐

해결책 1
- Treatment는 단순히 존재 유무(0,1)로 표시
- Strain은 제거하여 차원 축소

수정된 입력 구조
- Observed: 17개 임상 feature
- Known: Treatment (0/1)
- Static: Code
- Target: NEWS

문제 2: 항생제의 균주 특이성과 반응 지연 무시. 항생제 효과가 균주에 따라 다를 수 있음. 항생제 효과가 나타나는 시간도 다름. 중복 투여 시 상호작용 무시

해결책 2
- Paired_antibiotics: 특정 항생제가 특정 균주에 효과가 있는지 여부 (0/1)
- Response_time: 항생제 투여 후 NEWS 감소까지 걸린 시간 (A, B, C 구분)

요약
- 항생제와 균주를 직접 feature로 넣는 대신, 임상적으로 중요한 특성만 요약하여 반영하기.
- 다차원 특성을 압축하여 모델 단순화 및 해석력 향상

### 3. 설계상의 고려사항

- Paired_antibiotics 및 Response_time은 수작업 기반으로 생성되며 새로운 항생제나 균주에는 적용이 어려울 수 있음
- Treatment feature는 과거 시점에 0으로만 존재하는데, 이 점이 TFT의 known feature 처리 방식에 영향을 미치는지 검토 필요
- Encoder와 Decoder의 feature 구성을 다르게 설정하면 더 효율적일 수 있음

### 4. 결과 해석 및 모델 의의

- 항생제 자체를 직접 넣은 원-핫 인코딩 방식보다 차원을 줄여 성능 향상
- 항생제-균주 반응과 효과 지연 특성을 반영함으로써 예측 정확도 향상
- 임상적으로 의미 있는 정보만 반영하여 해석 가능성과 성능 모두 확보
- 시뮬레이션 실험을 통해 최적 항생제 선택 가능성 확인

### 5. Q&A

Q1: 항생제 투여 이전 시점의 Treatment가 0인데 괜찮은가?

A1: TFT 구조상 과거에는 비어 있어도 되고, Decoder에 known input으로 들어가므로 문제 없음

Q2: 100종 항생제 one-hot이나 embedding 구조의 한계

A2: 차원이 크고 sparse하여 과적합 우려 있음

Q3: Multi-hot 임베딩을 사용할 경우

A3: 단순하긴 하나 feature 수가 많고 해석이 어려움

Q4: 항생제를 계열로 분류하여 사용

A4: feature 수 줄일 수 있고 해석력 좋지만, 결과가 일반적일 수 있음

Q5: 항생제 임베딩을 통해 latent vector 생성

A5: 항생제-균주-반응시간 간 관계를 자동으로 학습 가능하나, 해석력이 떨어질 수 있음

Q6: 항생제마다 중요한 feature가 다를 수 있음

A6: 모든 feature를 동일하게 처리하면 일반적인 중요도만 얻음. Decoder 분기 등 조건부 구조 도입 필요

Q7: 중복 투여 영향은 아직 반영되지 않았으며 추가 실험 필요

### 6. Future work - 모델 구조

Multi-path TFT with Antibiotic × Strain Interaction
- Multi-path 구조: 항생제별로 Decoder 흐름 다르게 설정, 시나리오 변경 가능
- Antibiotic × Strain 상호작용 임베딩: 항균력 차이를 자동 반영
- Conditional Gated Layer: 특정 조합별로 예측 흐름 조정
- 효과 지연 반영: 항생제마다 효과 지연시간을 가중치로 반영

### 7. Future work의 기대 효과

- 항생제-균주 조합별 임상 반응 반영
- 환자별 항생제 변경 시 예후 시뮬레이션 가능
- 데이터 손실 없이 다양한 조합 반영 가능
- 잠재 공간에서 항생제-균주 상호작용 학습
- 반응 지연 반영으로 예측 정확도 향상
- 항생제별로 중요한 feature가 다를 수 있음을 반영한 설계 가능


#
