---
date : 2025-06-27
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "HLA-peptide interaction affected by mutation of c315 and c442"
bookComments: true
---

# HLA-peptide interaction affected by mutation of c315 and c442

#2025-06-27

---

### 1. Load package

```python
import pandas as pd
import numpy as np
```

### 2. Load affinity data

```python
with open('/data/home/ysh980101/2411/data-mhc/patient_id.txt', 'r') as file:
    patients = [line.strip() for line in file]
len(patients)
```
```plain text
388 #including reference
```

### 3. Merge affinity tables

```python
hotspot = "c315"
dfs = []

for pid in patients:
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

```python
res_df.iloc[:, 1:] = res_df.iloc[:, 1:].subtract(res_df['reference'], axis=0)
res_df
```
![image](https://github.com/user-attachments/assets/c3459fa7-c809-4402-985a-46a3f7425603)

```python
res_df.to_csv(f"/data/home/ysh980101/2411/data/{hotspot}/aff-table.csv")
```
만든건 저장. 


### 4. Load peptide data

```python
hotspot = "c315"
peptide_df_list = []

for patient in patients:
    peptide_df = pd.read_csv(f"/data/home/ysh980101/2411/data-mhc/{hotspot}/{patient}/peptides_HLA-I.csv")
    # 특수 문자가 포함된 Peptide 제거
    peptide_df = peptide_df[~peptide_df['Peptide'].str.contains('[-*]', regex=True, na=False)]
    
    # Patients 컬럼의 첫 번째 행 값 가져오기
    patient_name = patient  # 파일 이름 또는 경로에서 patient ID를 사용
    
    # Peptide 컬럼 이름을 patient_name으로 변경
    peptide_df = peptide_df[['Peptide']]  # Peptide 컬럼만 남기기
    peptide_df.columns = [patient_name]   # 컬럼 이름 변경
    
    # 리스트에 추가
    peptide_df_list.append(peptide_df)
```

### 5. Merge peptide data

```python
merged_df = pd.concat(peptide_df_list, axis = 1)
merged_df.index = f"{hotspot}." + merged_df.index.astype(str)
merged_df.to_csv(f"/data/home/ysh980101/2412/result/epitope_{hotspot}.csv")
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
