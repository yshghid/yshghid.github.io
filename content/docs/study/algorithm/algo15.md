---
date : 2025-08-11
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #4 클러스터링 로그 뽑기 (3)"
---

# 학위논문작업 #4 클러스터링 로그 뽑기 (3)

#2025-08-11

---

### 1. Previous

```plain text
[ccm_idx 28624] Start expand_cluster: left_cur_dist=0, right_cur_dist=0, es_l=1, left_max_dist=5, right_max_dist=5
[ccm_idx 28624] Left expansion: left_index=28623, ld=1, updated es_l=1.0, mut_deps=5.0, left_max_dist=5
[ccm_idx 28624] Left expansion: left_index=28622, ld=2, updated es_l=1.0, mut_deps=5.0, left_max_dist=5.0
[ccm_idx 28624] Left expansion: left_index=28621, ld=3, updated es_l=1.6666666666666665, mut_deps=8.333333333333332, left_max_dist=5.0
[ccm_idx 28624] Left expansion: left_index=28620, ld=4, updated es_l=1.4444444444444444, mut_deps=7.222222222222222, left_max_dist=8.333333333333332
[ccm_idx 28624] Left expansion: left_index=28619, ld=5, updated es_l=1.2962962962962963, mut_deps=6.481481481481481, left_max_dist=7.222222222222222
[ccm_idx 28624] Left expansion: left_index=28618, ld=6, updated es_l=3.197530864197531, mut_deps=15.987654320987655, left_max_dist=6.481481481481481
```

<mark>Init</mark>
- es_l(28624) = 1 / mut_deps(0) = 5.0

<mark>ld=1-6</mark>
- eps_scaler(28623) = 1 / mut_deps(1) = 5.0
- eps_scaler(28622) = 1 / mut_deps(2) = 5.0
- eps_scaler(28621) = 3.0 / mut_deps(3) = 8.333333333333332
- eps_scaler(28620) = 1.0 / mut_deps(4) = 7.222222222222222
- eps_scaler(28619) = 1.0 / mut_deps(5) = 6.481481481481481
- eps_scaler(28618) = 7.0 / mut_deps(6) = 15.987654320987655

<mark>ld=6까지의 해석</mark>
- ld=2까지 최대허용거리 deps는 5.0으로 유지중이었다.
- ld=3에서 약간중요한변이를 만나(scaler 3.0) 허용거리가 8.3bp로 갱신되었다.
- ld=4~5에서 중요도가 낮은 변이를 만나 허용거리는 감소중이었다. (8.3->7.2->6.4)
- ld=6에서 중요도가 높은 변이를 만나(scaler 7.0) 허용거리가 15.9로 갱신되었다.

###

### 2. 코드수정

현재 이웃의 scaler(current es_l)를 안뽑으니까 불편해서 다음과 같이 수정해서 로그 다시뽑앗다.

```python
# expand left
with open('/data/home/ysh980101/2506/clustering_log/clustering_log.txt', 'a') as log:
    log.write(f"[ccm_idx {ccm_idx}] Left expansion: left_index={left_cur_index}, "
              f"ld={ld}, current es_l={total_mutation_info_list[left_cur_index]['eps_scaler']}, updated es_l={es_l}, mut_deps={mut_deps}, left_max_dist={left_max_dist}\n")

# expand right
with open('/data/home/ysh980101/2506/clustering_log/clustering_log.txt', 'a') as log:
    log.write(f"[ccm_idx {ccm_idx}] Right expansion: right_index={right_cur_index}, "
              f"rd={rd}, current es_r={total_mutation_info_list[right_cur_index]['eps_scaler']}, updated es_r={es_r}, mut_deps={mut_deps}, right_max_dist={right_max_dist}\n")
```

###

### 3. Left expansion (ld=7~)

```plain text
[ccm_idx 28624] Left expansion: left_index=28617, ld=7, current es_l=5, updated es_l=3.7983539094650207, mut_deps=18.991769547325102, left_max_dist=15.987654320987655
[ccm_idx 28624] Left expansion: left_index=28616, ld=8, current es_l=5, updated es_l=4.198902606310014, mut_deps=20.99451303155007, left_max_dist=18.991769547325102
```

- ld = 7
  - 현재 한도 left_max_dist(6) = 15.987654320987655 이므로 7 ≤ 15.987654320987655 -> 확장 가능
  - eps_scaler(28617) = 5.0 / es_l(7) = 3.7983539094650207 / mut_deps(7) = 18.991769547325102

- ld = 8
  - 현재 한도 left_max_dist(7) = 18.991769547325102 이므로 8 ≤ 18.991769547325102 -> 확장 가능
  - eps_scaler(28616) = 5.0 / es_l(8) = 4.198902606310014 / mut_deps(8) = 20.99451303155007

```plain text
[ccm_idx 28624] Left expansion: left_index=28615, ld=9, current es_l=65, updated es_l=24.465935070873343, mut_deps=122.32967535436671, left_max_dist=20.99451303155007
```

- ld = 9
  - variables
    - left_max_dist: 진입 시 한도
    - mut_deps: 다음 스텝 한도

  - 확장 가능?
    - ld = 9
    - 현재 한도 left_max_dist(8) = 20.99451303155007 이므로 9 ≤ 20.99451303155007 -> 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 4.198902606310014으로 기록되어있음.
    - eps_scaler(28615) = 65.0
    - delta_es = es_l(8) - eps_scaler(28615) = 4.198902606310014 − 65 = −60.801097393689986
    - <mark>es_l(9)</mark> = es_l(8) - delta_es / 3 = 4.198902606310014 - (−60.801097393689986/3) = <mark>24.465935070873343</mark>

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(9)</mark> = info.eps * es_l(9) = 5 * 24.465935070873343 = <mark>122.32967535436671</mark>
    - 이 값은 다음 줄(ld=10)에 left_max_dist로 반영됨.

- ld=9에서 아주 중요한 이웃(eps_scaler=65) 을 만나 es가 4.198 -> 24.466로 대폭 상승, 허용거리도 20.99bp -> 122.33bp로 폭발적으로 확대. 

```plain text
[ccm_idx 28624] Left expansion: left_index=28614, ld=10, current es_l=1, updated es_l=16.64395671391556, mut_deps=83.2197835695778, left_max_dist=122.32967535436671
[ccm_idx 28624] Left expansion: left_index=28613, ld=11, current es_l=1, updated es_l=11.429304475943706, mut_deps=57.14652237971853, left_max_dist=83.2197835695778
[ccm_idx 28624] Left expansion: left_index=28612, ld=12, current es_l=17, updated es_l=13.28620298396247, mut_deps=66.43101491981236, left_max_dist=57.14652237971853
[ccm_idx 28624] Left expansion: left_index=28611, ld=13, current es_l=2, updated es_l=9.524135322641646, mut_deps=47.62067661320823, left_max_dist=66.43101491981236
[ccm_idx 28624] Left expansion: left_index=28610, ld=14, current es_l=1, updated es_l=6.682756881761097, mut_deps=33.41378440880548, left_max_dist=47.62067661320823
[ccm_idx 28624] Left expansion: left_index=28609, ld=15, current es_l=1, updated es_l=4.788504587840731, mut_deps=23.942522939203656, left_max_dist=33.41378440880548
[ccm_idx 28624] Left expansion: left_index=28608, ld=16, current es_l=1, updated es_l=3.5256697252271545, mut_deps=17.62834862613577, left_max_dist=23.942522939203656
[ccm_idx 28624] Left expansion: left_index=28607, ld=17, current es_l=1, updated es_l=2.683779816818103, mut_deps=13.418899084090514, left_max_dist=17.62834862613577
```

- ld = 10
  - 현재 한도 left_max_dist(9) = 122.32967535436671 이므로 10 ≤ 122.32967535436671 -> 확장 가능
  - eps_scaler(28614) = 1.0 / es_l(10) = 16.64395671391556 / mut_deps(10) = 83.2197835695778

- ld = 11
  - 현재 한도 left_max_dist(10) = 83.2197835695778 이므로 11 ≤ 83.2197835695778 -> 확장 가능
  - eps_scaler(28613) = 1.0 / es_l(11) = 11.429304475943706 / mut_deps(11) = 57.14652237971853

- ld = 12
  - 현재 한도 left_max_dist(11) = 57.14652237971853 이므로 12 ≤ 57.14652237971853 -> 확장 가능
  - eps_scaler(28612) = 17.0 / es_l(12) = 13.28620298396247 / mut_deps(12) = 66.43101491981236

- ld = 13
  - 현재 한도 left_max_dist(12) = 66.43101491981236 이므로 13 ≤ 66.43101491981236 -> 확장 가능
  - eps_scaler(28611) = 2.0 / es_l(13) = 9.524135322641646 / mut_deps(13) = 47.62067661320823

- ld = 14
  - 현재 한도 left_max_dist(13) = 47.62067661320823 이므로 14 ≤ 47.62067661320823 -> 확장 가능
  - eps_scaler(28610) = 1.0 / es_l(14) = 6.682756881761097 / mut_deps(14) = 33.41378440880548

- ld = 15
  - 현재 한도 left_max_dist(14) = 33.41378440880548 이므로 15 ≤ 33.41378440880548 -> 확장 가능
  - eps_scaler(28610) = 1.0 / es_l(15) = 4.788504587840731 / mut_deps(15) = 23.942522939203656

- ld = 16
  - 현재 한도 left_max_dist(15) = 23.942522939203656 이므로 16 ≤ 23.942522939203656 -> 확장 가능
  - eps_scaler(28609) = 1.0 / es_l(16) = 3.5256697252271545 / mut_deps(6) = 17.62834862613577

- ld = 17
  - 현재 한도 left_max_dist(16) = 17.62834862613577 이므로 17 ≤ 17.62834862613577 -> 확장 가능
  - eps_scaler(28609) = 1.0 / es_l(17) = 2.683779816818103 / mut_deps(6) = 13.418899084090514

- ld = 18 (시행x)
  - 현재 한도 left_max_dist(17) = 13.418899084090514 이므로 18 > 13.418899084090514 -> 확장 불가
  - 다음 이웃(ld=18)의 거리(=18bp)가 새 한도(13.418899084090514)를 초과 
    - 왼쪽 확장 정지.

###

### 4. Right expansion

```plain text
[ccm_idx 28624] Right expansion: right_index=28625, rd=1, current es_r=1, updated es_r=1.0, mut_deps=5.0, right_max_dist=5
```

- es_l=1, left_max_dist=5, <mark>es_r=1</mark>, <mark>right_max_dist=5</mark>
- 초기 반경 mut_deps: 5*1 = 5 bp

```plain text
[ccm_idx 28624] Right expansion: right_index=28625, rd=1, updated es_r=1.0, mut_deps=5.0, right_max_dist=5
[ccm_idx 28624] Right expansion: right_index=28626, rd=2, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28627, rd=3, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28628, rd=4, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
[ccm_idx 28624] Right expansion: right_index=28629, rd=5, updated es_r=1.0, mut_deps=5.0, right_max_dist=5.0
```

- rd = 1
  - 현재 한도 right_max_dist(0) = 5 이므로 1 ≤ 5 -> 확장 가능
  - eps_scaler(28625) = 1 / es_r(1) = 1.0 / mut_deps(1) = 5.0

- rd = 2
  - 현재 한도 right_max_dist(1) = 5 이므로 2 ≤ 5 -> 확장 가능
  - eps_scaler(28626) = 1 / es_r(2) = 1.0 / mut_deps(2) = 5.0

- rd = 3
  - 현재 한도 right_max_dist(2) = 5 이므로 3 ≤ 5 -> 확장 가능
  - eps_scaler(28627) = 1 / es_r(3) = 1.0 / mut_deps(3) = 5.0

- rd = 4
  - 현재 한도 right_max_dist(3) = 5 이므로 4 ≤ 5 -> 확장 가능
  - eps_scaler(28628) = 1 / es_r(4) = 1.0 / mut_deps(4) = 5.0

- rd = 5
  - 현재 한도 right_max_dist(4) = 5 이므로 5 ≤ 5 -> 확장 가능
  - eps_scaler(28629) = 1 / es_r(5) = 1.0 / mut_deps(5) = 5.0

- rd = 6 (시행x)
  - 현재 한도 right_max_dist(5) = 5 이므로 6 > 5 -> 확장 불가
  - 다음 이웃(rd=6)의 거리(=6bp)가 새 한도(5)를 초과 
    - 오른쪽 확장 정지.

###

### 5. Termination

```plain text
[ccm_idx 28624] Final cluster: left_position=28872, right_position=28896, length=25
```

- 최종 생성 클러스터
  - 인덱스: 28607-28629
  - 유전체 좌표(POS): 28872-28896
  - length: 25

- cf
  - 시작/끝 좌표는 HSCORE>0인 첫/마지막 좌표.




#
