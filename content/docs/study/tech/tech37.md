---
date : 2025-06-27
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "HLA 결합력 변화 비교"
bookComments: true
---

# HLA 결합력 변화 비교

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
388 #387+reference
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
    peptide_df = peptide_df[~peptide_df['Peptide'].str.contains('[-*]', regex=True, na=False)]
    
    patient_name = patient 
    
    peptide_df = peptide_df[['Peptide']]  
    peptide_df.columns = [patient_name]  
    
    peptide_df_list.append(peptide_df)
```

### 5. Merge peptide data

```python
merged_df = pd.concat(peptide_df_list, axis = 1)
merged_df.index = f"{hotspot}." + merged_df.index.astype(str)
merged_df
```
![image](https://github.com/user-attachments/assets/94e51133-4af4-4653-97eb-0a8a1afd0459)

```python
merged_df.to_csv(f"/data/home/ysh980101/2412/result/epitope_{hotspot}.csv")
```

### 6. Check affinity between moderate and severe group

```python

```
