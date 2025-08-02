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

## 개념

DBSCAN은 밀도 기반 클러스터링 알고리즘으로
- 데이터가 밀집된 영역을 클러스터로 인식하고
- 밀도가 낮은 영역은 노이즈(이상치)로 간주하는 방법.

KMeans와 달리, 군집 수를 미리 정하지 않아도 되며, 
- 비선형 구조나 잡음이 있는 데이터에서 잘 작동한다.

## 파라미터와 핵심 용어

주요 파라미터는 2개
- eps: 반지름 거리. 한 점에서 eps 거리 내에 있는 점들을 "이웃"이라고 판단.
- min_samples: core point로 인정되기 위해 필요한 최소 이웃 수

핵심 용어는 3개
- Core Point (중심점): eps 거리 내에 min_samples 이상 이웃이 있는 점
- Border Point (경계점): core point의 eps 거리 내에 있으나, 자기 자신은 core point가 아닌 점
- Noise Point (잡음점): 어떤 core point의 eps 안에도 포함되지 않는 점

## 장점과 단점

장점 4개
- 자동 군집수 결정
- 이상치 탐지 가능
- 복잡한 클러스터 형태 탐지
- 비지도 학습

단점 3개
- eps 값 설정이 민감함
- 밀도가 다른 클러스터는 잘 분리 못함 (밀도 기준이 하나뿐이라 불균형 분포에 약함)
- 고차원 데이터에선 거리 개념이 희석되므로 차원 축소(t-SNE, PCA 등) 필요.

## Q&A

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

## 성능 평가

DBSCAN은 비지도 학습 알고리즘이기 때문에, 성능 평가에 있어서 supervised 방식과는 다른 접근이 필요

내부 평가 지표
- Silhouette Score
  - 각 점이 속한 클러스터 내부 응집도와, 가장 가까운 다른 클러스터와의 거리 차이를 비교
  - -1 ~ 1 (1: 잘 클러스터됨, 0: 경계에 있음)
- Davies-Bouldin Index
  - 클러스터 간 간격이 멀고, 내부 응집도가 높을수록 좋은 값
  - 값이 작을수록 우수
- Calinski-Harabasz Index
  - 클러스터 간 분산 / 클러스터 내 분산 비율
  - 값이 클수록 좋은 클러스터링

외부 평가 지표 (만약 정답 레이블이 있다면 다음 지표들도 사용 가능)
- Adjusted Rand Index (ARI): 무작위 군집과 비교하여 클러스터 일치 정도 확인 (1에 가까울수록 좋음)
- Normalized Mutual Information (NMI): 군집 정보가 얼마나 label과 유사한지 확인
- Fowlkes–Mallows index (FMI): TP 기준 군집 일치 정도

시각화 기반 평가
- 2D나 t-SNE로 클러스터링 결과 시각화해서, 클러스터 모양, 분리 정도 노이즈의 위치 분포 군집 수가 과도하지 않은지 등을 확인
- 1D 데이터에서는 사용 불가


## 파이썬 구현 - DBSCAN

```python
import math

def euclidean_distance(p1, p2):
    return math.sqrt(sum((a - b) ** 2 for a, b in zip(p1, p2)))

def region_query(data, point_idx, eps):
    neighbors = []
    for idx, point in enumerate(data):
        if euclidean_distance(data[point_idx], point) <= eps:
            neighbors.append(idx)
    return neighbors

def expand_cluster(data, labels, point_idx, neighbors, cluster_id, eps, min_samples):
    labels[point_idx] = cluster_id
    i = 0
    while i < len(neighbors):
        n_idx = neighbors[i]
        if labels[n_idx] == -1:  # noise → now becomes part of a cluster
            labels[n_idx] = cluster_id
        elif labels[n_idx] == 0:
            labels[n_idx] = cluster_id
            n_neighbors = region_query(data, n_idx, eps)
            if len(n_neighbors) >= min_samples:
                neighbors += n_neighbors
        i += 1

def dbscan(data, eps, min_samples):
    labels = [0] * len(data)  # 0 = unvisited, -1 = noise, ≥1 = cluster id
    cluster_id = 0

    for idx in range(len(data)):
        if labels[idx] != 0:
            continue  # 이미 방문한 점

        neighbors = region_query(data, idx, eps)

        if len(neighbors) < min_samples:
            labels[idx] = -1  # noise
        else:
            cluster_id += 1
            expand_cluster(data, labels, idx, neighbors, cluster_id, eps, min_samples)

    return labels

```

## 파이썬 구현 - k distance plot

```python
import numpy as np
import matplotlib.pyplot as plt

def euclidean_distance(p1, p2):
    return np.sqrt(np.sum((p1 - p2) ** 2))

def compute_k_distances(X, k):
    """
    각 포인트에 대해 k번째 최근접 이웃까지의 거리 계산
    
    Parameters:
    - X: (n_samples, n_features) ndarray
    - k: 이웃의 수 (k = min_samples - 1)
    
    Returns:
    - k_distances: 각 포인트의 k번째 최근접 이웃 거리 리스트
    """
    n_samples = len(X)
    k_distances = []

    for i in range(n_samples):
        distances = []
        for j in range(n_samples):
            if i != j:
                dist = euclidean_distance(X[i], X[j])
                distances.append(dist)
        distances.sort()
        k_distances.append(distances[k - 1])  # k번째 작은 거리

    return np.sort(k_distances)

def plot_k_distance_manual(X, k):
    """
    sklearn 없이 k-distance plot 그리기
    
    Parameters:
    - X: (n_samples, n_features) ndarray
    - k: int, 이웃 수 (= min_samples - 1)
    """
    k_distances = compute_k_distances(X, k)

    plt.figure(figsize=(8, 4))
    plt.plot(k_distances)
    plt.ylabel(f"{k}-th nearest neighbor distance")
    plt.xlabel("Points sorted by distance")
    plt.title(f"Manual k-distance plot (k={k})")
    plt.grid(True)
    plt.show()

```

## 파이썬 구현 - silhouette score

```python
import numpy as np

def silhouette_score_manual(X, labels):
    """
    Silhouette Score를 직접 계산하는 함수

    Parameters:
    - X: (n_samples, n_features) ndarray
    - labels: (n_samples,) 클러스터 ID, 노이즈는 제외되어 있어야 함 (-1 제거 필수)

    Returns:
    - 평균 Silhouette Score (float)
    """
    unique_labels = set(labels)
    if len(unique_labels) <= 1:
        raise ValueError("클러스터가 1개 이하입니다. Silhouette Score를 계산할 수 없습니다.")

    n_samples = len(X)
    silhouette_values = []

    for i in range(n_samples):
        own_cluster = labels[i]
        same_cluster_indices = [j for j in range(n_samples) if labels[j] == own_cluster and j != i]
        
        # a(i): 같은 클러스터 내 평균 거리
        if same_cluster_indices:
            a = np.mean([np.linalg.norm(X[i] - X[j]) for j in same_cluster_indices])
        else:
            a = 0  # 고립된 점
        
        # b(i): 가장 가까운 다른 클러스터와의 평균 거리
        b = float('inf')
        for other_cluster in unique_labels:
            if other_cluster == own_cluster:
                continue
            other_indices = [j for j in range(n_samples) if labels[j] == other_cluster]
            if other_indices:
                b_dist = np.mean([np.linalg.norm(X[i] - X[j]) for j in other_indices])
                b = min(b, b_dist)

        # s(i): silhouette score for point i
        if max(a, b) == 0:
            s = 0
        else:
            s = (b - a) / max(a, b)
        silhouette_values.append(s)

    return np.mean(silhouette_values)
```

## 전체 파이프라인 실행

```python
# 1. 데이터 생성
X, _ = make_moons(n_samples=300, noise=0.05, random_state=0)

# 2. k-distance plot
min_samples = 5
plot_k_distance_manual(X, k=min_samples - 1)

# 3. 클러스터링 (여기선 elbow 보고 eps=0.125 정도 선택)
eps = 0.15
labels = dbscan(X, eps=eps, min_samples=min_samples)

# 4. 클러스터 시각화
plt.figure(figsize=(6, 5))
plt.scatter(X[:, 0], X[:, 1], c=labels, cmap='tab10', s=10)
plt.title(f"DBSCAN Clustering (eps={eps}, min_samples={min_samples})")
plt.xlabel("X1")
plt.ylabel("X2")
plt.grid(True)
plt.show()

# 5. Silhouette Score 계산
score = silhouette_score_manual(X, labels)
print(f"\nSilhouette Score: {score:.4f}")
```
![image](https://github.com/user-attachments/assets/0dacd3df-037c-40a1-84f8-7ea8012e61fa)
![image](https://github.com/user-attachments/assets/aed95c7b-432d-4299-bd50-052775a34760)

```plain text
Silhouette Score: 0.3327
```

