---
date : 2025-08-12
tags: ['2025-08']
categories: ['Algorithm']
bookHidden: true
title: "학위논문작업 #5 코드 리펙토링 계획"
---

# 학위논문작업 #5 코드 리펙토링 계획

#2025-08-12

---

### 1. Objective

로그 뽑기 작업하면서 
- 변수이름이 너무 직관적이지않은거같기도 하고 
- 주석도 깔끔하게 달면 좋을것같아서
- 클러스터링 관련 함수만이라도 리펙토링 작업을 해볼려고한다.
- 그리고 어제 수업시간에 로그를 print로 뽑는게 비효율적이라고하셔서 log 적용도 해볼려고한다. 

###

### 2. Pipeline

```python
#1 Load package
import pandas as pd
import numpy as np
import os
os.sys.path.append("/data/home/ysh980101/2407/Mutclust") 

from pathlib import Path
from Bin.Utils.utils import *
from Bin.arg_parser import *
from Bin.mlib import *

#2 Set params
i = 1
tag = f"test{i}"
input_path = "/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle"
outdir = f"/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_{tag}/"
Path(outdir).mkdir(parents=True, exist_ok=True)

info = set_env(input = input_path, output = outdir)
Input_df = readPickle(input_path)
init(Input_df, info)

#3 Find CCM
mutInfo, ccms = get_candidate_core_mutations(Input_df, info, tag, i)

#4 Perform clustering
hotspots = dynaclust(mutInfo, ccms, info, tag, i)
```
```plain text
--- Configurations ---
Input data: '/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle' (29903, 5)
Output dir: '/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_test1/'
Parameters:
  Min Eps=5
  Max Eps=1000
  Min per_sum=0.0
  Eps scaling factor=10.0
  Expansion diminishing factor=3
  Min cluster length=10
----------------------

Searching candidate core mutations...


1990 CCMs found.

Performing dynamic clustering...
1990 clusters found
Merging clusters...
Merged clusters: 477
```

전체 파이프라인은 이렇고 이중에 dynaclust 관련 함수를 수정볼 예정이고

작업 순서는
1. 변수 수정
2. 주석 달기

요순서로 할거같다. 

~*(코드 리펙토링도 할려다가 로직 안꼬이게 건드릴 자신 없어서 포기 ..)*~

#