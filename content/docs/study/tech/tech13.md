---
date : 2025-05-29
tags: ['2025-05']
categories: ['DL']
bookHidden: true
title: "TFT PyTorch Forecasting - Stallion 튜토리얼 #2"
---

# TFT PyTorch Forecasting - Stallion 튜토리얼 #2

#2025-05-29

---

#introduction

- 데이터셋: [Kaggle - Stallion 데이터셋](https://www.kaggle.com/datasets/utathya/future-volume-prediction)
- 목적: Temporal Fusion Transformer(TFT)를 활용하여 음료 판매량을 예측


#version check

- 예제 코드에 맞는 패키지 버전
  - CUDA: 11.7
  - PyTorch: 1.13.1+cu117
  - PyTorch Lightning: 1.6.5
  - PyTorch Forecasting: 0.10.3


- PyTorch Forecasting 0.10.3 선택 이유: 최신 버전은 아래 코드랑 호환 안됨
  1. `Tuner().lr_find()` -> 학습률 탐색, lightning>=2.x에서는 내부 콜백 구조 변경됨
  2. `trainer.checkpoint_callback.best_model_path` -> 베스트 모델 로드, trainer.checkpoint_callback 속성 제거됨
  3. `optimizer="ranger"` -> Ranger 옵티마이저 지정, 제거됨
  4. `plot_prediction`, `plot_interpretation`, `plot_dependency` -> 시각화 함수, 제거되거나 구조 변경됨
  5. `optimize_hyperparameters` -> Optuna 기반 튜닝, deprecated 또는 작동 오류







- 여기에 맞게 설치해주기. python=3.8이 가장 호환성 좋다고 함.

```bash
conda create -n tftspace python=3.9 -y

pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1+cu117 -f https://download.pytorch.org/whl/torch_stable.html
pip install pytorch-lightning==1.9.0
pip install pytorch-forecasting==0.10.3

pip install ranger-adabelief #optimizer="ranger" 수행에 필요함
pip install ipykernel #커널등록에 필요함
python -m ipykernel install --user --name tftspace --display-name "tftspace" #커널등록

```

#set package

```python
import copy
from pathlib import Path
import warnings

import lightning.pytorch as pl
from lightning.pytorch.callbacks import EarlyStopping, LearningRateMonitor
from lightning.pytorch.loggers import TensorBoardLogger
import numpy as np
import pandas as pd
import torch

from pytorch_forecasting import Baseline, TemporalFusionTransformer, TimeSeriesDataSet
from pytorch_forecasting.data import GroupNormalizer
from pytorch_forecasting.metrics import MAE, SMAPE, PoissonLoss, QuantileLoss
from pytorch_forecasting.models.temporal_fusion_transformer.tuning import (
    optimize_hyperparameters,
)
```


