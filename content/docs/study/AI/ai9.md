---
date : 2025-07-28
tags: ['2025-07']
categories: ['AI', 'DBSCAN']
bookHidden: true
title: "DBSCAN #2 슈도코드"
---

# DBSCAN #2 슈도코드

#2025-07-28

---


### 1

```
Input:
    - D: 데이터 포인트 집합
    - eps: 이웃 거리 임계값
    - minPts: 최소 이웃 수 (밀도 기준)

Output:
    - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1)

Initialize:
    - cluster_id ← 0
    - label[x] ← UNVISITED for all x in D

```

데이터 집합 D, 파라미터 eps와 minPts가 들어간다.


### 2

```
For each point x in D:
    If label[x] ≠ UNVISITED:
        continue

    N ← regionQuery(x, eps)   // x 주변의 eps 이내 이웃 포인트 탐색

    If |N| < minPts:
        label[x] ← NOISE
//  Else:
//      cluster_id ← cluster_id + 1
//      expandCluster(x, N, cluster_id, eps, minPts, label)

Function regionQuery(x, eps):
    return { all points y in D such that distance(x, y) ≤ eps }
```

주석 처리 안된 부분만 보기.

먼저 현재위치 x의 eps 내에 데이터 포인트가 몇개인지부터 확인한다. minPts보다 작으면, 노이즈로 처리한다.

```
    Else:
        cluster_id ← cluster_id + 1
        expandCluster(x, N, cluster_id, eps, minPts, label)
```

아니면? 클러스터 확장을 수행한다. 

```
Function expandCluster(x, neighbors, cluster_id, eps, minPts, label):
    label[x] ← cluster_id

    For each point y in neighbors:
        If label[y] = UNVISITED:
            label[y] ← cluster_id
            N' ← regionQuery(y, eps)
            If |N'| ≥ minPts:
                neighbors ← neighbors ∪ N'  // Core point이면 이웃 확장

//      Else If label[y] = NOISE:
//          label[y] ← cluster_id  // noise → border point로 재분류
```

현재위치 x의 eps 내에 데이터포인트들을 봣을때, 노이즈가 아닌 이웃포인트 y는 regionQuery를 수행해서 반경 내 데이터포인트수가 minPts 보다 크면 반경 내 모든 데이터포인트들을 x의 neighbor로 통합한다.

```
        Else If label[y] = NOISE:
            label[y] ← cluster_id  // noise → border point로 재분류
```

노이즈였지만 현재는 x의 이웃포인트가 된 y는 border point로 재분류해준다.

#
