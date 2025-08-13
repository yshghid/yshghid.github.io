---
date : 2025-08-11
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #3 클러스터링 로그 뽑기 (2)"
---

# 학위논문작업 #3 클러스터링 로그 뽑기 (2)

#2025-08-11

---


### 1. Init

```plain text
[ccm_idx 28624] Start expand_cluster: left_cur_dist=0, right_cur_dist=0, es_l=1, left_max_dist=5, right_max_dist=5
```

- <mark>es_l=1</mark>, <mark>left_max_dist=5</mark>, es_r=1, right_max_dist=5
- 초기 반경 mut_deps: 5*1 = 5 bp


###

### 2. Left expansion

```plain text
[ccm_idx 28624] Left expansion: left_index=28623, ld=1, updated es_l=1.0, mut_deps=5.0, left_max_dist=5
```

- ld = 1 
  - 확장 가능?
    - ld = POS(center) - POS(28623) = 1
    - 현재 한도 left_max_dist(0)=5 이므로 ld(=1) ≤ 5 여서 확장 가능

  - scaler update?
    - eps_scaler(28623) = 1
    - delta_es = es_l(0) - eps_scaler(28623) = 1 - 1 = 0
    - <mark>es_l(1)</mark> = es_l(0) - delta_es / es_control_const = 1 - 0/3 = <mark>1.0</mark> (유지)

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(1)</mark> = info.eps * es_l(1) = 5 * 1.0 = <mark>5.0</mark>
    - 이 값이 다음 줄부터 left_max_dist로 반영됨.

```plain text
[ccm_idx 28624] Left expansion: left_index=28622, ld=2, updated es_l=1.0, mut_deps=5.0, left_max_dist=5.0
```

- ld=2
  - 확장 가능?
    - ld = POS(center) - POS(28622) = 2
    - 직전 스텝에서 계산된 새 한도 mut_deps(1)=5.0이 이번 스텝의 한도로 적용: left_max_dist(1)=5.0이고 ld(=2) ≤ 5.0 여서 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 1.0으로 유지됨. 
    - eps_scaler(28622) = 1
    - delta_es = es_l(1) - eps_scaler(28622) = 1.0 - 1 = 0
    - <mark>es_l(2)</mark> = es_l(1) - delta_es / 3 = 1.0 - 0 = <mark>1.0</mark> (유지)

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(2)</mark> = info.eps * es_l(2) = 5 * 1.0 = <mark>5.0</mark>

```plain text
[ccm_idx 28624] Left expansion: left_index=28621, ld=3, updated es_l=1.6666666666666665, mut_deps=8.333333333333332, left_max_dist=5.0
```
- ld=3
  - 확장 가능?
    - ld = POS(center) - POS(28621) = 3
    - 직전 스텝에서 계산된 새 한도 mut_deps(2)=5.0이 이번 스텝의 한도로 적용: left_max_dist(2)=5.0이고 ld(=3) ≤ 5.0 여서 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 1.0 
    - eps_scaler(28621) = 3.0
    - delta_es = es_l(2) - eps_scaler(28621) = 1.0 - 3 = -2
    - <mark>es_l(3)</mark> = es_l(2) - delta_es / 3 = 1.0 - (-2)/3 = <mark>1.6666666666666665</mark>

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(3)</mark> = info.eps * es_l(3) = 5 * 1.6666666666666665 = <mark>8.333333333333332</mark>
    - 이 값은 다음 줄(ld=4)에 left_max_dist로 반영됨.

```plain text
[ccm_idx 28624] Left expansion: left_index=28620, ld=4, updated es_l=1.4444444444444444, mut_deps=7.222222222222222, left_max_dist=8.333333333333332
```

- ld=4
  - 확장 가능?
    - ld = POS(center) - POS(28620) = 4
    - 직전 스텝에서 계산된 새 한도 mut_deps(3)=8.333333333333332이 이번 스텝의 한도로 적용: left_max_dist(3)=8.333333333333332이고 ld(=4) ≤ 8.333333333333332 여서 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 1.6666666666666665
    - eps_scaler(28620) = 1.0
    - delta_es = es_l(3) - eps_scaler(28620) = 1.6666666666666665 - 1 = 0.6666666666666665
    - <mark>es_l(4)</mark> = es_l(3) - delta_es / 3 = 1.6666666666666665 - 0.6666666666666665/3 = <mark>1.4444444444444444</mark>

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(4)</mark> = info.eps * es_l(4) = 5 * 1.4444444444444444 = <mark>7.222222222222222</mark>
    - 이 값은 다음 줄(ld=5)에 left_max_dist로 반영됨.

```plain text
[ccm_idx 28624] Left expansion: left_index=28619, ld=5, updated es_l=1.2962962962962963, mut_deps=6.481481481481481, left_max_dist=7.222222222222222
```
- ld=5
  - 확장 가능?
    - ld = POS(center) - POS(28619) = 5
    - 직전 스텝에서 계산된 새 한도 mut_deps(4)=7.222222222222222이 이번 스텝의 한도로 적용: left_max_dist(4)=7.222222222222222이고 ld(=5) ≤ 7.222222222222222 여서 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 1.4444444444444444으로 기록되어있음.
    - eps_scaler(28619) = 1.0
    - delta_es = es_l(4) - eps_scaler(28619) = 1.4444444444444444 − 1 = 0.4444444444444444
    - <mark>es_l(5)</mark> = es_l(4) - delta_es / 3 = 1.4444444444444444 - 0.4444444444444444/3 = <mark>1.2962962962962963</mark>

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(5)</mark> = info.eps * es_l(5) = 5 * 1.2962962962962963 = <mark>6.481481481481481</mark>
    - 이 값은 다음 줄(ld=6)에 left_max_dist로 반영됨.

```plain text
[ccm_idx 28624] Left expansion: left_index=28618, ld=6, updated es_l=3.197530864197531, mut_deps=15.987654320987655, left_max_dist=6.481481481481481
```
- ld=6
  - 확장 가능?
    - ld = POS(center) - POS(28618) = 6
    - 직전 스텝에서 계산된 새 한도 mut_deps(5)=6.481481481481481이 이번 스텝의 한도로 적용: left_max_dist(5)=6.481481481481481이고 ld(=6) ≤ 6.481481481481481 여서 확장 가능

  - scaler update?
    - 직전 스텝에서 updated es_l는 1.2962962962962963으로 기록되어있음.
    - eps_scaler(28618) = 7.0
    - delta_es = es_l(5) - eps_scaler(28618) = 1.2962962962962963 − 7 = −5.703703703703703
    - <mark>es_l(6)</mark> = es_l(5) - delta_es / 3 = 1.2962962962962963 - (−5.703703703703703/3) = <mark>3.197530864197531</mark>

  - 새 한도(다음 스텝에 적용)
    - <mark>mut_deps(6)</mark> = info.eps * es_l(6) = 5 * 3.197530864197531 = <mark>15.987654320987655</mark>
    - 이 값은 다음 줄(ld=7)에 left_max_dist로 반영됨.

- ld=6에서 아주 중요한 이웃(eps_scaler=7) 을 만나 es가 1.296에서 3.197로 크게 상승, 허용거리도 6.48bp에서 15.99bp로 급팽창. (이때문에 ld=7,8,9에서 창이 더 멀리 열릴 수 있음)

#