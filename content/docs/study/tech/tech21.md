---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#2 입력 feature 생성"
bookComments: true
---

# #2 입력 feature 생성

#2025-06-17

---

### 1. Load package

```python
%load_ext autoreload
%autoreload 2

import sys
import os

import pickle
import matplotlib.pyplot as plt
from matplotlib.backends.backend_pdf import PdfPages
import pandas as pd

sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin')
from sc import *

os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### 2. Previous

```python
seqdir = 'data/res_dict'
seq_list = os.listdir(seqdir)
print(len(seq_list))
```
```plain text
169
```

항생제 169종에 대해서 size 10 sequence를 생성했었는데
- 모델 입력 feature로 다음을 제외하는대신
  1) antibiotics 리스트
  2) strain 리스트
- 저 2개 feature를 반영하는 새로운 feature를 2개 생성하려고 한다:
  1) 현재 antibiotics가 현재 strain 환자의 NEWS를 감소시킨 이력이 있는지? (binary: 0/1)
  2) 현재 antibiotics가 NEWS를 감소시키는데 소요 기간은? (범주형: short/mid/long)

### 3. Create feature1

먼저 feature1을 생성하기 위해 
- 투여 후 NEWS가 감소한 sequence를 남기고
- keep된 sequence의 균주-항생제 pair를 얻는데
- 이때 '투여 후 NEWS의 감소'는?
  - 투여 전날(D-1) NEWS 수치와
  - 투여 후 7일(D+0~D+6)를 봣을때
  - 투여 후 최고치가 투여 전날보다 낮으면 NEWS가 감소한 것으로 보았다.

```python
med_dir = 'res'
seqdir = 'data/res_dict'
outdir = 'res/feature1'

with open(f"{med_dir}/all_meds.txt", 'r') as f:
    all_meds = [line.strip() for line in f if line.strip()]
    all_meds = [s.replace("/", "_") for s in all_meds]

for med in all_meds:
    print(med)
    cur_path = f'{seqdir}/{med}.pkl'

    with open(cur_path, 'rb') as f:
        res_dict = pickle.load(f)
        
    feature1_list = []

    for pid, df in res_dict.items():
        news_bf = df.iloc[2]['NEWS']  # 3번째 행 (0-indexed)
        news_af = df.iloc[3:]['NEWS'].max()  # 4번째 행부터 마지막까지 중 최댓값

        if news_af < news_bf:  # "작은" 경우만 (같은 건 포함하지 않음)
            feature1_list.append(pid)
    
    print(len(feature1_list))
    filtered_res_dict = {pid: res_dict[pid] for pid in feature1_list if pid in res_dict}
    
    with open(f"{outdir}/{med}.pkl", 'wb') as f:
        pickle.dump(filtered_res_dict, f)
```

 ![image](https://github.com/user-attachments/assets/83a8ab05-f9c6-40d6-89ff-412c4f71cfa8)

Dexamethasone에 대해 selected sequence를 시각화한것을 보면
- 투여일(점선) 이후의 NEWS 수치들이 투여전날보다 낮은 것만 잘 선택된것을 확인 가능하다!

```python
strain_dic = {}

for med in all_meds:
    cur_path = f'{outdir}/{med}.pkl'

    with open(cur_path, 'rb') as f:
        filtered_res_dict = pickle.load(f)

    for pid, df in filtered_res_dict.items():
        if len(df) < 3:
            continue  # 3번째 행이 없는 경우 skip

        try:
            cur_strain = df.iloc[2]['strain']
            if isinstance(cur_strain, list):
                strains = cur_strain
            else:
                strains = [cur_strain]
        except Exception as e:
            continue

        for strain in strains:
            if strain in strain_dic:
                strain_dic[strain].append(med)
            else:
                strain_dic[strain] = [med]

for strain in strain_dic:
    strain_dic[strain] = list(set(strain_dic[strain]))
```

keep된 sequence의 균주-항생제 pair를 얻을 때는
- 각 항생제에 대해
  - selected sequence의 투여 전날(D-1) 균주(들)에 해당 항생제 매핑
  - 하는 방식으로 수행했다.

```python
strains = list(strain_dic.keys())
print(len(strains))
print(strains[0], '\n', strain_dic[strains[0]])
```
```plain text
98
Pseudomonas aeruginosa 
 ['Imicil Kit', 'Prepenem', 'Sevatrim', 'Meropen', 'Finibax', 'Ciprobay', 'Hanomycin', 'Citopcin', 'Tazocin', 'Dexamethasone', 'Pospenem', 'Tygacil', 'Pentamidine Isethionate', 'Lagevrio', 'Plunazol', 'Vancomycin HCl']
```

균주별 효과 항생제 딕셔너리 strain_dic를 확인해보면 
- 98개 균주에 대해
- 효능을 보인(것으로 추정되는) 항생제 목록이 제대로 생성돼있다!

```python
ourdir = 'res'

with open(f"{outdir}/Feature1.pkl", 'wb') as f:
    pickle.dump(strain_dic, f)
```

만든건 저장하기.
    
### 4. Create feature2

feature2는 솔직히 좀 애매한데 로직을 짜보면
- 일단 투여 후 NEWS가 감소한 sequence를 모두 모으고
- '일정 수준'이하로 감소하는데 소요된 시간을 봐서 (ex. 3이하는 moderate니까 3까지 도달하는데 소요된 날짜)
  - 상위 30%/하위30%/나머지 << 이런 식으로 가려고 했으나?
  - sequence의 선택 기준이 '투여 전날 news'로써
    - sequence마다 기준이 달랐기때문에
    - y축 즉 news 범위가 다 달라서 절대적인 값으로 설정하기 어려울거같다. (ex. 투여 전날 최고치가 3보다 낮을 수도 있음. 또는 투여후 3 아래로 안떨어지는 날이 있을수도있음)
  - 그래서 상대적인 값으로 볼까 했는데?
  - 기준을 '절반 이하로 떨어지기'로 잡는다고 치면
    - news가
      1. 전날 12 -> 8로 감소
      2. 전날 3 -> 1.5로 감소
      - 인 경우 1은 좋은 데이터지만 non selected 되어 라벨링되지않고 2는 별로인 데이터지만 selected 되어 라벨링되게된다.

결론: feature2는 일단 보류하기.
