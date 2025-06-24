---
date : 2025-06-24
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#5 결과 검증: 계통 결정 돌연변이와 연관성"
bookComments: true
---

# #5 결과 검증: 계통 결정 돌연변이와 연관성

#2025-06-24

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

os.sys.path.append("/data/home/ysh980101/2506/mutclust") 
from Bin.sc import *

os.chdir("/data/home/ysh980101/2506/mutclust")
```
