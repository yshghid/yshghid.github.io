---
weight: 16
title: "AI"
bookComments: false
type: docs
bookHidden: false
---

# AI


*2025-07-28* ⋯ DBSCAN #2 슈도코드

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D For each point x in D: If label[x] ≠ UNVISITED: continue ⋯](https://yshghid.github.io/docs/study/ai/ai9/)

---

*2025-07-28* ⋯ DBSCAN: #1 1D 클러스터링의 성능 평가

[1. Problem 클러스터 응집도는 보통 클러스터 내 데이터 간의 평균 거리나 분산, 혹은 실루엣 계수처럼 군집 내 응집도와 군집 간 분리도를 동시에 평가한다. 하지만 1차원 데이터에서는 클러스터 응집도(Cluster Cohesion) 또는 실루엣 계수(Silhouette coefficient) 같은 지표가 잘 작동하지 않는다. 2. 클러스터 응집도 클러스터링 성능을 평가하는 지표 중 하나인 응집도 ⋯](https://yshghid.github.io/docs/study/ai/ai8/)

---

*2025-07-23* ⋯ [TFT #3 모델 학습](https://yshghid.github.io/docs/study/ai/ai7/)

#

---

*2025-07-23* ⋯ [TFT #2 입력 feature 생성](https://yshghid.github.io/docs/study/ai/ai6/)

#

---

*2025-07-23* ⋯ [TFT #1 입력 시퀀스 생성](https://yshghid.github.io/docs/study/ai/ai5/)

# 
---

*2025-07-23* ⋯ TFT #0 연구 방향

[0. 연구 개요 목적: 항생제 종류에 따라 NEWS score를 예측 모델: Temporal Fusion Transformer(TFT)  1. 데이터 구성 및 TFT 입력 형식 1. 데이터 종류 Clinical feature (17개, float): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, DBP, SBP, ⋯](https://yshghid.github.io/docs/study/ai/ai4/)

---

*2025-05-29* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼 #2

[#version check 예제 코드에 맞는 패키지 버전 CUDA: 11.7 PyTorch: 1.13.1+cu117 PyTorch Lightning: 1.9.0 PyTorch Forecasting: 0.10.3 PyTorch Forecasting 0.10.3 선택 이유: 최신 버전은 아래 코드랑 호환 안됨 1. Tuner().lr_find() -> 학습률 탐색, lightning>=2.x에서는 내부 콜백 구조 변경됨 2. trainer.checkpoint_callback.best_model_path ⋯](https://yshghid.github.io/docs/study/tech/tech13/)

---

*2025-05-28* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼

[#introduction 데이터셋: Kaggle - Stallion 데이터셋 목적: Temporal Fusion Transformer(TFT)를 활용하여 음료 판매량을 예측 #install $ nvidia-smi Wed May 28 14:00:07 2025 +---------------------------------------------------------------------------------------+ | NVIDIA-SMI 545.23.08 Driver Version: 545.23.08 CUDA Version ⋯](https://yshghid.github.io/docs/study/tech/tech12/) 

#
