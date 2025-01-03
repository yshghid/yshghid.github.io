---
date : 2025-01-02
weight: 601
tags: ['2025-01']
categories: ['BI']
bookHidden: true
title: "BI"
---

# [코드] Sleuth 워크플로우

##### 2025.01.02

---

![image](https://github.com/user-attachments/assets/8fc31917-284a-452b-857a-8b5256ec50cb)


## Run Sleuth

```R
# sleuth_sc.R

require("sleuth")
packageVersion("sleuth")
library("gridExtra")
library("cowplot")
library("biomaRt")
library(readr)

setwd("/data-blog/bi4")
getwd()

sample_id <- dir(file.path("./"))
sample_id <- grep("^output_(150|con)", sample_id, value = TRUE)
sample_id <- substring(sample_id, 8)
sample_id

kal_dirs <- file.path(paste0("output_", sample_id))
s2c <- read.table(file.path("kallisto_demo_150_con.tsv"),
                  header = TRUE,
                  stringsAsFactors = FALSE,
                  sep = "\t")
s2c <- dplyr::mutate(s2c, path = kal_dirs)
s2c

marts <- listMarts()
ensembl <- useMart("ensembl")
datasets <- listDatasets(ensembl)
filtered_datasets <- datasets[grepl("hsapiens", datasets$dataset), ]
hsapiens_mart <- useMart("ensembl",dataset="hsapiens_gene_ensembl")
datasets <- listDatasets(hsapiens_mart)
filtered_datasets <- datasets[grepl("hsapiens", datasets$dataset), ]
hsapiens_mart <- useMart("ensembl",dataset="hsapiens_gene_ensembl",host="ensembl.org")
datasets <- listDatasets(hsapiens_mart)

t2g <- getBM(attributes = c("ensembl_transcript_id_version",
                            "ensembl_gene_id",
                            "description",
                            "external_gene_name"),
             mart = hsapiens_mart)
head(t2g)

ttg <- dplyr::rename(t2g, target_id= ensembl_transcript_id_version, 
                     ens_gene = ensembl_gene_id, ext_gene = external_gene_name)
ttg <- dplyr::select(ttg, c('target_id', 'ens_gene', 'ext_gene'))
head(ttg)

s2c$condition <- as.factor(s2c$condition)
s2c$condition <- relevel(s2c$condition, ref = "con")
so <- sleuth_prep(s2c, target_mapping = ttg,
  aggregation_column = 'ens_gene', extra_bootstrap_summary = TRUE)

so <- sleuth_fit(so, ~condition, 'full')
so <- sleuth_fit(so, ~1, 'reduced')
so <- sleuth_lrt(so, 'reduced', 'full')
sleuth_table_gene <- sleuth_results(so, 'reduced:full', 'lrt', show_all = FALSE)

sleuth_save(so, '150_con_so.sleuth')
write_csv(sleuth_table_gene, '150_con.csv')
```
```plain text
[1] '0.30.1'
'/data-blog/bi4'
'33-1'
'33-2'
'33-3'
'con-1'
'con-2'
'con-3'
A data.frame: 6 × 4
sample	condition	treatment_s	path
<chr>	<chr>	<chr>	<chr>
33-1	33	33	./output_33-1
33-2	33	33	./output_33-2
33-3	33	33	./output_33-3
con-1	con	con	./output_con-1
con-2	con	con	./output_con-2
con-3	con	con	./output_con-3
A data.frame: 6 × 4
ensembl_transcript_id_version	ensembl_gene_id	description	external_gene_name
<chr>	<chr>	<chr>	<chr>
1	ENST00000387314.1	ENSG00000210049	mitochondrially encoded tRNA-Phe (UUU/C) [Source:HGNC Symbol;Acc:HGNC:7481]	MT-TF
2	ENST00000389680.2	ENSG00000211459	mitochondrially encoded 12S rRNA [Source:HGNC Symbol;Acc:HGNC:7470]	MT-RNR1
3	ENST00000387342.1	ENSG00000210077	mitochondrially encoded tRNA-Val (GUN) [Source:HGNC Symbol;Acc:HGNC:7500]	MT-TV
4	ENST00000387347.2	ENSG00000210082	mitochondrially encoded 16S rRNA [Source:HGNC Symbol;Acc:HGNC:7471]	MT-RNR2
5	ENST00000386347.1	ENSG00000209082	mitochondrially encoded tRNA-Leu (UUA/G) 1 [Source:HGNC Symbol;Acc:HGNC:7490]	MT-TL1
6	ENST00000361390.2	ENSG00000198888	mitochondrially encoded NADH:ubiquinone oxidoreductase core subunit 1 [Source:HGNC Symbol;Acc:HGNC:7455]	MT-ND1
A data.frame: 6 × 4
target_id	ens_gene	description	ext_gene
<chr>	<chr>	<chr>	<chr>
1	ENST00000387314.1	ENSG00000210049	mitochondrially encoded tRNA-Phe (UUU/C) [Source:HGNC Symbol;Acc:HGNC:7481]	MT-TF
2	ENST00000389680.2	ENSG00000211459	mitochondrially encoded 12S rRNA [Source:HGNC Symbol;Acc:HGNC:7470]	MT-RNR1
3	ENST00000387342.1	ENSG00000210077	mitochondrially encoded tRNA-Val (GUN) [Source:HGNC Symbol;Acc:HGNC:7500]	MT-TV
4	ENST00000387347.2	ENSG00000210082	mitochondrially encoded 16S rRNA [Source:HGNC Symbol;Acc:HGNC:7471]	MT-RNR2
5	ENST00000386347.1	ENSG00000209082	mitochondrially encoded tRNA-Leu (UUA/G) 1 [Source:HGNC Symbol;Acc:HGNC:7490]	MT-TL1
6	ENST00000361390.2	ENSG00000198888	mitochondrially encoded NADH:ubiquinone oxidoreductase core subunit 1 [Source:HGNC Symbol;Acc:HGNC:7455]	MT-ND1
```

R 돌릴때 ipython 주로 쓰는데 R 스크립트로 한 이유는... 프로그램이 너무 무거워서 자꾸 끊김 ㅠㅠ 게다가 시간도 엄청 오래걸렸다.

갠적으로 DESeq2나 edgeR 쓰면 10분이면 끝날거 두세시간씩 걸려서 할바에는 kallisto 안쓸듯. ㅡㅡ


### 출처

**Pachterlab - Getting started with sleuth** https://pachterlab.github.io/sleuth_walkthroughs/trapnell/analysis.html
**Differential expression of transcripts using Sleuth** https://hbctraining.github.io/DGE_workshop_salmon/lessons/09_sleuth.html

### additional data

코드에 사용된 데이터 정보는 [github](https://github.com/yshghid/data/tree/main/data-blog/bi4)에서 확인 가능하다.
