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

1) 데이터
   - 샘플 개수 K=3 (총 3개의 데이터 행렬)
   - 샘플의 행 개수 nk: [4,5,3] (각 데이터 행렬의 행 개수는 다름)
   - 특징 개수 J: 3 (각 데이터 행렬의 열 개수는 동일함)
   - 잠재 요인 개수 R: 2 (모델의 랭크)
   - 데이터 행렬 Xk
     ![image](https://github.com/user-attachments/assets/9ba6d188-dac1-482d-adb3-b19a5b655218)

2) 모델 초기화
   - 공통 요소 행렬
     - 공통 로딩 행렬 A: 모든 데이터 샘플에서 동일한 열 변수를 공유함.
       ![image](https://github.com/user-attachments/assets/ef8fb5cd-d59a-4ba5-8fb1-97ea6647714a)
     - 공통 요인 행렬 F
       ![image](https://github.com/user-attachments/assets/e9632f3c-389b-411c-8173-286861b88d07)
  
   - 샘플별 요소 행렬
     - 샘플별 가중치 행렬 Dk (대각 행렬)
       ![image](https://github.com/user-attachments/assets/59564d8b-54d2-4bab-be2d-06370112a3c3)
     - 샘플별 직교 행렬 Pk

       P1, P2, P3는 PTkPk = I 조건을 만족해야함.

3) 초기 손실함수 계산
   - 손실 함수
     ![image](https://github.com/user-attachments/assets/af0648fe-6eb2-44cc-af1d-ad3d2a92ee56)
   - 초기 손실값: 13.5951

4) 알고리즘 최적화 과정(Pk, F, A, Dk 업데이트)
   1) Pk 업데이트(SVD 사용)
      - 각 샘플에 대해 Pk를 최적화하여 현재 행렬과 가장 잘 맞도록 업데이트.
        ![image](https://github.com/user-attachments/assets/0054bf23-349f-4dc5-b22f-e4a4c74de313)
   2) F, A, Dk 업데이트 (ALS 사용)
      ![image](https://github.com/user-attachments/assets/a3fa897e-2a47-43c8-a5fb-24705d7289e7)

### 3-2. 슈도코드

- 알고리즘 개요
  1. 입력 데이터
     - 데이터 행렬 Xk (k=1, ..., K, 각 행렬의 크기는 다름)
     - 랭크 R, 반복횟수 max_iter
  2. 초기화
     - 공통 로딩 행렬 A (PCA를 이용해서 초기화)
     - 공통 요인 행렬 F (임의의 값 또는 단위행렬)
     - 샘플별 대각 행렬 Dk (랜덤 초기화)
     - 샘플별 직교 행렬 Pk (QR 분해로 초기화)
  3. 반복 최적화 과정
     - Pk 업데이트 (SVD 활용)
     - F, Dk, A 업데이트 (ALS 활용)
     - 수렴 확인 (Lold - Lnew < ϵ이면 종료)

- 슈도코드

```python
# PARAFAC2 Direct Fitting Algorithm (Pseudocode)

Input: 
    X_k  # List of K matrices with varying row sizes but fixed columns J
    R    # Rank of decomposition
    max_iter  # Maximum number of iterations
    epsilon   # Convergence threshold

# Step 1: Initialize matrices
Initialize A (J × R) using PCA on stacked X_k
Initialize F (R × R) as a random matrix
For each k in {1, ..., K}:
    Initialize D_k (R × R) as a diagonal random matrix
    Initialize P_k (n_k × R) using QR decomposition

# Step 2: Iterative Optimization
For iter in {1, ..., max_iter}:
    
    # (1) Update P_k using SVD
    For each k in {1, ..., K}:
        Compute M_k = F * D_k * A^T * X_k^T  # Projection matrix
        Compute U_k, S_k, V_k = SVD(M_k)
        Update P_k = V_k * U_k^T  # Ensure P_k is orthogonal

    # (2) Update F, D_k, A using ALS
    Compute X_k^T * P_k  # Projected data matrices
    Solve least squares problem:
        Minimize ||X_k P_k^T - F D_k A^T||_F^2 over F, D_k, A
        Update F, A, and D_k using ALS updates

    # Step 3: Check for convergence
    Compute loss L_new = sum(||X_k - P_k F D_k A^T||_F^2 for k in {1,...,K})
    If abs(L_old - L_new) < epsilon:
        Break  # Converged

    L_old = L_new  # Update loss

# Output: Optimized F, A, D_k, P_k matrices
Return F, A, {D_k}, {P_k}
```

- 상세설명

1) P_k 업데이트 (SVD 사용)
   - 각 샘플 k에 대해
     ![image](https://github.com/user-attachments/assets/1b857f62-c5f2-4b63-9fcb-d5813b0355ca)
   - 이를 특이값 분해(SVD)하여 Pk 업데이트 
     ![image](https://github.com/user-attachments/assets/eeb83bd9-8b83-4ba6-a0df-c00b5c1b4666)
   - Pk가 정규 직교 행렬이 되도록 보장.

2) F, A, Dk 업데이트 (ALS(Alternating Least Squares) 사용)
   - 최소화할 손실함수
     ![image](https://github.com/user-attachments/assets/77e51b9b-38d4-4ee0-b617-2b69acc14477)
   - 행렬을 순차적으로 업데이트.
     ![image](https://github.com/user-attachments/assets/dec7c6d8-016d-409e-8619-f3ae7c5bb793)
   - 각 업데이트는 선형 연립방정식을 푸는 Least Squares 문제로 변환.

## 4. 확장 가능성 및 응용

- 비음수 행렬 분해(NMF) 등 다양한 제약 조건 적용 가능.
- 결측치(missing data) 처리 가능 (기존 방법에서는 어려움).
- 고차원 데이터(N-way, Tensor data)에도 적용 가능.
- 화학 데이터 분석(예: 분광 분석, 크로마토그래피) 등에서 활용 가능.


### 논문 출처

PARAFAC2 - PART 1. A DIRECT FITTING ALGORITHM FOR THE PARAFAC2 MODEL https://three-mode.leidenuniv.nl/pdf/k/kierstenbergebro1999jch.pdf
