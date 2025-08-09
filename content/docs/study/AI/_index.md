---
weight: 16
title: "AI"
bookComments: false
type: docs
bookHidden: false
---

# AI

#ML #DL #Statistics #Langchain

---


*2025-08-07* ⋯ 데이터 분석 #3 회귀분석

[#1 Oversampling Techinique (p.69-71) SMOTE 소수 클래스 포인트 중 하나를 랜덤하게 고르고 이웃 포인트 k개를 찾고 이 이웃들과의 연결선을 따라 중간 어딘가에 새로운 샘플을 만든다. 즉 원본과 이웃 사이에 위치한 점들을 생성한다. SMOTE는 소수 클래스 포인트들 사이의 직선 위에서만 새로운 데이터를 만들기 때문에 실제로는 decision ⋯](https://yshghid.github.io/docs/study/ai/ai17/)

---

*2025-08-06* ⋯ 데이터 분석 #2 Preprocessing

[#1 머신러닝 프로세스 (p.25) test data가 필요한 이유? hyperparameter tuning을 하면서 validation data는 모델이 이미 참고했다 즉 간접적으로 학습에 영향을 줬기 때문에 모델 학습 과정에서 한번도 보지않은 데이터가 필요함. #2 Box plot (p.38) 그림이 7개 차종에서 연비 플롯이라고 가정 투입됏을때 예측에 긍정적영향을 줄수잇는건? ⋯](https://yshghid.github.io/docs/study/ai/ai16/)

---


*2025-08-05* ⋯ 데이터 분석 #1 기초통계

[1. 기술 통계 #1 IQR? 가운데 50%의 거리. #2 IQR 그림 설명 (p.34) 그림의 2,3: 각각 IQR의 1.5배 선, median 값 선. 그림의 B: ⚬ 가 많으면 특이값이 많은 것. 그림의 1,2,3: 1,2는 각각 IQR의 1.5배 선이라고 했는데 3과의 거리가 서로 다른 이유는? 1.5배 안쪽에 데이터들이 다 분포해서. 즉max가 1.5배보다 작아서. #3 변이 계수(Coefficient of ⋯](https://yshghid.github.io/docs/study/ai/ai14/)


---

*2025-08-04* ⋯ RF-SHAP #2 SHAP 분석

[1. Load data import pandas as pd import numpy as np import pickle import joblib import shap import matplotlib.pyplot as plt import seaborn as sns #Load rf model with open('/model/rf_model.pkl','rb') as f:    rf_model = joblib.load(f) #Load dataset with open('/preprocess ⋯](https://yshghid.github.io/docs/study/ai/ai13/)

---

*2025-08-04* ⋯ RF-SHAP #1 모델 학습

[1. Load data import pandas as pd import numpy as np from sklearn.ensemble import RandomForestClassifier from sklearn.model_selection import train_test_split, cross_val_score from sklearn.metrics import accuracy_score import pickle with open('/preprocessing/processed ⋯](https://yshghid.github.io/docs/study/ai/ai12/)

---

*2025-07-28* ⋯ DBSCAN #2 슈도코드

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D For each point x in D: If label[x] ≠ UNVISITED: continue ⋯](https://yshghid.github.io/docs/study/ai/ai9/)

---

*2025-07-28* ⋯ DBSCAN: #1 1D 클러스터링의 성능 평가

[1. Problem 클러스터 응집도는 보통 클러스터 내 데이터 간의 평균 거리나 분산, 혹은 실루엣 계수처럼 군집 내 응집도와 군집 간 분리도를 동시에 평가한다. 하지만 1차원 데이터에서는 클러스터 응집도(Cluster Cohesion) 또는 실루엣 계수(Silhouette coefficient) 같은 지표가 잘 작동하지 않는다. 2. 클러스터 응집도 클러스터링 성능을 평가 ⋯](https://yshghid.github.io/docs/study/ai/ai8/)

---

*2025-07-23* ⋯ TFT #3 모델 학습

[1. Load package import pytorch_lightning as pl from pytorch_lightning.callbacks import EarlyStopping, LearningRateMonitor from pytorch_lightning.loggers import TensorBoardLogger from pytorch_forecasting import TimeSeriesDataSet from pytorch_forecasting.models ⋯](https://yshghid.github.io/docs/study/ai/ai7/)

---

*2025-07-23* ⋯ TFT #2 입력 feature 생성

[1. Load package %load_ext autoreload %autoreload 2  import sys import pandas as pd import numpy as np import os import pickle import ast  sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin') from sc import * os.chdir('/data3/projects/2025_Antibiotics/YSH/workspace') 2. Make feature1 ⋯](https://yshghid.github.io/docs/study/ai/ai6/)

---

*2025-07-23* ⋯ TFT #1 입력 시퀀스 생성

[1. Load package %load_ext autoreload %autoreload 2 import sys import pandas as pd import numpy as np import os import pickle import ast sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin') from sc import * os.chdir('/data3/projects/2025_Antibiotics/YSH/workspace') ⋯](https://yshghid.github.io/docs/study/ai/ai5/)


---

*2025-07-23* ⋯ TFT #0 연구 방향

[0. 연구 개요 목적: 항생제 종류에 따라 NEWS score를 예측 모델: Temporal Fusion Transformer(TFT)  1. 데이터 구성 및 TFT 입력 형식 1. 데이터 종류 Clinical feature (17개, float): Creatinine, Hemoglobin, LDH, Lymphocytes, Neutrophils, Platelet count, WBC count, hs-CRP, D-Dimer, BDTEMP, BREATH, ⋯](https://yshghid.github.io/docs/study/ai/ai4/)

---

*2025-05-29* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼 #2

[#version check 예제 코드에 맞는 패키지 버전 CUDA: 11.7 PyTorch: 1.13.1+cu117 PyTorch Lightning: 1.9.0 PyTorch Forecasting: 0.10.3 PyTorch Forecasting 0.10.3 선택 이유: 최신 버전은 아래 코드랑 호환 안됨 1. Tuner().lr_find() -> 학습률 탐색, lightning>=2.x에서는 내부 콜백 구조 변경됨 2. trainer ⋯](https://yshghid.github.io/docs/study/tech/tech13/)

---

*2025-05-28* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼

[#introduction 데이터셋: Kaggle - Stallion 데이터셋 목적: Temporal Fusion Transformer(TFT)를 활용하여 음료 판매량을 예측 #install $ nvidia-smi Wed May 28 14:00:07 2025 +---------------------------------------------------------------------------------------+ | NVIDIA-SMI 545.23.08 Driver Version ⋯](https://yshghid.github.io/docs/study/tech/tech12/) 

#
