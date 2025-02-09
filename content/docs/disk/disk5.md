---
categories: ['◡̈⋆*']
bookHidden: true
title: "◡̈⋆*"
bookComments: true
---

## Tensor Decompositions and Machine Learning

##### 2025-02-09

---

## 1. 개요 (Abstract & Introduction)

- 텐서(Tensor)는 다차원 배열(multi-dimensional array)로, 행렬(matrix)의 일반화된 개념.
- 텐서 분해는 비지도 학습(unsupervised learning) 및 시간적(temporal) 및 다중 관계 데이터 분석(multi-relational data analysis) 분야에서 활용된다.

## 2. 행렬 분해의 한계 (Motivating Example with Matrix Decomposition)

- 행렬 분해(Matrix Decomposition)는 다양한 수학적 문제 해결에 사용되지만, 비고유성(non-uniqueness) 문제가 발생한다.
  - 예를 들어, 행렬 M을 M=AB^T로 분해할 때, 행렬 A와 B^T는 유일하지 않을 수 있다.

- Spearman’s Hypothesis
  - 1904년 심리학자 Charles Spearman은 인간의 지능을 두 가지 숨겨진 요인(추론적 지능, 재생적 지능)으로 설명할 수 있다고 주장했다.
  - 이를 검증하기 위해 학생들이 수행한 여러 테스트 점수를 행렬 M으로 저장하고, 이를 저차원 행렬로 분해하여 요인을 찾으려 했다.
  - 하지만 이러한 행렬 분해는 비고유성 문제를 가지며, 행렬 분해만으로는 확실한 요인을 찾기 어렵다.

- Rotation Problem
  - 행렬 분해의 비고유성 문제를 강조하기 위한 개념.
  - 행렬 M을 두개의 행렬 A,B로 분해할 때, 임의의 가역 행렬 R을 사용하면 새로운 분해가 가능하다.
    ![image](https://github.com/user-attachments/assets/1ba0db3e-99af-4603-ab14-2f760f9f3a9c)
  - 따라서 행렬 분해는 추가적인 제약(예: 직교성)을 부과하지 않으면 고유한 결과를 보장하지 않는다.


## 3. 텐서 기초 (Introduction to Tensors)

1. 텐서의 정의
- 텐서는 다차원 데이터 배열이며, 행렬보다 더 높은 차원을 가진 데이터 구조이다.
- 텐서 차수(Order of a Tensor):
  1) 스칼라 (scalar) → 0차원 텐서
  2) 벡터 (vector) → 1차원 텐서
  3) 행렬 (matrix) → 2차원 텐서
  4) 3차원 이상의 배열 → 고차원 텐서

2. 텐서 분해의 필요성

- 행렬(Matrix) 분해의 한계: 비고유성(Non-Uniqueness)
  - 대표적인 행렬 분해 기법
    ![image](https://github.com/user-attachments/assets/02e39f14-bbda-464e-99c3-e571fdd294a2)
  - 행렬 분해 기법들은 특정한 행렬을 저차원(latent space) 표현으로 변환하는 데 사용된다.
  - 그러나 행렬 분해는 고유한(Unique) 분해를 보장하지 않는 경우가 많다,
    ex) 행렬 회전 문제. 어떤 행렬 M을 두개의 행렬 A,B로 분해한다고 가정한다. 만약 임의의 가역 행렬 R이 존재한다면 즉 A와 B를 회전(Rotation)하는 새로운 행렬로 바꿔도 동일한 행렬 M을 생성할수있다.
    ![image](https://github.com/user-attachments/assets/0d5fdbda-34b5-48cf-9c51-a3c79e907af9)
    ![image](https://github.com/user-attachments/assets/ded230b7-7cf9-407f-acf4-e084b6b3f9bb)
    따라서, M을 A,B로 분해하는 방법이 유일하지 않다는 문제가 발생한다. 즉, 행렬 분해는 식별성이 떨어지며, 숨겨진 요인을 정확하게 찾기 어렵다.

- 텐서(Tensor) 분해의 강점: 높은 식별성 (Identifiability)
  1. 행렬은 2차원(행 × 열)이지만, 텐서는 3차원 이상이므로 추가적인 구조적 제약을 갖는다.
     이로 인해 텐서의 고유한(Unique) 분해가 가능하고 따라서 더 높은 차원의 정보를 활용 가능하다.
  2. 행렬 분해는 추가적인 제약(orthogonality, positive-definiteness 등)이 없으면 비고유성 문제가 발생하지만, 텐서는 별도의 제약을 추가하지 않아도 고유한 분해가 가능하다.
     ex) CP 분해(Canonical Polyadic Decomposition, CPD): 특정 조건만 만족하면 유일한 분해를 보장함.
     ![image](https://github.com/user-attachments/assets/42ba18df-feba-41c9-861f-bf60f8e60560)
  3. 강한 유일성 조건(Sufficient Uniqueness Conditions)
     - Kruskal Rank 조건
     - CP 분해의 유일성(uniqueness) 조건
       ![image](https://github.com/user-attachments/assets/47d1327d-d734-4a4c-8cc9-05ceb133a052)
     - 여기서 k_A(n)은 각 모드의 크루스칼 랭크. 이 조건이 충족되면 텐서 분해는 유일한 해를 가진다. 

3. 머신러닝에서 식별성이 중요한 이유
- 많은 머신러닝 모델들은 데이터에서 숨겨진 구조(Latent Structure)를 찾는 것이 목표인데, 텐서 분해가 행렬 분해보다 **강력한 식별성**을 가지므로, 머신러닝에서 더 신뢰할 수 있는 모델링이 가능.
- 특히, 혼합 모델(Gaussian Mixture Model, GMM), 주제 모델(Topic Model), 추천 시스템 등에서 텐서 분해는 고유한 요인을 학습하는 데 중요한 역할을 한다.

## 4. 주요 텐서 분해 알고리즘

1. CP (Canonical Polyadic) 분해
- 텐서를 Rank-1 텐서들의 합으로 표현하는 방식.
- ex) 3차원 텐서 X를 다음과 같이 표현 가능.
  ![image](https://github.com/user-attachments/assets/23f49a88-668a-4512-9b74-013b0ca1f3c9)
- ALS (Alternating Least Squares) 알고리즘 사용하여 분해.

2. Tucker 분해
- 텐서를 저차원 핵심 텐서(core tensor)와 인자 행렬(factor matrices)로 표현
- ex) 3차원 텐서 X를 다음과 같이 표현 가능.
  ![image](https://github.com/user-attachments/assets/01ef4a3a-faef-47b1-a1a2-3f441edd2e8a)
- HOSVD (Higher-Order SVD) 및 HOOI (Higher Order Orthogonal Iteration) 알고리즘 사용하여 분해.

### 4-1. CP (Canonical Polyadic) 분해

1. CP 분해?

   - CP (Canonical Polyadic) 분해는 고차원 텐서(Tensor)를 Rank-1 텐서들의 합으로 분해하는 방법.
   - 행렬의 특이값 분해(SVD) 와 유사한 개념이지만, 텐서에서는 더 높은 차원의 구조를 다룰 수 있다.

2. CP 분해의 기본 원리
   - 입력: 

## 5. 텐서 분해의 머신러닝 응용
- 시계열 데이터 분석(Temporal Data Analysis)
  - 텐서를 사용하면 시간축을 추가하여 더 복잡한 구조 분석 가능
  - 예: 추천 시스템(Recommender System), 소셜 네트워크 분석(Social Network Analysis)

- 다중 관계 데이터(Multi-relational Data)
  - 관계형 데이터베이스를 다차원 텐서 형태로 표현하여 분석 가능
  - 예: 지식 그래프(Knowledge Graph), 소셜 네트워크 내 다양한 관계 분석

- 잠재 변수 모델링(Latent Variable Modeling)
  - 숨겨진 요인(hidden factors) 분석 가능
  - Gaussian Mixture Model (GMM) 및 토픽 모델(Topic Model) 의 매개변수 추정에 사용 가능

## 6. 활용 가능한 소프트웨어 라이브러리

- Python: TensorLy, pytensor, scikit-tensor
- Matlab: Tensor Toolbox, Tensorlab
- R: rTensor
