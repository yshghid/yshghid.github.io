---
date : 2025-06-20
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#3 밀도 기반 클러스터링"
bookComments: true
---

# #3 밀도 기반 클러스터링

#2025-06-20

---

### 1. Load package

```python
import pandas as pd
import numpy as np
import os
os.sys.path.append("/data/home/ysh980101/2407/Mutclust") 

from pathlib import Path
from Bin.Utils.utils import *
from Bin.arg_parser import *
from Bin.mlib import *
```

### 2. Find CCMs

```python
i = 1
tag = f"test{i}"
input_path = "/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle"
outdir = f"/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_{tag}/"
Path(outdir).mkdir(parents=True, exist_ok=True)

info = set_env(input = input_path, output = outdir)
Input_df = readPickle(input_path)
init(Input_df, info)
mutInfo, ccms = get_candidate_core_mutations(Input_df, info, tag, i)
```
```plain text
--- Configurations ---
Input data: '/data/home/ysh980101/2407/Mutclust/Testdata/Input/GISAID_total.pickle' (29903, 5)
Output dir: '/data/home/ysh980101/2407/Mutclust/Testdata/Output/GISAID_test1/'
Parameters:
  Min Eps=5
  Max Eps=1000
  Min per_sum=0.0
  Eps scaling factor=10.0
  Expansion diminishing factor=3
  Min cluster length=10
----------------------

Searching candidate core mutations...

1990 CCMs found.
```
```python
sample_ccm = ccms[0]
mutInfo[sample_ccm]
```
```plain text
{'index': 11,
 'Position': 277,
 'Frequency': 86,
 'Percentage': 0.00038338430264178534,
 'Entropy': 0.6078847228873923,
 'H-score': 0.03323669788067187,
 'length': 12,
 'freq_sum': 1476,
 'freq_avr': 123.0,
 'per_sum': 0.0065799445430148274,
 'per_avr': 0.0005483287119179023,
 'ent_sum': 6.254087818941727,
 'ent_avr': 0.5211739849118106,
 'H-score_sum': 0.15877807556629392,
 'H-score_avr': 0.01323150629719116,
 'eps_scaler': 1,
 'left_distance': 5,
 'right_distance': 5,
 'l_pos': 272,
 'r_pos': 282,
 'mut_n': 11}
```

### 3. Perform clustering

```python
hotspots = dynaclust(mutInfo, ccms, info, tag, i)
```
```plain text
Performing dynamic clustering...
1990 clusters found
Merging clusters...
Merged clusters: 477
```
```python
print(hotspots)
```
```plain text
     left_position  right_position  length  \
0              272             290      19   
1              332             347      16   
2              358             392      35   
3              433             448      16   
4              482             495      14   
..             ...             ...     ...   
472          29568           29577      10   
473          29581           29599      19   
474          29613           29633      21   
475          29640           29651      12   
476          29654           29671      18   

                                         mut_positions  
0    272,273,274,275,277,278,279,280,281,282,283,28...  
1      332,334,335,336,337,338,341,343,344,345,346,347  
2    358,360,361,362,363,364,365,366,367,368,369,37...  
3    433,435,436,437,438,439,440,441,442,443,444,44...  
4              482,483,485,487,488,489,490,491,493,495  
..                                                 ...  
472    29568,29570,29571,29572,29573,29574,29575,29577  
473  29581,29583,29584,29585,29586,29587,29588,2958...  
474  29613,29615,29616,29617,29618,29619,29620,2962...  
475  29640,29641,29643,29645,29647,29648,29649,2965...  
476  29654,29655,29656,29657,29659,29660,29661,2966...  

[477 rows x 4 columns]
```
