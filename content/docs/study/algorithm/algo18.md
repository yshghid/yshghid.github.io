---
date : 2025-08-12
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #6 Intro 구성"
---

# 학위논문작업 #6 Intro 구성

#2025-08-16

---

### 1. 고민인점

저널 제출용은 Background로 다음 내용을 사용함.

- 바이러스의 전파력, 중증 질환 유발 능력, 항체 회피 능력과 같은 특성을 변화시키는 많은 돌연변이가 발생
  - 돌연변이와 바이러스 특성, 특히 환자의 질병 중증도 간의 연관성을 설명하는 연구들이 있고 주로 아미노산 또는 뉴클레오타이드 수준에서의 돌연변이 빈도를 활용하며 빈도 높은 돌연변이가 기능적으로 더 중요할 가능성이 높다는 가정에 기반하는데
    - 이 접근법은 계통에 따른 편향에 취약하고
    - 빈도가 다소 낮지만 다양한 돌연변이들이 신호하는 바이러스 적응이나 면역 회피를 간과하는 경우가 많다.  
  - 샤넌 엔트로피와 같은 엔트로피 기반 접근법이 돌연변이 다양성을 측정하기 위해 도입되었지만
    - 전체적인 돌연변이 발생 빈도를 고려하지 못하기 때문에 단독으로는 충분하지 않다. 

- 실제로 돌연변이는 랜덤하게 분포하기보다는 클러스터를 이룬다. 
  - 구조적/면역학적 선택 압력 하에서 기능적으로 중요한 돌연변이 핫스팟이 발생하고
  - 불균일하고 불규칙한 패턴으로 클러스터링되는 경향이 있다.
    - 고정 길이 슬라이딩 윈도우나 기존 클러스터링 알고리즘과 같은 전통적인 탐지 방법은 바이러스 유전체의 생물학적 복잡성을 포착하기에 부적절하다.

- 새로운 접근법
  - MutClust: H-score라는 돌연변이 중요도 지표를 활용하는 새로운 밀도 인식(density-aware) 클러스터링 알고리즘 
  - MutClust는 DBSCAN 프레임워크를 기반으로 하지만 몇 가지 핵심적인 개선점을 도입했다. H-score를 기반으로 한 지역적 ε 조정(돌연변이 밀도와 생물학적 중요도를 모두 반영), 감쇠 계수를 사용하여 클러스터 경계를 동적으로 조정할 수 있도록 하는 가중치 처리, 다양하지만 중간 수준 빈도의 돌연변이가 존재하는 영역에서 생물학적으로 의미 있는 클러스터를 더 잘 탐지할 수 있는 개선된 탐지 능력이 그것이다. 이러한 향상된 기능을 통해 MutClust는 밀도 기반 클러스터링에 면역학적 중요도을 통합하여 돌연변이 hotspot을 보다 정확하게 탐지할 수 있다.



###

학위논문은 이렇게가면 머리(?) 서론이 너무 커질거같아서 related study보다는 사용된 도구 위주로 가는게 좋을것같은데..

###

<mark>Background</mark>

Clustering 분석
- 빈도 높은 돌연변이에 대한 기능적 해석
- ~Lineage defining 돌연변이와 기능적 annotation~
- 돌연변이와 샤넌 엔트로피
- 기능적으로 중요한 돌연변이는 클러스터를 이룬다: window based 핫스팟 식별
- ~DBSCAN 클러스터링~

Multi omics 분석
- Network Propagation 분석
- Differentially expressed gene 분석
- HLA affinity 예측 도구

고민포인트
- DBSCAN을 언급할것인가? & Lineage를 언급할것인가?
  - 저널제출용은 했지만 학위논문용은 둘다안하는게 좋을거같음. (설명해야할게 너무많아짐)
- HLA affinity 내용을 넣을것인가?
  - 위내용을 다뺄거면 넣는게 이쁠것같음. 

###

<mark>Related Study</mark>

후보들

- SARS-CoV-2 hot-spot mutations are significantly enriched within inverted repeats and CpG island loci
- Understanding mutation hotspots for the SARS-CoV-2 spike protein using Shannon Entropy and K-means clustering
- ~T-CoV: a comprehensive portal of HLA-peptide interactions affected by SARS-CoV-2 mutations~ *얘를포기하자*




###

<mark>Objective</mark>

기존 분석
- Frequency 또는 Entropy 단일 접근
- window based 핫스팟 식별

새로운 접근법
- Frequency, Entropy를 통합한 돌연변이 중요도 지표 H-score 사용
- 중요도를 반영한 local Eps 선택
- ~감쇠 계수를 사용하여 클러스터 경계를 동적으로 조정~ *이얘기도 뒤에서만하자*

#
