---
weight: 17
title: "알고리즘"
bookComments: false
type: docs
bookHidden: false
---

# 알고리즘

*2025-07-31* ⋯ MutClust 코드 리펙토링 #2 arg_parser

[MutClust 알고리즘의 코드 구성은 아래와 같은데 MutClust ├── sc/ │    └── lib.py  │    └── arg_parser.py // 실행 설정 │    └── utils.py └── Test arg_parser.py는 실험 환경 파라미터 세팅 및 CLI 인자 파싱을 포함한다. # === arg_parser.py ===
import argparse from os.path import ⋯](https://yshghid.github.io/docs/study/algorithm/algo2/)


---

*2025-07-31* ⋯ MutClust 코드 리펙토링 #1 lib

[MutClust 알고리즘의 코드 구성은 아래와 같은데 MutClust ├── sc/ │    └── lib.py // 핵심 알고리즘 로직 │    └── arg_parser.py │    └── utils.py └── Test lib.py는 후보 Core 선택 로직과 클러스터 탐지 알고리즘을 포함한다. 1. Config & Constant 선언 # === mlib.py === from math import ceil ⋯](https://yshghid.github.io/docs/study/algorithm/algo1/)

#
