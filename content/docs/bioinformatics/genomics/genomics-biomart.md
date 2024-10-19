---
author: "kaya"
date: 2024-07-28
title: "Biomart; convert ensembl id into gene symbol"
categories: ["code"]
tags: ["2024-07"]
---

# Biomart; convert ensembl id into gene symbol

## 1. 사전 설치

아래 코드로 Biomart를 설치해준다.

```R
if (!requireNamespace("BiocManager", quietly = TRUE))
     install.packages("BiocManager")
BiocManager::install("biomaRt")
```

설치가 완료되었다면 R 환경과 Biomart 패키지의 버전을 확인해보자. 

```R
R.version
               _
platform       x86_64-conda-linux-gnu
arch           x86_64
os             linux-gnu
system         x86_64, linux-gnu
status
major          4
minor          0.5
year           2021
month          03
day            31
svn rev        80133
language       R
version.string R version 4.0.5 (2021-03-31)
nickname       Shake and Throw

packageVersion("biomaRt")
[1] '2.46.3'
```

나의 경우 R(4.0.5), Biomart(2.46.3)을 사용해주었다. 참고로 R(>=2.1) 버전을 필요로 한다.

## 2. 데이터 가져오기

어노테이션을 수행할 발현량 데이터를 데이터프레임으로 읽어온다. 유전자 이름은 ENST로 시작하므로 Ensembl transcript ID임을 알수있고 이를 gene symbol로 매핑해줄것이다.

```R
df <- read.csv("counts.csv", header = TRUE)
dim(df)
head(df)
```
```plain text
2266517
A data.frame: 6 x 7
ens_id	WT1	WT2	WT3	TR1	TR2	TR3
<chr>	<int>	<int>	<int>	<int>	<int>	<int>
1	ENST00000000233.9	9	7	8	3	10	12
2	ENST00000000412.7	8	6	12	8	5	6
3	ENST00000000442.10	16	6	7	6	6	10
4	ENST00000001008.5	12	10	6	10	17	6
5	ENST00000001146.6	8	11	7	7	3	6
6	ENST00000002125.8	6	8	8	10	7	8

```

## 3. mart 객체 가져오기

먼저 Ensembl BioMart를 mart 객체로 가져와준다. "hsapiens_gene_ensembl"을 선택하면 인간 유전자에 대한 mart 객체 `ensembl_hsapiens`을 가져올수 있다.

```R
ensembl <- useMart("ENSEMBL_MART_ENSEMBL", host = "www.ensembl.org")
ensembl_hsapiens <- useDataset("hsapiens_gene_ensembl", mart = ensembl)
```

`searchAttributes()` 함수로 선택한 BioMart 데이터셋에서 사용 가능한 피쳐 목록을 볼수있다. 'ensembl' 또는 'external'과 관련된 피쳐만 검색하였다. 

```R
head(searchAttributes(mart = ensembl_hsapiens, 'ensembl|external'))
```
```plain text
A data.frame: 6 x 3
name	description	page
<chr>	<chr>	<chr>
1	ensembl_gene_id	Gene stable ID	feature_page
2	ensembl_gene_id_version	Gene stable ID version	feature_page
3	ensembl_transcript_id	Transcript stable ID	feature_page
4	ensembl_transcript_id_version	Transcript stable ID version	feature_page
5	ensembl_peptide_id	Protein stable ID	feature_page
6	ensembl_peptide_id_version	Protein stable ID version	feature_page

```
여기서는 ensembl_transcript_id_version, external_gene_name을 사용할것이다.

## 4. Ensembl ID 어노테이션

Ensembl ID를 gene symbol로 어노테이션 해준다. attributes에는 매핑하려는 피쳐인 "ensembl_transcript_id_version"(전사체 ID)과 "external_gene_name"(외부 유전자 이름)을 넣어주었다.

filters로는 데이터 `transcript_id`의 피쳐가 Ensembl ID이므로 "ensembl_transcript_id_version"을 넣어줬다.

```R
gene_symbols <- getBM(attributes = c("ensembl_transcript_id_version", "external_gene_name"),
                        filters = "ensembl_transcript_id_version",
                        values = transcript_id,
                        mart = ensembl_hsapiens)
```

생성된 gene_symbols 데이터프레임의 external_gene_name 컬럼을 보면 결측값인 행이 존재한다. 이는 전사체이면서 매핑되는 유전자는 없는 transcript들이다. 유전자 행만 남기고 싶다면 결측값 행을 제거해준다.

```R
gene_symbols_na <- gene_symbols[gene_symbols$external_gene_name != '', ]
```

매핑 결과 데이터프레임은 다음과 같다.

```R
dim(gene_symbols_na)
head(gene_symbols_na)
```
```plain text
1701062
A data.frame: 6 x 2
ensembl_transcript_id_version	external_gene_name
<chr>	<chr>
1	ENST00000253838.3	TTTY6
2	ENST00000253848.3	TTTY6B
3	ENST00000215473.7	PCDH11Y
4	ENST00000258589.8	TPTE2P4
5	ENST00000253470.4	TTTY11
6	ENST00000253320.8	TXLNGY
```

위에서 dim(df)가 2,266,517이었던 것을 상기해보자. 1,701,062개 행만 유전자 이름이 매핑되었고 나머지 행들은 전사만 되고 유전자는 아닌 전사체들의 id였다는 점을 생각해볼수 있다. 

전체 코드는 아래와 같다.

```R
library("biomaRt")

ensembl <- useMart("ENSEMBL_MART_ENSEMBL", host = "www.ensembl.org")
ensembl_hsapiens <- useDataset("hsapiens_gene_ensembl", mart = ensembl)

df <- read.csv("counts.csv", header = TRUE)
transcript_id <- df$ens_id

ensembl_attributes <- searchAttributes(mart = ensembl_hsapiens, 'ensembl|external')
ensembl_attributes_filtered <- ensembl_attributes[ensembl_attributes$page == "feature_page", ]

gene_symbols <- getBM(attributes = c("ensembl_transcript_id_version", "external_gene_name"),
                      filters = "ensembl_transcript_id_version",
                      values = transcript_id,
                      mart = ensembl_hsapiens)

gene_symbols_na <- gene_symbols[gene_symbols$external_gene_name != '', ]
```

**참고문서**
1. https://bioconductor.org/packages/release/bioc/html/biomaRt.html
2. https://rdrr.io/bioc/biomaRt/man/listAttributes.html


