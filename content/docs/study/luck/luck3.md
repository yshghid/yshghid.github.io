---
date : 2025-06-07
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 7일"
---

# 6월 7일

#2025-06-07

---

#백신연구 진행상황

##1 

목적이 개인의 면역 환경을 대표하고 면역 반응을 예측하는 feature를 찾는 것이라고 할때.
- 개인의 면역 환경을 대표하는 feature를 찾는 것은 쉽다. 단일 시점 clustering을 하고 군집의 feature를 찾으면 됨.
- 다만 이중에 '면역 반응'을 예측하는데 유용한 feature를 골라내는게 어렵다.
  - '면역 반응'을 type1 2 3등으로 정의하는게 필요하고
  - 그 반응을 대표하는 feature를 찾는게 필요하다.

목적이 '면역 반응'이 비슷한 환자를 군집화하는것일때.
- '면역 반응'을 유전자 발현량 패턴이라고 정의하면 
- (단일 시점 clustering과) spherical kmeans는 편향이 최소화된 비지도학습 방법이지만 feature 선택이 어렵다.

어떤 feature가 비슷해야 면역 반응이 비슷한것인가?
- 정답 feature(gene set) X가 있다고 가정했을때 이 feature를 맞히기는 어렵다.
- 근데 공동 발현 유전자가 많으면 면역 반응이 비슷할것이다.
  - 공동 발현 유전자가 많은 애들의 특징이, 비슷한 면역 반응을 보이는 애들의 특징이 되지 않을까?

목적
- 면역 반응이 비슷한 환자를 분류해내는 feature를 찾기.

가정
- 공동 발현 유전자가 많으면 면역 반응이 비슷할것이다.

방향
- 면역 반응이 비슷한 환자를 분류해내는 feature를 한번에 찾기 어려우니까, 면역 반응이 비슷한 3 type을 찾아서 걔네의 특징을 feature로 쓰자.
- 3type의 특징을 잘 나타내면서
  - 그중 개인의 면역 환경을 잘 대표하는 gene set을 골라내기. 즉 3 type의 특성 중 t0으로 각 그룹을 분류 가능한 애들을 찾기.
  - 또는 1차의 효과로(t2-t0) 분류 가능한 애들을 찾기.

결과는?
- 개인의 면역 반응을 정의
- 개인의 면역 반응을 대표하는 feature를 찾음
- 그중 개인의 면역 환경 정의도 할수있는 feature를 고름
- 최종 feature인 '개인의 면역 환경'을 상요해서 면역 반응 유형을 예측 가능.

참고하면 좋을 논문 추가
1. Dictionary of immune responses to cytokines at single-cell resolution
2. Single-cell immune aging clocks reveal inter-individual heterogeneity during infection and vaccination
