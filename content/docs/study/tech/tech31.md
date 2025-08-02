---
date : 2025-06-20
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#4 결과 검증: 임상 결과와의 연관성"
bookComments: true
---

# #4 결과 검증: 임상 결과와의 연관성

#2025-06-20

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
```

### 2. Load COVID19 data

```python
i = 1
tag = f"test{i}"
resdir = f"/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_{tag}/"
covid19_dir = "/data3/projects/2020_MUTCLUST/Data/Projects/COVID19/Sequence/Preprocessed/Nucleotide/Mutationinfo"
meta_path = "/data/home/ysh980101/2506/data/meta.csv"

hotspots = pd.read_csv(f"{resdir}clusters_{tag}.txt",sep="\t")
metaData = pd.read_csv(meta_path, index_col=0)
mutInfo = make_mutInfo_covid19(covid19_dir)
mutSignature = make_mutSignature(mutInfo, hotspots, metaData)
print(mutSignature)
```
```plain text
      COV-CCO-001  COV-CCO-002  COV-CCO-003  COV-CCO-004  COV-CCO-006  \
c0              0            0            0            0            0   
c1              0            0            0            0            0   
c2              0            0            0            0            0   
c3              0            0            0            0            0   
c4              0            0            0            0            0   
...           ...          ...          ...          ...          ...   
c472            0            1            0            0            0   
c473            0            0            0            0            0   
c474            0            0            0            0            0   
c475            0            0            0            0            0   
c476            0            0            0            0            0   

      COV-CCO-008  COV-CCO-009  COV-CCO-010  COV-CCO-011  COV-CCO-013  ...  \
c0              0            0            0            0            0  ...   
c1              0            0            0            0            0  ...   
c2              0            0            0            0            0  ...   
c3              0            0            0            0            0  ...   
c4              0            0            0            0            0  ...   
...           ...          ...          ...          ...          ...  ...   
c472            0            0            0            0            0  ...   
c473            0            0            0            0            0  ...   
c474            0            0            0            0            0  ...   
c475            0            0            0            0            0  ...   
c476            0            0            0            0            0  ...    

[477 rows x 387 columns]
```

### 3. Select severity related hotspots

```python
sig_hotspots, significance = select_sig_hotspots(mutSignature, metaData, hotspots)
significance
```
```plain text
   Hotspot       p-value           FDR  Significant
0      c22  1.882327e-07  4.489349e-06         True
1      c90  1.158366e-03  2.051443e-02         True
2     c118  9.750940e-15  1.162800e-12         True
3     c123  8.587634e-14  6.827169e-12         True
4     c124  1.051981e-03  2.007179e-02         True
5     c198  2.827480e-10  1.123923e-08         True
6     c239  5.739929e-16  2.737946e-13         True
7     c258  1.489502e-08  4.301825e-07         True
8     c292  6.617715e-07  1.372457e-05         True
9     c298  1.205966e-04  2.396858e-03         True
10    c309  2.746212e-08  7.277461e-07         True
11    c315  7.603734e-08  1.908937e-06         True
12    c319  5.323421e-07  1.154215e-05         True
13    c334  4.989612e-10  1.830804e-08         True
14    c337  8.625002e-12  4.114126e-10         True
15    c350  4.178970e-07  9.492232e-06         True
16    c364  9.750940e-15  1.162800e-12         True
17    c385  4.112387e-13  2.802298e-11         True
18    c390  1.161194e-03  2.051443e-02         True
19    c412  5.946573e-12  3.151684e-10         True
20    c429  3.073511e-09  9.773764e-08         True
21    c431  7.755493e-14  6.827169e-12         True
22    c438  1.929048e-09  6.572544e-08         True
23    c442  5.644954e-13  3.365804e-11         True
24    c444  1.362927e-15  3.250582e-13         True
25    c455  1.928137e-03  3.171454e-02         True
26    c460  1.723333e-03  2.935821e-02         True
27    c462  1.533145e-08  4.301825e-07         True
28    c468  1.169796e-11  5.072662e-10         True
```
```python
outdir = "result/"
sig_hotspots.to_csv(f"{outdir}sig_hotspots.csv", index=False)
```
만든건 저장하기.
