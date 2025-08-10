---
date : 2025-06-18
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "Related Study #4 Clustering 알고리즘의 parametric test"
bookComments: true
---

# Related Study #4 Clustering 알고리즘의 parametric test

#2025-06-18

---

<mark>Parametric test</mark>

정답 label이 없는 unsupervised learning인 clustering은 supervised learning과 달리 정확도, AUC curve 등으로 성능 평가 불가.

<mark>정량적 평가 지표?</mark>
- Intra-cluster genetic distance (클러스터 내 유전 거리): 작을수록 내부 군집 응집도가 좋음
- Silhouette score, SSE, BIC 등의 지표 사용

<mark>그 외 방법?</mark>
- 방향성이 같은 또는 같지 않아야 하는 비교 feature를 선택하고 비교 ex) 계통학적 구조가 지리적 패턴과 일치함
- t‑SNE 시각화 등 시각적 확인
  - t‑SNE로 축소된 2D scatter plot 위에 DBSCAN으로 얻은 cluster를 색상별로 표시해서
  - 군집 간의 명확한 경계, 군집 내 응집성이 시각적으로 확인

#