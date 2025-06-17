---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#1 Entropy 기반 mutation 분석"
bookComments: true
---

# #1 Entropy 기반 mutation 분석

#2025-06-17

---

[1] An entropy-based study on the mutational landscape of SARS‑CoV‑2

### 1. 단백질 서열과 샤넌 엔트로피

샤논 엔트로피는 단백질 서열 상 특정 위치에 다양한 아미노산이 얼마나 골고루 존재하는지를 나타내는 지표이다. 
- 어떤 위치에 여러 아미노산이 비슷한 비율로 존재한다면 엔트로피가 높고, 하나의 아미노산이 압도적으로 우세하다면 엔트로피가 낮다.

전통적인 샤논 엔트로피에 대한 해석은 논코딩 영역의 식별이다.
- 염기의 돌연변이에 따른 아미노산의 결실 및 변동은
  - 개체에 대부분은 부정적인 영향을 줌으로써 돌연변이를 가진 개체가 태어날 수 없게 할 확률이 높기 때문이다.
  - 하지만 일부 돌연변이는 개체의 발생에 영향을 주지 않으며,
    - 극히 일부는 '살아남는 데 그치지 않고' 각기 다양한 방식으로 개체의 생존력을 높임으로써 '진화'의 원인이 되기도 한다.

이에 샤넌 엔트로피가 높은 위치, 
- 즉 '살아남은 염기 다양성'에 대한 분석은 흥미로운 결과를 낼 수 있다.

샤넌 엔트로피가 높다는 것은 
- 바이러스가 해당 아미노산 자리에 대해
  - 여러 가능한 '돌연변이 조합'을 실험하고 있다는 신호로 해석될 수 있다.
  - 돌연변이는
    - 바이러스의 적응력(adaptability), 면역 회피 능력(immune evasion), 치료 저항성(antiviral resistance) 등에 영향을 줄 수 있고
    - 돌연변이가 다양할수록 특정 돌연변이가 선택받아 우세해지는 기반(pool)이 되기 때문에
      - 숙주의 방어(면역, 백신, 치료제)를 회피할 확률이 높아진다.


### 2. 엔트로피 기반의 돌연변이 연구

샤넌 엔트로피를 기반으로 
- 돌연변이의 다양성을 정량화하고
- 해당 돌연변이의 기능적 역할을 설명하는 여러 연구들이 수행되었고
  - 이는 다양한 에피톱에 대응하는 백신 설계, 저항성 돌연변이 출현 시점 예측을 통한 항바이러스제 개발, 전파 가능성 높은 변이 조기 감지를 통한 공중보건 대응 전략 수립 등에 사용할 수 있다.

| 논문 / 연도 | 논문 제목 | 방법론 특징 | High Entropy와 무엇의 연관성을 봤는가? | 결과 | 해석 |
| --- | --- | --- | --- | --- | --- |
| **Rouchka et al. (2024)**
[Entropy] | *Information-Theoretic Approaches for the Analysis of Genetic Diversity in Viral Populations* | HIV, HCV, SARS-CoV-2 등 다양한 바이러스의 변이 hotspot 비교 | 유전자 또는 기능적 annotated 영역 | 면역 회피 영역에서 높은 entropy 관측
필수 보존 유전자에서 낮은 entropy 관측 | 고 entropy 영역은 선택 압력, 면역 회피 및 진화 적응이 일어난 자리로 해석됨 |
| **Zhou et al. (2021)**
[Cell Host Microbe] | *Mutational landscape of SARS-CoV-2 reveals three mutually exclusive clusters of mutations* | SARS-CoV-2 전 유전체에 대해 site-wise Shannon Entropy 계산 | VOCs의 주요 변이 자리 | VOCs의 주요 변이 자리(N501Y, E484K 등)에서 높은 entropy 관측 |  |
| **Singh et al. (2022)**
[PLoS Pathogens] | *Temporal entropy of SARS-CoV-2 genome reflects adaptive evolution during Delta to Omicron transition* | 시계열 entropy 계산을 통해 시간 경과에 따른 다양성 추적 | VOCs의 출현 시기와 Entropy 급등 시기 사이 연관성 | 오미크론 출현기 spike protein 부위 entropy 급등 확인 | 시점별 entropy 변화는 전파력 및 면역 회피 변이의 확산 시점을 포착할 수 있음 |
| **Veeravalli et al. (2023)**
[Virus Evolution] | *Entropy and Hellinger distance highlight mutational shifts in SARS-CoV-2 evolution* | Shannon Entropy + Hellinger Distance 조합으로 구성적 돌연변이 분포 변화 추적 | VOCs의 출현 시기와 ‘구성적 다양성 지표’ 급등 시기 사이 연관성 | VOC 출현 시점에서 구성적 다양성 변화 급증 탐지 | 다양한 돌연변이가 새롭게 출현한 시점을 수치적으로 정량화 가능 |
| **Kim et al. (2025)**
[Nature Communications] | *Impact of vaccination on genetic diversity of SARS-CoV-2 spike protein* | 정규화된 Shannon Entropy를 지역/시기별 비교 | 백신 접종 전후와 entropy 증감 사이 연관성 | 백신 접종 후 spike 영역 다양성 감소 확인, escape 부위는 지속적으로 다양성 유지 | 백신이 일부 epitope의 다양성을 억제하였으나 면역 회피 hotspot은 여전히 활동 중 |
| **Gonzalez et al. (2024)**
[Bioinformatics] | *Codon-level entropy analysis differentiates synonymous and functional mutation hotspots* | synonymous/nonsynonymous 분리 분석 |  |  | Shannon entropy 단독으론 기능적 영향 해석이 어려우며, 구조-기능 정보 결합 필요 |
|  |  | Mutation-focused entropy + frequency 통합 분석 | 기능적 annotated 변이,
Covid-19 outcome | 면역 회피, 중증 연관 돌연변이를 포함한 높은 ‘H-score’ 영역 확인
중요한 돌연변이 영역 2개에서 중증 환자 마커 기능 확인, 면역학적 해석 | Frequency, mutation focused entropy가 높은 H-score 영역이 기능적으로 중요함 |







