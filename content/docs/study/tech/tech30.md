---
date : 2025-06-20
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#2 중요도 지표 계산"
bookComments: true
---

# #2 중요도 지표 계산

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

### 2. Load GISAID data

```python
indir = "/data/home/ysh980101/2407/Mutclust/Testdata/Input/"

Refseq = getNucleotideRefSeq()
GISAID_Freq = pd.read_csv(f'{indir}gisaid_freq_all.csv', index_col=0)
GISAID_meta = get_GISAID_meta()
print(GISAID_Freq)
```
```plain text
           A    C    G      T   R   Y   S   W   K   M  B  D  H  V       N
1      10612  390  415    785  11   1   3   4  24   2  1  2  0  0  219995
2        287  502  218  12942   3  31  14   4  61   0  1  2  1  0  218179
3        166  461  348  18168   1  12  29  10  15   1  0  1  1  0  213032
4      19398  267  502    972  12   5   1  33  37   6  1  1  0  1  211009
5      24962  281  334    699   6  21   6  17  15  10  5  1  1  1  205886
...      ...  ...  ...    ...  ..  ..  ..  ..  ..  .. .. .. .. ..     ...
29899  41707   36   38    100   1   0   2   5   0   3  0  0  1  0  190351
29900  40483   30   25     99   8   1   0   2   1   4  0  1  0  0  191590
29901  39258   25   19     22   1   0   0   4   1   1  0  0  0  0  192913
29902  38015   23   22     19   1   0   0   5   0   1  0  0  0  0  194158
29903  34729   18   32     99   0   3   0   4   0   3  0  0  1  0  197355

[29903 rows x 15 columns]
```

### 3. Calculate H-score

```python
def calculate_hscore(Refseq, Freq, N):
    freq_df = Freq[['A','T','G','C']].copy()
    for i,row in enumerate(Refseq):
        freq_df.iloc[i][row] = 0

    per_df = freq_df.apply(lambda row: row/row.sum(), axis=1)
    per_df = per_df.fillna(0)
    ent_df = per_df.apply(lambda row: entropy(row, base=2), axis = 1)
    ent_df = ent_df.fillna(0)

    count_df = freq_df.apply(lambda row: row.sum(), axis=1)
    ratio_df = freq_df.apply(lambda row: row.sum()/N, axis=1)
    hscore_df = np.log2(ratio_df*ent_df*100+1)

    Input_df = pd.concat([count_df, ratio_df, ent_df, hscore_df], axis=1, keys=[FREQ, PER, ENT, HSCORE])
    Input_df = Input_df.reset_index()
    Input_df = Input_df.rename(columns={'index': POS})  
    
    return Input_df
```
```python
N = len(GISAID_meta)

Input_df = calculate_hscore(Refseq, GISAID_Freq, N)
print(Input_df)
```
```plain text
       Position  Frequency  Percentage   Entropy   H-score
0             1       1590    0.007088  1.505823  1.047783
1             2       1007    0.004489  1.494709  0.740711
2             3        975    0.004347  1.476319  0.715176
3             4       1741    0.007761  1.401635  1.062019
4             5       1314    0.005858  1.462576  0.892773
...         ...        ...         ...       ...       ...
29898     29899        174    0.000776  1.408897  0.149631
29899     29900        154    0.000687  1.295297  0.122905
29900     29901         66    0.000294  1.575992  0.065393
29901     29902         64    0.000285  1.580312  0.063624
29902     29903        149    0.000664  1.236853  0.113909

[29903 rows x 5 columns]
```

### 4. Save

```python
Input_df.to_pickle(f"{indir}GISAID_total.pickle")
```
