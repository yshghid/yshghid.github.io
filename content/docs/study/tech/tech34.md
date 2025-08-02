---
date : 2025-06-24
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#6 알고리즘 성능 평가 - k dist plot"
bookComments: true
---

# #6 알고리즘 성능 평가 - k dist plot

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
indir = 'result/'
resdir = 'result/GISAID_test1/'

with open(f"{indir}GISAID_total.pickle", "rb") as f:
    Input_df = pickle.load(f)

hotspots = pd.read_csv(f"{resdir}clusters_test1.txt", sep='\t')
sig_hotspots = pd.read_csv(f"{indir}sig_hotspots.csv")
```

### 3. K-dist plot

```python
kdist_plot(Input_df, hotspots, sig_hotspots, k=5)
```

