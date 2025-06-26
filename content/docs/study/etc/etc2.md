---
date : 2025-06-26
tags: ['2025-06']
categories: ['AI']
bookHidden: true
title: "#2 Explainable AI"
bookComments: true
---

# #2 Explainable AI

#2025-06-26

---

### 1. Explainable AI란?

Explainable AI는 인공지능(AI) 또는 머신러닝(ML) 모델이 어떤 방식으로 특정 결과를 도출했는지 사람이 이해할 수 있도록 설명하는 기술과 방법론.

### 2. XAI 기법 분류

모델 구조	
- Intrinsic:	모델 자체가 설명 가능한 구조 (예: 의사결정나무, 선형회귀 등)
- Post-hoc:	모델 학습 후 별도로 설명 생성 (예: SHAP, LIME)
대상
- Global:	전체 모델의 작동 원리를 설명
- Local:	특정 샘플의 예측 결과를 설명

### 3. 주요 Post-hoc 설명 기법

LIME (Local Interpretable Model-Agnostic Explanations): 주변 입력을 랜덤하게 생성하고, 단순 모델(선형 회귀 등)을 학습해 근사

SHAP (SHapley Additive exPlanations): 게임 이론의 샤플리 값 기반
- 각 피처가 기여한 정도를 공정하게 분배하여 설명
  - 장점: 수학적으로 정당성 확보, 일관된 설명 제공
  - 단점: 계산 비용 큼

Permutation Importance: 입력 피처를 무작위로 섞은 후 예측 성능 감소 정도 측정
- 예측 성능이 크게 감소하면, 중요한 피처로 판단

Saliency Maps (이미지 분야)

### 4. SHAP이란?

각 feature(입력 변수)가 모델의 예측값에 얼마나 기여했는지 정량적으로 계산.

원래는 협력 게임 이론에서
- 여러 플레이어가 팀을 이뤄 보상을 받았을 때, 각 플레이어가 전체 보상에 얼마나 기여했는지를 계산하는 방법인데
- 예시로
  - 축구 게임을 하여 팀 전체가 100점을 획득했다고 가정
  - 팀에는 선수 A, B, C가 있다
    - 누가 더 중요한 선수인지, 각 선수가 점수에 얼마나 기여했는지를 구하려면?
    - 샤플리 값은 다음 순서로 기여도를 평가:
      1) 가능한 모든 순열을 고려
      2) 각 순열에서 A, B, C가 언제 팀에 합류했는지
      3) 그 선수가 팀에 들어오면서 얼마나 점수가 늘었는지 확인 -> 각 선수의 이 평균 기여도를 “샤플리 값”이라고 함.

머신러닝 모델에서:
- 각 feature가 플레이어 역할을 함
  - 모델의 예측값이 팀이 얻은 점수이고
    - SHAP은 "이 예측값이 나오는 데, 각 feature가 얼마나 기여했는가?"를 계산.
    - 예를 들어 모델이 어떤 환자의 사망 확률을 80%라고 예측했을 때
      - 나이: +15%
      - 흡연 여부: +10%
      - 혈압: +5%
      - 기본값: 50%
        - 총합: 50% + 15 + 10 + 5 = 80%
- 즉 SHAP은 예측값을 base value + 각 feature의 기여도로 분해해준다.

수학적 계산 과정:
- 3개의 feature (A, B, C)에서 가능한 feature 조합:
  - {}, {A}, {B}, {C}, {A,B}, {A,C}, {B,C}, {A,B,C}
  - SHAP은 모든 조합에 대해,
    - 해당 feature가 들어갔을 때와 안 들어갔을 때 예측값 차이를 계산
      - 이를 평균하여 기여도(샤플리 값)로 설정한다.
        - 단점: 조합 수가 2^M이라서 feature 수가 많으면 계산량 폭발

### 5. 예시 코드

```python
import pandas as pd
import numpy as np
import pickle
import joblib
import shap
import matplotlib.pyplot as plt
import seaborn as sns
```

