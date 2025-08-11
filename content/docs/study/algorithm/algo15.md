---
date : 2025-08-11
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #3 MutClust 로직 정리: 클러스터 확장"
---

# 학위논문작업 #3 MutClust 로직 정리: 클러스터 확장

#2025-08-11

---

<mark>Concept</mark>

- `expand_cluster(ccm_idx, total_mutation_info_list, info)`

- 하나의 CCM 인덱스(`ccm_idx`)를 중심으로 반경을 점진적으로 조절하며 좌우로 확장한다.
- 핵심: 가까운 이웃일수록 eps 스케일러를 덜 깎고, 멀거나 중요도 낮은 이웃일수록 더 깎는다.

### 1. Input

```python
def expand_cluster(ccm_idx, total_mutation_info_list, info):
```

- ccm_idx: 중심이 되는 CCM 인덱스.
- total_mutation_info_list: 각 변이(행)에 대한 dict들의 리스트. 
  - key: POS(좌표), HSCORE, eps_scaler, left_distance, right_distance 등.

- info: 하이퍼파라미터 묶음
  - info.eps : 스케일러를 실제 거리(bp)로 변환하는 기본 단위
  - info.es_control_const: 감쇠(또는 증폭) 완만함을 제어하는 상수


### 2. 초기 한도 설정

```python
left_cur_dist = right_cur_dist = 0
left_cur_index = ccm_idx - 1
right_cur_index = ccm_idx + 1
mut_n = len(total_mutation_info_list)

# 경계 보정
if right_cur_index >= mut_n:
    right_cur_index = ccm_idx

# 양쪽 '현재 eps 스케일러'는 중심점의 스케일러로 시작
es_l = es_r = total_mutation_info_list[ccm_idx]['eps_scaler']

# 최초 최대 확장 한도
left_max_dist  = total_mutation_info_list[ccm_idx]['left_distance']
right_max_dist = total_mutation_info_list[ccm_idx]['right_distance']
```
- left_max_dist/right_max_dist: “초기 한도” (진행 도중 계속 갱신)




