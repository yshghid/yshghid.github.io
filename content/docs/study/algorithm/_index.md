---
weight: 18
title: "알고리즘"
bookComments: false
type: docs
bookHidden: false
---

# 알고리즘

#####

*2025-08-10* ⋯ 학위논문작업 #2 클러스터링 로그 뽑기

[Objective MutClust의 기존 코드에서는 클러스터링 수행후 클러스터 정보만 출력할뿐 neighbor eps scaler에 따른 ccm eps scaler의 업데이트와 그에 따른 eps 업데이트 내역을 따로 빼진 않았었다. 근데 클러스터링 과정을 설명하기에 좋은 예시를 만들기가 어려워서 (기존 예시는 맘에 안들고..) 그냥 로그를  ⋯](https://yshghid.github.io/docs/study/algorithm/algo14/)

---

*2025-08-05* ⋯ 학위논문작업 #1 핵심함수 로직 정리

[1. input def expand_cluster(ccmIdx, mutData, info): ccm의 인덱스 ccmIdx 돌연변이 중요도 정보 mutData info: 기본 세팅 파라미터  scaler_l = mutData[ccmIdx]['eps_scaler'] idx_l = ccmIdx - 1 eps_l = mutData[ccmIdx]['left_distance'] pos_l = mutData[ccmIdx] ⋯](https://yshghid.github.io/docs/study/algorithm/algo13/)

---

*2025-07-28* ⋯ DBSCAN #2 슈도코드

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D For each point x in D: If label[x] ≠ UNVISITED: continue ⋯](https://yshghid.github.io/docs/study/ai/ai9/)

---

*2025-07-28* ⋯ DBSCAN: #1 1D 클러스터링의 성능 평가

[1. Problem 클러스터 응집도는 보통 클러스터 내 데이터 간의 평균 거리나 분산, 혹은 실루엣 계수처럼 군집 내 응집도와 군집 간 분리도를 동시에 평가한다. 하지만 1차원 데이터에서는 클러스터 응집도(Cluster Cohesion) 또는 실루엣 계수(Silhouette coefficient) 같은 지표가 잘 작동하지 않는다. 2. 클러스터 응집도 클러스터링 성능을 평가 ⋯](https://yshghid.github.io/docs/study/ai/ai8/)

---

*2025-07-28* ⋯ MutClust 슈도코드 작성하기

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D 데이터 집합 D, 파라미터 eps와 minPts가 들어간다. 2 ⋯](https://yshghid.github.io/docs/study/ai/ai10/)

---

*2025-06-18* ⋯ Related Study #4 Clustering 알고리즘의 parametric test

[정답 label이 없는 unsupervised learning인 clustering은 supervised learning과 달리 정확도, AUC curve 등으로 성능 평가 불가. 정량적 평가 지표? Intra-cluster genetic distance (클러스터 내 유전 거리): 작을수록 내부 군집 응집도가 좋음 Silhouette score, SSE, BIC 등의 지표 사용 그 외 방법으로는: 방향성이  ⋯](https://yshghid.github.io/docs/study/tech/tech25/)

---

*2025-06-18* ⋯ Related Study #3 Density-based approach

[1. Density based approach가 잘 적용되는 데이터의 특성 (에 mutation 데이터 끼워맞추면)? 비정규적 분포 (non-uniform): 돌연변이는 일정 위치에 집중되는 hotspot 현상을 보인다. ex) spike 단백질 특정 영역에 몰림. 클러스터 수 미정: 몇 개의 변이 집단(hotspot)이 존재하는지 사전 지식이 없다. 군집의 불규칙한 모양과 크기: hotspot의 길이 ⋯](https://yshghid.github.io/docs/study/tech/tech24/)

---

*2025-06-17* ⋯ Related Study #2 Cluster detection algorithm

[돌연변이는 무작위로 발생하지만 실제 분포를 확인해보면 그렇지 않다. 엄연히 군집을 형성하고 있으며 이는 해당 돌연변이의 '생존'에 관여한 외부 요인의 존재를 보여준다. 논문 "Computational methods for detecting cancer hotspots" 암에서 반복적으로 관찰되는 돌연변이 즉 핫스팟(hotspot)을 식별하기 위한 계산적 방법 40여개에 대한 리뷰 ⋯](https://yshghid.github.io/docs/study/tech/tech23/)

---

*2025-06-17* ⋯ Related Study #1 샤넌 엔트로피

[1 샤넌 엔트로피? - 단백질 서열 상 특정 위치에 다양한 아미노산이 얼마나 골고루 존재하는지를 나타내는 지표 - 어떤 위치에 여러 아미노산이 비슷한 비율로 존재한다면 엔트로피가 높고, 하나의 아미노산이 압도적으로 우세하다면 엔트로피가 낮다. 돌연변이 데이터에서 샤넌 엔트로피 - 전통적인 샤논 엔트로피에 대한 해석은 논코딩 영역의 식별. ⋯](https://yshghid.github.io/docs/study/tech/tech22/)


#
