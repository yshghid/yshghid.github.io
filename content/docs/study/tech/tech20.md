---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#1 입력 데이터 생성"
bookComments: true
---

# #1 입력 데이터 생성

#2025-06-17

---

### Load package

```python
%load_ext autoreload
%autoreload 2

import sys
import os

sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin')
from sc import *

os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### Check data

```python
raw_path = '/data3/projects/2025_Antibiotics/YSH/res/sev_dict_filtered.pkl'

with open(raw_path, 'rb') as f:
    raw_data = pickle.load(f)

keys = list(raw_data.keys())
print(len(keys))
print(keys[0], '\n', raw_data[keys[0]])
```
```plain text
4515
74374 
         Date  NEWS  med_cnt                    med_list  \
0 2020-10-30     4        2          Trizele;Cefotaxime   
1 2020-10-31     4        2          Trizele;Cefotaxime   
2 2020-11-01    12        2         Pospenem;Pospenem_2   
3 2020-11-02     9        3  Pospenem;Meropen;Vanco Kit   
4 2020-11-03    12        2           Vanco Kit;Meropen   
5 2020-11-04     8        2           Vanco Kit;Meropen   
6 2020-11-05     9        0                               

                               strain  
0                                  []  
1                                  []  
2                                  []  
3  [Enterobacter cloacae ssp cloacae]  
4  [Enterobacter cloacae ssp cloacae]  
5  [Enterobacter cloacae ssp cloacae]  
6  [Enterobacter cloacae ssp cloacae]  
```

4515명 환자 데이터이고

첫번째 환자 '74374'의 데이터를 확인해보면 날짜, NEWS 중증도 점수, 항생제 투여 횟수, 항생제 투여 종류, 균주 정보가 있다.

```python
indir = 'res'

with open(f"{indir}/all_meds.txt", 'r') as f:
    all_meds = [line.strip() for line in f if line.strip()]
    all_meds = [s.replace("/", "_") for s in all_meds]
```
