---
date : 2025-06-18
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#3 Density based clustering 알고리즘 - DBSCAN"
bookComments: true
---

# #3 Density based clustering 알고리즘 - DBSCAN

#2025-06-18

---

### 1. 유전체 돌연변이 연구와 DBSCAN

유전체 돌연변이 연구에서 DBSCAN 또는 유사한 density-based clustering을 통해 군집을 식별하는 여러 연구가 있다.

DBSCAN이 사용된 유전체 돌연변이 연구들은 서로 다른 바이러스나 유전체 영역을 분석했지만
- 사용한 데이터에는 공통 특성이 있다.
  1) 변이의 비정규적 분포 (non-uniform):
     - 돌연변이는 일정 위치에 집중되는 hotspot 현상을 보인다.
       - ex) spike 단백질 특정 영역에 몰림.
  2) 클러스터 수 미정:
     - 몇 개의 변이 집단(hotspot)이 존재하는지 사전 지식이 없다.
  3) 군집의 불규칙한 모양과 크기:
     - hotspot의 길이, 모양(밀도, 거리)이 다양하다.
  4) 노이즈 존재:
     - 무작위적 돌연변이, 측정 오류 등으로 인해 의미 없는 변이들(outlier)가 섞여 있다.

### 2. 왜 density-based clustering이 적절한가?

DBSCAN의 기본 아이디어는
- 같은 군집 내에서는 점들이 서로 가깝고 군집 사이에는 점 간 거리가 멀다.
  - 또한 일정 밀도 이하의 점은 군집이 아닌 노이즈로 간주한다.

이는 다음과 같이 효과를 발휘한다
- 전체 데이터로부터 군집을 형성할 때
  1) 군집의 분포에 대한 사전 가정 없음
     - 군집의 분포가 비정규적, 일정 위치에 집중되는 경향이 있을 때 효과적
  2) 군집 수 지정 불필요
     - 몇 개의 변이 집단(hotspot)이 존재하는지 사전 지식이 없을 때 효과적
  3) 불규칙한 모양의 클러스터 탐지 가능
     - 군집의 길이, 모양이 다양할 때 효과적
  4) 밀도 차를 이용해 클러스터 경계 형성(노이즈 제거)
     - 무작위적 돌연변이, 측정 오류 등으로 인해 의미 없는 변이들(outlier)을 제거할 때에
       - 밀도를 반영하고 싶을 때 효과적

### 3. DBSCAN의 핫스팟 검출 성능 판단

기능적 영역(Functional Domains)과의 중복률 분석
- 단백질 도메인 (Pfam), splice site, promoter region, 5′/3′ UTR, transcription factor binding site 등
  - 기능적 유전체 요소들과 유의하게 겹쳤는지 통계적으로 평가 결과, 
    - 무작위 구간 대비 기능 영역과의 중첩 빈도가 유의하게 높음 (p-value < 1e-5 수준)
    - 즉, 이 SMR들이 우연히 모인 hotspot이 아니라 기능적으로 중요한 유전체 위치에 집중된 것임을 입증함 [1]

타겟 유전자와의 중복도 분석
- 타겟 유전자가 암 드라이버 유전자였고
  - 전체 SMR 중 약 30~50%가 Cancer Gene Census (CGC)에 포함된 드라이버 유전자에 존재하거나 overlap됨
  - 일부 SMR는 기존 driver mutation 메서드(MutSigCV 등)로는 탐지되지 않았지만
    - DBSCAN 기반 SMR에 포함되어 있었고 
    - Cancer Gene Census (CGC)에 포함된 드라이버 유전자였다
      - 이는 SMR가 단순한 변이 밀집 영역이 아니라 ‘암을 유발할 수 있는’ 기능적 hotspot일 가능성이 높다는 것을 보여줌 [1]

타 feature와의 연관성 분석
- 각 암 유형에서 SMR에 포함된 mutation을 가진 환자군을 구분하여 분석 (feature: 암 유형, 예후)
  - 특정 SMR를 가진 환자군이 유의미하게 나쁜 예후 또는 표현형적 특징 차이를 보이는 경우 확인
    - 일부 SMR는 암 발생 경로가 알려진 유전자 경로(예: p53 signaling, PI3K/AKT 경로)와 연관되어 있었음
      - 이는 탐지된 hotspot이 임상적 표현형, 예후, 치료 반응 등과도 연계됨을 보여줌 [1]
- 각 클러스터가 특정 대륙에 치우쳐 있는지 혹은 전 지리권에 고르게 분포하는지 분석 (feature: 대륙, 분포 양상)
  - 오세아니아는: 모든 cluster가 거의 균등하게 존재 = 다양한 variant 존재
  - 유럽은: 특정 cluster가 강하게 집중됨 → 지역 유행 strain 반영
  - 아시아, 아프리카는: 일부 cluster 집중 및 몇 개 variant에 편중
    - 즉 DBSCAN이 생성한 클러스터가
      - 단순 벡터 유사도 기반 grouping이 아니라 지역 유행성과 유전적 계통 차이를 반영한 군집임을 보여줌
      - clustering 결과가 epidemiological insight 제공 가능성 확인 [2]
- 각 클러스터의 대표 서열을 계통수 상에 표시했을 때, 같은 클러스터 내 서열들이 계통수에서도 인접하는지 아닌지 확인 (feature: 계통수와 matching 유무)
  - 같은 클러스터 내 서열들이 계통수에서도 인접함
    - 즉 클러스터 내 유전적 근접성과 진화적 근접성이 일치함을 확인 [2]

통계적 유의성 분석
- 각 SMR가 우연히 형성된 것이 아니라는 것을 입증하기 위해
  - Monte Carlo 시뮬레이션 기반의 p-value 산출
  - FDR (False Discovery Rate) ≤ 0.05 기준으로 유의한 SMR만 남김
    - 이로써 “우연히 밀집된 변이 집합”이 아닌, 통계적으로 신뢰할 수 있는 hotspot임을 검증 [1]

타 method와 정량적 지표 기반 비교
- K-means, Hierarchical, BIRCH과
  - Intra-cluster genetic distance(각 클러스터 내 서열 간 pairwise distance 평균) 비교하였고
    - hierarchical/BIRCH보다 응집도 높음을 확인
      - noise 제거와 경계 구분에서 더 우수함을 확인 [2]
- 딥러닝 기반 clustering (Deep Embedded Clustering, DEC)과
  - Intra-cluster 거리 및 Silhouette score, SSE (Sum of Squared Error), BIC를 비교하였고
    - 딥러닝보다 응집도는 약간 낮았으나
      - Silhouette score를 봣을때 클러스터 간 이질성(inter-cluster separation)이 뛰어남 [2]

시각적 구조 평가
- t-SNE plot 시각화
  - 결과
    1) 각 군집 간 경계 명확 (well-separated clusters)
    2) 군집 내 샘플들이 밀집도 유지 (dense core clusters)
    3) 일부 샘플은 Noise (DBSCAN의 outlier 분류)로 감지되어 클러스터 경계 바깥에 위치함
    - 을 확인
      - 즉 군집 수를 미리 지정하지 않아도 자동으로 cluster + noise를 구분할 수 있었음을 눈으로 확인 [2]
- 세포 밀도 기반으로 ε 및 minPts 조합을 자동 선택하는 성능 검증?
  - 여러 ε–minPts 조합에 대해 실제 세포 이미지와 결과 클러스터 위치의 일치도를 눈으로 검토
  - 클러스터 형상이 세포 덩어리와 실제 겹치는지의 시각적 일치를 통해 “올바른 클러스터”를 판단 [3]

### 4. DBSCAN의 커스텀과 성능 평가 및 후속 검증

DBSCAN의 다음과 같은 단점에 따라 특정 데이터에서는 그대로 적용하기에 부적합
- 군집 내 지역 밀도 차이 (일부 클러스터는 매우 조밀하고, 다른 클러스터는 상대적으로 희박)가 있는 데이터
  - Global ε, MinPts 고정이어서
    - 세포 배양 밀도(density)가 이미지 내 위치마다 다른데 동일한 파라미터를 적용하면 저밀도 영역은 클러스터 누락 고밀도 영역은 클러스터 과도 확장 발생함 [3]
  - Edge 구분 불가능이어서
    - 세포를 core vs. noise로만 나누며 경계(edge)에 있는 세포를 명확히 분류하지 못함 [3]
  - 클러스터 수 불확정성 때문에
    - 실험 조건, 이미지 품질, 배양 상태에 따라 군집 수가 크게 달라져 비교·해석이 어려움 [3] 
  - 데이터 별 중요도 가중치 반영 불가 여서
    - 클러스터 형성에 돌연변이의 density만 반영되어 중요도를 반영하지 못함 [4]
  - 클러스터 크기 즉 경계 설정이 MinPts와 ε에 따라 수동적으로 결정
    - 클러스터 size를 density를 반영하여 좀더 robust하게 커스텀하지 못함 [4]

- DBSCAN‑CellX는 다음과 같은 구조적 커스터마이징을 통해 위 문제를 해결:
  - Local adaptive ε & minPts 설정으로
    - 세포 밀도를 기반으로 위치별 ε 조정해서 세포가 희박한 위치는 더 넓게, 밀집된 위치는 좁게 탐색함
  - Core / Edge / Noise 3분류를 수행해서
    - 기존의 이분법(core/noise)에서 벗어나 edge 세포를 따로 구분하여 생물학적으로 중요한 경계 특성 반영
  - 자동 파라미터 튜닝 알고리즘 탑재해서
    - 사용자가 설정할 필요 없이, 이미지에서 local density를 추정하여 적절한 파라미터 추론함 [3]

- Mutclust는 다음과 같은 커스터마이징:
  - 중요도 기반 Local adaptive ε & minPts 설정으로
    - 클러스터 형성에 돌연변이의 density와 중요도를 모두 반영
  - 클러스터 크기를 MinPts와 ε에 따라 1차적으로 설정한 다음
    - density와 중요도를 모두 반영하여 경계 보정하는 알고리즘을 도입하여
      - 클러스터 크기 즉 경계 설정에서 density와 중요도를 모두 반영하도록 커스텀 가능하게함 (diminishing factor) [4]

- DBSCAN‑CellX의 클러스터 성능 검증:
  -  

