---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "Related Study #1 샤넌 엔트로피"
bookComments: true
---

# Related Study #1 샤넌 엔트로피

#2025-06-17

---

### 1

<mark>샤넌 엔트로피?<mark>
- 단백질 서열 상 특정 위치에 다양한 아미노산이 얼마나 골고루 존재하는지를 나타내는 지표
- 어떤 위치에 여러 아미노산이 비슷한 비율로 존재한다면 엔트로피가 높고, 하나의 아미노산이 압도적으로 우세하다면 엔트로피가 낮다.

<mark>돌연변이 데이터에서 샤넌 엔트로피</mark>
- 전통적인 샤논 엔트로피에 대한 해석은 논코딩 영역의 식별.
  - 염기의 돌연변이에 따른 아미노산의 결실 및 변동은 개체에 대부분은 부정적인 영향을 줌으로써 돌연변이를 가진 개체가 태어날 수 없게 할 확률이 높기 때문.
  - 하지만 일부 돌연변이는 개체의 발생에 영향을 주지 않고
  - 극히 일부는 '살아남는 데 그치지 않고' 각기 다양한 방식으로 개체의 생존력을 높임으로써 '진화'의 원인이 되기도 한다. [1]

- 이에 샤넌 엔트로피가 높은 위치 즉 '살아남은 염기 다양성'에 대한 분석은 흥미로운 결과를 낼 수 있다.

<mark>샤넌 엔트로피가 높다는 것은?</mark>
- 바이러스가 해당 아미노산 자리에 대해 여러 가능한 '돌연변이 조합'을 실험하고 있다는 신호로 볼수잇다.

<mark>돌연변이 다양성의 효과?</mark>
- 바이러스의 적응력(adaptability), 면역 회피 능력(immune evasion), 치료 저항성(antiviral resistance) 등에 영향을 줄 수 있고
- 돌연변이가 다양할수록 특정 돌연변이가 선택받아 우세해지는 기반(pool)이 되기 때문에 숙주의 방어(면역, 백신, 치료제)를 회피할 확률이 높아진다.

###

### 2

<mark>엔트로피 based 돌연변이 연구</mark>
- 샤넌 엔트로피를 기반으로 돌연변이의 다양성을 정량화하고 높은 엔트로피를 보인 위치를 식별 및 분석하는 연구.
- 다양한 에피톱에 대응하는 백신 설계, 저항성 돌연변이 출현 시점 예측을 통한 항바이러스제 개발, 전파 가능성 높은 변이 조기 감지를 통한 공중보건 대응 전략 수립 등에 사용할 수 있다.

![image](https://github.com/user-attachments/assets/cb4fd6ee-88ed-4a8d-ac61-cb7e072fe6f4)

<mark>(높은 엔트로피 영역에 대한) 분석 결과들</mark>

1. 면역 회피 영역에서 높은 entropy, 필수 보존 유전자에서 낮은 entropy가 관측됨에 따라 고 entropy 영역은 선택 압력, 면역 회피 및 진화 적응이 일어난 자리로 해석되었다. [2]

2. VOCs의 VOCs의 주요 변이 자리(N501Y, E484K 등)에서 높은 entropy가 관측되었다. [3]

3. 오미크론 출현기에 spike protein 부위 entropy가 급등함이 확인됨에 따라,
- VOCs의 출현 시기와 Entropy 급등 시기 사이 연관성이 있으며
- 시점별 entropy 변화는 전파력 및 면역 회피 변이의 확산 시점을 포착할 수 있다고 해석되었다. [4]

4. 백신 접종 후 spike 영역 다양성 감소가 확인되었고 escape 부위는 지속적으로 다양성을 유지함이 확인됨에 따라
- 백신 접종 전후와 entropy 증감 사이 연관성이 있으며 백신이 일부 epitope의 다양성을 억제하였다고 해석되었다.
- 면역 회피 부위와 다양성 유지 사이 연관성이 있음도 확인되었다. [5]

5. Shannon Entropy + Hellinger Distance 조합으로 구성적 돌연변이 분포 변화를 분석했을때
- VOC 출현 시점에서 구성적 다양성 변화가 급증함을 확인함으로써 VOCs의 출현 시기와 ‘구성적 다양성 지표’ 급등 시기 사이 연관성이 있으며 엔트로피 기반으로 한 해당 지표로 다양한 돌연변이가 새롭게 출현한 시점을 수치적으로 정량화 가능하다고 해석하였다. [6]

6. 하지만 synonymous/nonsynonymous 돌연변이를 분리해서 분석한 연구에서는 Shannon entropy 단독으론 기능적 영향 해석이 어렵고 분석을 위해서는 구조-기능 정보가 필요함을 확인하였다. [7]

###

<mark>우리 연구는?</mark>
- Mutation diversity focused entropy + Frequency 조합으로 다양하게 발생한 돌연변이 영역을 식별하였고
  - 면역 회피 능력, 강화된 전파능, 중화 항체 기능 감소, 인간 세포 침입능 강화 등의 기능을 가진 여러 돌연변이를 포착했으며
  - 중증 outcome을 보인 환자 그룹에서 돌연변이 빈도가 높음이 확인됨에 따라 해당 영역과 중증 outcome 사이 연관성이 있다고 해석 후 여러 임상 데이터로 이를 검증하였다. 

###

<mark>#Reference</mark>

[1] Information theory in molecular biology
[2] Information-Theoretic Approaches for the Analysis of Genetic Diversity in Viral Populations
[3] Mutational landscape of SARS-CoV-2 reveals three mutually exclusive clusters of mutations
[4] Temporal entropy of SARS-CoV-2 genome reflects adaptive evolution during Delta to Omicron transition
[5] Impact of vaccination on genetic diversity of SARS-CoV-2 spike protein
[6] Entropy and Hellinger distance highlight mutational shifts in SARS-CoV-2 evolution
[7] Codon-level entropy analysis differentiates synonymous and functional mutation hotspots

#