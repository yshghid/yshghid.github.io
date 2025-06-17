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

import pickle
import matplotlib.pyplot as plt
from matplotlib.backends.backend_pdf import PdfPages
import pandas as pd

sys.path.append('/data3/projects/2025_Antibiotics/YSH/bin')
from sc import *

os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### Set path
```python
os.chdir('/data3/projects/2025_Antibiotics/YSH')
```

### Load data

```python
indir = 'res'

with open(f"{indir}/all_meds.txt", 'r') as f:
    all_meds = [
        line.strip().replace("/", "_")
        for line in f
        if line.strip()
    ]

print(len(all_meds))
print(all_meds)
```
```plain text
169
['Sevatrim', 'Nystatin syrup', 'Fungizone', 'Vancozin', 'Gavir', 'Regkirona', 'Omnicef Granule_g', 'Pyrazinamide', 'Cotrim', 'Ubacsin', 'Netilmicin', 'Cycin', 'Amoxicle', 'Vancomycin HCl', 'Anycef', 'Valcyte', 'Septrin tab', 'Imicil Kit', 'Rifampin', 'Enped', 'Meropen', 'Valvirus', 'Azitops', 'Viramid', 'Cymevene', 'Flumarin', 'Yuhanzid', 'Foxolin', 'Vgavir', 'Suprax', 'Vivir', 'Cefetat', 'Pospenem', 'Minocin', 'Ceftazidime', 'Banan dry syrup', 'Vivaquine', 'Rifodex', 'Duricef', 'Tygacil', 'Amocla duo tab', 'Famvics', 'Baraclude', 'Veklury', 'Taurolin', 'Diflucan POS', 'Rulid', 'Klaricid Dry syrup', 'Teracycline', 'Closerin', 'Zithromax Dry Syrup', 'Tapocin', 'Zinperazone', 'Amoxapen', 'Prevymis', 'Trison Kit', 'Aclova', 'Doxycyclin', 'Cefazedone', 'Finipenem', 'Cefazolin', 'Epocelin', 'Ceftezole', 'Ciprobay', 'Adefovir', 'Tamiflu', 'Zavicefta', 'Nafcillin Sodium', 'Bearcef', 'Linoped', 'Prepenem', 'Roxithromycin', 'Cravit', 'Invanz', 'Tobra', 'Zeffix', 'Sporanox cap', 'Ampibactam', 'Levoplus', 'Itra', 'Cravit tab', 'Viread', 'Zithromax', 'Penbrex', 'Paxlovid', 'Myambutol', 'Daptocin', 'Finibax', 'Zyvox', 'Omnicef', 'Colis', 'Amoxicillin', 'Flasinyl', 'Pentamidine Isethionate', 'Kaletra', 'Adikan', 'Maxipime', 'Amikacin', 'Triaxone', 'Ceradolan', 'Moveloxin', 'Meiact', 'Hanmiflu solution', 'Avelox', 'Acillin', 'Entecabell ODT', 'Fullgram', 'Ceftil', 'Augmentin', 'Remdesivir', 'Lagevrio', 'Lamiffix', 'Ambisome', 'Monodoxy-M', 'Unasyn', 'Prezcobix', 'Ceftriaxone', 'Noxafil tab', 'Tiroxin', 'Rukasyn', 'Amikin', 'Prothionamide', 'Gentamicin', 'Hanomycin', 'Monurol', 'Mezactam', 'Plunazol', 'Cancidas', 'Citopcin', 'Claric', 'Isepacin', 'Oxiklorine', 'Nitrofurantoin', 'Combicin', 'Mycamine', 'Amocla Duo Syrup', 'Distocide', 'Rulid D', 'Meicelin', 'Tazocin', 'Vfend', 'Zerbaxa', 'Akocin', 'Yamatetan', 'Oneflu', 'Sebivo', 'Enteone', 'Trizele', 'Gomcephin', 'Amocla', 'Banan Dry Syrup', 'Synagis', 'Isepamicin', 'Famvir', 'Dexamethasone Inj', 'Sporanox Oral Solution', 'Pamoxin Dry Syrup', 'Vanco Kit', 'Factive', 'Cefotaxime', 'Casfun', 'Banan', 'Tubes', 'Eraxis', 'Ubacillin', 'Dexamethasone', 'Normix', 'Peramiflu', 'Vemlidy']
```
