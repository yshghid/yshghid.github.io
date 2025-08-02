---
date : 2025-07-31
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "MutClust 코드 리펙토링 #1 lib.py"
---

# MutClust 코드 리펙토링 #1 lib.py

#2025-07-31

---

MutClust 알고리즘의 코드 구성은 아래와 같은데

```plain text
MutClust
├── sc/
│    └── lib.py // 핵심 알고리즘 로직
│    └── arg_parser.py
│    └── utils.py
└── Test
```

lib.py는 후보 Core 선택 로직과 클러스터 탐지 알고리즘을 포함한다.

#

### 1. Config & Constant 선언

```python
# === mlib.py ===
from math import ceil
import numpy as np
from src.utils import mutation_filtering

# --- Constants ---
POS = 'Position'
FREQ = 'Frequency'
PER = 'Percentage'
ENT = 'Entropy'
HSCORE = 'H-score'
HSCORE_SUM = 'H-score_sum'
HSCORE_AVR = 'H-score_avr'
PER_SUM = 'per_sum'
ENT_SUM = 'ent_sum'
PER_AVR = 'per_avr'
ENT_AVR = 'ent_avr'
EPSILON = 5
EPSILON_SCALING_FACTOR = 10
DIMINISHING_FACTOR = 3
MIN_CLUSTER_LENGTH = 10
CCM_MIN_HSCORE_SUM = 0.05
CCM_MIN_HSCORE_AVR = 0.01
CCM_MIN_HSCORE = 0.03
MIN_MUTATIONS = 5

# --- Config Init ---
def init(d, info):
    print('\n--- Configurations ---')
    print(f"Input data: '{info.fin}' {d.shape}")
    print(f"Output dir: '{info.outdir}'")
    print('Parameters:')
    print(f"  Min Eps={info.eps}")
    print(f"  Max Eps={info.maxeps}")
    print(f"  Min per_sum={info.min_persum:.1f}")
    print(f"  Eps scaling factor={info.eps_scaler_const:.1f}")
    print(f"  Expansion diminishing factor={info.es_control_const}")
    print(f"  Min cluster length={info.min_cluster_length}")
    print('----------------------\n')
```

### 2. Eps 내 중요도 계산

```python
# --- EPS Stats ---
class get_eps_stats:
    def __init__(self, idx, pos, df, lr_index, lr_distance, es):
        self.idx = idx
        self.i = pos
        self.i_per = df.loc[idx, PER]
        self.i_freq = df.loc[idx, FREQ]
        self.i_ent = df.loc[idx, ENT]
        self.i_hscore = df.loc[idx, HSCORE]

        self.l_dist, self.r_dist = lr_distance
        ccm_df = df.loc[lr_index[0]:lr_index[1] + 1]

        self.length = len(ccm_df)
        self.l_pos = df.loc[lr_index[0], POS]
        self.r_pos = df.loc[lr_index[1], POS]
        self.mut_n = len(ccm_df[ccm_df[HSCORE] > 0])
        self.eps_scaler = es

        self.freq_sum = ccm_df[FREQ].sum()
        self.freq_avr = self.freq_sum / self.length

        self.per_sum = ccm_df[PER].sum()
        self.per_avr = self.per_sum / self.length

        self.ent_sum = ccm_df[ENT].sum()
        self.ent_avr = self.ent_sum / self.length

        self.hscore_sum = ccm_df[HSCORE].sum()
        self.hscore_avr = self.hscore_sum / self.length

    def to_dict(self):
        return {
            'index': self.idx, POS: self.i, FREQ: self.i_freq, PER: self.i_per,
            ENT: self.i_ent, HSCORE: self.i_hscore, 'length': self.length,
            'freq_sum': self.freq_sum, 'freq_avr': self.freq_avr,
            PER_SUM: self.per_sum, PER_AVR: self.per_avr,
            ENT_SUM: self.ent_sum, ENT_AVR: self.ent_avr,
            HSCORE_SUM: self.hscore_sum, HSCORE_AVR: self.hscore_avr,
            'eps_scaler': self.eps_scaler, 'left_distance': self.l_dist,
            'right_distance': self.r_dist, 'l_pos': self.l_pos,
            'r_pos': self.r_pos, 'mut_n': self.mut_n
        }
```

### 3. Local Eps 계산

```python
# --- EPS Region ---
def get_eps_region(df, idx, info):
    pos = df.loc[idx, POS]
    cur_hscore = df.loc[idx, HSCORE]
    eps_scaler = ceil(EPSILON_SCALING_FACTOR * cur_hscore)
    ldeps = rdeps = eps_scaler * EPSILON

    ldeps = min(ldeps, info.maxeps)
    rdeps = min(rdeps, info.maxeps)

    l_idx, r_idx = idx - 1, idx + 1
    while l_idx >= 0 and (pos - df.loc[l_idx, POS]) <= ldeps:
        l_idx -= 1
    while r_idx < len(df) and (df.loc[r_idx, POS] - pos) <= rdeps:
        r_idx += 1

    return [l_idx + 1, r_idx - 1], [idx - (l_idx + 1), (r_idx - 1) - idx], eps_scaler
```

### 4. 후보 Core 돌연변이 선택

```python
# --- Core Mutation Detection ---
def get_candidate_core_mutations(df, info, tag):
    mut_list = []
    ccm_list = []
    df = mutation_filtering(df)

    for idx, pos in enumerate(df[POS]):
        lr_idx, lr_dist, es = get_eps_region(df, idx, info)
        stat = get_eps_stats(idx, pos, df, lr_idx, lr_dist, es)
        d = stat.to_dict()
        mut_list.append(d)

        if d['mut_n'] >= MIN_MUTATIONS and d[HSCORE_SUM] >= CCM_MIN_HSCORE_SUM and d[HSCORE_AVR] >= CCM_MIN_HSCORE_AVR and d[HSCORE] >= CCM_MIN_HSCORE:
            ccm_list.append(idx)

    with open(f"{info.outdir}/total_results_{tag}.tsv", 'w') as f:
        f.write('\t'.join(mut_list[0].keys()) + '\n')
        for m in mut_list:
            f.write('\t'.join(map(str, m.values())) + '\n')

    with open(f"{info.outdir}/ccm_results_{tag}.tsv", 'w') as f:
        f.write('\t'.join(mut_list[0].keys()) + '\n')
        for i in ccm_list:
            f.write('\t'.join(map(str, mut_list[i].values())) + '\n')

    return np.array(mut_list), ccm_list

```

### 5. Cluster Expansion 로직

```python
# --- Cluster Expansion ---
def expand_cluster(ccm_idx, mut_list, info):
    es_l = es_r = mut_list[ccm_idx]['eps_scaler']
    l_idx, r_idx = ccm_idx - 1, ccm_idx + 1
    mut_n = len(mut_list)

    l_max, r_max = mut_list[ccm_idx]['left_distance'], mut_list[ccm_idx]['right_distance']
    l_pos = mut_list[ccm_idx][POS]

    while l_idx >= 0 and (l_pos - mut_list[l_idx][POS]) <= l_max:
        delta = es_l - mut_list[l_idx]['eps_scaler']
        es_l -= delta / info.es_control_const
        l_max = max(info.eps * es_l, 0)
        l_idx -= 1

    while r_idx < mut_n and (mut_list[r_idx][POS] - l_pos) <= r_max:
        delta = es_r - mut_list[r_idx]['eps_scaler']
        es_r -= delta / info.es_control_const
        r_max = max(info.eps * es_r, 0)
        r_idx += 1

    l_idx = max(l_idx + 1, 0)
    r_idx = min(r_idx - 1, mut_n - 1)
    clust = [a[POS] for a in mut_list[l_idx:r_idx + 1] if a[HSCORE] > 0]

    return {
        'left_position': min(clust),
        'right_position': max(clust),
        'length': max(clust) - min(clust) + 1,
        'mut_positions': ','.join(map(str, sorted(clust)))
    }
```

### 6. Dynamic Clustering 로직

```python
# --- Dynamic Clustering ---
def dynaclust(mut_list, ccm_list, info, tag):
    clusters = [expand_cluster(i, mut_list, info) for i in ccm_list]
    clusters.sort(key=lambda x: x['left_position'])

    merged = []
    i = 0
    while i < len(clusters):
        l, r = clusters[i]['left_position'], clusters[i]['right_position']
        muts = set(map(int, clusters[i]['mut_positions'].split(',')))
        j = i + 1
        while j < len(clusters) and clusters[j]['left_position'] <= r:
            r = max(r, clusters[j]['right_position'])
            muts.update(map(int, clusters[j]['mut_positions'].split(',')))
            j += 1
        if len(muts) >= MIN_MUTATIONS:
            merged.append({
                'left_position': min(muts),
                'right_position': max(muts),
                'length': max(muts) - min(muts) + 1,
                'mut_positions': ','.join(map(str, sorted(muts)))
            })
        i = j

    with open(f"{info.outdir}/clusters_{tag}.txt", 'w') as f:
        f.write('\t'.join(merged[0].keys()) + '\n')
        for m in merged:
            f.write('\t'.join(map(str, m.values())) + '\n')

    return merged
```





