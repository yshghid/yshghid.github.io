---
weight: 18
title: "알고리즘"
bookComments: false
type: docs
bookHidden: false
---

# 알고리즘

*2025-08-04* ⋯ MutClust 연구: method contribution

[#Paper Identification of Severity Related Mutation Hotspots in SARS-CoV-2 Using a Density-Based Clustering Approach  0. 참여 파트 #Algorithm └── Computing the H-score └── Density-based mutation hotspot clustering #Omics-analysis └── Selection of severity related ⋯](https://yshghid.github.io/docs/study/algorithm/algo10/)

---

*2025-08-01* ⋯ MutClust 코드 리펙토링 #3 utils

[MutClust 알고리즘의 코드 구성은 아래와 같은데 MutClust ├── sc/ │    └── lib.py │    └── arg_parser.py │    └── utils.py // 전처리 및 분석 └── Test lib.py는 데이터 전처리 및 분석 함수를 포함한다. # === Fasta 전처리 === def fasta2csv(home_dir, nation_dir, ⋯](https://yshghid.github.io/docs/study/algorithm/algo9/)

---

*2025-07-31* ⋯ MutClust 코드 리펙토링 #2 arg_parser

[MutClust 알고리즘의 코드 구성은 아래와 같은데 MutClust ├── sc/ │    └── lib.py  │    └── arg_parser.py // 실행 설정 │    └── utils.py └── Test arg_parser.py는 실험 환경 파라미터 세팅 및 CLI 인자 파싱을 포함한다. # === arg_parser.py ===
import argparse from os.path import ⋯](https://yshghid.github.io/docs/study/algorithm/algo2/)


---

*2025-07-31* ⋯ MutClust 코드 리펙토링 #1 lib

[MutClust 알고리즘의 코드 구성은 아래와 같은데 MutClust ├── sc/ │    └── lib.py // 핵심 알고리즘 로직 │    └── arg_parser.py │    └── utils.py └── Test lib.py는 후보 Core 선택 로직과 클러스터 탐지 알고리즘을 포함한다. 1. Config & Constant 선언 # === mlib.py === from math import ceil ⋯](https://yshghid.github.io/docs/study/algorithm/algo1/)

---

*2025-07-28* ⋯ MutClust 슈도코드 작성하기

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D 데이터 집합 D, 파라미터 eps와 minPts가 들어간다. 2 ⋯](https://yshghid.github.io/docs/study/ai/ai10/)

#
