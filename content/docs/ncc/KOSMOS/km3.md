---
date : 2026-04-09
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "FMI #1 FMI 파일명 변경"
---

# FMI #1 FMI 파일명 변경

#2026-04-09

---

#1

```python
# 모듈 저장 위치 확인 (해당 위치에 rename.py) 모듈 저장되어 있어야 함.
import os
os.getcwd()
```
```plain text
'C:/Users/User/Documents'
```
```python
# FMI 파일명 변경
# 주소 변경 필요한 변수: save_path

import shutil
import pandas as pd

# summary에서 대상자 id 가져오기
summary_path = "X:/암빅데이터센터/160.[로슈]정밀 의료 생태계 구축 사업/04. (로슈)공유 자료/5. FMI file(PDF, XML)"
file_name = 'FMI summary.xlsx'

summary2 = pd.read_excel(summary_path + '/' + file_name, header=1, usecols=['확인일', '대상자 아이디', '파일명', '파일 확장자'])
summary2['파일명 수정'] = summary2['파일명'] + '.' + summary2['파일 확장자']

save_path = 'X:/암빅데이터센터/160.[로슈]정밀 의료 생태계 구축 사업/04. (로슈)공유 자료/5. FMI file(PDF, XML)/20251001_FMI file' # 원본 파일 저장 위치
change_path = save_path + '_파일명변경' # 사본 폴더 생성(변경 파일 저장 위치)

summary2 = summary2.loc[summary2.확인일.isin([save_path[71:79][0:4] + '-' + save_path[71:79][6:8]])] # 마지막 확인일에 해당하는 파일 리스트만 추출
```
```python
shutil.copytree(save_path, change_path)
file_names = os.listdir(change_path) # 사본 폴더에 있는 항목들의 이름을 달고 있는 리스트를 반환
len(file_names) # 4
```
```python
summary2 = summary2.loc[summary2.파일명_수정.isin(file_names),['대상자 아이디', '파일명_수정']].reset_index() # 해당 월 파일의 대상자 아이디만 summary에서 추출
len(summary) # 이번달 summary에 작성된 파일 개수 출력 # 4
```
```python
# 파일 이름 변경 함수 적용
from rename import F_change
blank_plus = F_change(file_names, summary2)

# 변경된 파일 이름으로 파일 재저장
for i in range(len(file_names)):
  os.renames(change_path + '/' + file_names[i], change_path + '/' + blank_plus[i])

# 파일명 변경 정보(AS-IS, TO-BE)
rename_file_names = pd.DataFrame({"AS-IS": file_names, "TO-BE": blank_plus})
rename_file_names.to_excel(change_path + "/rename_file_names.xlsx") # 저장할 위치 및 파일명 지정
```

###

#2 

```python
# rename.py

#!/usr/bin/env python
# coding: utf-8

# NGS 파일 이름 변경용 함수

def N_change(file_names):
  
  import os
  import shutil
  import pandas as pd

  file_names_list = []
  name_plus = []
  blank_plus = []
  pdf_plus = []
  bracket_plus = []

  for i in range(len(file_names)):
    file_names_list.append(file_names[i][1:9])
    name_plus.append(file_names_list[i] + '_' + file_names[i]) # 대상자 아이디 + _ + 기존 파일명
    blank_plus.append(name_plus[i].replace(' ', '_')) # 공백 -> '_'로 변경
    pdf_plus.append(blank_plus[i].replace('.pdf.pdf', '.pdf')) # '.pdf.pdf' 확장자 -> '.pdf'로 변경
    bracket_plus.append(pdf_plus[i].replace('[]', '').replace(']', '')) # 대괄호 ('[]', ']' 제거)
  
  return(bracket_plus)

# VCF, 병리결과지 파일 이름 변경용 함수

def VR_change(file_names, summary):

  name_plus = []
  blank_plus = []
  vcf_plus = []
  bracket_plus = []

  for i in range(len(file_names)):
    a = summary.loc[summary["파일명_수정"] == str(file_names[i])]['대상자 아이디'] + '_' + str(file_names[i]) # 대상자 아이디 + _ + 기존 파일명
    name_plus.append(a.values[0])
    blank_plus.append(name_plus[i].replace(' ', '_')) # 공백 -> '_'로 변경
    vcf_plus.append(blank_plus[i].replace('.vcf.vcf','.vcf')) # '.vcf.vcf’확장자 → '.vcf’로 변경
    bracket_plus.append(vcf_plus[i].replace('[','').replace(']','')) # 대괄호(‘[', ‘]’) → 제거
    
  return(bracket_plus)

#FMI 파일 이름 변경용 함수

def F_change(file_names, summary2):
  name_plus = []
  blank_plus = []

  for i in range(len(file_names)):
    a = summary2.loc[summary2["파일명_수정"]==str(file_names[i])]['대상자 아이디']+'_'+str(file_names[i]) # 대상자 아이디 + _ + 기존 파일명
    name_plus.append(a.values[0])
    blank_plus.append(name_plus[i].replace(' ','_')) # 공백 → ‘_’로 변경
  
  return(blank_plus)

#WGS 파일 이름 변경용 함수

def W_change(file_names, summary3):

  name_plus = []
  blank_plus = []

  for i in range(len(file_names)):
    a = summary3.loc[summary3["파일명"]==str(file_names[i])]['대상자 아이디']+'_'+str(file_names[i]) #대상자 아이디 + _ + 기존 파일명
    name_plus.append(a.values[0])
    blank_plus.append(name_plus[i].replace(' ','_')) #공백 → ‘_’로 변경

  return(blank_plus)

#MAF(WGS) 파일 이름 변경용 함수

def MAF_WGS_change(file_names, summary4):

  name_plus = []

  for i in range(len(file_names)):
    a = summary4.loc[summary4["파일명_수정"]==str(file_names[i])]['대상자 아이디']+'_'+str(file_names[i]) #대상자 아이디 + _ + 기존 파일명
    name_plus.append(a.values[0])

  return(name_plus)

#MAF(VCF) 파일 이름 변경용 함수

def VCF_MAF_change(file_names):

  hyphen_change = []

  for i in range(len(file_names)):
    hyphen_change.append(file_names[i].replace('_','-')) #"_"를 "-"로 변환

  return(hyphen_change)
```