---
date : 2025-05-28
tags: ['2025-05']
categories: ['DL']
bookHidden: true
title: "TFT - PyTorch Forecasting Stallion 튜토리얼"
---

# TFT - PyTorch Forecasting Stallion 튜토리얼

#2025-05-28

---

#introduction

- 데이터셋: [Kaggle - Stallion 데이터셋](https://www.kaggle.com/datasets/utathya/future-volume-prediction)
- 목적: Temporal Fusion Transformer(TFT)를 활용하여 음료 판매량을 예측


#install

```bash
$ nvidia-smi
```
```plain text
Wed May 28 14:00:07 2025       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 545.23.08              Driver Version: 545.23.08    CUDA Version: 12.3     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA RTX A6000               Off | 00000000:3B:00.0 Off |                  Off |
| 30%   59C    P2             204W / 300W |   8339MiB / 49140MiB |     95%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   1  NVIDIA RTX A6000               Off | 00000000:5E:00.0 Off |                  Off |
| 30%   60C    P2             213W / 300W |   6897MiB / 49140MiB |     94%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   2  NVIDIA RTX A6000               Off | 00000000:B1:00.0 Off |                  Off |
| 30%   60C    P2             203W / 300W |   6799MiB / 49140MiB |     94%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
|   3  NVIDIA RTX A6000               Off | 00000000:D9:00.0 Off |                  Off |
| 32%   63C    P2             212W / 300W |   6885MiB / 49140MiB |     96%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A     20199      C   ...dg/miniconda3/envs/woodg/bin/python      664MiB |
|    0   N/A  N/A    860801      C   ...jyj/miniconda3/envs/TiCC/bin/python      338MiB |
|    0   N/A  N/A   1201205      C   ...u1098/anaconda3/envs/dna/bin/python     6198MiB |
|    0   N/A  N/A   1216286      C   ...jyj/miniconda3/envs/TiCC/bin/python      338MiB |
|    0   N/A  N/A   1225349      C   python                                      782MiB |
|    1   N/A  N/A   1201206      C   ...u1098/anaconda3/envs/dna/bin/python     6104MiB |
|    1   N/A  N/A   1224607      C   python                                      782MiB |
|    2   N/A  N/A   1201207      C   ...u1098/anaconda3/envs/dna/bin/python     6006MiB |
|    2   N/A  N/A   1224848      C   python                                      782MiB |
|    3   N/A  N/A   1201208      C   ...u1098/anaconda3/envs/dna/bin/python     6092MiB |
|    3   N/A  N/A   1225121      C   python                                      782MiB |
+---------------------------------------------------------------------------------------+
```

- NVIDIA 드라이버 버전: 545.23.08
- CUDA 버전: 12.3

- PyTorch 및 관련 패키지를 설치할 때 CUDA 12.3을 지원하는 버전으로 맞춰야 GPU 사용이 가능.
  - CUDA 12.3을 그대로 쓰는 경우 PyTorch GPU 버전과의 호환성이 낮거나 불안정할 수 있어 CUDA 11.7로  설치해준다

```bash
$ pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1 -f https://download.pytorch.org/whl/torch_stable.html
```
```bash
Looking in links: https://download.pytorch.org/whl/torch_stable.html
Collecting torch==1.13.1+cu117
  Downloading https://download.pytorch.org/whl/cu117/torch-1.13.1%2Bcu117-cp37-cp37m-linux_x86_64.whl (1801.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.8/1.8 GB 1.5 MB/s eta 0:00:00
Collecting torchvision==0.14.1+cu117
  Downloading https://download.pytorch.org/whl/cu117/torchvision-0.14.1%2Bcu117-cp37-cp37m-linux_x86_64.whl (24.3 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 24.3/24.3 MB 42.5 MB/s eta 0:00:00
Collecting torchaudio==0.13.1
  Downloading https://download.pytorch.org/whl/rocm5.2/torchaudio-0.13.1%2Brocm5.2-cp37-cp37m-linux_x86_64.whl (3.9 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 3.9/3.9 MB 60.0 MB/s eta 0:00:00
Requirement already satisfied: typing-extensions in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from torch==1.13.1+cu117) (4.7.1)
Requirement already satisfied: numpy in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from torchvision==0.14.1+cu117) (1.21.6)
Requirement already satisfied: requests in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from torchvision==0.14.1+cu117) (2.31.0)
Requirement already satisfied: pillow!=8.3.*,>=5.3.0 in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from torchvision==0.14.1+cu117) (9.5.0)
Requirement already satisfied: charset-normalizer<4,>=2 in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from requests->torchvision==0.14.1+cu117) (3.3.2)
Requirement already satisfied: certifi>=2017.4.17 in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from requests->torchvision==0.14.1+cu117) (2022.12.7)
Requirement already satisfied: urllib3<3,>=1.21.1 in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from requests->torchvision==0.14.1+cu117) (1.26.20)
Requirement already satisfied: idna<4,>=2.5 in /home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages (from requests->torchvision==0.14.1+cu117) (3.7)
Installing collected packages: torch, torchvision, torchaudio
  Attempting uninstall: torch
    Found existing installation: torch 1.13.1
    Uninstalling torch-1.13.1:
      Successfully uninstalled torch-1.13.1
Successfully installed torch-1.13.1+cu117 torchaudio-0.13.1+rocm5.2 torchvision-0.14.1+cu117
```

정상 설치 여부 확인

```bash
python -c "import torch; print(torch.__version__); print(torch.cuda.is_available()); print(torch.cuda.get_device_name(0) if torch.cuda.is_available() else 'No GPU')"
```
```bash
1.13.1+cu117
True
NVIDIA RTX A6000
```

문제없이 설치되었다!

#load package

```bash
$ pip install lightning
$ pip install pytorch-forecasting
```

```python
import warnings

warnings.filterwarnings("ignore")  # avoid printing out absolute paths
```
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



> 코드: https://pytorch-forecasting.readthedocs.io/en/latest/tutorials/stallion.html
