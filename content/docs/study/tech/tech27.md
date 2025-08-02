---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#1 입력 데이터 생성"
bookComments: true
---

# #1 입력 데이터 생성

#2025-06-18

---

### 1. Load package

```python
import pandas as pd
import numpy as np
import os
import pickle
import ast

os.chdir('/data3/projects/2025_Antibiotics/YSH/')
```

### 2. Load raw data

```python
datadir = '/data3/projects/2025_Antibiotics/PreprocessedData/TimecourseData'
outdir = 'res'

pids =[d for d in os.listdir(datadir) if os.path.isdir(os.path.join(datadir, d))]
len(pids)
```
```plain text
4589
```

datadir에 4589명 환자의 의료 데이터가 존재한다.

```python
cur_pid = pids[0]
sev = pd.read_csv(f"{datadir}/{cur_pid}/SeverityScore.csv")
lab = pd.read_csv(f"{datadir}/{cur_pid}/Laboratory_processed.csv")
med = pd.read_csv(f"{datadir}/{cur_pid}/Medication.csv")

print(cur_pid)
print(len(sev.columns.tolist()), sev.columns.tolist())
print(len(lab.columns.tolist()), lab.columns.tolist())
print(med)
```
```plain text
74374
6, ['Date', 'NEWS', 'WHO', 'SOFA', 'PBS', 'qPitt']
23, ['Date', 'ALT (U/L)', 'AST (U/L)', 'BUN (mg/dL)', 'Creatinine (mg/dL)', 'D-Dimer (ug/mL )', 'Ferritin (ng/mL)', 'HCO3 (mmol/L)', 'Hemoglobin (g/dL)', 'LDH (U/L)', 'Lymphocytes (%)', 'MDRD eGFR (mL/min/BSA)', 'Neutrophils (%)', 'O2 saturation (%)', 'PCO2 (mmHg)', 'PO2 (mmHg)', 'Platelet count (10^3/uL)', 'Potassium (mmol/L)', 'Sodium (mmol/L)', 'WBC count (10^3/uL)', 'hs-CRP (mg/dL)', 'pH ()', 'total CO2, calculated (mmol/L)']
         Date antimicrobials antimicrobials_dose antimicrobials_2  \
0  2020-10-30        Trizele           500.0mg/2       Cefotaxime   
1  2020-10-31        Trizele           500.0mg/3       Cefotaxime   
2  2020-11-01       Pospenem              1.0g/1         Pospenem   
3  2020-11-02       Pospenem              1.0g/1          Meropen   
4  2020-11-03      Vanco Kit              1.0g/1          Meropen   
5  2020-11-04      Vanco Kit              1.0g/1          Meropen   
6  2020-11-05            NaN                 NaN              NaN   

  antimicrobials_2_dose antimicrobials_3 antimicrobials_3_dose  
0               2.0mg/2              NaN                   NaN  
1               2.0mg/3              NaN                   NaN  
2                1.0g/2              NaN                   NaN  
3             500.0mg/2        Vanco Kit                1.0g/1  
4             500.0mg/3              NaN                   NaN  
5             500.0mg/3              NaN                   NaN  
6                   NaN              NaN                   NaN 
```

환자 '74374'의 데이터를 예시로 확인해보면
- 중증도 정보 sev에는
  - date별로 5개의 중증도 점수가 있고,
- 임상 정보 lab에는
  - date별로 22개의 임상 데이터가 있다.
- 항생제 정보 med에는
  - date 별로 투여한 항생제 종류와 투여 용량 데이터가 있다.

작업 계획은 
- 

```python
sev_dict = {}

for pid in pids:
    try:
        # 1. SeverityScore 불러오기
        sev = pd.read_csv(f"{datadir}/{pid}/SeverityScore.csv")

        # 2. Laboratory 데이터 불러오기 및 병합
        lab = pd.read_csv(f"{datadir}/{pid}/Laboratory_processed.csv")
        sev = pd.merge(sev, lab, on='Date', how='left')  # 'Date' 기준 오른쪽에 lab 붙이기

        # 3. Medication 불러오기
        med = pd.read_csv(f"{datadir}/{pid}/Medication.csv")
        med_filtered = med[med['Date'].isin(sev['Date'])]
        med_filtered = med_filtered.loc[:, ~med_filtered.columns.str.endswith('_dose')]

        # 4. Medication 관련 열 추가
        sev['med_cnt'] = 0
        sev['med_list'] = ""

        # 5. 날짜별로 약물 정보 병합
        for _, row in med_filtered.iterrows():
            cur_date = row['Date']
            cur_meds_raw = row.iloc[1:]
            cur_meds_clean = cur_meds_raw.dropna().tolist()

            med_freq = {}
            cur_meds = []
            for med in cur_meds_clean:
                if med not in med_freq:
                    med_freq[med] = 1
                    cur_meds.append(med)
                else:
                    med_freq[med] += 1
                    cur_meds.append(f"{med}_{med_freq[med]}")

            cur_med_cnt = len(cur_meds)
            cur_med_str = ";".join(cur_meds)

            sev_idx = sev[sev['Date'] == cur_date].index
            if len(sev_idx) > 0:
                sev.loc[sev_idx, 'med_cnt'] = cur_med_cnt
                sev.loc[sev_idx, 'med_list'] = cur_med_str

        zero_cnt = (sev['med_cnt'] == 0).sum()

    except FileNotFoundError:
        sev['med_cnt'] = ""
        sev['med_list'] = ""
        zero_cnt = "N/A (no med file)"

    if sev.shape[0] == zero_cnt:
        print(pid)

    if zero_cnt == "N/A (no med file)":
        print(pid, zero_cnt)

    sev_dict[pid] = sev
```
