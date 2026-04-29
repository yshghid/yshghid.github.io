---
date : 2026-04-09
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "FMI #2 VAF 데이터 추출"
pageHidden: true
---

# FMI #2 VAF 데이터 추출

#2026-04-09

---


```python
import xml.etree.ElementTree as et
xtree = et.parse("C:/Users/User/Desktop/XML파일_230428/20230316_FMI file/ORD-1489645-01/ORD_1489645_01.xml")
xroot = xtree.getroot()

import os
import pandas as pd

# 키값
object = xroot[1][0]
dataframe_list = []

for item in object.findall('PMI'):
  a = item.find('MRN').text
  b = item.find('FirstName').text
  c = item.find('LastName').text

  dataframe = pd.DataFrame({
  'MEDICAL RECORD' : [a],
  'FIRST NAME' : [b],
  'LAST NAME' : [c]
  })

dataframe_list.append(dataframe)

# VAF값
object = xroot[1][1][2]
df_cols=["GENE","ALTERATION","TRANSCRIP_ID","CODING_SEQUENCE_EFFECT","VARIANT_CHROMOSOMAL_POSITION","VARIANT_ALLELE_FREQUENCY_(%VAF)"]
row=[]

for node in object:
  s_gene=node.attrib.get("gene")
  s_protein_effect=node.attrib.get("protein-effect")
  s_transcript=node.attrib.get("transcript")
  s_cds_effect=node.attrib.get("cds-effect")
  s_position=node.attrib.get("position")
  s_percent_reads=node.attrib.get("percent-reads")

  row.append({"GENE":s_gene,"ALTERATION":s_protein_effect,"TRANSCRIP_ID":s_transcript,"CODING_SEQUENCE_EFFECT":s_cds_effect, "VARIANT_CHROMOSOMAL_POSITION":s_position,"VARIANT_ALLELE_FREQUENCY_(%VAF)":s_percent_reads})

out_df = pd.DataFrame(row, columns=df_cols)

# 키값을 VAF값 앞에 추가
MEDICAL_RECORD=pd.DataFrame(dataframe_list,columns=['MEDICAL RECORD','FIRST NAME','LAST NAME'])
out_df.insert(0,'MEDICAL RECORD', MEDICAL_RECORD.to_string(header=False, index=False))

# VAF_FIND함수 생성

def VAF_FIND(address):

  import xml.etree.ElementTree as et
  import pandas as pd

  xtree = et.parse(address)
  xroot = xtree.getroot()

  object = xroot[1][0]
  dataframe_list = []

  for item in object.findall('PMI'):
    a = item.find('MRN').text
    b = item.find('FirstName').text
    c = item.find('LastName').text

    dataframe = pd.DataFrame({
    'MEDICAL RECORD' : [a],
    'FIRST NAME' : [b],
    'LAST NAME' : [c]
    })

    dataframe_list.append(dataframe)

  object = xroot[1][1][2]
  df_cols2=["GENE","ALTERATION","TRANSCRIP_ID","CODING_SEQUENCE_EFFECT","VARIANT_CHROMOSOMAL_POSITION","VARIANT_ALLELE_FREQUENCY_(%VAF)"]
  row=[]

  for node in object:
    s_gene=node.attrib.get("gene")
    s_protein_effect=node.attrib.get("protein-effect")
    s_transcript=node.attrib.get("transcript")
    s_cds_effect=node.attrib.get("cds-effect")
    s_position=node.attrib.get("position")
    s_percent_reads=node.attrib.get("percent-reads")

    row.append({"GENE":s_gene,"ALTERATION":s_protein_effect,"TRANSCRIP_ID":s_transcript,"CODING_SEQUENCE_EFFECT":s_cds_effect, "VARIANT_CHROMOSOMAL_POSITION":s_position,"VARIANT_ALLELE_FREQUENCY_(%VAF)":s_percent_reads})

  out_df2 = pd.DataFrame(row, columns=df_cols2)

  MEDICAL_RECORD=pd.DataFrame(dataframe_list[0:2],columns=['MEDICAL RECORD','FIRST NAME','LAST NAME'])
  out_df2.insert(0,'MEDICAL RECORD', MEDICAL_RECORD.to_string(header=False, index=False))
  #out_df = out_df1.append(out_df2)

  return(out_df2)
```

```python
# 오류 찾기

def VAF_FIND(address):

  import xml.etree.ElementTree as et
  import pandas as pd

  xtree = et.parse(address)
  xroot = xtree.getroot()

  object = xroot[1][0]
  dataframe_list1 = []

  for item in object.findall('PMI'):
    a = item.find('MRN').text
    b = item.find('FirstName').text
    c = item.find('LastName').text

    dataframe = pd.DataFrame({
    'MEDICAL RECORD' : [a],
    'FIRST NAME' : [b],
    'LAST NAME' : [c]
    })

    dataframe_list1.append(dataframe)

  object = xroot[1][1][2]
  df_cols2=["GENE","ALTERATION","TRANSCRIP_ID","CODING_SEQUENCE_EFFECT","VARIANT_CHROMOSOMAL_POSITION","VARIANT_ALLELE_FREQUENCY_(%VAF)"]
  row=[]

  for node in object:
    s_gene=node.attrib.get("gene")
    s_protein_effect=node.attrib.get("protein-effect")
    s_transcript=node.attrib.get("transcript")
    s_cds_effect=node.attrib.get("cds-effect")
    s_position=node.attrib.get("position")
    s_percent_reads=node.attrib.get("percent-reads")

    row.append({"GENE":s_gene,"ALTERATION":s_protein_effect,"TRANSCRIP_ID":s_transcript,"CODING_SEQUENCE_EFFECT":s_cds_effect, "VARIANT_CHROMOSOMAL_POSITION":s_position,"VARIANT_ALLELE_FREQUENCY_(%VAF)":s_percent_reads})

  out_df2 = pd.DataFrame(row, columns=df_cols2)

  MEDICAL_RECORD=pd.DataFrame(dataframe_list1,columns=['MEDICAL RECORD','FIRST NAME','LAST NAME'])
  out_df2.insert(0,'MEDICAL RECORD', MEDICAL_RECORD.to_string(header=False, index=False))
  #out_df = out_df1.append(out_df2)

  return(out_df2)
```
```python
#for문으로 xml파일이름 출력

import os
path = "C:/Users/User/Desktop/XML파일_230428/20230316_FMI file/for문"

dataframe_list = []
file_list = os.listdir(path)
file_list_py = [file for file in file_list if file.endswith('xml')]

for i in range(len(file_list_py)):
  data = '"{}/{}"'.format(path, file_list_py[i])
  file_address=data.replace("\\","/")
  print(file_address)
```

```python
# VAF함수 생성(사용하지 않는 함수(참고용))
def VAF(file_address):

  import xml.etree.ElementTree as et
  import os
  import ast

  path = file_address
  file_dataframe_list = []
  file_list = os.listdir(path)
  file_list_py = [file for file in file_list if file.endswith('xml')]
  df_final = pd.DataFrame()

  #\\를 /로 변형한 파일 주소들이 들어있는 리스트 생성

  for i in range(len(file_list_py)):
    data = '{}/{}'.format(path, file_list_py[i])
    file_address=data.replace("\\","/")
    file_dataframe_list.append(file_address)

    #리스트를 불러와서 VAF함수에 집어넣기
    for i in range(len(file_dataframe_list)):
      a = file_dataframe_list[i]
      #데이터프레임에 하나로 저장
      df_final = df_final.append(VAF_FIND(a), ignore_index=True)
      print(df_final)
      #df_final.to_excel('VAF_data.xlsx')
      #각각 엑셀 파일로 저장
      #VAF_FIND(a).to_excel('{}_VAF.xlsx'.format(a))
```
```python
# ↓ 아래 코드 돌리면 VAF 데이터 생성
path = "C:/Users/User/Desktop/XML파일_230428/20230316_FMI file/for문"
dataframe_list = []

file_list = os.listdir(path)
file_list_py = [file for file in file_list if file.endswith('xml')]

df_final = pd.DataFrame()
for i in range(len(file_list_py)):
  data = '{}/{}'.format(path, file_list_py[i])
  file_address=data.replace("\\","/")
  df_final=df_final.append(VAF_FIND(file_address))
  #print(str(file_address))
```