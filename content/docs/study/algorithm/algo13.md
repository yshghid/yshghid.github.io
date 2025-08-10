---
date : 2025-08-05
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #1 핵심함수 로직 정리"
---

# 학위논문작업 #1 핵심함수 로직 정리

#2025-08-05

---


### 1. input

```python
def expand_cluster(ccmIdx, mutData, info):
```

- ccm의 인덱스 ccmIdx
- 돌연변이 중요도 정보 mutData
- info: 기본 세팅 파라미터

###

### 2

```python
scaler_l = mutData[ccmIdx]['eps_scaler']
idx_l = ccmIdx - 1
eps_l = mutData[ccmIdx]['left_distance']
pos_l = mutData[ccmIdx][POS]
```

- scaler_l: ccm의 eps scaler
- idx_l: 최초 이웃의 인덱스
- eps_l: ccm의 최초 eps
- pos_l: ccm의 postion

###

### 3

```python
while idx_l >= 0 and (pos_l - mutData[idx_l][POS]) <= eps_l:
    delta = scaler_l - mutData[idx_l]['eps_scaler']
    scaler_l -= delta / info.es_control_const
    eps_l = max(info.eps * scaler_l, 0)
    idx_l -= 1
```

<mark>#logic</mark>

1. ccm의 eps scaler와 최초 이웃의 eps scaler의 차를 delta로 받는다.
2. ccm의 eps scaler를 delta/DIMIN_FACTOR을 뺀 값으로 업데이트(DIMIN_FACTOR=3)
3. eps scaler * EPSILON으로 ccm의 eps(eps_l)를 업데이트. (EPSILON=5)
4. 다음 이웃에 대해 반복한다.
   - 그만둬야하는 경우: 현재 이웃 인덱스가 0미만이 될때
   - 언제까지 반복하는가: ccm(pos_l)에서 현재 이웃(mutData[idx_l])까지의 거리가 업데이트 된 eps(eps_l)이하로 떨어지면 종료.

<mark>#keypoint</mark>

```python
delta = scaler_l - mutData[idx_l]['eps_scaler']
scaler_l -= delta / info.es_control_const
eps_l = max(info.eps * scaler_l, 0)
```

1. eps scaler가 작다는 건 중요도가 낮다는 뜻이다. 
2. 이웃 변이의 중요도가 낮으면 delta가 커진다. delta가 크면 그만큼 scaler을 더 많이 깎아내리므로 eps_l도 작아지고 클러스터를 더 왼쪽으로 확장할 수 있는 거리 한계가 빠르게 줄어든다. 즉, 품질이 낮은 변이를 만나면 그쪽으로는 금방 확장이 멈춘다.
3. 반대로 이웃의 eps_scaler가 중심 변이와 비슷하거나 더 크다면 delta가 작아지고, es_l 감소폭도 작아져서 l_max가 크게 줄어들지 않는다. 그러면 확장할 수 있는 여유가 더 생기고, 결과적으로 비슷한 품질의 이웃들을 더 많이 포함하는 클러스터를 만들 수 있게 된다.

###

<mark>#full code</mark>

```python
def expand_cluster(ccm_idx, total_mutation_info_list, info):
    # 초기 값 설정
    left_cur_dist = right_cur_dist = 0             # 좌측, 우측 확장 거리
    left_cur_index = ccm_idx - 1                     # 좌측 이동 인덱스
    right_cur_index = ccm_idx + 1                    # 우측 이동 인덱스
    mut_n = len(total_mutation_info_list)
    if right_cur_index >= mut_n:
        right_cur_index = ccm_idx

    es_l = es_r = total_mutation_info_list[ccm_idx]['eps_scaler']  
    left_max_dist = total_mutation_info_list[ccm_idx]['left_distance']
    right_max_dist = total_mutation_info_list[ccm_idx]['right_distance']

    # expand left
    while left_cur_dist < left_max_dist and left_cur_index >= 0:
        ld = total_mutation_info_list[ccm_idx][POS] - total_mutation_info_list[left_cur_index][POS]
        if ld > left_max_dist:
            break
        left_cur_dist = ld

        # eps 스케일러 감소
        delta_es = es_l - total_mutation_info_list[left_cur_index]['eps_scaler']
        es_l = es_l - (delta_es) / info.es_control_const
        mut_deps = info.eps * es_l

        if mut_deps > 0:
            left_max_dist = mut_deps
        else:
            break
        left_cur_index -= 1

    # expand right
    while right_cur_dist < right_max_dist and right_cur_index < mut_n:
        rd = total_mutation_info_list[right_cur_index][POS] - total_mutation_info_list[ccm_idx][POS]
        if rd > right_max_dist:
            break
        right_cur_dist = rd

        # eps 스케일러 감소
        delta_es = es_r - total_mutation_info_list[right_cur_index]['eps_scaler']
        es_r = es_r - (delta_es) / info.es_control_const
        mut_deps = info.eps * es_r

        if mut_deps > 0:
            right_max_dist = mut_deps
        else:
            break
        right_cur_index += 1

    if right_cur_index == mut_n:
        right_cur_index -= 1
    if left_cur_index < 0:
        left_cur_index = 0 

    ret_dict = { 
        'length': total_mutation_info_list[right_cur_index][POS] - total_mutation_info_list[left_cur_index][POS] + 1,
        'ccm_position': ccm_idx,
        'mut_positions': sorted([a[POS] for a in total_mutation_info_list[left_cur_index:right_cur_index+1] if a[HSCORE] > 0])
    }
    ret_dict['left_position'] = ret_dict['mut_positions'][0]
    ret_dict['right_position'] = ret_dict['mut_positions'][-1]

    return ret_dict
```

#

<mark>#code availability</mark>

Lab github - https://github.com/cobi-git/mutclust

#