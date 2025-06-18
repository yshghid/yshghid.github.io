---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#4 모델 학습"
bookComments: true
---

# #4 모델 학습

#2025-06-18

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

### 2. Check version

```python
import pytorch_forecasting
import torch
import pytorch_lightning as pl

print("PyTorch Forecasting:", pytorch_forecasting.__version__)
print("PyTorch:", torch.__version__)
print("PyTorch Lightning:", pl.__version__)
```
```plain text
PyTorch Forecasting: 0.10.1
PyTorch: 1.13.1+cu117
PyTorch Lightning: 1.6.5
```


### 3. Load data

