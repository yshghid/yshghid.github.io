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

# 1. DBSCAN이 사용된 유전체 돌연변이 연구

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
  5) 군집 내 지역 밀도 차이:
     - 일부 클러스터는 매우 조밀하고, 다른 클러스터는 상대적으로 희박하다.

# 2. 왜 density-based clustering이 적절한가?

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
  
# 3. 데이터 세부 특성에 따라 density-based clustering을 어떻게 커스텀했는가?

연구마다 데이터와 분석 목적은 조금씩 상이한데,
- DBSCAN을 naive하게 사용했는지 커스텀했는지를 보고
  - 커스텀했다면 어떤 특성 또는 목적에 효과적이길 기대하고 커스텀했는지
  - 결과는 어땠는지 확인.
 


# 4. '핫스팟 검출 성능 우수' 판단 방법

기능적 영역(Functional Domains)과의 중복률 분석
- 단백질 도메인 (Pfam), splice site, promoter region, 5′/3′ UTR, transcription factor binding site 등
  - 기능적 유전체 요소들과 유의하게 겹쳤는지 통계적으로 평가 결과, 
    - 무작위 구간 대비 기능 영역과의 중첩 빈도가 유의하게 높음 (p-value < 1e-5 수준)
    - 즉, 이 SMR들이 우연히 모인 hotspot이 아니라 기능적으로 중요한 유전체 위치에 집중된 것임을 입증함 [1]

암 드라이버 유전자와의 중복도 분석
- 전체 SMR 중 약 30~50%가 Cancer Gene Census (CGC)에 포함된 드라이버 유전자에 존재하거나 overlap됨
  - 일부 SMR는 기존 driver mutation 메서드(MutSigCV 등)로는 탐지되지 않았지만
    - DBSCAN 기반 SMR에 포함되어 있었고 
    - Cancer Gene Census (CGC)에 포함된 드라이버 유전자였다
      - 이는 SMR가 단순한 변이 밀집 영역이 아니라 ‘암을 유발할 수 있는’ 기능적 hotspot일 가능성이 높다는 것을 보여줌 [1]

각 암 유형(cohort)에서 SMR에 포함된 mutation을 가진 환자군을 구분하여 분석
- 특정 SMR를 가진 환자군이
  - 유의미하게 나쁜 예후 또는 표현형적 특징 차이를 보이는 경우가 발견되었고
    - 일부 SMR는 암 발생 경로가 알려진 유전자 경로(예: p53 signaling, PI3K/AKT 경로)와 연관되어 있었음
      - 이는 탐지된 hotspot이 임상적 표현형, 예후, 치료 반응 등과도 연계됨을 보여줌 [1]

통계적 유의성 분석
- 각 SMR가 우연히 형성된 것이 아니라는 것을 입증하기 위해
  - Monte Carlo 시뮬레이션 기반의 p-value 산출
  - FDR (False Discovery Rate) ≤ 0.05 기준으로 유의한 SMR만 남김
    - 이로써 “우연히 밀집된 변이 집합”이 아닌, 통계적으로 신뢰할 수 있는 hotspot임을 검증 [1]

파라미터 검증
- 세포 밀도 기반으로 ε 및 minPts 조합을 자동 선택했는데
  - 이 성능 검증은?
    - 여러 ε–minPts 조합에 대해 실제 세포 이미지와 결과 클러스터 위치의 일치도를 눈으로 검토,
    - 클러스터 형상이 세포 덩어리와 실제 겹치는지의 시각적 일치를 통해 “올바른 클러스터”를 판단함 [2]




정답 label이 없는 unsupervised learning인 clustering은 supervised learning과 달리 정확도, AUC curve 등으로 성능 평가 불가
- 정량적 평가 지표로는:
  - Intra-cluster genetic distance (클러스터 내 유전 거리): 작을수록 내부 군집 응집도가 좋음
  - Silhouette score, SSE, BIC 등의 지표 사

- 그 외 방법으로는:
  - 방향성이 같은 또는 같지 않아야 하는 비교 feature를 선택하고 비교
    - ex) 계통학적 구조가 지리적 패턴과 일치함
  - t‑SNE 시각화 등 시각적 확인
    - t‑SNE로 축소된 2D scatter plot 위에 DBSCAN으로 얻은 cluster를 색상별로 표시해서
      - 군집 간의 명확한 경계, 군집 내 응집성이 시각적으로 확인
