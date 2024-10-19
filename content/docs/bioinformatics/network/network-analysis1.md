---
author: "kaya"
date: 2024-08-31
title: "Protein interaction analysis; between SARS-CoV-2 virus and human"
categories: ["code"]
tags: ["2024-08"]
---

# Protein interaction analysis; between SARS-CoV-2 virus and human

## Human ppi network

**Network 다운로드**

[STRING db](https://string-db.org/)에 접속해서 download를 선택하고, organism of interest에 homo sapiens를 입력해준다. 

![image](https://github.com/user-attachments/assets/9a2b0abf-4cf4-4f29-99f5-32b04a79190b)

이중에 protein.links.full.v12.0.txt.gz를 다운받아준다.

cf) **Subscore 의미** https://string-db.org/cgi/info?sessionId=bEKyfCQ15Bqb&footer_active_subpage=scores

> **The basic principle**
> - In STRING, each protein-protein interaction is annotated with one or more 'scores'.
> - Importantly, these scores do not indicate the strength or the specificity of the interaction. Instead, they are indicators of confidence, i.e. how likely STRING judges an interaction to be true, given the available evidence. All scores rank from 0 to 1, with 1 being the highest possible confidence. A score of 0.5 would indicate that roughly every second interaction might be erroneous (i.e., a false positive).

> **Transfer scores**
> - For most types of evidence, there are two types of scores: the 'normal' score, and the 'transferred' score. The latter is computed from data that is not originally observed in the organism of interest, but instead in some other organism and then transferred via homology/orthology. All potential source organisms are searched for evidence, but the actual transfers to the receiving organism are made non-redundant (according to 'clades' of closely related organisms in the tree of life).

STRING 데이터베이스에서 각 점수의 의미는 상호작용의 강도가 아닌 상호작용이 실제로 존재할 가능성을 나타낸다. 점수는 0에서 1까지이며 1은 가장 높은 신뢰도를 나타냄. 

**Network 프로세싱**

다운받은 네트워크의 컬럼은 다음과 같이 구성돼있다.

```python
string_col = pd.read_csv("9606.protein.links.full.v11.0.txt", sep=" ", nrows=0)
string_col
```
```plain text
protein1	protein2	neighborhood	neighborhood_transferred	fusion	cooccurence	homology	coexpression	coexpression_transferred	experiments	experiments_transferred	database	database_transferred	textmining	textmining_transferred	combined_score
```

이중 protein1, protein2, combined_score 컬럼만 사용해줄것이다. combined_score 컬럼의 값은 아래와같이 분포해있다.

```python
string_score = pd.read_csv("/data3/PUBLIC_DATA/STRING/H.sapiens_9606/9606.protein.links.full.v11.0.txt", sep=" ", usecols=[15])

plt.figure(figsize=(10, 6))
plt.hist(string_score['combined_score'], bins=50, color='blue', edgecolor='black')
plt.title('Distribution of Combined Scores')
plt.xlabel('Combined Score')
plt.ylabel('Frequency')
plt.grid(True)
plt.show()
```

![image](https://github.com/user-attachments/assets/a1ec5340-c5ab-412d-b14b-83af1ab90efa)

위에서 subscore이 0-1값을 갖는다고 했는데 왜인지 100-1000사이 값을 갖고있다. 이를 0.1-1로 스케일링 해줘도 되는지 모르겠어서 일단 두기로햇다

```python
string = pd.read_csv("/data3/PUBLIC_DATA/STRING/H.sapiens_9606/9606.protein.links.full.v11.0.txt", sep=" ", usecols=[0, 1])
string_score = pd.read_csv("/data3/PUBLIC_DATA/STRING/H.sapiens_9606/9606.protein.links.full.v11.0.txt", sep=" ", usecols=[15])

string_info = pd.read_csv("/data3/PUBLIC_DATA/STRING/H.sapiens_9606/9606.protein.info.v11.0.txt",sep="\t", usecols=[0, 1])
```

string, string_score, string_info는 순서대로 노드, 에지 weight, 어노테이션정보 이다. 

```python
string_info.shape

nodes = list(set(string['protein1'].tolist()+string['protein2'].tolist()))
len(nodes)
```
```plain text
(19566, 2)
19354
```

어노테이션정보는 19566개고 네트워크 내 노드는 19354개이다. 

```python
string
```
```plain text
  protein1	protein2
0	9606.ENSP00000000233	9606.ENSP00000272298
1	9606.ENSP00000000233	9606.ENSP00000253401
2	9606.ENSP00000000233	9606.ENSP00000401445
3	9606.ENSP00000000233	9606.ENSP00000418915
4	9606.ENSP00000000233	9606.ENSP00000327801
...  ...	...
11759449	9606.ENSP00000485678	9606.ENSP00000310488
11759450	9606.ENSP00000485678	9606.ENSP00000342448
11759451	9606.ENSP00000485678	9606.ENSP00000350222
11759452	9606.ENSP00000485678	9606.ENSP00000367590
11759453	9606.ENSP00000485678	9606.ENSP00000349930
11759454 rows × 2 columns
```

ens id로 되어있는 노드명들을 gene symbol로 바꿔준다. 

```python
drop_list = []

for col in ['protein1', 'protein2']:
    string[col] = string[col].apply(lambda x: string_info[string_info['protein_external_id'] == x]['preferred_name'].values[0] if x in string_info['protein_external_id'].values else drop_list.append(x))

print(len(drop_list)
```
```plain text
0
```

변환된 node 정보와 score 정보를 합쳐서 네트워크 데이터프레임을 만든다.

```python
merged_df = pd.concat([string, string_score], axis=1)
merged_df
```
```plain text
protein1	protein2	combined_score
0	ARF5	CALM2	490
1	ARF5	ARHGEF9	198
2	ARF5	ERN1	159
3	ARF5	CDKN2A	606
4	ARF5	P4HB	167
...	...	...	...
11759449	OR6Q1	OR7D4	167
11759450	OR6Q1	OR5W2	175
11759451	OR6Q1	OR51D1	195
11759452	OR6Q1	REEP2	900
11759453	OR6Q1	OR11G2	213
11759454 rows × 3 columns
```

11759454개 에지를 갖는 네트워크 데이터프레임을 만들었다.

```python
merged_df.to_csv("string.tsv",sep="\t",index=False)
```

## SARS-CoV-2 human interaction network

Biogrid에서 [COVID-19 Coronavirus project 파일](https://downloads.thebiogrid.org/File/BioGRID/Latest-Release/BIOGRID-PROJECT-covid19_coronavirus_project-LATEST.zip)을 다운받아준다.

cf2) **curation guide** https://wiki.thebiogrid.org/doku.php/curation_guide

> Do we curate cross-species interactions?
> - We curate cross-species interactions, e.g. between a yeast and a human protein. The curation tool allows us to select separate species for the bait and hit using the relevant pulldown menus. However, this does not include cross-species complementation experiments because they are not really genetic interactions between two genes, but rather a test to determine functional orthologs in other species.


