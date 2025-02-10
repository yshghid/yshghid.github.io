---
categories: ['◡̈⋆*']
bookHidden: true
title: "◡̈⋆*"
bookComments: true
---

## PARAFAC2

##### 2025-02-09

---

## 1. PARAFAC(Parallel Factor Analysis)

- PARAFAC은 주성분 분석(PCA)의 확장판으로, 다차원 데이터(Three-way data)를 분석하는 데 사용된다.
- PARAFAC1 모델은 데이터 행과 열 단위가 모두 동일한 경우 적용 가능하지만, PARAFAC2 모델은 행 단위가 다를 때도 사용 가능하다.

## 2. PARAFAC2 모델 적합 알고리즘

- PARAFAC2 모델
  ![image](https://github.com/user-attachments/assets/2e1be9f0-5a0b-426d-853c-b70679164425)

- 손실 함수
  ![image](https://github.com/user-attachments/assets/daba6987-93a4-4391-9b12-cb66ca4d490f)
  - 이 손실 함수를 최소화한다.
  - 여기서 Pk는 정규 직교 행렬.
    ![image](https://github.com/user-attachments/assets/882be398-16bf-43ee-9f80-09abc6505234)
  - 제약 조건은
    1) Fk = PkF
    2) PTkPk = IR
    ![image](https://github.com/user-attachments/assets/d2517748-8c1a-4f0f-84b0-e4c3c05ad3fa)

- 개선점
  1) XTkXk를 직접 계산하지 않고 원본 데이터 Xk를 그대로 사용 -> 연산량이 감소함.
  2) Pk를 도입하여 각 샘플의 행 정보를 보존함.
  3) Fk = PkF로 직접 최적화 가능 -> 행 정보 해석 가능.
  4) 다양한 제약 조건(ex. 비음수성, 결측값 처리) 적용 가능.

- 알고리즘 개요
  - 목표: 손실 함수(오차 제곱합)를 최소화하는 PARAFAC2 모델의 최적 매개변수(F, A, Dk, Pk) 찾기.
  - 핵심 아이디어:
    1) 기존의 교차곱 기반 방식 대신, 데이터 행렬을 직접 활용.
    2) Alternating Least Squares (ALS) 방식을 사용하여 행렬 업데이트.
    3) 반복적으로 행렬 Pk, F, A, Dk를 최적화하면서 수렴.


## 3. 알고리즘 절차
  1) 초기화: A는 PCA로 초기화하고, F와 Dk는 단위 행렬로 설정.
  2) Pk 업데이트: SVD를 사용하여 최적화.
     ![image](https://github.com/user-attachments/assets/bfe26057-f49c-4603-978d-bb05c5d16515)
  3) F,A,Dk 업데이트: Alternating Least Squares (ALS) 방식 사용.
  4) 수렴 조건 확인: 손실 함수의 변화가 특정 임계값 이하일때 종료

### 3-1. 예시
