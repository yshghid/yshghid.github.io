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

print(len(all_meds))
print(all_meds)
```
```plain text
169
['Sevatrim', 'Nystatin syrup', 'Fungizone', 'Vancozin', 'Gavir', 'Regkirona', 'Omnicef Granule_g', 'Pyrazinamide', 'Cotrim', 'Ubacsin', 'Netilmicin', 'Cycin', 'Amoxicle', 'Vancomycin HCl', 'Anycef', 'Valcyte', 'Septrin tab', 'Imicil Kit', 'Rifampin', 'Enped', 'Meropen', 'Valvirus', 'Azitops', 'Viramid', 'Cymevene', 'Flumarin', 'Yuhanzid', 'Foxolin', 'Vgavir', 'Suprax', 'Vivir', 'Cefetat', 'Pospenem', 'Minocin', 'Ceftazidime', 'Banan dry syrup', 'Vivaquine', 'Rifodex', 'Duricef', 'Tygacil', 'Amocla duo tab', 'Famvics', 'Baraclude', 'Veklury', 'Taurolin', 'Diflucan POS', 'Rulid', 'Klaricid Dry syrup', 'Teracycline', 'Closerin', 'Zithromax Dry Syrup', 'Tapocin', 'Zinperazone', 'Amoxapen', 'Prevymis', 'Trison Kit', 'Aclova', 'Doxycyclin', 'Cefazedone', 'Finipenem', 'Cefazolin', 'Epocelin', 'Ceftezole', 'Ciprobay', 'Adefovir', 'Tamiflu', 'Zavicefta', 'Nafcillin Sodium', 'Bearcef', 'Linoped', 'Prepenem', 'Roxithromycin', 'Cravit', 'Invanz', 'Tobra', 'Zeffix', 'Sporanox cap', 'Ampibactam', 'Levoplus', 'Itra', 'Cravit tab', 'Viread', 'Zithromax', 'Penbrex', 'Paxlovid', 'Myambutol', 'Daptocin', 'Finibax', 'Zyvox', 'Omnicef', 'Colis', 'Amoxicillin', 'Flasinyl', 'Pentamidine Isethionate', 'Kaletra', 'Adikan', 'Maxipime', 'Amikacin', 'Triaxone', 'Ceradolan', 'Moveloxin', 'Meiact', 'Hanmiflu solution', 'Avelox', 'Acillin', 'Entecabell ODT', 'Fullgram', 'Ceftil', 'Augmentin', 'Remdesivir', 'Lagevrio', 'Lamiffix', 'Ambisome', 'Monodoxy-M', 'Unasyn', 'Prezcobix', 'Ceftriaxone', 'Noxafil tab', 'Tiroxin', 'Rukasyn', 'Amikin', 'Prothionamide', 'Gentamicin', 'Hanomycin', 'Monurol', 'Mezactam', 'Plunazol', 'Cancidas', 'Citopcin', 'Claric', 'Isepacin', 'Oxiklorine', 'Nitrofurantoin', 'Combicin', 'Mycamine', 'Amocla Duo Syrup', 'Distocide', 'Rulid D', 'Meicelin', 'Tazocin', 'Vfend', 'Zerbaxa', 'Akocin', 'Yamatetan', 'Oneflu', 'Sebivo', 'Enteone', 'Trizele', 'Gomcephin', 'Amocla', 'Banan Dry Syrup', 'Synagis', 'Isepamicin', 'Famvir', 'Dexamethasone Inj', 'Sporanox Oral Solution', 'Pamoxin Dry Syrup', 'Vanco Kit', 'Factive', 'Cefotaxime', 'Casfun', 'Banan', 'Tubes', 'Eraxis', 'Ubacillin', 'Dexamethasone', 'Normix', 'Peramiflu', 'Vemlidy']
```

항생제 종류는 169종이고

각 항생제에 따라 NEWS sequence를 생성해서 input data를 만들 예정이다.

### Make sequence

```python
indir = 'res'
outdir = 'data/sev_dict'

for med in all_meds:
    print(med)
    sev_dict = make_sev_dict(med, indir, outdir)
```

항생제별로 sequence를 분리해서 위의 raw_data와 동일한 형식의 딕셔너리 169개를 outdir에 생성했다. 

이제 생성한 sequence의 길이를 10으로 맞출건데,

항생제 투여 시점 기준으로
- 투여 전 3일부터
- 투여 후 7일(D-3 ~ D+6)
- 10일짜리 NEWS sequence를 만들어줄 예정이다.


```python
#1
indir = 'data/sev_dict'
outdir = 'data/timecourse'

for med in all_meds:
    timecourse = make_timecourse(indir, outdir, med)

#2
indir = 'data/timecourse'
outdir = 'data/sev_idx'

for med in all_meds:
    sev_idx = make_sev_idx(indir, outdir, med)

#3
indir = 'data/sev_dict'
indexdir = 'data/sev_idx'
outdir = 'data/res_dict'

for med in all_meds:
    res_dict = make_res_dict(indir, indexdir, outdir, med)
```

각 항생제에 따라 10 day sequence를 생성해서 outdir에 저장했다.

```python
cur_path = 'data/res_dict'
cur_med = 'Dexamethasone'

with open(f"{cur_path}/{cur_med}", 'rb') as f:
    res_dict = pickle.load(f)

cur_keys = list(res_dict.keys())
print(len(cur_keys))
print(res_dict[cur_keys[0]])
```
```plain text
783
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

항생제 'Dexamethasone'에서 생성된 sequence를 확인해보면
- 783개 sequence가 생성되었고
- 투여일(21일) 기준으로 투여전 3일, 투여후 7일로 잘 생성된것을 확인 가능하다!




