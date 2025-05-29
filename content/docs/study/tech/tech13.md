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


#environment check

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
