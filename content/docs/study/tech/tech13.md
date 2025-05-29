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

#version check

예제 코드에 맞는 패키지 버전
  - CUDA: 11.7
  - PyTorch: 1.13.1+cu117
  - PyTorch Lightning: 1.9.0
  - PyTorch Forecasting: 0.10.3


PyTorch Forecasting 0.10.3 선택 이유: 최신 버전은 아래 코드랑 호환 안됨
  1. `Tuner().lr_find()` -> 학습률 탐색, lightning>=2.x에서는 내부 콜백 구조 변경됨
  2. `trainer.checkpoint_callback.best_model_path` -> 베스트 모델 로드, trainer.checkpoint_callback 속성 제거됨
  3. `optimizer="ranger"` -> Ranger 옵티마이저 지정, 제거됨
  4. `plot_prediction`, `plot_interpretation`, `plot_dependency` -> 시각화 함수, 제거되거나 구조 변경됨
  5. `optimize_hyperparameters` -> Optuna 기반 튜닝, deprecated 또는 작동 오류


여기에 맞게 설치해주기.

```bash
conda create -n tftspace python=3.9 -y

pip install pip==23.3.1 #pip다운그레이드

#pip uninstall torch -y
pip install torch==1.13.1+cu117 torchvision==0.14.1+cu117 torchaudio==0.13.1+cu117 -f https://download.pytorch.org/whl/torch_stable.html
pip install torchmetrics==0.10.3  # torch<2.0 호환
pip install pytorch-lightning==1.6.5
pip install pytorch-forecasting==0.10.3

pip install ranger-adabelief #optimizer="ranger" 수행에 필요함
```

python 스크립트 상에서 확인하기

```bash
pip install ipykernel
python -m ipykernel install --user --name tftspace --display-name "tftspace" #커널등록
```

```python
#!pip install "numpy<2.0"

import torch
import torchvision
import torchaudio
import torchmetrics
import pytorch_lightning
import pytorch_forecasting

print("torch:", torch.__version__)
print("torchvision:", torchvision.__version__)
print("torchaudio:", torchaudio.__version__)
print("torchmetrics:", torchmetrics.__version__)
print("pytorch_lightning:", pytorch_lightning.__version__)
print("pytorch_forecasting:", pytorch_forecasting.__version__)
)
```
```plain text
torch: 1.13.1+cu117
torchvision: 0.14.1+cu117
torchaudio: 0.13.1+cu117
torchmetrics: 0.10.3
pytorch_lightning: 1.6.5
pytorch_forecasting: 0.10.3
```

제대로 설치됨!

#set package

```python

```

