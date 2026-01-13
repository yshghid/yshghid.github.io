---
weight: 16
title: "AI"
bookComments: false
type: docs
bookHidden: false
---

# AI

---

*2026-01-09* ⋯ 데이터 분석 #6 pandas numpy 데이터 처리

[#1 데이터 처리 pandas scipy sklearn pandas로 데이터프레임으로 데이터를 확인하고 scipy와 sklearn에서 통계 패키지와 머신러닝 패키지를 사용한다 numpy python이 제공하는 머신러닝 패키지는 sklearn인데 이를 사용하려면 numpy를 알아야 한다. tensorflow같은 딥러닝 패키지들이 입출력을 위해 numpy를 사용한다 numpy는 ⋯](https://yshghid.github.io/docs/study/ai/ai40/)

---

*2025-09-22* ⋯ AI #2 HPO, XAI 실습

[1. 실습 개요 목적 UCI Breast Cancer 데이터를 로드하고 전처리 후 XGBoost 모델을 구축 및 평가 교차검증(StratifiedKFold, KFold)과 하이퍼파라미터 탐색 기법(RandomizedSearchCV, Optuna)을 비교하여 최적 성능을 도출 SHAP을 활용하여 전역적·집단적·개별적 수준에서 해석력을 확보하고 도메인 지식과 연결 ⋯](https://yshghid.github.io/docs/study/ai/ai38/)

---

*2025-09-19* ⋯ AI #1 ML 방법론 기초

[#1 ML 방법론 통계기반 방법론은? linear regression이나 logistic regression 같은걸 말함 가설과 근거가 명확히 세워져 있고 데이터가 알고리즘에 맞게 정제돼있고 통계적 유의성으로 결과가 나오는 깔끔한 방식 ML 방법론은? 작은 경연을 열듯 시행착오를 거치며 가장 적합한 모델을 찾는다는 컨셉이다. #2 지도 비지도 준지도 모두 입력 데이터 ⋯](https://yshghid.github.io/docs/study/ai/ai36/)
  
---

*2025-09-15* ⋯ Ray #1 (스터디) Batch Prediction with Ray Core

[스터디때 준비해갔던 Ray Core를 사용해서 batch prediction 수행하는 예제!! batch prediction이 batch를 예측하는건줄알았는데(..) batch로 prediction하는것이었다. 순서는 Task 기반, actor 기반 batch prediction, 그리고 GPU 기반 수행 코드이다. 출처는 Ray Document의 Batch Prediction with Ray Core이다. ⋯](https://yshghid.github.io/docs/study/ai/ai34/)

---

*2025-08-22* ⋯ MLflow #2 mlflow 파이프라인

[1. 코드 #1 트래킹 서버 설정 import os import mlflow 1. 로그를 저장할 서버/위치 지정 mlflow.set_tracking_uri(uri=os.getenv("MLFLOW_TRACKING_URI", "")) # MLFLOW_TRACKING_URI로 MLflow 서버를 연결 current_uri = mlflow.get_tracking_uri() print(f"Current Tracking URI ⋯](https://yshghid.github.io/docs/study/ai/ai25/)

---

*2025-08-21* ⋯ MLflow #1 설치 & 실습

[1. mlflow 설치 및 docker 띄우기 $ export CR_PAT=* # *: github token 블라인드 처리 $ echo $CR_PAT | docker login ghcr.io -u yshghid --password-stdin Login Succeeded 로그인햇으면 도커를 켠다음에 다음을 수행. $ docker pull ghcr.io/mlflow/mlflow:v2.0.1 v2.0.1: Pulling from mlflow ⋯](https://yshghid.github.io/docs/study/ai/ai24/)

---

*2025-08-19* ⋯ LLM #2 LLM과 AI 기술요소를 활용하여 비즈니스 서비스 기획안 작성

[1. 목적 등기부등본/건축물대장 업로드 시 AI가 자동으로 문서를 분석하여 전세사기 위험 요소를 탐지하고 수치화한다. 2. 모델 구성도 #1 데이터 수집및 정규화  기술요소: PaddleOCR 선택 이유: 한국어 인식 정확도와 속도가 좋고, 오픈소스+온프레미스 운영 가능(비용·보안 유리), 표 레이아웃/좌표 추출 지원. 입력 파일: PDF/스캔 이미지(JPG/ ⋯](https://yshghid.github.io/docs/study/ai/ai23/)

---

*2025-08-19* ⋯ 데이터분석 #4 리뷰 데이터 분석

[이제야복습하는 저번주실습 1. 목적 리뷰 데이터를 보고 감성 점수와 평점의 관계 리뷰 길이와 감성 점수의 관계 카테고리별 감성 차이 Review_length가 AI 임베딩 유사도에 영향을 줄 수 있는지 인사이트 생성하기. 2. 코드 import os import pandas as pd import numpy as np import seaborn as sns import matplotlib.pyplot ⋯](https://yshghid.github.io/docs/study/ai/ai22/)

---

*2025-08-11* ⋯ LLM #1 LLM 이해와 Transformer

[1. LLM 기본이해 #1 Word Embedding (p.27-28) Word Embedding 핵심 아이디어는 단어가 어떤 맥락에서 자주 함께 등장하는지를 학습. “you say goodbye and I say hello”에서  ‘goodbye’주변에는 ‘you’, ‘say’, ‘and’, ‘I’ 같은 단어가 함께 등장하고 그 관계를 학습하도록 신경망을 훈련시킨다. 학습이 반복되면 각 단어는 ⋯](https://yshghid.github.io/docs/study/ai/ai21/)

---

*2025-08-09* ⋯ 생성형 AI #2 Prompt Engineering 실습 미리돌려보기

[1. VOC 분석 setting https://openrouter.ai/ Model: GPT-5 Temperature: 0.2 (낮게: 일관성 있는 분류 결과) Top-k / Top-p: default Max tokens: 1024 system prompt 너는 IT 시스템의 평가전문가야. 이번에 개발한 AI를 적용한 회계세무 시스템을 테스트한 고객의 평가내용인 VOC를 분석하는 것이 너의 역할이야 ⋯](https://yshghid.github.io/docs/study/ai/ai19/)

---

*2025-08-09* ⋯ 생성형 AI #1 생성형 AI 기초 및 Prompt Engineering 

[#1 RAG (p.27) RAG의 역할? 질문을 LLM에 던지기 전에 knowledge corpus에 질문을 미리 검색한다(회사 데이터에 대한 지식 벡터 db). 질문과 연관된 문서를 찾고 적절하게 만들어서 retrieval 던지면 의도대로 답변이 잘 나온다. #2 LLM 출력 구성 (p.42-45) Output Length (Max Tockens) - 500자로 제한을 걸면 ⋯](https://yshghid.github.io/docs/study/ai/ai18/)

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
