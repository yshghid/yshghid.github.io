---
date : 2025-08-10
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #2 클러스터링 로그 뽑기"
---

# 학위논문작업 #2 클러스터링 로그 뽑기

#2025-08-10

---

<mark>Objective</mark>

- MutClust의 기존 코드에서는 클러스터링 수행후 클러스터 정보만 출력할뿐 neighbor eps scaler에 따른 ccm eps scaler의 업데이트와 그에 따른 eps 업데이트 내역을 따로 빼진 않았었다.
- 근데 클러스터링 과정을 설명하기에 좋은 예시를 만들기가 어려워서 (기존 예시는 맘에 안들고..) 그냥 로그를 다 뽑고 괜찮아 보이는걸 건져보기로 했다.


###

### 1. 로깅 코드 추가하기

일단 로그는 총 4번뽑을건데
1. 시작 (left_cur_dist & right_cur_dist=0일때)
2. Left expansion 과정 
3. Right expansion 과정
4. 최종 결과 

이렇게뽑을려고한다.

<mark>#로그1</mark>

```python
# 시작 로그 기록: ccm_idx, 초기 left_cur_dist, right_cur_dist, 초기 es 값, 최대 확장 거리
with open('clustering_log.txt', 'a') as log:
    log.write(f"[ccm_idx {ccm_idx}] Start expand_cluster: left_cur_dist={left_cur_dist}, right_cur_dist={right_cur_dist}, "
                f"es_l={es_l}, left_max_dist={left_max_dist}, right_max_dist={right_max_dist}\n")
```

- ccm_idx: 이번 iter의 ccm 인덱스
- left_cur_dist: 최종 좌측 확장 거리(는 아직 시작 안했으니까 0)
- es_l: ccm의 왼쪽 eps scaler.

###

<mark>#로그2</mark>

```python
# expand left
while left_cur_dist < left_max_dist and left_cur_index >= 0:
    # 현재 left 관련 값을 로그에 기록
    with open('clustering_log.txt', 'a') as log:
        log.write(f"[ccm_idx {ccm_idx}] Left expansion: left_index={left_cur_index}, "
                    f"ld={ld}, updated es_l={es_l}, mut_deps={mut_deps}, left_max_dist={left_max_dist}\n")
```


- ld: 현재 검사중인 왼쪽이웃과 ccm사이 distance
- updated es_l: 이웃 중요도를 반영해서 갱신된 eps scaler.
- mut_deps: 갱신된 eps scaler로 계산된 ccm의 현재 deps
- left_max_dist: 최대 확장 거리


###

<mark>#로그3</mark>

```python
# expand right
while right_cur_dist < right_max_dist and right_cur_index < mut_n:
    # 현재 right 관련 값을 로그에 기록
    with open('clustering_log.txt', 'a') as log:
        log.write(f"[ccm_idx {ccm_idx}] Right expansion: right_index={right_cur_index}, "
                    f"rd={rd}, updated es_r={es_r}, mut_deps={mut_deps}\n, right_max_dist={right_max_dist}\n")
```
(왼쪽 확장과 동일)


###

<mark>#로그4</mark>

```python
# 최종 확장된 결과 기록
with open('clustering_log.txt', 'a') as log:
    log.write(f"[ccm_idx {ccm_idx}] Final cluster: left_position={ret_dict['left_position']}, "
                f"right_position={ret_dict['right_position']}, length={ret_dict['length']}\n\n")

return ret_dict
```
- left_position, right_position: 최종 클러스터의 시작/끝 인덱스


###

<mark>#로깅포함 전체 코드</mark>

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

    # 시작 로그 기록: ccm_idx, 초기 left_cur_dist, right_cur_dist, 초기 es 값, 최대 확장 거리
    with open('clustering_log.txt', 'a') as log:
        log.write(f"[ccm_idx {ccm_idx}] Start expand_cluster: left_cur_dist={left_cur_dist}, right_cur_dist={right_cur_dist}, "
                  f"es_l={es_l}, left_max_dist={left_max_dist}, right_max_dist={right_max_dist}\n")

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

        # 현재 left 관련 값을 로그에 기록
        with open('clustering_log.txt', 'a') as log:
            log.write(f"[ccm_idx {ccm_idx}] Left expansion: left_index={left_cur_index}, "
                      f"ld={ld}, updated es_l={es_l}, mut_deps={mut_deps}\n")

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

        # 현재 right 관련 값을 로그에 기록
        with open('clustering_log.txt', 'a') as log:
            log.write(f"[ccm_idx {ccm_idx}] Right expansion: right_index={right_cur_index}, "
                      f"rd={rd}, updated es_r={es_r}, mut_deps={mut_deps}\n")

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

    # 최종 확장된 결과 기록
    with open('clustering_log.txt', 'a') as log:
        log.write(f"[ccm_idx {ccm_idx}] Final cluster: left_position={ret_dict['left_position']}, "
                  f"right_position={ret_dict['right_position']}, length={ret_dict['length']}\n\n")

    return ret_dict
```

###

### 2. 클러스터링 수행

<mark>#1 Load package</mark>

```python
import pandas as pd
import numpy as np
import os
os.sys.path.append("/data/home/ysh980101/2407/Mutclust") 

from pathlib import Path
from Bin.Utils.utils import *
from Bin.arg_parser import *
from Bin.mlib import *
```

<mark>#2 Find CCM</mark>

```python
i = 1
tag = f"test{i}"
input_path = "/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle"
outdir = f"/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_{tag}/"
Path(outdir).mkdir(parents=True, exist_ok=True)

info = set_env(input = input_path, output = outdir)
Input_df = readPickle(input_path)
init(Input_df, info)
mutInfo, ccms = get_candidate_core_mutations(Input_df, info, tag, i)
```
```plain text
--- Configurations ---
Input data: '/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle' (29903, 5)
Output dir: '/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_test1/'
Parameters:
  Min Eps=5
  Max Eps=1000
  Min per_sum=0.0
  Eps scaling factor=10.0
  Expansion diminishing factor=3
  Min cluster length=10
----------------------

Searching candidate core mutations...


1990 CCMs found.
```

<mark>#3 Perform clustering</mark>

```python
hotspots = dynaclust(mutInfo, ccms, info, tag, i)
```
```plain text
Performing dynamic clustering...
1990 clusters found
Merging clusters...
Merged clusters: 477
```

###

### 3. 로그 확인

```plain text
[ccm_idx 28624] Start expand_cluster: left_cur_dist=0, right_cur_dist=0, es_l=1, left_max_dist=5, right_max_dist=5
[ccm_idx 28624] Left expansion: left_index=28623, ld=1, updated es_l=1.0, mut_deps=5.0, left_max_dist=5
[ccm_idx 28624] Left expansion: left_index=28622, ld=2, updated es_l=1.0, mut_deps=5.0, left_max_dist=5.0
[ccm_idx 28624] Left expansion: left_index=28621, ld=3, updated es_l=1.6666666666666665, mut_deps=8.333333333333332, left_max_dist=5.0
[ccm_idx 28624] Left expansion: left_index=28620, ld=4, updated es_l=1.4444444444444444, mut_deps=7.222222222222222, left_max_dist=8.333333333333332
[ccm_idx 28624] Left expansion: left_index=28619, ld=5, updated es_l=1.2962962962962963, mut_deps=6.481481481481481, left_max_dist=7.222222222222222
[ccm_idx 28624] Left expansion: left_index=28618, ld=6, updated es_l=3.197530864197531, mut_deps=15.987654320987655, left_max_dist=6.481481481481481
[ccm_idx 28624] Left expansion: left_index=28617, ld=7, updated es_l=3.7983539094650207, mut_deps=18.991769547325102, left_max_dist=15.987654320987655
[ccm_idx 28624] Left expansion: left_index=28616, ld=8, updated es_l=4.198902606310014, mut_deps=20.99451303155007, left_max_dist=18.991769547325102
[ccm_idx 28624] Left expansion: left_index=28615, ld=9, updated es_l=24.465935070873343, mut_deps=122.32967535436671, left_max_dist=20.99451303155007
[ccm_idx 28624] Left expansion: left_index=28614, ld=10, updated es_l=16.64395671391556, mut_deps=83.2197835695778, left_max_dist=122.32967535436671
[ccm_idx 28624] Left expansion: left_index=28613, ld=11, updated es_l=11.429304475943706, mut_deps=57.14652237971853, left_max_dist=83.2197835695778
[ccm_idx 28624] Left expansion: left_index=28612, ld=12, updated es_l=13.28620298396247, mut_deps=66.43101491981236, left_max_dist=57.14652237971853
[ccm_idx 28624] Left expansion: left_index=28611, ld=13, updated es_l=9.524135322641646, mut_deps=47.62067661320823, left_max_dist=66.43101491981236
[ccm_idx 28624] Left expansion: left_index=28610, ld=14, updated es_l=6.682756881761097, mut_deps=33.41378440880548, left_max_dist=47.62067661320823
[ccm_idx 28624] Left expansion: left_index=28609, ld=15, updated es_l=4.788504587840731, mut_deps=23.942522939203656, left_max_dist=33.41378440880548
[ccm_idx 28624] Left expansion: left_index=28608, ld=16, updated es_l=3.5256697252271545, mut_deps=17.62834862613577, left_max_dist=23.942522939203656
[ccm_idx 28624] Left expansion: left_index=28607, ld=17, updated es_l=2.683779816818103, mut_deps=13.418899084090514, left_max_dist=17.62834862613577
[ccm_idx 28624] Right expansion: right_index=28625, rd=1, updated es_r=1.0, mut_deps=5.0, right_max_dist=5
[ccm_idx 28624] Right expansion: right_index=28626, rd=2, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28627, rd=3, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28628, rd=4, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28629, rd=5, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Final cluster: left_position=28872, right_position=28896, length=25
```

1990 CCM에 대해서 이런식으로 로그가 다뽑혓는데 좌우 차이 많이나보이는것만 일단 가져와봤다.



#



