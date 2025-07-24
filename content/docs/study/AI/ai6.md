---
date : 2025-07-23
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "TFT #2 입력 feature 생성"
---

# TFT #2 입력 feature 생성

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


### 2. Make feature1 

#data

```plain text
/data
└── all_meds.txt

/data_knuch
└── sequence
     └── *.pkl (*: antibiotics)

/data_knuh
└── sequence
     └── *.pkl (*: antibiotics)
```

```python

medinfo = '/data/all_meds.txt'

with open(medinfo, 'r') as f:
    meds = [line.strip().replace("/", "_") for line in f if line.strip()]

outdir = f'data_{dtype}'

strain_dic = {}

for med in meds:
    with open(f'data_{dtype}/sequence/{med}.pkl', 'rb') as f:
        res_dict = pickle.load(f)
        
    feature1_list = []

    for pid, df in res_dict.items():
        news_bf = df.iloc[2]['NEWS']  # 3번째 행 (0-indexed)
        news_af = df.iloc[3:]['NEWS'].max()  # 4번째 행부터 마지막까지 중 최댓값

        if news_af < news_bf:  # "작은" 경우만 (같은 건 포함하지 않음)
            feature1_list.append(pid)
    
    #print(len(feature1_list))
    filtered_res_dict = {pid: res_dict[pid] for pid in feature1_list if pid in res_dict}
    
    with open(f"data_{dtype}/temp/feature1/{med}.pkl", 'wb') as f:
        pickle.dump(filtered_res_dict, f)

    for pid, df in filtered_res_dict.items():
        if len(df) < 3:
            continue  

        try:
            cur_strain = df.iloc[2]['strain']
            if isinstance(cur_strain, list):
                strains = cur_strain
            else:
                strains = [cur_strain]
        except Exception as e:
            #print(med)
            continue

        for strain in strains:
            if strain in strain_dic:
                strain_dic[strain].append(med)
            else:
                strain_dic[strain] = [med]

for strain in strain_dic:
    strain_dic[strain] = list(set(strain_dic[strain]))
```
```python
# Save feature1
with open(f"{outdir}/feature1.pkl", 'wb') as f:
    pickle.dump(strain_dic, f)
```

#result

```plain text
/data
└── all_meds.txt

/data_knuch
├── sequence
│    └── *.pkl (*: antibiotics)
└── feature1.pkl

/data_knuh
├── sequence
│    └── *.pkl (*: antibiotics)
└── feature1.pkl
```

# Details

Details shortcode is a helper for `details` html5 element. It is going to replace `expand` shortcode.

## Example
```tpl
{{%/* details "Title" [open] */%}}
## Markdown content
Lorem markdownum insigne...
{{%/* /details */%}}
```
```tpl
{{%/* details title="Title" open=true */%}}
## Markdown content
Lorem markdownum insigne...
{{%/* /details */%}}
```

{{% details "Title" open %}}
## Markdown content
Lorem markdownum insigne...
{{% /details %}}


{{% details "제목" %}}
내용 내용
{{% /details %}}
