---
date : 2025-06-18
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "Related Study #3 Density-based approach"
bookComments: true
---

# Related Study #3 Density-based approach

#2025-06-18

---

### 1. Density based approach가 잘 적용되는 데이터의 특성

(에 mutation 데이터 끼워맞추면)?

- 비정규적 분포 (non-uniform): 돌연변이는 일정 위치에 집중되는 hotspot 현상을 보인다. ex) spike 단백질 특정 영역에 몰림.
- 클러스터 수 미정: 몇 개의 변이 집단(hotspot)이 존재하는지 사전 지식이 없다.
- 군집의 불규칙한 모양과 크기: hotspot의 길이, 모양(밀도, 거리)이 다양하다.
- 노이즈 존재: 무작위적 돌연변이, 측정 오류 등으로 인해 의미 없는 변이들(outlier)가 섞여 있다.

###

### 2. 왜 Density-based clustering이 적절한가?

Density based clustering의 대표적인 알고리즘은 DBSCAN. 

DBSCAN의 기본 아이디어는
- 같은 군집 내에서는 점들이 서로 가깝고 군집 사이에는 점 간 거리가 멀다.
- 또한 일정 밀도 이하의 점은 군집이 아닌 노이즈로 간주한다.

이는 다음과 같이 효과를 발휘한다.

(전체 데이터로부터 군집을 형성할 때)
1. 군집의 분포에 대한 사전 가정 없음
   - 군집의 분포가 비정규적, 일정 위치에 집중되는 경향이 있을 때 효과적
2. 군집 수 지정 불필요
   - 몇 개의 변이 집단(hotspot)이 존재하는지 사전 지식이 없을 때 효과적
3. 불규칙한 모양의 클러스터 탐지 가능
   - 군집의 길이, 모양이 다양할 때 효과적
4. 밀도 차를 이용해 클러스터 경계 형성(노이즈 제거)
   - 무작위적 돌연변이, 측정 오류 등으로 인해 의미 없는 변이들(outlier)을 제거할 때에 밀도를 반영하고 싶을 때 효과적

### 3. DBSCAN의 커스텀

다음 특성을 가진 데이터에서는 DBSCAN의 특성에 따라 그대로 적용하기에 부적합.

<mark>데이터의 특성</mark>

- 군집 내 지역 밀도 차이 (일부 클러스터는 매우 조밀하고 다른 클러스터는 상대적으로 희박)가 있는 데이터에서?


<mark>DBSCAN의 특성</mark>

- Global ε, MinPts 고정이어서
    - 세포 배양 밀도(density)가 이미지 내 위치마다 다른데 동일한 파라미터를 적용하면 저밀도 영역은 클러스터 누락 고밀도 영역은 클러스터 과도 확장 발생함 [3]
- Edge 구분 불가능이어서
    - 세포를 core vs. noise로만 나누며 경계(edge)에 있는 세포를 명확히 분류하지 못함 [3]
    - 중요도를 반영하여 edge를 선택적으로 통합하는 등,
      - 경계 즉 클러스터 size를 robust하게 커스텀하지 못함 [4]
- 클러스터 수 불확정성 때문에
    - 실험 조건, 이미지 품질, 배양 상태에 따라 군집 수가 크게 달라져 비교·해석이 어려움 [3] 
- 데이터 별 중요도 가중치 반영 불가 여서
    - 클러스터 형성에 돌연변이의 density만 반영되어 중요도를 반영하지 못함 [4]


<mark>DBSCAN‑CellX의 커스터마이징</mark>
  - Local adaptive ε & minPts 설정으로
    - 세포 밀도를 기반으로 위치별 ε 조정해서 세포가 희박한 위치는 더 넓게, 밀집된 위치는 좁게 탐색함
  - Core / Edge / Noise 3분류를 수행해서
    - 기존의 이분법(core/noise)에서 벗어나 edge 세포를 따로 구분하여 생물학적으로 중요한 경계 특성 반영
  - 자동 파라미터 튜닝 알고리즘 탑재해서
    - 사용자가 설정할 필요 없이, 이미지에서 local density를 추정하여 적절한 파라미터 추론함 [3]

<mark>Mutclust의 커스터마이징</mark>
  - 중요도 기반 Local adaptive ε & minPts 설정으로
    - 클러스터 형성에 돌연변이의 density와 중요도를 모두 반영
  - 기존의 edge 처리 방식(core에 edge를 포함시킴)에서 벗어나,
    - density와 중요도를 반영하여 cluster에 edge 포함 유무를 판단(하여 경계를 보정)하는 알고리즘을 도입하여
      - potential edge의 중요도와 데이터의 density를 모두 반영하여 클러스터 크기 즉 경계 설정을 커스텀 가능하게함 (diminishing factor) [4]

> cf) DBSCAN의 작동 방식
> - DBSCAN은 세 데이터를 다음 세 부류로 분류:
>   - Core point: 반경 ε 내에 minPts 이상 이웃이 있는 점 (밀집 지역의 중심)
>   - Border point: Core의 이웃이지만 minPts 조건은 안 됨 (경계에 있는 점)
>   - Noise point: 어떤 클러스터에도 속하지 않음 (외부 이상치)
>     - 이때 Border point를 따로 취급하지 않고, 그냥 Core와 같은 클러스터에 소속시킴

###

### 4. 성능 평가 및 후속 검증

<mark>DBSCAN‑CellX의 클러스터 성능 검증</mark>

1. 시각적 유효성 평가:
   - 여러 ε, minPts 설정에 따라 클러스터링 결과를 실제 세포 이미지에 중첩하여 시각적으로 비교

2. Core / Edge / Noise 비율 분석
   - edge cell 비율이 높은지 낮은지를 확인, 높은 경우는 core 세포가 중심에 몰리는 구조로 해석되고 고밀도 배양 세포에서 이와 같이 나오는것을 확인. ex) 고밀도 배양: edge 20%, core 70%, noise 10% / 저밀도 배양: edge 40%, core 30%, noise 30%
   -  이를 통해 DBSCAN‑CellX가 구조를 잘 반영하고 있음을 확인

3. 다양한 세포주(cell lines)에 적용
   - 여러 세포주 (e.g., T84, HeLa, H2B-turquoise 등)에 적용하여 범용성 평가.
   - 각 세포주마다 밀도, 분포 양상이 다름에도 불구하고 자동 파라미터 탐색, 경계 세포 구분, 클러스터 형태 재현이 잘 작동함 [3]

<mark>Mutclust의 클러스터 성능 검증</mark>

1. 기능이 알려진 중요한 돌연변이와 중복률 분석
   - spike 단백질의 기능이 알려진 중요한 돌연변이 10개 중 9개를 잘 포함하고 있음.

2. 타 feature와의 연관성 분석 (feature: covid-19 예후)
   - covid-19에서 hotspot에 포함된 Mutation 개수에 따라 환자를 구분하여 분석. 
   - 특정 hotspot의 mutation이 높은 환자군이 유의미하게 나쁜 예후를 보이는 것을 확인, 해당 환자군과 환자군 유래 바이러스 분석 결과 NK 수용체의 교란이 확인.
   - 이는 탐지된 hotspot이 임상적 예후, 선천 면역 반응 등과도 연계됨을 보여줌

3. 타 feature와의 연관성 분석 (feature: 계통수)
   - 각 hotspot이 계통수 결정 돌연변이를 포함하고 있는지를 분석. 
   - 어떤 hotspot은 계통수 결정 돌연변이를 포함, 어떤 핫스팟은 미포함 / 어떤 hotspot은 적은 계통수와 연관, 어떤 hotspot은 다양한 계통수와 연관
   - 즉 식별한 클러스터가 단순 유전적 계통 차이를 기반으로 하는 군집을 포함함과 동시에 유전적 계통 차이 외에도 밝혀지지 않은 중요한 feature를 반영한 군집임을 보여줌. 
   - 이와 같은 군집들은 단순 유전적 계통 차이를 기반으로 한 연구에서는 밝히기 어려움

4. 통계적 유의성 분석
   - 각 핫스팟이 우연히 형성된 것이 아니라는 것을 입증하기 위해 bootstrap으로 통계 분석 수행

5. naive method와 정량적 지표 기반 비교
   - 중요도 지표 계산 시 mutation entropy를 도입한 경우와 shannon entropy를 도입한 경우를 계산하고 계산된 중요도를 확인
   - 나쁜 예후를 보인 환자에서 mutation이 높았던 중요 핫스팟 2개의 중요도가 mutation entropy를 도입한 경우에서 훨씬 상위권에 위치했으며 해당 핫스팟 식별에 해당 방법론이 유용하게 작용함을 확인

6. 시각적 평가: elbow plot
   - dbscan의 파라미터 결정 방식에서 사용되는 elbow plot을 해당 방법론에 적절하게 수정(기존: k번째로 가까운 데이터포인트까지의 거리로 plotting/수정: k번째로 가까우면서 클러스터 형성 조건인 h-score 평균, Minpts, h-score 합 등을 만족하는 클러스터가 형성될때까지의 거리로 plotting)
   - '중요한' 477개 핫스팟이 elbow 아래에 위치함을 확인
   - '중증도와 연관된' 28개 핫스팟이 elbow 아래에 위치하면서도 그중 낮은 위치(좋음)에 위치함을 확인
   - 파라미터 설정이 '중요한' 핫스팟을 잘 식별하도록 설정됨을 확인

7. 범용성 평가: 다른 virus에 적용
   - Influenza genome에 적용하여 범용성 평가
   - 데이터 수, 돌연변이의 밀도, 분포 양상이 다름에도 불구하고 클러스터가 잘 포착되었으며 기능이 알려진 중요한 돌연변이와 중복 또한 일부 확인됨.


###

<mark>#Reference</mark>

[1] Identifying recurrent mutations in cancer reveals widespread lineage diversity and mutational specificity
[2] [Unsupervised clustering analysis of SARS-Cov-2 population structure reveals six major subtypes at early stage across the world](https://pubmed.ncbi.nlm.nih.gov/34845455/)
[3] Extended methods for spatial cell classification with DBSCAN-CellX
[4] Our research
