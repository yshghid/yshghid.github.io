---
date : 2025-08-05
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "MutClust 함수 #1 expand_cluster"
---

# MutClust 함수 #1 expand_cluster

#2025-08-05

---


#1 input
- ccm의 인덱스 ccm_idx
- mutation 중요도 정보 mut_list

#2

```python
# --- Cluster Expansion ---
def expand_cluster(ccm_idx, mut_list, info):
    es_l = mut_list[ccm_idx]['eps_scaler']
    l_idx = ccm_idx - 1
    l_max = mut_list[ccm_idx]['left_distance']
    l_pos = mut_list[ccm_idx][POS]
```

- es_l: ccm의 eps scaler
- l_idx: 최초 이웃의 인덱스
- l_max: ccm의 최초 eps
- l_pos: ccm의 postion

#3

```python
# --- Cluster Expansion ---
def expand_cluster(ccm_idx, mut_list, info):
    #es_l = mut_list[ccm_idx]['eps_scaler']
    #l_idx = ccm_idx - 1
    #l_max = mut_list[ccm_idx]['left_distance']
    #l_pos = mut_list[ccm_idx][POS]

    while l_idx >= 0 and (l_pos - mut_list[l_idx][POS]) <= l_max:
        delta = es_l - mut_list[l_idx]['eps_scaler']
        es_l -= delta / info.es_control_const
        l_max = max(info.eps * es_l, 0)
        l_idx -= 1
```

<Logic>
1. ccm의 eps scaler와 최초 이웃의 eps scaler의 차를 delta로 받는다.
2. ccm의 eps scaler를 delta/3을 뺀 값으로 업데이트한다 (DIMIN_FACTOR=3)
3. eps scaler * EPSILON으로 ccm의 eps(l_max)를 업데이트한다.
4. 다음 이웃에 대해 반복한다.
   - 그만둬야하는 경우: 현재 이웃 인덱스가 0미만이 될때
   - 언제까지 반복하는가: ccm(l_pos)에서 현재 이웃(mut_list[l_idx])까지의 거리가 업데이트 된 eps(l_max)이하로 떨어지면 종료.

<Key point>

```python
delta = es_l - mut_list[l_idx]['eps_scaler']
es_l -= delta / info.es_control_const
l_max = max(info.eps * es_l, 0)
```

1. 확장을 반복할수록 es_l과 es_r이 줄어든다.
2. 이웃의 eps scaler가 작으면 (즉 중요도가 낮으면) 

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
