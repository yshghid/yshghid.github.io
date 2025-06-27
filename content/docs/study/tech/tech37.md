---
date : 2025-06-27
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "﹂HLA-peptide interaction affected by mutation of c315 and c442"
bookComments: true
---

# ﹂HLA-peptide interaction affected by mutation of c315 and c442

#2025-06-27

---

### 1. Load package

```python
import pandas as pd
import numpy as np
```

### 2. Load data

```python
with open('/data/home/ysh980101/2411/data-mhc/patient_id.txt', 'r') as file:
    patient_ids = [line.strip() for line in file]
len(patient_ids)
```
```plain text
388 #including reference
```
```python
# Load common_mhc.txt and split into a list
with open('/data/home/ysh980101/2411/data-mhc/common_mhc.txt', 'r') as file:
    common_mhc = file.read().strip().split('\n')
common_mhc = [item[:5] + '*' + item[5:] for item in common_mhc]
print(common_mhc)
```
```plain text
['HLA-A*26:01', 'HLA-C*07:06', 'HLA-C*03:03', 'HLA-A*24:02', 'HLA-C*03:02', 'HLA-B*07:02', 'HLA-C*14:03', 'HLA-A*02:06', 'HLA-C*04:01', 'HLA-B*51:01', 'HLA-C*01:02', 'HLA-B*40:01', 'HLA-B*35:01', 'HLA-B*55:02', 'HLA-A*24:50', 'HLA-A*31:01', 'HLA-A*26:03', 'HLA-B*44:03', 'HLA-B*40:06', 'HLA-C*07:02', 'HLA-B*46:01', 'HLA-C*14:02', 'HLA-B*40:02', 'HLA-B*54:01', 'HLA-A*33:03', 'HLA-B*52:01', 'HLA-B*15:01', 'HLA-C*08:22', 'HLA-A*30:04', 'HLA-C*12:02', 'HLA-B*13:01', 'HLA-C*08:01', 'HLA-B*48:01', 'HLA-A*03:01', 'HLA-C*03:04', 'HLA-A*26:02', 'HLA-A*02:07', 'HLA-A*24:286', 'HLA-A*11:01']
```
```python
hotspot = "c315"
dfs = []

for pid in patient_ids:
    file_path = f"/data/home/ysh980101/2411/data-mhc/{hotspot}/{pid}/binding_affinities_HLA-I.csv"
    df = pd.read_csv(file_path)
    
    df.rename(columns={'Affinity': f'{pid}'}, inplace=True)
    df.rename(columns={'Peptide': f'Peptide_{pid}'}, inplace=True)
    
    if pid == 'reference':
        dfs.append(df)
    else:
        dfs.append(df[[f'{pid}']])
        #dfs.append(df[[f'{pid}', f'Peptide_{pid}']])

res_df = pd.concat(dfs, axis=1)
res_df = res_df.set_index('Allele')
res_df
```
![image](https://github.com/user-attachments/assets/f42cabe2-ace8-4fad-af95-054120e129b7)




