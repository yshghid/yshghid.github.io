---
date : 2025-07-23
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "TFT #3 모델 학습"
---

# TFT #3 모델 학습

#2025-07-23

---


### 1. Load package

```python
import pytorch_lightning as pl
from pytorch_lightning.callbacks import EarlyStopping, LearningRateMonitor
from pytorch_lightning.loggers import TensorBoardLogger

from pytorch_forecasting import TimeSeriesDataSet
from pytorch_forecasting.models import TemporalFusionTransformer
from pytorch_forecasting.models.baseline import Baseline

from pytorch_forecasting.metrics import QuantileLoss
from pytorch_forecasting.metrics import MAE
from pytorch_forecasting.data import GroupNormalizer, NaNLabelEncoder

import numpy as np
import pandas as pd
import torch
import pickle
import matplotlib.pyplot as plt
```

#data

```plain text
/data
└── Sequence.pkl
```

### 2. Load data

```python
sequence = pd.read_pickle("/data/Sequence.pkl")
sequence
```
![image](https://github.com/user-attachments/assets/2dc80ef0-4da5-4d72-bf2d-4d7087b38b64)

### 3. 

```python
# 예측 대상
target_variable = "NEWS"

# 시계열 길이
max_encoder_length = 7
max_prediction_length = 3
context_length = max_encoder_length + max_prediction_length

# 수치형 변수 목록 (merged_df 기준, 특수문자 제거된 이름 사용)
numeric_features = [
    'WHO', 'SOFA', 'PBS', 'qPitt',
    'ALT_U_L', 'AST_U_L', 'BUN_mg_dL', 'Creatinine_mg_dL', 'd_Dimer_ug_ml_FEU',
    'Ferritin_ng_mL', 'HCO3_mmol_L', 'Hemoglobin_g_dL', 'LDH_U_L',
    'Lymphocytes_pct', 'MDRD_eGFR_mL_min_BSA', 'Seg_neutrophils_pct',
    'O2_Saturation_pct', 'PCO2_mmHg', 'PO2_mmHg', 'Platelet_count_10^3_uL',
    'Potassium_mmol_L', 'Sodium_mmol_L', 'WBC_Count_10＾3_uL', 'CRP_mg_dL',
    'pH_', 'total_CO2_mmol_L', 'med_cnt'
]

# 범주형 변수
categorical_features = ["pid", "med"]

# 타입 정리
sequence["time_idx"] = sequence["time_idx"].astype(int)
sequence["pid"] = sequence["pid"].astype(str)
sequence["med"] = sequence["med"].astype(str)
sequence["group_id"] = sequence["group_id"].astype(str)

# 결측치 제거
sequence = sequence.dropna(subset=[target_variable, "time_idx", "pid"]).reset_index(drop=True)

# 유효한 group_id만 필터링 (10개 이상만 통과)
valid_groups = sequence.groupby("group_id")["time_idx"].count()
valid_groups = valid_groups[valid_groups >= context_length].index
filtered_df = sequence[sequence["group_id"].isin(valid_groups)].copy()

# TimeSeriesDataSet 정의
ts_dataset = TimeSeriesDataSet(
    data=filtered_df,
    time_idx="time_idx",
    target="NEWS",
    group_ids=["group_id"],

    max_encoder_length=7,
    max_prediction_length=3,

    static_categoricals=["pid"],
    time_varying_known_categoricals=["med"],
    time_varying_known_reals=["time_idx", "effective_med"],
    time_varying_unknown_reals=numeric_features,

    target_normalizer=GroupNormalizer(groups=["group_id"]),
    categorical_encoders={"med": NaNLabelEncoder(add_nan=True)},

    add_relative_time_idx=True,
    add_target_scales=True,
    add_encoder_length=True,

    allow_missing_timesteps=True,
)

# 검증용 데이터셋 (predict=True)
validation = TimeSeriesDataSet.from_dataset(
    ts_dataset, merged_df, predict=True, stop_randomization=True
)

# DataLoader 생성
batch_size = 128
train_dataloader = ts_dataset.to_dataloader(train=True, batch_size=batch_size, num_workers=0)
val_dataloader = validation.to_dataloader(train=False, batch_size=batch_size * 10, num_workers=0)

# Baseline 예측
baseline_model = Baseline()
y_pred = baseline_model.predict(val_dataloader)  # y는 별도로 저장되지 않음

# 실제 정답 y 추출 (val_dataloader에서 수동으로 추출해야 함)
actuals = torch.cat([y[0] for x, y in iter(val_dataloader)])  # y[0] = target 값

# MAE 계산
mae_score = MAE()(y_pred, actuals)
print(f"Baseline MAE: {mae_score:.4f}")
```
```plain text
Baseline MAE: 1.2169
```
```python
# configure network and trainer
pl.seed_everything(42)
trainer = pl.Trainer(
    accelerator="cpu",
    gradient_clip_val=0.1,
)


tft = TemporalFusionTransformer.from_dataset(
    ts_dataset,
    # not meaningful for finding the learning rate but otherwise very important
    learning_rate=0.03,
    hidden_size=8,  # most important hyperparameter apart from learning rate
    # number of attention heads. Set to up to 4 for large datasets
    attention_head_size=1,
    dropout=0.1,  # between 0.1 and 0.3 are good values
    hidden_continuous_size=8,  # set to <= hidden_size
    loss=QuantileLoss(),
    optimizer="adam",
    # reduce learning rate if no improvement in validation loss after x epochs
    # reduce_on_plateau_patience=1000,
)
print(f"Number of parameters in network: {tft.size() / 1e3:.1f}k")
```
```plain text
Number of parameters in network: 65.3k
```
```python
#학습률 계산

lr_finder = trainer.tuner.lr_find(
    model=tft,
    train_dataloaders=train_dataloader,
    val_dataloaders=val_dataloader,
    min_lr=1e-6,
    max_lr=10.0,
    num_training=100,
)


print(f"suggested learning rate: {lr_finder.suggestion()}")
fig = lr_finder.plot(show=True, suggest=True)
fig.show()
```
```plain text
Finding best initial lr: 100%|██████████| 100/100 [00:46<00:00,  2.17it/s]
`Trainer.fit` stopped: `max_steps=100` reached.
suggested learning rate: 0.007079457843841384
```

![image](https://github.com/user-attachments/assets/747b5393-4da9-4acc-b3c3-f55133ca8191)

```python
early_stop_callback = EarlyStopping(
    monitor="val_loss", min_delta=1e-4, patience=10, verbose=False, mode="min"
)
lr_logger = LearningRateMonitor()  # log the learning rate
logger = TensorBoardLogger("lightning_logs")  # logging results to a tensorboard

trainer = pl.Trainer(
    max_epochs=50,
    accelerator="cpu",
    enable_model_summary=True,
    gradient_clip_val=0.1,
    limit_train_batches=50,  # coment in for training, running valiation every 30 batches
    callbacks=[lr_logger, early_stop_callback],
    logger=logger,
)


tft = TemporalFusionTransformer.from_dataset(
    ts_dataset,
    embedding_sizes={'med': (140, 25), 'pid': (5688, 100)},  # ✅ 이렇게 넘겨줘야 함!
    learning_rate=0.00708,
    hidden_size=8,
    attention_head_size=1,
    dropout=0.1,
    hidden_continuous_size=8,
    loss=QuantileLoss(),
    log_interval=0,
    optimizer="adam",
    reduce_on_plateau_patience=4,
)

print(f"Number of parameters in network: {tft.size() / 1e3:.1f}k")
```
```plain text
Number of parameters in network: 65.3k
```
```python
trainer.fit(
    tft,
    train_dataloaders=train_dataloader,
    val_dataloaders=val_dataloader,
)
```
```plain text
   | Name                               | Type                            | Params
----------------------------------------------------------------------------------------
0  | loss                               | QuantileLoss                    | 0     
1  | logging_metrics                    | ModuleList                      | 0     
2  | input_embeddings                   | MultiEmbedding                  | 46.6 K
3  | prescalers                         | ModuleDict                      | 528   
4  | static_variable_selection          | VariableSelectionNetwork        | 1.2 K 
5  | encoder_variable_selection         | VariableSelectionNetwork        | 12.5 K
6  | decoder_variable_selection         | VariableSelectionNetwork        | 1.2 K 
7  | static_context_variable_selection  | GatedResidualNetwork            | 304   
8  | static_context_initial_hidden_lstm | GatedResidualNetwork            | 304   
9  | static_context_initial_cell_lstm   | GatedResidualNetwork            | 304   
10 | static_context_enrichment          | GatedResidualNetwork            | 304   
11 | lstm_encoder                       | LSTM                            | 576   
12 | lstm_decoder                       | LSTM                            | 576   
13 | post_lstm_gate_encoder             | GatedLinearUnit                 | 144   
14 | post_lstm_add_norm_encoder         | AddNorm                         | 16    
15 | static_enrichment                  | GatedResidualNetwork            | 368   
16 | multihead_attn                     | InterpretableMultiHeadAttention | 280   
17 | post_attn_gate_norm                | GateAddNorm                     | 160   
18 | pos_wise_ff                        | GatedResidualNetwork            | 304   
19 | pre_output_gate_norm               | GateAddNorm                     | 160   
20 | output_layer                       | Linear                          | 63    
----------------------------------------------------------------------------------------
65.3 K    Trainable params
0         Non-trainable params
65.3 K    Total params
0.261     Total estimated model params size (MB)
Sanity Checking: 0it [00:00, ?it/s]

Epoch 0:  74%|███████▎  | 50/68 [00:22<00:08,  2.21it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Validation: 0it [00:00, ?it/s]
Validation:   0%|          | 0/18 [00:00<?, ?it/s]
Validation DataLoader 0:   0%|          | 0/18 [00:00<?, ?it/s]
Epoch 0:  75%|███████▌  | 51/68 [00:23<00:07,  2.15it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  76%|███████▋  | 52/68 [00:24<00:07,  2.09it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  78%|███████▊  | 53/68 [00:26<00:07,  2.02it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  79%|███████▉  | 54/68 [00:27<00:07,  1.96it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  81%|████████  | 55/68 [00:29<00:06,  1.89it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  82%|████████▏ | 56/68 [00:32<00:06,  1.74it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  84%|████████▍ | 57/68 [00:33<00:06,  1.71it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  85%|████████▌ | 58/68 [00:34<00:05,  1.68it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  87%|████████▋ | 59/68 [00:36<00:05,  1.63it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  88%|████████▊ | 60/68 [00:37<00:04,  1.60it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  90%|████████▉ | 61/68 [00:38<00:04,  1.57it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  91%|█████████ | 62/68 [00:40<00:03,  1.54it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  93%|█████████▎| 63/68 [00:41<00:03,  1.53it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  94%|█████████▍| 64/68 [00:42<00:02,  1.51it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  96%|█████████▌| 65/68 [00:43<00:02,  1.50it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  97%|█████████▋| 66/68 [00:44<00:01,  1.48it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0:  99%|█████████▊| 67/68 [00:45<00:00,  1.46it/s, loss=0.543, v_num=8, train_loss_step=0.582]
Epoch 0: 100%|██████████| 68/68 [00:47<00:00,  1.44it/s, loss=0.543, v_num=8, train_loss_step=0.582, val_loss=0.524]
Epoch 1:  74%|███████▎  | 50/68 [00:22<00:08,  2.22it/s, loss=0.531, v_num=8, train_loss_step=0.493, val_loss=0.524, train_loss_epoch=0.581]
Validation: 0it [00:00, ?it/s]
Validation:   0%|          | 0/18 [00:00<?, ?it/s]
...
Epoch 49:  96%|█████████▌| 65/68 [00:46<00:02,  1.41it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.434, train_loss_epoch=0.453]
Epoch 49:  97%|█████████▋| 66/68 [00:47<00:01,  1.39it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.434, train_loss_epoch=0.453]
Epoch 49:  99%|█████████▊| 67/68 [00:48<00:00,  1.37it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.434, train_loss_epoch=0.453]
Epoch 49: 100%|██████████| 68/68 [00:49<00:00,  1.36it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.431, train_loss_epoch=0.453]
Epoch 49: 100%|██████████| 68/68 [00:49<00:00,  1.36it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.431, train_loss_epoch=0.444]
Output is truncated. View as a scrollable element or open in a text editor. Adjust cell output settings...
`Trainer.fit` stopped: `max_epochs=50` reached.
Epoch 49: 100%|██████████| 68/68 [00:50<00:00,  1.35it/s, loss=0.446, v_num=8, train_loss_step=0.416, val_loss=0.431, train_loss_epoch=0.444]
```
```python
n_rows, n_cols = 5, 2
fig, axs = plt.subplots(n_rows, n_cols, figsize=(20, 20))

plotted = 0
idx = 10
max_plots = n_rows * n_cols

while plotted < max_plots and idx < len(x["decoder_target"]):
    try:
        target = x["decoder_target"][idx].detach().cpu().numpy()
        if np.isnan(target).all() or np.all(target == target[0]):
            idx += 1
            continue

        ax = axs.flat[plotted]
        tft.plot_prediction(
    x,
    raw_predictions,
    idx=idx,
    add_loss_to_title=True,
    ax=ax)

        ax.set_ylim(0, 20)

        plotted += 1
        idx += 1

    except Exception as e:
        print(f"[{idx}] 예측 시각화 중 오류 발생: {e}")
        idx += 1

plt.tight_layout()
plt.show()
```
![image](https://github.com/user-attachments/assets/a853fe73-f5dc-49a6-91d9-0a0273c4d238)

```python
# 예측 결과 시각화: y축을 고정하여 개별 출력
for idx in range(11, 21):
    fig, ax = plt.subplots(figsize=(8, 4))  # 각 그래프는 개별로
    try:
        tft.plot_prediction(
            x,
            raw_predictions,
            idx=idx,
            add_loss_to_title=True,
            ax=ax
        )
        ax.set_ylim(0, 20)  # y축 범위 고정 (원하는 범위로 수정 가능)
        plt.show()
    except Exception as e:
        print(f"[{idx}] 예측 시각화 중 오류 발생: {e}")
```

![image](https://github.com/user-attachments/assets/408b7342-1e80-4b8a-83f5-ec86e55ff3b0)
![image](https://github.com/user-attachments/assets/0b205eaa-9cad-4a36-b876-1420c7b819a1)
![image](https://github.com/user-attachments/assets/ab685d82-a121-41ca-a623-e4214c2938d7)
![image](https://github.com/user-attachments/assets/509c4721-fa37-4c17-b079-eba584602f7e)
![image](https://github.com/user-attachments/assets/39d98517-482e-4d3e-ad28-1058b855329c)
![image](https://github.com/user-attachments/assets/3c3f27d7-2569-4b73-873c-10dc7e7e2b97)
![image](https://github.com/user-attachments/assets/4dd58b53-12f9-4e0c-a2e0-3c3e631ec824)
![image](https://github.com/user-attachments/assets/9a89cacd-66b1-4f1d-9c83-5534d275cb56)
![image](https://github.com/user-attachments/assets/041525c9-1e8f-4637-a406-ee2bce5af35a)
![image](https://github.com/user-attachments/assets/e0e967eb-31a4-457e-b104-b54491c1facd)

```python
print(type(raw_predictions))
print(len(raw_predictions))

for i, item in enumerate(raw_predictions):
    print(f"[{i}] type: {type(item)}")

print("예측 길이:", ts_dataset.max_prediction_length)  # 3이어야 함
# 1. 예측 결과에서 예측값만 추출
y_hat = raw_predictions[0]

# 2. 예측값 shape 확인
print("y_hat shape:", y_hat.shape)  # 예상: (batch_size, target_dim=1, prediction_length=3)
```
```plain text
<class 'pytorch_forecasting.utils.TupleOutputMixIn.to_network_output.<locals>.Output'>
8
[0] type: <class 'torch.Tensor'>
[1] type: <class 'list'>
[2] type: <class 'torch.Tensor'>
[3] type: <class 'torch.Tensor'>
[4] type: <class 'torch.Tensor'>
[5] type: <class 'torch.Tensor'>
[6] type: <class 'torch.Tensor'>
[7] type: <class 'torch.Tensor'>
예측 길이: 3
y_hat shape: torch.Size([23001, 3, 7])
```
```python
# 인코딩된 항생제 정보 확인
ts_dataset.get_parameters()["categorical_encoders"]["med"].classes_

# dict 방향 뒤집기
med_index_to_str = {v: k for k, v in med_index_to_str.items()}

# 인코더 정보 가져오기
cat_encoders = ts_dataset.get_parameters()["categorical_encoders"]

# med 클래스: str → int 형태라면 → dict 뒤집기
med_index_to_str = cat_encoders["med"].classes_
if isinstance(med_index_to_str, dict):
    if list(med_index_to_str.values())[0] < 1000:  # int 값이면 → 뒤집기
        med_index_to_str = {v: k for k, v in med_index_to_str.items()}

# med가 categorical feature 몇 번째인지 확인
cat_features = ts_dataset.categoricals
med_index = cat_features.index("med")  # 예: 1번

# 예측 구간에서 med 인덱스 가져오기
future_med_indices = x['decoder_cat'][0, :, med_index].tolist()

# 인덱스 → 약물이름
future_med_names = [med_index_to_str.get(int(idx), "UNKNOWN") for idx in future_med_indices]
print("예측 구간의 항생제:", future_med_names)
```
```plain text
예측 구간의 항생제: ['Cefotaxime', 'Cefotaxime', 'Cefotaxime']
```

```python
for i in range(5):  # 첫 5개 시퀀스
    meds = [med_index_to_str.get(int(idx), "UNKNOWN") for idx in x['decoder_cat'][i, :, med_index]]
    print(f"#{i} 예측 구간 항생제:", meds)
```

```plain text
#0 예측 구간 항생제: ['Cefotaxime', 'Cefotaxime', 'Cefotaxime']
#1 예측 구간 항생제: ['Remdesivir', 'Remdesivir', 'Remdesivir']
#2 예측 구간 항생제: ['Tazocin', 'Tazocin', 'Tazocin']
#3 예측 구간 항생제: ['Hanomycin', 'Hanomycin', 'Hanomycin']
#4 예측 구간 항생제: ['Meropen', 'Meropen', 'Meropen']
```
```python
# 예측 수행 (validation 데이터셋 대상)
raw_predictions, x = tft.predict(
    val_dataloader,
    mode="raw",       # 예측값 전체를 출력 (raw tensor)
    return_x=True     # 입력 데이터도 함께 반환
)
```
```python
# 디코딩용 인덱스
med_index_to_str = list(ts_dataset.get_parameters()["categorical_encoders"]["med"].classes_)
pid_index_to_str = list(ts_dataset.get_parameters()["categorical_encoders"]["pid"].classes_)

# 예측 및 실제값
preds = raw_predictions["prediction"].detach().cpu().numpy()[:, :, 0]
targets = x["decoder_target"].detach().cpu().numpy()

# 인덱스 추출
med_indices = x["decoder_cat"][:, 0, 0].int().cpu().numpy()
pid_indices = x["groups"][:, 0].int().cpu().numpy()

maes = np.mean(np.abs(preds - targets), axis=1)

# 시각화
n = 20
ncols = 5
nrows = (n + ncols - 1) // ncols
plt.figure(figsize=(ncols * 4, nrows * 3))

for i in range(n):
    plt.subplot(nrows, ncols, i + 1)

    true = targets[i]
    pred = preds[i]
    mae = maes[i]

    # med 및 pid 인덱스를 문자열로 변환
    med_idx = med_indices[i]
    med = med_index_to_str[med_idx] if med_idx < len(med_index_to_str) else "UNKNOWN"
    
    pid_idx = pid_indices[i]
    pid = pid_index_to_str[pid_idx] if pid_idx < len(pid_index_to_str) else "UNKNOWN"

    # 플롯
    plt.plot(true, label="True", marker="o")
    plt.plot(pred, label="Pred", marker="x")
    plt.ylim(0, 20)
    plt.title(f"PID: {pid}\nMED: {med}\nMAE: {mae:.2f}")
    plt.grid(True)

plt.tight_layout()
plt.legend(loc="upper right", bbox_to_anchor=(1.2, 1.05))
plt.show()
```
![image](https://github.com/user-attachments/assets/1c11b019-9ec4-450d-85c5-c668c235c6d1)

