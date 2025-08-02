---
date : 2025-06-23
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#1 입력 데이터 생성"
bookComments: true
---

# #1 입력 데이터 생성

#2025-06-23

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

os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### 2. Check data

```python
datadir = '/data3/projects/2025_Antibiotics/PreprocessedData/TimecourseData'
outdir = 'res'

pids =[d for d in os.listdir(datadir) if os.path.isdir(os.path.join(datadir, d))]
len(pids)
```
```plain text
4589
```
4589명 환자의 의료 데이터.

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
6 ['Date', 'NEWS', 'WHO', 'SOFA', 'PBS', 'qPitt']
23 ['Date', 'ALT (U/L)', 'AST (U/L)', 'BUN (mg/dL)', 'Creatinine (mg/dL)', 'D-Dimer (ug/mL )', 'Ferritin (ng/mL)', 'HCO3 (mmol/L)', 'Hemoglobin (g/dL)', 'LDH (U/L)', 'Lymphocytes (%)', 'MDRD eGFR (mL/min/BSA)', 'Neutrophils (%)', 'O2 saturation (%)', 'PCO2 (mmHg)', 'PO2 (mmHg)', 'Platelet count (10^3/uL)', 'Potassium (mmol/L)', 'Sodium (mmol/L)', 'WBC count (10^3/uL)', 'hs-CRP (mg/dL)', 'pH ()', 'total CO2, calculated (mmol/L)']
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

환자 '74374'를 확인해보면
- SeverityScore는 날짜별 5개의 중증도 점수
- Laboratory는 22개 임상 정보
- Medication은 날짜별 투여 항생제 및 투여용량
  - 정보이다.

### 3. Merge data

```python
input_dict = make_input(datadir, pids)
len(list(input_dict.keys()))
```
```plain text
4516
```

Severity, Laboratory, Medication 정보가 모두 있는 환자(4516명)만 사용해서
- 의료 데이터 딕셔너리 input_dict를 생성했다

```python
input_dict = add_strain_info(input_dict)

print(cur_pid)
print(input_dict[cur_pid])
```
```plain text
        Date  NEWS  WHO  SOFA  PBS  qPitt  ALT (U/L)  AST (U/L)  BUN (mg/dL)  \
0 2020-10-30     4    5     0    0      0       43.0       79.0         16.9   
1 2020-10-31     4    5     1    0      0       71.0      149.0         21.5   
2 2020-11-01    12    5     5    1      2       83.0      149.0         30.8   
3 2020-11-02     9    5     6    1      2       83.0      149.0         19.2   
4 2020-11-03    12    5     5    1      1       83.0      149.0         20.2   
5 2020-11-04     8    5     6    2      1       83.0      149.0         22.5   
6 2020-11-05     9    5     7    4      2       83.0      149.0         22.5   

   Creatinine (mg/dL)  ...  Platelet count (10^3/uL)  Potassium (mmol/L)  \
0                0.68  ...                     395.0                 3.6   
1                1.22  ...                     340.0                 3.0   
2                1.42  ...                     272.0                 4.2   
3                0.93  ...                      83.0                 4.7   
4                0.77  ...                      61.0                 4.7   
5                0.84  ...                      67.0                 5.2   
6                0.84  ...                      67.0                 5.2    

   total CO2, calculated (mmol/L)  med_cnt                    med_list  \
0                            18.3        2          Trizele;Cefotaxime   
1                            18.3        2          Trizele;Cefotaxime   
2                            15.7        2         Pospenem;Pospenem_2   
3                            15.7        3  Pospenem;Meropen;Vanco Kit   
4                            31.0        2           Vanco Kit;Meropen   
5                            31.0        2           Vanco Kit;Meropen   
6                            31.0        0                               

                               strain  
0                                  []  
1                                  []  
2                                  []  
3  [Enterobacter cloacae ssp cloacae]  
4  [Enterobacter cloacae ssp cloacae]  
5  [Enterobacter cloacae ssp cloacae]  
6  [Enterobacter cloacae ssp cloacae]  

[7 rows x 31 columns]
```
환자별 균주 정보를 넣어주고
- 환자 '74374'를 확인해보면
- 날짜별 중증도(5), 임상 정보(22), 항생제 정보(2), 균주 리스트(1)까지 총 30개 feature가 통합 정리된 딕셔너리가 생성됐다.

```python
with open(f"{outdir}/Input.pkl", 'wb') as f:
    pickle.dump(input_dict, f)
```
만든건 저장하기.

### 4. Sequence 생성

```python
indir = 'res'

with open(f"{indir}/Input.pkl", 'rb') as f:
        input_dict = pickle.load(f)

pids = input_dict.keys().tolist()
cur_pid = pids[0]

print(cur_pid)
print(input_dict[cur_pid])
```
```plain text
        Date  NEWS  WHO  SOFA  PBS  qPitt  ALT (U/L)  AST (U/L)  BUN (mg/dL)  \
0 2020-10-30     4    5     0    0      0       43.0       79.0         16.9   
1 2020-10-31     4    5     1    0      0       71.0      149.0         21.5   
2 2020-11-01    12    5     5    1      2       83.0      149.0         30.8   
3 2020-11-02     9    5     6    1      2       83.0      149.0         19.2   
4 2020-11-03    12    5     5    1      1       83.0      149.0         20.2   
5 2020-11-04     8    5     6    2      1       83.0      149.0         22.5   
6 2020-11-05     9    5     7    4      2       83.0      149.0         22.5   

   Creatinine (mg/dL)  ...  Platelet count (10^3/uL)  Potassium (mmol/L)  \
0                0.68  ...                     395.0                 3.6   
1                1.22  ...                     340.0                 3.0   
2                1.42  ...                     272.0                 4.2   
3                0.93  ...                      83.0                 4.7   
4                0.77  ...                      61.0                 4.7   
5                0.84  ...                      67.0                 5.2   
6                0.84  ...                      67.0                 5.2    

   total CO2, calculated (mmol/L)  med_cnt                    med_list  \
0                            18.3        2          Trizele;Cefotaxime   
1                            18.3        2          Trizele;Cefotaxime   
2                            15.7        2         Pospenem;Pospenem_2   
3                            15.7        3  Pospenem;Meropen;Vanco Kit   
4                            31.0        2           Vanco Kit;Meropen   
5                            31.0        2           Vanco Kit;Meropen   
6                            31.0        0                               

                               strain  
0                                  []  
1                                  []  
2                                  []  
3  [Enterobacter cloacae ssp cloacae]  
4  [Enterobacter cloacae ssp cloacae]  
5  [Enterobacter cloacae ssp cloacae]  
6  [Enterobacter cloacae ssp cloacae]  

[7 rows x 31 columns]
```

환자별 임상 정보는 최소 5 / 최대 1202일 길이의 데이터인데
- 이를 항생제 투여일 기준 D-3~D+6만 남겨서
- 길이 10의 sequence로 만들어준다.

```python
indir = 'res'
outdir = 'data/res_dict'

make_sequence(med, indir, outdir)

res_list = os.listdir(outdir)
print(len(res_list))
```
```plain text
169
```

169개의 input sequence가 생성되었고
- outdir에 저장되었다.

```python
with open(f'{outdir}/Dexamethasone.pkl', 'rb') as f:
    dexamethasone = pickle.load(f)

cur_ids = cur_ids = list(dexamethasone.keys())
print(len(cur_ids))
print(cur_ids[0])
print(dexamethasone[cur_ids[0]])
```
```plain text
783
543865_0
         Date  NEWS  med_cnt                        strain
4  2017-08-18     4        0  [Staphylococcus epidermidis]
5  2017-08-19     4        0  [Staphylococcus epidermidis]
6  2017-08-20     4        0  [Staphylococcus epidermidis]
7  2017-08-21     4        1  [Staphylococcus epidermidis]
8  2017-08-22     3        1  [Staphylococcus epidermidis]
9  2017-08-23     4        1  [Staphylococcus epidermidis]
10 2017-08-24     4        1      [Pseudomonas aeruginosa]
11 2017-08-25     7        1      [Pseudomonas aeruginosa]
12 2017-08-26     4        1      [Pseudomonas aeruginosa]
13 2017-08-27     4        1      [Pseudomonas aeruginosa]
```

항생제 'dexamethasone'에 대해 생성된 sequence를 확인해보면
- 783개 sequence가 생성되었고
- 환자 543865의 첫번째 시퀀스 '543865_0'를 확인해보면
  - D-3~D+6인 것을 확인 가능하다!
