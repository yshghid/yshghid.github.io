---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#2 입력 feature 생성"
bookComments: true
---

# #2 입력 feature 생성

#2025-06-17

---

### Load package

```python
%load_ext autoreload
%autoreload 2

import sys
import os

import pickle
import matplotlib.pyplot as plt
from matplotlib.backends.backend_pdf import PdfPages
import pandas as pd

sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin')
from sc import *
```

### Set path
```python
os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### Previous

```python
indir = 'res'

with open(f"{indir}/all_meds.txt", 'r') as f:
    all_meds = [
        line.strip().replace("/", "_")
        for line in f
        if line.strip()
    ]

cur_path = 'data/res_dict'
cur_med = 'Dexamethasone'

with open(f"{cur_path}/{cur_med}", 'rb') as f:
    res_dict = pickle.load(f)

cur_keys = list(res_dict.keys())

print(len(all_meds))
print(res_dict[cur_keys[0]])
```
```plain text
169
         Date  NEWS  med_cnt                        strain
4  2017-08-18     4        0  [Staphylococcus epidermidis]
5  2017-08-19     4        0  [Staphylococcus epidermidis]
6  2017-08-20     4        0  [Staphylococcus epidermidis]
7  2017-08-21     4        1  [Staphylococcus epidermidis]
8  2017-08-22     3        1  [Staphylococcus epidermidis]
9  2017-08-23     4        1  [Staphylococcus epidermidis]
10 2017-08-24     4        1      [Pseudomonas aeruginosa]
11 2017-08-25     7        1      [Pseudomonas aeruginosa]
12 2017-08-26     4        1      [Pseudomonas aeruginosa]
13 2017-08-27     4        1      [Pseudomonas aeruginosa]
```

항생제 169종에 대해서 size 10 sequence를 생성했었는데
- 모델 입력 feature로 다음을 제외하는대신
  1) antibiotics 리스트
  2) strain 리스트
- 저 2개 feature를 반영하는 새로운 feature를 2개 생성하려고 한다:
  1) 현재 antibiotics가 현재 strain 환자의 NEWS를 감소시킨 이력이 있는지? (binary: 0/1)
  2) 현재 antibiotics가 NEWS를 감소시키는데 소요 기간은? (범주형: short/mid/long)

