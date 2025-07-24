---
date : 2025-07-23
tags: ['2025-07']
categories: ['AI']
bookHidden: true
title: "TFT #1 입력 시퀀스 생성"
---

# TFT #1 입력 시퀀스 생성

#2025-07-23

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

os.chdir('/data3/projects/2025_Antibiotics/YSH/workspace')
```

### 2. Load raw data

#data

```plain text
/data
├── PreprocessedData/
│   └── TimecourseData/
│       └── * (*: patient id)
│           ├── SeverityScore.csv
│           ├── Laboratory_processed.csv 
│           └── Medication.csv
├── PreprocessedData_knuh/
│    └── (PreprocessedData와 동일)
└── 병원체자원은행 균주현황(2014-2024.06)_Sepsis.xlsx

/data_knuch
└── (empty)

/data_knuh
└── (empty)
```

```python
data_knuch = '/data/PreprocessedData/TimecourseData'
data_knuh = '/data/PreprocessedData_knuh/TimecourseData'

pids = [d for d in os.listdir(data_knuch)] + [d for d in os.listdir(data_knuh)]
len(pids)
```
```plain text
13779
```

### 3. Raw data processing

```python
#processing knuch
datadir = '/data/PreprocessedData/TimecourseData'
pids = [d for d in os.listdir(datadir)]

input_dict = make_input(datadir, pids)
input_dict, no_strains = add_strain(input_dict)

outdir = "data_knuch"
with open(f"{outdir}/Input.pkl", 'wb') as f:
    pickle.dump(input_dict, f)
print(len(list(input_dict.keys())))
print(len(no_strains))
```
```plain text
4516
4
```

```python
#processing knuh
datadir = '/data/PreprocessedData_knuh/TimecourseData'
pids = [d for d in os.listdir(datadir)]

input_dict = make_input(datadir, pids)
input_dict, no_strains = add_strain(input_dict)

outdir = "data_knuh"
with open(f"{outdir}/Input.pkl", 'wb') as f:
    pickle.dump(input_dict, f)
print(len(list(input_dict.keys())))
print(len(no_strains))
```
```plain text
9100
1
```

#result

```plain text
/data
├── PreprocessedData/
│   └── TimecourseData/
│       └── * (*: patient id)
│           ├── SeverityScore.csv
│           ├── Laboratory_processed.csv 
│           └── Medication.csv
├── PreprocessedData_knuh/
│    └── (PreprocessedData와 동일)
└── 병원체자원은행 균주현황(2014-2024.06)_Sepsis.xlsx

/data_knuch
└── Input.pkl

/data_knuh
└── Input.pkl
```

### 4. Make input sequence

#data

```plain text
/data
└── all_meds.txt

/data_knuch
├── Input.pkl
└── sequence
     └── (empty)

/data_knuh
├── Input.pkl
└── sequence
     └── (empty)
```

```python
dtype = 'knuh'

indir = f'data_{dtype}'
medinfo = '/data/all_meds.txt'

with open(medinfo, 'r') as f:
    meds = [line.strip().replace("/", "_") for line in f if line.strip()]
    
with open(f"{indir}/Input.pkl", 'rb') as f:
        input_dict = pickle.load(f)

pids = list(input_dict.keys())
outdir = f'data_{dtype}/sequence'

for med in meds:
    make_sequence(med, indir, outdir)
```

#result

```plain text
/data
└── all_meds.txt

/data_knuch
├── Input.pkl
└── sequence
     └── *.pkl (*: antibiotics)

/data_knuh
├── Input.pkl
└── sequence
     └── *.pkl (*: antibiotics)
```

#functions

<details>
  <summary> functions used in this code </summary>
    
    import pandas as pd
    import numpy as np
    import os
    import pickle
    import ast
    
    from sklearn.cluster import KMeans
    from sklearn.metrics import silhouette_score
    import matplotlib.pyplot as plt
    import numpy as np
    import math
    
    import warnings
    
    from matplotlib.backends.backend_pdf import PdfPages
    
    os.chdir('/data3/projects/2025_Antibiotics/YSH/')
    
    ### 2 ###
    
    def make_input(datadir, pids):
        sev_dict = {}
        sev_dict_filtered = {}
    
        for pid in pids:
            try:
                # 1. SeverityScore 불러오기
                sev = pd.read_csv(f"{datadir}/{pid}/SeverityScore.csv")
    
                # 2. Laboratory 데이터 불러오기 및 병합
                lab = pd.read_csv(f"{datadir}/{pid}/Laboratory_processed.csv")
                sev = pd.merge(sev, lab, on='Date', how='left')
    
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
    
            # Remove med cnt 0
            if 'med_cnt' not in sev.columns:
                print(f"{pid} - Error: 'med_cnt' column doesn't exist")
                continue
            sev['med_cnt'] = pd.to_numeric(sev['med_cnt'], errors='coerce')
    
            if sev['med_cnt'].isna().all():
                continue
    
            if sev['med_cnt'].fillna(0).eq(0).all():
                continue
    
            sev_dict_filtered[pid] = sev
            
        return sev_dict_filtered
        
    def add_strain(sev_dict_filtered):
        strain_path = '/data3/projects/2025_Antibiotics/data/병원체자원은행 균주현황(2014-2024.06)_Sepsis.xlsx'
        strain_df = pd.read_excel(strain_path)
    
        valid_columns = strain_df.iloc[0].dropna().index
        strain_legend = strain_df.loc[:1, valid_columns].copy()
    
        strain_df.columns = strain_df.iloc[1]
        strain_df = strain_df.drop(index=[0, 1])
        strain_df = strain_df[['접수일', '등록번호', '균']]
        strain_df['등록번호'] = strain_df['등록번호'].astype(int)
    
        valid_ids = [int(k) for k in sev_dict_filtered.keys()]
        #strain_df['접수번호'] = strain_df['접수번호'].astype(str).astype(int)
        strain_df = strain_df[strain_df['등록번호'].isin(valid_ids)].reset_index(drop=True)
    
        strain_df = strain_df.drop_duplicates(subset=['균', '등록번호'], keep='first').reset_index(drop=True)
        strain_df['접수일'] = pd.to_datetime(strain_df['접수일'])
        strain_df['접수일'] = strain_df['접수일'].dt.strftime('%Y-%m-%d')
    
        # 등록번호 컬럼을 str로 변환 (딕셔너리 key와 비교하기 위함)
        strain_df['등록번호'] = strain_df['등록번호'].astype(str)
    
        pid_without_strains = []   
    
        for cur_key, cur_sev_df in sev_dict_filtered.items():
            cur_key_str = str(cur_key)
    
            # 등록번호가 현재 key인 행들 필터링
            cur_df = strain_df[strain_df['등록번호'] == cur_key_str]
    
            if cur_df.empty:
                print(cur_key)
                continue
    
            # 날짜 변환
            cur_sev_df['Date'] = pd.to_datetime(cur_sev_df['Date'])
            cur_df['접수일'] = pd.to_datetime(cur_df['접수일'])
    
            for _, row in cur_df.iterrows():
                cur_date = row['접수일']
                cur_strain = row['균']
    
                # 접수일과 같거나 이후인 첫 행의 인덱스 찾기
                matched_idx = cur_sev_df[cur_sev_df['Date'] >= cur_date].index.min()
    
                if pd.isna(matched_idx):
                    continue  # 매칭된 날짜가 없다면 skip
    
                # strain 컬럼이 없다면 빈 리스트 생성
                if 'strain' not in cur_sev_df.columns:
                    cur_sev_df['strain'] = [[] for _ in range(len(cur_sev_df))]
    
                # strain 컬럼이 리스트 형식이 아니면 변환
                if not isinstance(cur_sev_df.at[matched_idx, 'strain'], list):
                    cur_sev_df.at[matched_idx, 'strain'] = []
    
                # 중복 방지를 원할 경우 다음 줄에 조건 추가 가능
                cur_sev_df.at[matched_idx, 'strain'].append(cur_strain)
    
            # strain 컬럼이 없는 경우 스킵
            if 'strain' not in cur_sev_df.columns:
                pid_without_strains.append(cur_key_str)
                continue
    
            cur_sev_df = cur_sev_df.reset_index(drop=True)
    
            last_strain = []  # 최근에 발견된 strain 리스트
            for i in range(len(cur_sev_df)):
                current_strain = cur_sev_df.at[i, 'strain']
    
                if isinstance(current_strain, list) and len(current_strain) > 0:
                    # 비어있지 않은 strain 리스트 발견 → 이를 저장
                    last_strain = current_strain
                elif isinstance(current_strain, list) and len(current_strain) == 0:
                    # 비어있다면 → 가장 최근의 strain 리스트를 할당
                    cur_sev_df.at[i, 'strain'] = last_strain.copy()
    
            # 각 strain 리스트에서 중복 제거
            cur_sev_df['strain'] = cur_sev_df['strain'].apply(
                lambda x: list(set(x)) if isinstance(x, list) else x
            )
    
            # 다시 딕셔너리에 반영
            sev_dict_filtered[cur_key] = cur_sev_df
            
        return sev_dict_filtered, pid_without_strains
    
        
    ### 3 ###
    
    def make_sev_dict(med, indir, outdir):
        with open(f"{indir}/Input.pkl", 'rb') as f:
            sev_dict_filtered = pickle.load(f)
        
        if med != "total":
            for pid, cur_df in sev_dict_filtered.items():
                try:
                    cur_df['cur_med_list'] = cur_df['med_list'].fillna('').apply(lambda x: x.split(';'))
                    cur_df['med_cnt'] = cur_df['cur_med_list'].apply(
                        lambda meds: sum([1 for item in meds if item.startswith(med)])
                    )
                    #cur_df = cur_df[['Date', 'NEWS', 'med_cnt', 'strain']]
                    #print(cur_df.shape)
                    sev_dict_filtered[pid] = cur_df
                except KeyError as e:
                    print(f"[{pid}] 건너뜀 - 누락 컬럼: {e}")
                    continue
        else:
            for pid, cur_df in sev_dict_filtered.items():
                try:
                    #cur_df = cur_df[['Date', 'NEWS', 'med_cnt', 'strain']]
                    sev_dict_filtered[pid] = cur_df
                except KeyError as e:
                    print(f"[{pid}] 건너뜀 - 누락 컬럼: {e}")
                    continue
    
        with open(f"{outdir}/{med}.pkl", 'wb') as f:
            pickle.dump(sev_dict_filtered, f)
        
        return sev_dict_filtered
    
    def summarize_med_cnt(med_cnt_series):
        info_list = []
        if med_cnt_series.empty:
            return info_list
    
        cur_type = 'm' if med_cnt_series.iloc[0] > 0 else 'n'
        count = 1
    
        for prev, curr in zip(med_cnt_series[:-1], med_cnt_series[1:]):
            curr_type = 'm' if curr > 0 else 'n'
            if curr_type == cur_type:
                count += 1
            else:
                info_list.append(f"{count}{cur_type}")
                cur_type = curr_type
                count = 1
    
        info_list.append(f"{count}{cur_type}")
        return info_list
    
    def make_timecourse(indir, outdir, med):
        with open(f"{indir}/{med}.pkl", 'rb') as f:
            sev_dict_filtered = pickle.load(f)
    
        timecourse_list = []
    
        for pid, sev in sev_dict_filtered.items():
            if 'med_cnt' not in sev.columns or sev['med_cnt'].isnull().all():
                print(f"{pid} - Error: 'med_cnt' column doesn't exist")
                continue
    
            sev['med_cnt'] = pd.to_numeric(sev['med_cnt'], errors='coerce').fillna(0).astype(int)
    
            info_list = summarize_med_cnt(sev['med_cnt'])
    
            timecourse_list.append({
                'pid': pid,
                'days': len(sev),
                'info': info_list
            })
    
        timecourse = pd.DataFrame(timecourse_list)
        timecourse.to_csv(f"{outdir}/{med}.csv", index=False)
        
        return timecourse
    
    def filter_info(info_list):
        has_m_prefix = any(item.startswith('m') for item in info_list)
        has_m_suffix = any(item.endswith('m') for item in info_list)
        has_n_suffix = any(item.endswith('n') for item in info_list)
    
        if has_m_prefix:
            return False
        if all(item.endswith('m') for item in info_list):
            return False
        if all(item.endswith('n') for item in info_list):
            return False
        return True
    
    def convert_info_with_repeats(info_list):
        result = ''
        for item in info_list:
            if item.endswith('n'):
                num = int(item[:-1])
                result += '_' * num
            elif item.endswith('m'):
                num = int(item[:-1])
                result += 'm' * num
        return result
    
    def find_valid_start_end(s):
        valid_starts = []
        valid_ends = []
        for i in range(len(s) - 3):
            if s[i:i+4] == '___m':
                end = i + 9
                if end < len(s):
                    valid_starts.append(i)
                    valid_ends.append(end)
        return pd.Series({'start_idx': valid_starts, 'end_idx': valid_ends})
    
    #def find_valid_start_end(s):
    #    valid_starts = []
    #    valid_ends = []
    #    for i in range(len(s) - 7): 
    #        if s[i:i+8] == '_______m':
    #            end = i + 13  
    #            if end < len(s):
    #                valid_starts.append(i)
    #                valid_ends.append(end)
    #    return pd.Series({'start_idx': valid_starts, 'end_idx': valid_ends})
    
    def make_sev_idx(indir, outdir, med):
        timecourse = pd.read_csv(f"{indir}/{med}.csv")
    
        if isinstance(timecourse['info'].iloc[0], str):
            timecourse['info'] = timecourse['info'].apply(ast.literal_eval)
    
        timecourse = timecourse[timecourse['info'].apply(filter_info)].reset_index(drop=True)
        timecourse['info_timecourse'] = timecourse['info'].apply(convert_info_with_repeats)
        
        if not timecourse.empty:
            timecourse[['start_idx', 'end_idx']] = timecourse['info_timecourse'].apply(find_valid_start_end)
            timecourse = timecourse[~((timecourse['start_idx'].apply(len) == 0) & (timecourse['end_idx'].apply(len) == 0))].reset_index(drop=True)
        
        timecourse.to_csv(f"{outdir}/{med}.csv", index=False)
    
        return timecourse
    
    
    def make_res_dict(indir, indexdir, outdir, med):
        
        with open(f"{indir}/{med}.pkl", 'rb') as f:
            sev_dict_filtered = pickle.load(f)
    
        sev_idx = pd.read_csv(f"{indexdir}/{med}.csv")
        
        res_dict = {}
    
        if not sev_idx.empty:  
            if isinstance(sev_idx['start_idx'].iloc[0], str):
                sev_idx['start_idx'] = sev_idx['start_idx'].apply(ast.literal_eval)
    
            if isinstance(sev_idx['end_idx'].iloc[0], str):
                sev_idx['end_idx'] = sev_idx['end_idx'].apply(ast.literal_eval)
    
            for _, row in sev_idx.iterrows():
                cur_pid = row['pid']
                cur_start_list = row['start_idx']
                cur_end_list = row['end_idx']
                cur_df = sev_dict_filtered[str(cur_pid)]
    
                for i in range(len(cur_start_list)):
                    cur_start_idx = cur_start_list[i]
                    cur_end_idx = cur_end_list[i]
                    cur_filtered_df = cur_df.iloc[cur_start_idx:cur_end_idx+1].copy()
                    key = f"{cur_pid}_{i}"
                    res_dict[key] = cur_filtered_df
    
        with open(f"{outdir}/{med}.pkl", 'wb') as f:
            pickle.dump(res_dict, f)
            
        return res_dict
    
    def make_sequence(med, indir, outdir):
        sevdir = f'data_{dtype}/temp/sev_dict'
        sev_dict = make_sev_dict(med, indir, sevdir)
        
        timecourse_dir = f'data_{dtype}/temp/timecourse'
        timecourse = make_timecourse(sevdir, timecourse_dir, med)
        
        idx_dir = f'data_{dtype}/temp/sev_idx'
        sev_idx = make_sev_idx(timecourse_dir, idx_dir, med)
        
        res_dict = make_res_dict(sevdir, idx_dir, outdir, med)
        print(f'Sequence saved as {outdir}/{med}.pkl')
</details>


#
