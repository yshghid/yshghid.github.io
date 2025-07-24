---
date : 2025-07-23
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "TFT #1 입력 시퀀스 생성"
---

# TFT #1 입력 시퀀스 생성

#2025-07-23

---

### 1. Load package

```python
%load_ext autoreload
%autoreload 2

import sys
import pandas as pd
import numpy as np
import os
import pickle
import ast

sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin')
from sc import *

os.chdir('/data3/projects/2025_Antibiotics/YSH/workspace')
```

### 2. Load raw data

#data

```plain text
/data
├── PreprocessedData/
│   └── TimecourseData/
│       └── * (*: patient id)
│           ├── SeverityScore.csv
│           ├── Laboratory_processed.csv 
│           └── Medication.csv
├── PreprocessedData_knuh/
│    └── (PreprocessedData와 동일)
└── 병원체자원은행 균주현황(2014-2024.06)_Sepsis.xlsx

/data_knuch
└── (empty)

/data_knuh
└── (empty)
```

```python
data_knuch = '/data/PreprocessedData/TimecourseData'
data_knuh = '/data/PreprocessedData_knuh/TimecourseData'

pids = [d for d in os.listdir(data_knuch)] + [d for d in os.listdir(data_knuh)]
len(pids)
```
```plain text
13779
```

### 3. Raw data processing

```python
#processing knuch
datadir = '/data/PreprocessedData/TimecourseData'
pids = [d for d in os.listdir(datadir)]

input_dict = make_input(datadir, pids)
input_dict, no_strains = add_strain(input_dict)

outdir = "data_knuch"
with open(f"{outdir}/Input.pkl", 'wb') as f:
    pickle.dump(input_dict, f)
print(len(list(input_dict.keys())))
print(len(no_strains))
```
```plain text
4516
4
```

```python
#processing knuh
datadir = '/data/PreprocessedData_knuh/TimecourseData'
pids = [d for d in os.listdir(datadir)]

input_dict = make_input(datadir, pids)
input_dict, no_strains = add_strain(input_dict)

outdir = "data_knuh"
with open(f"{outdir}/Input.pkl", 'wb') as f:
    pickle.dump(input_dict, f)
print(len(list(input_dict.keys())))
print(len(no_strains))
```
```plain text
9100
1
```

#result

```plain text
/data
├── PreprocessedData/
│   └── TimecourseData/
│       └── * (*: patient id)
│           ├── SeverityScore.csv
│           ├── Laboratory_processed.csv 
│           └── Medication.csv
├── PreprocessedData_knuh/
│    └── (PreprocessedData와 동일)
└── 병원체자원은행 균주현황(2014-2024.06)_Sepsis.xlsx

/data_knuch
└── Input.pkl

/data_knuh
└── Input.pkl
```

### 4. Make input sequence

#data

```plain text
/data
└── all_meds.txt

/data_knuch
├── Input.pkl
└── sequence
     └── (empty)

/data_knuh
├── Input.pkl
└── sequence
     └── (empty)
```

```python
dtype = 'knuh'

indir = f'data_{dtype}'
medinfo = '/data/all_meds.txt'

with open(medinfo, 'r') as f:
    meds = [line.strip().replace("/", "_") for line in f if line.strip()]
    
with open(f"{indir}/Input.pkl", 'rb') as f:
        input_dict = pickle.load(f)

pids = list(input_dict.keys())
outdir = f'data_{dtype}/sequence'

for med in meds:
    make_sequence(med, indir, outdir)
```

#result

```plain text
/data
└── all_meds.txt

/data_knuch
├── Input.pkl
└── sequence
     └── *.pkl (*: antibiotics)

/data_knuh
├── Input.pkl
└── sequence
     └── *.pkl (*: antibiotics)
```

<details>
  <summary>자세히 보기</summary>
  여기에 상세 내용을 입력하세요.
</details>


#
