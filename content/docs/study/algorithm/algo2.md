---
date : 2025-08-01
tags: ['2025-08']
categories: ['알고리즘']
bookHidden: true
title: "MutClust 코드 리펙토링 #2 arg_parser"
---

# MutClust 코드 리펙토링 #2 arg_parser

#2025-08-01

---

MutClust 알고리즘의 코드 구성은 아래와 같은데

```plain text
MutClust
├── sc/
│    └── lib.py
│    └── arg_parser.py // 실행 설정
│    └── utils.py
└── Test
```

arg_parser.py는 실험 환경 파라미터 세팅 및 CLI 인자 파싱을 포함한다.

```python
# === arg_parser.py ===
import argparse
from os.path import exists
from src.mlib import (
    DIMINISHING_FACTOR, EPSILON, EPSILON_SCALING_FACTOR,
    MAX_EPS, MIN_CLUSTER_LENGTH, CCM_MIN_PERCENTAGE_SUM
)

class ArgsInfo:
    def __init__(self):
        self.args = {}
        self.fin = ''
        self.ref = ''
        self.outdir = ''
        self.eps = EPSILON
        self.maxeps = MAX_EPS
        self.min_persum = CCM_MIN_PERCENTAGE_SUM
        self.eps_scaler_const = EPSILON_SCALING_FACTOR
        self.es_control_const = DIMINISHING_FACTOR
        self.min_cluster_length = MIN_CLUSTER_LENGTH

def set_env(input_path=None, reference=None, output_path=None):
    info = ArgsInfo()
    parser = argparse.ArgumentParser(prog="cluster.py")

    parser.add_argument('-f', '--input_file', type=str, default='/data3/projects/2020_MUTCLUST/Data/Rawdata/COVID19/nucleotide_data/mutclust_input_data.txt', help='mutation frequency data file')
    parser.add_argument('-r', '--ref', type=str, default='/data3/projects/2020_MUTCLUST/Data/Rawdata/COVID19/nucleotide_data/new_reference.fasta', help='the reference genome')
    parser.add_argument('-e', '--eps', type=int, default=EPSILON, help='width of window (epsilon)')
    parser.add_argument('--maxeps', type=int, default=MAX_EPS, help='maximum eps')
    parser.add_argument('--minps', type=float, default=CCM_MIN_PERCENTAGE_SUM, help='minimum per_sum')
    parser.add_argument('--es', type=float, default=EPSILON_SCALING_FACTOR, help='eps scaling factor')
    parser.add_argument('--exd', type=float, default=DIMINISHING_FACTOR, help='cluster expansion es diminishing factor')
    parser.add_argument('--minl', type=int, default=MIN_CLUSTER_LENGTH, help='minimum cluster length')

    args = parser.parse_args()

    info.fin = input_path if input_path else args.input_file
    info.ref = reference if reference else args.ref
    info.outdir = output_path if output_path else './output'

    if not exists(info.fin):
        print(f"Input file does not exist: {info.fin}")
        exit()

    info.eps = args.eps
    info.maxeps = args.maxeps
    info.min_persum = args.minps
    info.eps_scaler_const = args.es
    info.es_control_const = args.exd
    info.min_cluster_length = args.minl

    return info
```