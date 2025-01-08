---
date : 2025-01-08
weight: 605
tags: ['2025-01']
categories: ['BI']
bookHidden: true
title: "BI"
---

# Pathway enrichment bar plot of GO terms

##### 2025-01-08

---

## Load package

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import os
import math
```

## Set path

```python
os.chdir("/data/home/ysh980101/2402/Data")
os.chdir("/data-blog/bi7")
os.getcwd()
```
```plain text
'/data-blog/bi7'
```

## Draw bar plot

```python
# plot1
data_to_plot = enrichr_c1.head(10)
data_to_plot['-log10(P-value)'] = -np.log10(data_to_plot['P-value'])

plt.figure(figsize=(10,6))
bars = plt.barh(data_to_plot.index[::-1], data_to_plot['-log10(P-value)'], color='skyblue')
for bar in bars:
    plt.text(bar.get_width(), bar.get_y() + bar.get_height()/2.0, 
             f"{data_to_plot['Term'][bars.index(bar)]}   *{data_to_plot['P-value'][bars.index(bar)]:.2e}", 
             ha='right', va='center', color='black', fontsize=7)

plt.xlabel('-Log10(P-value)')
plt.title('GO Biological Process 2023')
plt.yticks([])
plt.xlim(0, 4)
plt.xticks(np.arange(0, 5.0, 1.0))
plt.show()
```
![image](https://github.com/user-attachments/assets/e2fe7b43-6c19-4de6-b02d-345c18a523bd)

```python
# plot2: color scaled by p-value
data_to_plot = enrichr_c1.head(10)
data_to_plot['-log10(P-value)'] = -np.log10(data_to_plot['P-value'])

plt.figure(figsize=(10,5))
colors = np.array([[0.97176471, 0.36653595, 0.25882353, 1.        ],
                      [0.98357555, 0.41279508, 0.28835063, 1.        ],
                      [0.98535948, 0.45751634, 0.33202614, 1.        ],
                      [0.98646674, 0.501807  , 0.3763168 , 1.        ],
                      [0.98646674, 0.501807  , 0.3763168 , 1.        ],
                      [0.98646674, 0.501807  , 0.3763168 , 1.        ],
                      [0.98646674, 0.501807  , 0.3763168 , 1.        ],
                      [0.98823529, 0.58579008, 0.4622376 , 1.        ],
                      [0.98823529, 0.67154171, 0.56053825, 1.        ],
                  [0.98823529, 0.77154171, 0.66053825, 1.        ]])
bars = plt.barh(data_to_plot.index[::-1], data_to_plot['-log10(P-value)'], color=colors)
for bar, color in zip(bars, colors):
    plt.text(bar.get_width(), bar.get_y() + bar.get_height()/2.0, 
             f"{data_to_plot['Term'][bars.index(bar)]}   *{data_to_plot['P-value'][bars.index(bar)]:.2e}", 
             ha='right', va='center', color='black', fontsize=7.0)

plt.xlabel('-Log10(P-value)')
plt.title('GO Biological Process 2023')
plt.yticks([])
plt.xlim(0, 4)
plt.xticks(np.arange(0, 5.0, 1.0))
plt.show()
```
![image](https://github.com/user-attachments/assets/556837db-81f4-43c8-beae-b5bc05be3939)

### additional data

코드에 사용된 데이터 정보는 [github](https://github.com/yshghid/data/tree/main/data-blog/bi9)에서 확인 가능하다.
