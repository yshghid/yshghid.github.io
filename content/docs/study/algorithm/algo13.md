---
date : 2025-07-31
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "MutClust 함수 #1 expand_cluster"
---

# MutClust 함수 #1 expand_cluster

#2025-08-05

---


#1 

input 변수
- ccm의 인덱스 ccm_idx
- 



```python
# --- Cluster Expansion ---
def expand_cluster(ccm_idx, mut_list, info):
    es_l = mut_list[ccm_idx]['eps_scaler']
    l_idx, r_idx = ccm_idx - 1, ccm_idx + 1
    mut_n = len(mut_list)

    l_max, r_max = mut_list[ccm_idx]['left_distance'], mut_list[ccm_idx]['right_distance']
    l_pos = mut_list[ccm_idx][POS]

    while l_idx >= 0 and (l_pos - mut_list[l_idx][POS]) <= l_max:
        delta = es_l - mut_list[l_idx]['eps_scaler']
        es_l -= delta / info.es_control_const
        l_max = max(info.eps * es_l, 0)
        l_idx -= 1

    l_idx = max(l_idx + 1, 0)
    r_idx = min(r_idx - 1, mut_n - 1)
    clust = [a[POS] for a in mut_list[l_idx:r_idx + 1] if a[HSCORE] > 0]
```


#full code

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
