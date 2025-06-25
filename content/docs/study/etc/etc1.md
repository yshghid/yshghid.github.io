---
date : 2025-06-25
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#1 DBSCAN"
bookComments: true
---

# #1 DBSCAN

#2025-06-25

---

### DBSCAN의 개념

DBSCAN은 밀도 기반 클러스터링 알고리즘으로
- 데이터가 밀집된 영역을 클러스터로 인식하고
- 밀도가 낮은 영역은 노이즈(이상치)로 간주하는 방법.

KMeans와 달리, 군집 수를 미리 정하지 않아도 되며, 
- 비선형 구조나 잡음이 있는 데이터에서 잘 작동한다.

### 파라미터와 핵심 용어

주요 파라미터는 2개
- eps: 반지름 거리. 한 점에서 eps 거리 내에 있는 점들을 "이웃"이라고 판단.
- min_samples: core point로 인정되기 위해 필요한 최소 이웃 수

핵심 용어는 3개
- Core Point (중심점): eps 거리 내에 min_samples 이상 이웃이 있는 점
- Border Point (경계점): core point의 eps 거리 내에 있으나, 자기 자신은 core point가 아닌 점
- Noise Point (잡음점): 어떤 core point의 eps 안에도 포함되지 않는 점

### 장점과 단점

장점 4개
- 자동 군집수 결정
- 이상치 탐지 가능
- 복잡한 클러스터 형태 탐지
- 비지도 학습

단점 3개
- eps 값 설정이 민감함
- 밀도가 다른 클러스터는 잘 분리 못함 (밀도 기준이 하나뿐이라 불균형 분포에 약함)
- 고차원 데이터에선 거리 개념이 희석되므로 차원 축소(t-SNE, PCA 등) 필요.

### Q&A

Q1) DBSCAN은 몇차원에서 제일 효율적인가?

A1) 

2(~3)차원에서 가장 효율적. 
- 거리 개념이 명확하고 시각화 가능
- 시각화 가능 -> 시각화 통해 군집 구조 확인 가능 -> eps 직관적으로 조정 가능

4~10차원에서 점점 어려워짐.
- 거리 분포가 평평해지고, core point 조건을 충족시키기 어려움
- 유클리드 거리 기반 eps 조정이 매우 민감해짐
- 차원 축소(PCA, t-SNE, UMAP) 후 사용 추천

10차원 이상
- 거리 희소성(dimensionality curse): 모든 점 간 거리가 비슷해져 밀도 기반 판별이 어려워짐
- eps와 min_samples 조합이 성능에 큰 영향을 주며, 조정이 어렵고 불안정함
- 고차원에선 DBSCAN보다 HDBSCAN, Spectral Clustering, 또는 Spherical KMeans 등을 고려 / 또는 차원 축소를 선행한 후 DBSCAN 사용

Q2) 파라미터 선택법?

A2)

1. 이론적 기준으로 min_samples=2*d를 적용해서 min_samples 후보값을 정함
2. k = min_samples-1로 설정하여 k-distance plot을 그림
3. elbow point을 찾아 eps를 결정
4. 다양한 min_samples로 그래프를 여러 번 그려보고 -> 가장 뚜렷한 elbow point을 주는 min_samples를 선택

### 성능 평가


```python

```
