---
date : 2025-06-24
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#5 결과 검증: 계통 결정 돌연변이와 연관성"
bookComments: true
---

# #5 결과 검증: 계통 결정 돌연변이와 연관성

#2025-06-24

---

### 1. Load package

```python
import pandas as pd
import numpy as np
import os
os.sys.path.append("/data/home/ysh980101/2407/Mutclust") 

from pathlib import Path
from Bin.Utils.utils import *
from Bin.arg_parser import *
from Bin.mlib import *

os.sys.path.append("/data/home/ysh980101/2506/mutclust") 
from Bin.sc import *

os.chdir("/data/home/ysh980101/2506/mutclust")
```

### 2. Load data

```python
lineage_info_dir = '/data/home/ysh980101/2411/data/mutation_info'
covid_annotation = "/data/home/ysh980101/2404/Data/covid_annotation.tsv"
sig_hotspots = "result/sig_hotspots.csv"

lineage_info = make_lineage_info(lineage_info_dir)
```

![image](https://github.com/user-attachments/assets/458f5aa5-f2b0-4ec4-bad0-560f8b889d48)

```python
hotspot_lineage = make_hotspot_lineage(lineage_info, sig_hotspots_path, covid_annotation)
hotspot_lineage
```
![image](https://github.com/user-attachments/assets/2df486b8-2ccb-4b68-a0dc-de452d3cb8a0)

```python
plot_hotspot_lineage(hotspot_lineage)
```
![image](https://github.com/user-attachments/assets/0ecf300b-e844-42f9-910f-943604e5cddf)

```python
outdir = "result/"
hotspot_lineage.to_csv(f"{outdir}Supplementary_table_1.csv", index=False)
```
만든건 저장.
