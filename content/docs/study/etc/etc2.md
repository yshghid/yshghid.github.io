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

### 5. RF의 feature importance와의 차이?

| 항목 | Random Forest Feature Importance | SHAP |
| --- | --- | --- |
| 기반 개념 | 모델 구조 기반 (gini 감소 등) | 게임 이론 기반 (샤플리 값) |
| 설명 방식 | 전체 모델 수준 (global) | 전체 + 개별 샘플 수준 (global + local) |
| 음/양 구분 | 없음 (0 이상, 크기만 제공) | 있음 (양수: 예측 ↑, 음수: 예측 ↓) |
| 상호작용 고려 | 부분적으로만 고려 | 일부 고려 가능 (특정 SHAP variant) |
| 정확성 | 대략적인 영향도 | 수학적으로 보장된 기여도 |
| 단점 | bias 있음 (범주 수 많은 변수 선호 등) | 느릴 수 있음, 계산 비용 높음 |

Random Forest의 Feature Importance
- 작동 방식
- RF는 다수의 결정트리를 만들고
  - 각 트리에서 어떤 feature를 쪼갤 때 예측 성능이 얼마나 좋아졌는지(ex: Gini impurity 감소량)를 기록.
  - 여러 트리에서 해당 feature가 얼마나 자주, 얼마나 크게 성능 향상에 기여했는지를 평균하여 importance로 계산
- 단점
  - 범주 수가 많은 feature가 유리 (더 잘 쪼갤 확률 높음)
  - 상호작용 고려 부족
  - 왜 중요했는지 설명 불가
  - 개별 샘플 설명 불가

SHAP의 Feature Importance
- SHAP은 다음을 제공:
  - 각 feature가 개별 예측값에 얼마나 영향을 줬는지. 양/음 포함.
  - 모든 샘플에 대해 계산한 후 평균을 내면, global feature importance가 됨.
  - 왜 중요했는지 샘플별로 추적 가능

먼소린지 이해 안돼서.. 직관적 예시.
- Random Forest의 Feature Importance는 누가 결정 과정에 자주 참여했는지 본다, 마치 회의에서 많이 말한 사람을 중요한 사람이라고 보는 것과 같음.
- SHAP의 Feature Importance는 누가 실제로 의사결정 결과에 영향을 줬는지 본다, 마치 회의에서 실제로 투표를 바꿔놓은 사람을 중요한 사람으로 보는 것과 같음.
  즉 RF는 참여 횟수, SHAP은 결과에 기여한 정도를 보는 거예요.

모델 예시
- 모델이 환자의 사망 확률을 예측할때
  - 환자 입력: 나이 80세 / 체온 39도 / 혈압 100 / 흡연 여부 Yes
- Random Forest는?
  - 100개의 트리에서 나이로 70번 쪼갬 / 체온으로 10번 쪼갬 / 혈압으로 15번 쪼갬 / 흡연 여부로 5번 쪼갬
    - 그래서 나이가 제일 중요하다고 판단 (근데 ‘나이’가 예측값에 얼마나 영향을 줬는지는 모름)
- SHAP은?
  - 이 환자의 예측값은 0.80 (기본값은 0.50)
  - 기여도: 나이 +0.20 / 체온: +0.10 / 흡연: +0.08 / 혈압: -0.08
    - 합치면: 0.50 + 0.20 + 0.10 + 0.08 - 0.08 = 0.80
    - 즉 SHAP은 예측값이 왜 0.80이 되었는지 명확하게 설명.

하나의 feature라도 값이 높을 때 어떤 경우엔 예측을 ↑ 어떤 경우엔 예측을 ↓ 시킬 수 있다 그리고 이 복잡한 관계를 시각적으로 한 번에 보여주는 것이 바로 SHAP의 summary plot.

'체온' feature로 예시.

```plain text
      체온  ───🔵🔵🔵🔴🔴🔴🔴🔴🔵🔵──▶
          (SHAP 값: 음수~양수)
```

🔴: 체온이 높은 샘플들
- 오른쪽(+)에 위치한 🔴: 체온이 높아서 예측값(사망 확률)이 증가
- 왼쪽(-)에 위치한 🔴: 체온이 높지만 예측값은 오히려 감소
🔵: 체온이 낮은 샘플들
- 오른쪽(+)에 위치한 🔵: 체온이 낮지만 예측값은 증가
- 왼쪽(-)에 위치한 🔵: 체온이 낮고 예측값도 낮음

샘플마다 기여도와 방향이 다른 이유
- SHAP은 모든 feature를 "다른 feature와 함께 썼을 때"의 영향력을 따진다.
  - 그래서 "조건부 기여도"라고도 함.
    - 즉 체온이 높아도 젊은 환자라면 사망 확률이 낮을 수 있고 체온이 낮아도 기저질환이 심한 환자라면 사망 확률이 높을 수 있다
    - 이런 복잡한 상호작용을 반영하다보니 같은 feature라도 샘플마다 기여 방향이 다를 수 있다. 

### 6. 결론

같은 feature라도, 샘플마다 다른 상황(context)이기 때문에, 그 feature의 예측에 대한 기여 방향(↑ 또는 ↓)이 달라질 수 있다.

예를 들어
- 나이 = 70인 사람이라도
  - 다른 feature(혈압, 체온, 기저질환 등)에 따라
    - 어떤 샘플에선 사망 확률 ↑에 기여 (양의 SHAP 값)
    - 어떤 샘플에선 사망 확률 ↓에 기여 (음의 SHAP 값)
      - 그래서 SHAP summary plot에서 같은 feature의 🔴와 🔵 점들이 좌우로 흩어져 있다.

정리하면?
- SHAP은 "같은 feature"가 "다양한 맥락에서 어떻게 작용하는가"를 보여주는 도구이다.

### 7. 예시 코드

```python
import pandas as pd
import numpy as np
import pickle
import joblib
import shap
import matplotlib.pyplot as plt
import seaborn as sns
```
```python
#Load rf model
with open('/model/rf_model.pkl','rb') as f:
    rf_model = joblib.load(f)

#Load dataset
with open('/preprocessing/processed_data.pickle','rb') as f:
    preproc_data = pickle.load(f)

cytokine_df = preproc_data['cytokine_data']
patient_meta = preproc_data['metadata'] 
patient_info = preproc_data['clinical'] 
```
```python
# Get feature importances
importances = rf_model.feature_importances_
feature_names = cytokine_df.columns
feature_importances = pd.DataFrame({'feature': feature_names, 'importance': importances})

# Sort the feature importances in descending order and select the top 20
top_20_features = feature_importances.sort_values(by='importance', ascending=False).head(20)

# Plot the top 20 feature importances
plt.figure(figsize=(6, 10))
sns.barplot(x='importance', y='feature', data=top_20_features)
plt.show()
```
RF 내부의 feature importance를 시각화
- 어떤 사이토카인이 모델에서 자주 쓰였는지(중요한지)를 보여줌
- 이 값은 SHAP처럼 "예측에 얼마나 기여했는가"를 나타내지 않고, 단순히 "쪼개는 데 많이 쓰였는가" 기준.


```python
tree_explainer = shap.TreeExplainer(rf_model) ## TreeExplainer
shap_values = tree_explainer.shap_values(cytokine_df) ## SHAP Value
```
이진 분류 모델(Random Forest, XGBoost 등)에 shap.TreeExplainer를 적용하면
- 이 shap_values는 리스트 2개로 구성:
  - shap_values[0]: 클래스 0 (음성 클래스)에 대한 SHAP 값
  - shap_values[1]: 클래스 1 (양성 클래스)에 대한 SHAP 값



```python
fig = plt.figure(figsize=(8,8))
fig.set_facecolor('white')
ax = fig.add_subplot()
#Plot SHAP as sever probability
shap.summary_plot(shap_values[1], cytokine_df, 
                  cmap='bwr', 
                  show=False, 
                 plot_type='dot')
ax.set_xlabel('SHAP Value')
ax.set_title('SHAP Dot Plot', fontsize=20)
plt.show()
```
shap_values[1]: 이진 분류에서 양성 클래스에 대한 SHAP 값

summary plot: 각 feature가 예측에 미친 영향(양/음, 세기)을 샘플별로 시각화 (빨강=값 큼, 파랑=값 작음)

```python
shap_df = pd.DataFrame(shap_values[1],columns = cytokine_df.columns)
shap_df.index = cytokine_df.index
shap_df
```
![image](https://github.com/user-attachments/assets/32d7410b-f67c-4f88-9587-e6b41e8c8276)

```python
import umap.umap_ as umap
import pandas as pd
import matplotlib.pyplot as plt
import seaborn as sns

reducer = umap.UMAP()
embedding = reducer.fit_transform(shap_df)
```
```python
import matplotlib.pyplot as plt

# Extract UMAP coordinates and labels
umap_x = embedding[:, 0]
umap_y = embedding[:, 1]

# Create scatter plot
plt.figure(figsize=(10, 8))
scatter = plt.scatter(umap_x, umap_y, cmap="bwr", s=50, alpha=0.7, edgecolors="w", linewidth=0.5)
```
```python
from sklearn.cluster import DBSCAN

# Initialize DBSCAN
dbscan = DBSCAN(eps=0.8, min_samples=3) # partial data is too small to set min_sample=20.

# Fit to UMAP data and get cluster labels
clusters = dbscan.fit_predict(embedding)
embedding, clusters
```
```python
(array([[16.714314 , -2.0475426],
        [17.279623 , -2.4140635],
        [16.705837 , -3.002305 ],
        [17.19955  , -1.342096 ],
        [17.838465 , -2.021136 ],
        [18.537838 , -1.5079662],
        [21.44188  , -2.1259143],
        [21.123413 , -3.075382 ],
        [20.373632 , -3.0233152],
        [21.83852  , -2.899527 ],
        [20.435349 , -2.2629123]], dtype=float32),
 array([ 0,  0, -1, -1,  0, -1, -1,  1,  1,  1,  1]))
```
```python
plt.figure(figsize=(10, 6))
unique_clusters = np.unique(clusters)

for cluster in unique_clusters:
    idx = clusters == cluster
    plt.scatter(embedding[idx, 0], embedding[idx, 1], label=f'Cluster {cluster}')

plt.title('Scatter Plot of UMAP Colored by Cluster')
plt.xlabel('UMAP_1')
plt.ylabel('UMAP_2')
plt.legend()
plt.grid(True)
plt.show()
```
