---
date : 2026-04-09
tags: ['2026-04']
categories: ['kosmos']
bookHidden: true
title: "FMI #3 XML 파일 불러오기 (VAF,VAF_FIND)"
---

# FMI #3 XML 파일 불러오기 (VAF,VAF_FIND)

#2026-04-09
ㄷ
---


```python
import xml.etree.ElementTree as et

xtree = et.parse("C:/Users/User/Desktop/XML파일_230428/20230316_FMI file/ORD-1489645-01/ORD_1489645_01.xml")

xroot = xtree.getroot()



import pandas as pd



#키값

object = xroot[1][0]

dataframe_list = []

for item in object.findall('PMI'):

a = item.find('MRN').text

dataframe = pd.DataFrame({

'MEDICAL RECORD' : [a],

})

dataframe_list.append(dataframe)



#VAF값

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



row.append({"GENE":s_gene,"ALTERATION":s_protein_effect,"TRANSCRIP_ID":s_transcript,"CODING_SEQUENCE_EFFECT":s_cds_effect,

"VARIANT_CHROMOSOMAL_POSITION":s_position,"VARIANT_ALLELE_FREQUENCY_(%VAF)":s_percent_reads})



out_df = pd.DataFrame(row, columns=df_cols)



#키값을 VAF값 앞에 추가

MEDICAL_RECORD=pd.DataFrame(dataframe_list[0],columns=['MEDICAL RECORD'])

out_df.insert(0,'MEDICAL RECORD', MEDICAL_RECORD.to_string(header=False, index=False))

out_df



#VAF_FIND함수 생성

def VAF_FIND(address):

import xml.etree.ElementTree as et

import pandas as pd

xtree = et.parse(address) #xtree = et.parse(address+".xml")

xroot = xtree.getroot()



object = xroot[1][0]

dataframe_list = []

for item in object.findall('PMI'):

a = item.find('MRN').text

dataframe = pd.DataFrame({

'MEDICAL RECORD' : [a],

})

dataframe_list.append(dataframe)



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



row.append({"GENE":s_gene,"ALTERATION":s_protein_effect,"TRANSCRIP_ID":s_transcript,"CODING_SEQUENCE_EFFECT":s_cds_effect,

"VARIANT_CHROMOSOMAL_POSITION":s_position,"VARIANT_ALLELE_FREQUENCY_(%VAF)":s_percent_reads})

out_df = pd.DataFrame(row, columns=df_cols)



MEDICAL_RECORD=pd.DataFrame(dataframe_list[0],columns=['MEDICAL RECORD'])

out_df.insert(0,'MEDICAL RECORD', MEDICAL_RECORD.to_string(header=False, index=False))



return(out_df)



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



#VAF함수 생성

def VAF(file_address):

import xml.etree.ElementTree as et

import os

import ast

path = file_address

file_dataframe_list = []

file_list = os.listdir(path)

file_list_py = [file for file in file_list if file.endswith('xml')]



#\\를 /로 변형한 파일 주소들이 들어있는 리스트 생성

for i in range(len(file_list_py)):

data = '{}/{}'.format(path, file_list_py[i])

file_address=data.replace("\\","/")

file_dataframe_list.append(file_address)



#리스트를 불러와서 VAF함수에 집어넣기

for j in range(len(file_dataframe_list)):

a = file_dataframe_list[j]

#각각 데이터 프레임별로 저장

globals()['data_{}'.format(j)] = VAF_FIND(a)

#각각 엑셀 파일로 저장

VAF_FIND(a).to_excel('{}_VAF.xlsx'.format(a))



# 예시

VAF() #xml파일들이 들어있는 주소 입력(\를 \\로 변경해서 입력)

VAF("C:\\Users\\User\\Desktop\\XML파일_230428\\20230316_FMI file\\for문")

data_2
```
