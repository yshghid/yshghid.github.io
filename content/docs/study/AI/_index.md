---
weight: 17
title: "AI"
bookComments: false
type: docs
bookHidden: false
---

# AI


*2025-07-28* ⋯ DBSCAN #3 MutClust 슈도코드

[1 Input: - D: 데이터 포인트 집합 - eps: 이웃 거리 임계값 - minPts: 최소 이웃 수 (밀도 기준) Output: - cluster_labels: 각 데이터 포인트에 대한 클러스터 라벨 (노이즈는 -1) Initialize: - cluster_id ← 0 - label[x] ← UNVISITED for all x in D 데이터 집합 D, 파라미터 eps와 minPts가 들어간다. 2 ⋯](https://yshghid.github.io/docs/study/ai/ai10/)

---

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

*2025-07-19* ⋯ RAG #3 자동 대화 이력 관리

[1. 자동 대화 이력 관리 이 코드는 LangChain을 활용하여 대화형 금융 상담 시스템을 구성한다. 핵심적으로는 LLM과 사용자 간의 대화를 보다 ‘인간답게’ 이어가기 위한 다양한 전략들—예를 들면 이전 대화 내용을 기억하고 반영하기, 오래된 메시지를 요약하거나 잘라내기, 메시지를 세션 단위로 관리하기—를 구현하는 과정을 담고 있다. 이 시스템은 GPT 모델이라는 뇌에다가 ‘기억력’과 ‘요약 능력’을 ⋯](https://yshghid.github.io/docs/study/ai/ai3/)

---

*2025-07-19* ⋯ RAG #2 출력 파서의 개념, Pydantic/Json 출력 파서

[1. 출력 파서의 개념과 종류 그리고 세가지 주요 메서드 출력 파서(output parser)는 LLM에서 생성된 응답을 받아서 우리가 원하는 형식으로 변환해주는 역할을 한다. 쉽게 말해, LLM은 텍스트만 생성하지만 우리는 그 텍스트를 리스트, 딕셔너리, JSON, 숫자 등 구조화된 데이터로 바꾸어서 프로그램에 넘기거나, 다음 단계 체인으로 활용하길 원할 때가 많다. 출력 파서는 이 연결고리 역할을 한다. 출력 파서는 ⋯](https://yshghid.github.io/docs/study/ai/ai2/)

---

*2025-07-17* ⋯ RAG #1 랭체인, LCEL, 프롬프트

[1. 랭체인 생태계의 주요 패키지 랭체인(LangChain)은 LLM(Large Language Model)을 활용한 애플리케이션을 쉽게 만들 수 있도록 돕는 프레임워크이다. 이 생태계는 단일 라이브러리로 구성된 것이 아니라 여러 개의 하위 패키지로 나뉘어 있고, 각각의 역할이 명확하게 분리되어 있다. 랭체인의 주요 목적은 LLM을 단순한 텍스트 생성 도구가 아니라, 여러 시스템과 결합하여 유의미한 작업을 수행하는 ⋯](https://yshghid.github.io/docs/study/ai/ai1/)

---

*2025-05-29* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼 #2

[#version check 예제 코드에 맞는 패키지 버전 CUDA: 11.7 PyTorch: 1.13.1+cu117 PyTorch Lightning: 1.9.0 PyTorch Forecasting: 0.10.3 PyTorch Forecasting 0.10.3 선택 이유: 최신 버전은 아래 코드랑 호환 안됨 1. Tuner().lr_find() -> 학습률 탐색, lightning>=2.x에서는 내부 콜백 구조 변경됨 2. trainer.checkpoint_callback.best_model_path ⋯](https://yshghid.github.io/docs/study/tech/tech13/)

---

*2025-05-28* ⋯ TFT PyTorch Forecasting - Stallion 튜토리얼

[#introduction 데이터셋: Kaggle - Stallion 데이터셋 목적: Temporal Fusion Transformer(TFT)를 활용하여 음료 판매량을 예측 #install $ nvidia-smi Wed May 28 14:00:07 2025 +---------------------------------------------------------------------------------------+ | NVIDIA-SMI 545.23.08 Driver Version: 545.23.08 CUDA Version ⋯](https://yshghid.github.io/docs/study/tech/tech12/) 

#
