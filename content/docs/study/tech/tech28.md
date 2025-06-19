---
date : 2025-06-19
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#5 Revision"
bookComments: true
---

# #5 Revision

#2025-06-19

---

### Reviewer 1 - Comment 1

> "In the introduction section, the authors note that most computational methods focus on the frequency of mutation occurrences rather than mutation diversity. This point should be more thoroughly discussed, with a clear explanation of the advantages and potential insights offered by analyzing mutation diversity."
>
> “서론에서 저자들은 대부분의 계산 방법들이 돌연변이 발생 빈도에 집중하고 있으며, 돌연변이 다양성(mutation diversity)을 간과한다고 언급하였습니다. 돌연변이 다양성을 분석하는 것의 장점과 잠재적인 통찰에 대해 보다 명확하게 논의해 주시기 바랍니다.”

### Reviewer 2 - comment 1

> “The results of this study hold significant value but are buried under technical redundancy. Condensing the manuscript and focusing only on the key contributions will enhance clarity and appeal to a broader audience.”
>
> “이 연구의 결과는 상당한 가치를 지니고 있으나, 과도한 기술적 설명으로 인해 그 가치가 묻혀 있습니다. 원고를 간결하게 다듬고 핵심 기여에 집중한다면 명확성이 향상되어 더 폭넓은 독자층에 어필할 수 있을 것입니다.”

#Response

리뷰어님의 통찰력 있는 의견에 감사드립니다. 저희는 본 연구의 가치가 방법론적 참신성과 그 함의에 있으며, 과도한 기술적 세부 사항이 이러한 기여를 흐릴 수 있다는 우려에 동의합니다. 이에 따라, 알고리즘 MutClust의 핵심 방법론적 혁신을 부각시키는 방향으로 원고를 수정하였으며, 생물학적 검증 결과는 명확하고 해석 가능한 여섯 개의 평가 항목으로 구조화하였습니다.

[방법론적 기여 강조]

본 연구의 핵심 기여는 MutClust의 개발입니다. 이는 DBSCAN 기반 밀도 클러스터링 알고리즘을 유전체 돌연변이의 생물학적 특성에 맞게 적응시킨 새로운 알고리즘입니다. 이 커스터마이징은 기존 DBSCAN의 다음과 같은 한계를 극복하기 위한 것입니다:
1) 고정된 밀도 파라미터 사용으로 지역별 돌연변이 중요도 변화에 민감하지 않음 2) 단순 빈도 기준 필터링으로 낮은 빈도의 기능적으로 중요한 돌연변이 탐지가 어려움 3) 클러스터 경계가 자동으로 결정되어 생물학적 신호를 반영한 커스텀 불가

MutClust의 주요 특징은 다음과 같습니다: 
1) 중요도 가중 클러스터링: MutClust는 각 유전체 위치에 대해 엔트로피와 돌연변이 빈도를 통합한 H-score를 도입합니다. 이는 단순한 빈도뿐만 아니라 돌연변이 다양성도 반영하여, 생물학적 적응성과 면역 회피 가능성을 함께 고려할 수 있습니다.

2) 지역 적응형 파라미터: ε(반경) 및 minPts(최소 점 개수)를 지역별 밀도와 H-중요도에 따라 동적으로 설정하여, 돌연변이 분포에 따라 민감도를 조절할 수 있도록 설계하였습니다.

3) 감쇠 계수 기반 경계 제어: 기존 DBSCAN은 모든 edge point를 core에 병합하지만, MutClust는 경계점(edge)의 포함 여부를 거리 및 중요도 기반 감쇠 함수를 통해 제어함으로써 생물학적으로 의미 있는 클러스터 경계 유지가 가능합니다.

이러한 개선을 통해 MutClust는 단순히 빈도 높은 클러스터뿐 아니라 기능적으로 중요한 돌연변이 군집을 보다 세밀하게 포착할 수 있습니다.

[생물학적 유의성 검증 – 6가지 평가로 구조화]

이전에는 사용된 기술 방법론에 따라 생물학적 해석 및 검증이 흩어져 있었으나 현재는 MutClust의 유효성을 다음 여섯 가지 평가 기준에 따라 명확히 정리하였습니다:

1) 기존 기능적 돌연변이와의 중복: MutClust는 SARS-CoV-2 스파이크 단백질 내의 기능적으로 특성화된 10개 돌연변이 중 9개를 성공적으로 재탐지하였으며, 이는 본 알고리즘이 핵심 기능적 위치를 정확히 포착함을 시사합니다.
2) 계통학적 분석: 일부 핫스팟은 계통 정의 돌연변이와 중첩되었지만, 일부는 기존 계통 기반 분석으로는 포착되지 않았던 새로운 기능 기반 군집으로 확인되어, MutClust가 계통학적 접근의 한계를 보완함을 보여줍니다.
3) 통계적 유의성 (부트스트랩 기반 검증): 무작위 기대 분포에 기반한 부트스트랩 분석을 통해, 탐지된 클러스터는 통계적으로 유의하게 무작위성에서 벗어남이 입증되었습니다.
4) 임상 결과와의 연관성: COVID-19 환자들을 핫스팟 돌연변이 개수 기준으로 계층화한 결과, 특정 핫스팟 돌연변이 수가 많은 환자일수록 COVID-19 중증도가 높았습니다. 이들 바이러스는 NK 세포 기능 변동에 영향을 주었으며, 이는 환자 NK 세포 수용체 교란과 동반되었습니다.
5) 중요도 점수 방법 비교: Shannon 엔트로피와 mutation entropy를 비교하여 핫스팟 우선순위를 평가한 결과, mutation entropy를 포함했을 때 중증 연관 핫스팟이 일관되게 상위에 랭크되어, H-중요도 설계의 타당성을 입증하였습니다.
6) 타 바이러스 적용 가능성: MutClust를 인플루엔자 유전체에 적용한 결과, 다른 돌연변이율과 분포 특성에도 불구하고 의미 있는 핫스팟이 식별되었고, 기능적으로 알려진 돌연변이도 일부 재탐지되어 알고리즘의 범용성이 확인되었습니다.


본 연구의 핵심인 방법론적 기여가 강조되도록 기술적 설명을 수정하고, 결과 섹션은 위의 6가지 생물학적 검증 중심 구조로 재구성하여 각 결과의 의미와 근거가 명확히 드러나도록 하였습니다.

다시 한 번 리뷰어님의 조언에 감사드리며, 본 논문이 왜 이 방법이 필요한지, 기존 방법보다 어떤 점이 개선되었는지, 어떤 증거가 그 유용성을 뒷받침하는지를 독자들이 보다 쉽게 이해할 수 있도록 구조를 명확히 정비하였습니다.




