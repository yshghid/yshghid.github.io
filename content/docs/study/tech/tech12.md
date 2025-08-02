---
date : 2025-05-28
tags: ['2025-05']
categories: ['DL']
bookHidden: true
title: "TFT PyTorch Forecasting - Stallion 튜토리얼"
---

# TFT PyTorch Forecasting - Stallion 튜토리얼

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
$ pip install pyarrow
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

#load data

```python
from pytorch_forecasting.data.examples import get_stallion_data

data = get_stallion_data()

# add time index
data["time_idx"] = data["date"].dt.year * 12 + data["date"].dt.month
data["time_idx"] -= data["time_idx"].min()

# add additional features
data["month"] = data.date.dt.month.astype(str).astype(
    "category"
)  # categories have be strings
data["log_volume"] = np.log(data.volume + 1e-8)
data["avg_volume_by_sku"] = data.groupby(
    ["time_idx", "sku"], observed=True
).volume.transform("mean")
data["avg_volume_by_agency"] = data.groupby(
    ["time_idx", "agency"], observed=True
).volume.transform("mean")

# we want to encode special days as one variable and thus need to first reverse one-hot encoding
special_days = [
    "easter_day",
    "good_friday",
    "new_year",
    "christmas",
    "labor_day",
    "independence_day",
    "revolution_day_memorial",
    "regional_games",
    "fifa_u_17_world_cup",
    "football_gold_cup",
    "beer_capital",
    "music_fest",
]
data[special_days] = (
    data[special_days].apply(lambda x: x.map({0: "-", 1: x.name})).astype("category")
)
data.sample(10, random_state=521)
```

![image](https://github.com/user-attachments/assets/e27cc10a-d51a-4182-9324-04f009ffb41b)

```python
data.describe()
```

![image](https://github.com/user-attachments/assets/fbffee77-5b44-4004-8f9b-0d3d3698a439)

#Create dataset and dataloaders

```python
max_prediction_length = 6
max_encoder_length = 24
training_cutoff = data["time_idx"].max() - max_prediction_length

training = TimeSeriesDataSet(
    data[lambda x: x.time_idx <= training_cutoff],
    time_idx="time_idx",
    target="volume",
    group_ids=["agency", "sku"],
    min_encoder_length=max_encoder_length
    // 2,  # keep encoder length long (as it is in the validation set)
    max_encoder_length=max_encoder_length,
    min_prediction_length=1,
    max_prediction_length=max_prediction_length,
    static_categoricals=["agency", "sku"],
    static_reals=["avg_population_2017", "avg_yearly_household_income_2017"],
    time_varying_known_categoricals=["special_days", "month"],
    variable_groups={
        "special_days": special_days
    },  # group of categorical variables can be treated as one variable
    time_varying_known_reals=["time_idx", "price_regular", "discount_in_percent"],
    time_varying_unknown_categoricals=[],
    time_varying_unknown_reals=[
        "volume",
        "log_volume",
        "industry_volume",
        "soda_volume",
        "avg_max_temp",
        "avg_volume_by_agency",
        "avg_volume_by_sku",
    ],
    target_normalizer=GroupNormalizer(
        groups=["agency", "sku"], transformation="softplus"
    ),  # use softplus and normalize by group
    add_relative_time_idx=True,
    add_target_scales=True,
    add_encoder_length=True,
)

# create validation set (predict=True) which means to predict the last max_prediction_length points in time
# for each series
validation = TimeSeriesDataSet.from_dataset(
    training, data, predict=True, stop_randomization=True
)

# create dataloaders for model
batch_size = 128  # set this between 32 to 128
train_dataloader = training.to_dataloader(
    train=True, batch_size=batch_size, num_workers=0
)
val_dataloader = validation.to_dataloader(
    train=False, batch_size=batch_size * 10, num_workers=0
)
```

#Create baseline model

```python
# calculate baseline mean absolute error, i.e. predict next value as the last available value from the history
baseline_predictions = Baseline().predict(val_dataloader, return_y=True)
MAE()(baseline_predictions.output, baseline_predictions.y)
```
```plain text
---------------------------------------------------------------------------
TypeError                                 Traceback (most recent call last)
/tmp/ipykernel_1239848/2174382858.py in <module>
      1 # calculate baseline mean absolute error, i.e. predict next value as the last available value from the history
----> 2 baseline_predictions = Baseline().predict(val_dataloader, return_y=True)
      3 MAE()(baseline_predictions.output, baseline_predictions.y)

~/miniconda3/envs/workspace/lib/python3.7/site-packages/pytorch_forecasting/models/base_model.py in predict(self, data, mode, return_index, return_decoder_lengths, batch_size, num_workers, fast_dev_run, show_progress_bar, return_x, mode_kwargs, **kwargs)
   1157 
   1158                 # make prediction
-> 1159                 out = self(x, **kwargs)  # raw output is dictionary
   1160 
   1161                 lengths = x["decoder_lengths"]

~/miniconda3/envs/workspace/lib/python3.7/site-packages/torch/nn/modules/module.py in _call_impl(self, *input, **kwargs)
   1192         if not (self._backward_hooks or self._forward_hooks or self._forward_pre_hooks or _global_backward_hooks
   1193                 or _global_forward_hooks or _global_forward_pre_hooks):
-> 1194             return forward_call(*input, **kwargs)
   1195         # Do not call functions when jit is used
   1196         full_backward_hooks, non_full_backward_hooks = [], []

TypeError: forward() got an unexpected keyword argument 'return_y'
```
- 왜인지 모르겠지만 `baseline_predictions = Baseline().predict(val_dataloader, return_y=True)`에서 return_y라는 인자는 안받는다고함.
  - return_y=True를 빼고 `Baseline().predict(val_dataloader)`만 사용하면 예측값(prediction)만 반환되고 실제값(y)은 반환되지 않음
  - return_y=True 결과를 얻으려면 즉 MAE를 계산하려면 직접 val_dataloader에서 y 값을 꺼내도록 코드 수정

```python
from pytorch_forecasting.metrics import MAE
from pytorch_forecasting.models.baseline import Baseline

baseline_model = Baseline()
y_pred = baseline_model.predict(val_dataloader)
y_true = torch.cat([batch[0]["decoder_target"] for batch in val_dataloader])

mae = MAE()(y_pred, y_true)
mae
```
```plain text
tensor(293.0088)
```

#Train the Temporal Fusion Transformer

##Find optimal learning rate

```python
# configure network and trainer
pl.seed_everything(42)
trainer = pl.Trainer(
    accelerator="cpu",
    # clipping gradients is a hyperparameter and important to prevent divergance
    # of the gradient for recurrent neural networks
    gradient_clip_val=0.1,
)


tft = TemporalFusionTransformer.from_dataset(
    training,
    # not meaningful for finding the learning rate but otherwise very important
    learning_rate=0.03,
    hidden_size=8,  # most important hyperparameter apart from learning rate
    # number of attention heads. Set to up to 4 for large datasets
    attention_head_size=1,
    dropout=0.1,  # between 0.1 and 0.3 are good values
    hidden_continuous_size=8,  # set to <= hidden_size
    loss=QuantileLoss(),
    optimizer="ranger",
    # reduce learning rate if no improvement in validation loss after x epochs
    # reduce_on_plateau_patience=1000,
)
print(f"Number of parameters in network: {tft.size() / 1e3:.1f}k")
```
```plain text
Global seed set to 42
GPU available: True (cuda), used: False
TPU available: False, using: 0 TPU cores
IPU available: False, using: 0 IPUs
HPU available: False, using: 0 HPUs
Number of parameters in network: 13.5k
```
```python
# find optimal learning rate
from lightning.pytorch.tuner import Tuner

res = Tuner(trainer).lr_find(
    tft,
    train_dataloaders=train_dataloader,
    val_dataloaders=val_dataloader,
    max_lr=10.0,
    min_lr=1e-6,
)

print(f"suggested learning rate: {res.suggestion()}")
fig = res.plot(show=True, suggest=True)
fig.show()
```
```plain text
---------------------------------------------------------------------------
ImportError                               Traceback (most recent call last)
/tmp/ipykernel_1239848/4268711780.py in <module>
      1 # find optimal learning rate
----> 2 from lightning.pytorch.tuner import Tuner
      3 
      4 res = Tuner(trainer).lr_find(
      5     tft,

ImportError: cannot import name 'Tuner' from 'lightning.pytorch.tuner' (/home/ysh980101/miniconda3/envs/workspace/lib/python3.7/site-packages/lightning/pytorch/tuner/__init__.py)
```

- `pip show lightning`으로 확인 결과 lightning 버전이 1.9.5이고 lightning.pytorch 패키지 구조가 도입되기 전 버전이어서 오류가 났다
- import를 수정해주고 import한거에 맞춰서 코드도 수정
  - lr_finder 종료 후 자동 복원, 학습률 자동 업데이트 <<를 충족하도록 수정했다. 

```python
# find optimal learning rate
from pytorch_lightning import Trainer
from pytorch_lightning.tuner.tuning import Tuner

tuner = Tuner(trainer)

lr_finder = tuner.lr_find(
    tft,
    train_dataloaders=train_dataloader,
    val_dataloaders=val_dataloader,
    min_lr=1e-6,
    max_lr=10.0,
    update_attr=False
)
```

- 왜인지 모르겟는데 수정한 코드에서는 'ranger'가 안먹어서, 학습률 선택은 다른 optimizer로 하고 선택한 학습률을 ranger optimizer 쓰는 원래 모델에 적용시키는 아래 코드를 챗지피티가 추천해줬다

```python
from pytorch_forecasting.models.temporal_fusion_transformer.tuning import optimize_hyperparameters
from pytorch_forecasting import TemporalFusionTransformer

# Ranger 대신 Adam 사용 (lr 찾기 전용)
tft_tmp = TemporalFusionTransformer.from_dataset(
    training,
    learning_rate=0.03,
    hidden_size=8,
    attention_head_size=1,
    dropout=0.1,
    hidden_continuous_size=8,
    loss=QuantileLoss(),
    optimizer="adam",
)

# lr_find()
tuner = Tuner(trainer)
lr_finder = tuner.lr_find(
    tft_tmp,
    train_dataloaders=train_dataloader,
    val_dataloaders=val_dataloader,
    min_lr=1e-6,
    max_lr=10.0,
)

suggested_lr = lr_finder.suggestion()
print(f"Suggested LR: {suggested_lr}")

fig = lr_finder.plot(suggest=True)
fig.show()
```
```plain text
Suggested LR: 0.0019498445997580445
```

![image](https://github.com/user-attachments/assets/7a9f568a-21a1-4b23-9617-169c02f79c66)

```python
tft.hparams.learning_rate = suggested_lr #ranger 옵티마이저를 사용하는 원래 tft 모델에 이 학습률을 적용
```

근데 이게 맞나.. 싶어서 버전 맞춰서 다시해볼예정.

> 코드: https://pytorch-forecasting.readthedocs.io/en/latest/tutorials/stallion.html
