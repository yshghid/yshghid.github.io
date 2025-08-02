---
date : 2025-06-23
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#3 모델 구축"
bookComments: true
---

# #3 모델 구축

#2025-06-23

---

### 1. Load package

```python
import warnings

warnings.filterwarnings("ignore")

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
```python
import pytorch_forecasting
import torch
import pytorch_lightning as pl

print("PyTorch Forecasting:", pytorch_forecasting.__version__)
print("PyTorch:", torch.__version__)
print("PyTorch Lightning:", pl.__version__)
```
```plain text
PyTorch Forecasting: 0.10.2
PyTorch: 1.13.1+cu117
PyTorch Lightning: 1.6.5
```
pytorch 및 관련 패키지 버전.


### 2. Load data

```python
os.chdir('/data3/projects/2025_Antibiotics/YSH/')

meddir = 'res'
seqdir = 'data/final_dict'
with open(f"{meddir}/all_meds.txt", 'r') as f:
    all_meds = [line.strip().replace("/", "_") for line in f if line.strip()]

all_df = []

for med in tqdm(all_meds):
    try:
        with open(f"{seqdir}/{med}.pkl", 'rb') as f:
            sequences = pickle.load(f)
    except Exception as e:
        print(f"[ERROR] {med}: {e}")
        continue

    for pid, df in sequences.items():
        df = df.copy()
        df["pid"] = pid
        df["med"] = med
        df["time_idx"] = range(len(df))
        all_df.append(df)

total_sequences = pd.concat(all_df).reset_index(drop=True)
```
```plain text
100%|██████████| 169/169 [00:14<00:00, 11.86it/s]
```

