---
date : 2026-04-09
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "NGS 파일명 변경"
pageHidden: true
---

# NGS 파일명 변경

#2026-04-09

---

#1

```python
import os
import pandas as pd

path = "X:/암빅데이터센터/160.[로슈]정밀 의료 생태계 구축 사업/11. my trial 자료/3. NGS report/20231207_11월분" # 주소 입력

dataframe_list = []
file_list = os.listdir(path)
file_list_py = [file for file in file_list]

for i in range(len(file_list_py)):
  data = file_list_py[i]
  dataframe_list.append(data)
  a = pd.DataFrame(dataframe_list)

a.to_excel("D:/NGS_파일명_11월분.xlsx") # 저장할 위치 및 파일명 지정
```


###

#2

```python
# cf) 코드 정리된 버전
import os
import pandas as pd

path = "X:/암빅데이터센터/160.[로슈]정밀 의료 생태계 구축 사업/11. my trial 자료/3. NGS report/20231207_11월분"
os.chdir(path)

file_list = os.listdir()
df = pd.DataFrame(file_list, columns=["파일명"])
df.to_excel("D:/NGS_파일명_11월분.xlsx", index=False)
```