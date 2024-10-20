---
author: "kaya"
date: 2024-10-19
title: "Biomart mmusculus annotation; convert uniprot id into mgi symbol"
categories: ["code"]
tags: ["2024-10"]
---

# Biomart; convert ensembl id into gene symbol

##### 2024.10.19

---

## 1. 데이터 로드

변환해야하는 유전자 데이터를 데이터프레임으로 읽어온다.

```R
df <- read.csv("de-genes_icm-dcm.csv")
df
```
```plain text
X	genes	counts
<chr>	<chr>	<int>
icm-down	ACTS|VDAC3|ACTB|ECHA|SCOT1|ETFA|PRDX3|ACON|TNNC1|ACADV|B1AR69|083|ODPA|IDHP|NDUS2|NDUBA|S10A1|PYGB|SUCB1|SODM|G6PI|NDUS1|ODP2|J3QP71|IVD|BDH|Q99N15|SMYD1|ABEC2|EF1A2|MMSA|CACP|Q7TMG8|A0A1W2|ECI1|E9PZF0|THTM|NDUS7|LDHA|1433G|F8VPN4|UBQL2|PGES2|SDHB|NDUS5|GSTK1|D39U1|DDX3X|AUHM|FADD|Q8C253|XDH|FABP5|CBPA3|NDUB9|SUOX|HINT2|PRS7|MCAT|A0A087|FBRL|TPPP|ABHDB	63
dcm-down	ACTB|ECHA|PRDX3|ACON|TNNC1|B1AR69|IDHP|NDUS2|S10A1|PYGB|G6PI|ADT2|NDUS1|BDH|RYR2|Q99N15|ABEC2|EF1A2|MMSA|CACP|CPT1B|Q7TMG8|AL4A1|MCCA|A0A1W2|ECI1|E9PZF0|THTM|NDUS7|LDHA|1433G|F8VPN4|UBQL2|PGES2|SDHB|NDUS5|GSTK1|D39U1|DDX3X|AUHM|FADD|Q8C253|XDH|FABP5|CBPA3|NDUB9|SUOX|HINT2|PRS7|MCAT|A0A087|FBRL|TPPP|ABHDB	54
icm-down-withdcm	ACTS|B1AR69|H2A1K|H3BJQ7|CALM3|ACBP|ODP2|SMYD1|FIBA|LMNB1|NDUAA|CAP2|BMP10|CLH1|KINH|0J0|A0A0R4J|AMACR|E9PV66|RM46|ATAD1|PAIP2	22
```

## 2. biomart로 gene id annotation

해당 데이터는 icm, dcm subtype과 WT간 차등 발현된 단백질 목록이다. genes 컬럼의 '|'으로 분리된 문자열들이 단백질 이름인데, 해당 symbol을 mgi gene symbol로 변환하기 위해서 R의 biomart를 사용해주었다.

 ```R
library(biomaRt)

ensembl <- useMart("ensembl", dataset = "mmusculus_gene_ensembl")

gene_symbol_list <- vector("list", nrow(df))
length_annotated_list <- numeric(nrow(df))

for (i in 1:nrow(df)) {
  protein_ids <- unlist(strsplit(df$genes[i], "\\|"))
  
  annotations <- getBM(
    attributes = c('uniprot_gn_symbol', 'mgi_symbol'),
    filters = 'uniprot_gn_symbol',
    values = protein_ids,
    mart = ensembl
  )
  
  gene_symbol_list[[i]] <- paste(na.omit(annotations$mgi_symbol), collapse = "|")
  
  length_annotated_list[i] <- length(na.omit(annotations$mgi_symbol))
}

df$gene_symbol <- unlist(gene_symbol_list)
df$length_annotated <- length_annotated_list

df
```
```plain text
X	genes	counts	gene_symbol	length_annotated
<chr>	<chr>	<int>	<chr>	<dbl>
icm-down	ACTS|VDAC3|ACTB|ECHA|SCOT1|ETFA|PRDX3|ACON|TNNC1|ACADV|B1AR69|083|ODPA|IDHP|NDUS2|NDUBA|S10A1|PYGB|SUCB1|SODM|G6PI|NDUS1|ODP2|J3QP71|IVD|BDH|Q99N15|SMYD1|ABEC2|EF1A2|MMSA|CACP|Q7TMG8|A0A1W2|ECI1|E9PZF0|THTM|NDUS7|LDHA|1433G|F8VPN4|UBQL2|PGES2|SDHB|NDUS5|GSTK1|D39U1|DDX3X|AUHM|FADD|Q8C253|XDH|FABP5|CBPA3|NDUB9|SUOX|HINT2|PRS7|MCAT|A0A087|FBRL|TPPP|ABHDB	63	Actb|Ddx3x|Eci1|Etfa|Fabp5|Fadd|Gstk1|Hint2|Ivd|Ldha|Mcat|Prdx3|Pygb|Sdhb|Smyd1|Suox|Tnnc1|Tppp|Vdac3|Xdh	20
dcm-down	ACTB|ECHA|PRDX3|ACON|TNNC1|B1AR69|IDHP|NDUS2|S10A1|PYGB|G6PI|ADT2|NDUS1|BDH|RYR2|Q99N15|ABEC2|EF1A2|MMSA|CACP|CPT1B|Q7TMG8|AL4A1|MCCA|A0A1W2|ECI1|E9PZF0|THTM|NDUS7|LDHA|1433G|F8VPN4|UBQL2|PGES2|SDHB|NDUS5|GSTK1|D39U1|DDX3X|AUHM|FADD|Q8C253|XDH|FABP5|CBPA3|NDUB9|SUOX|HINT2|PRS7|MCAT|A0A087|FBRL|TPPP|ABHDB	54	Actb|Cpt1b|Ddx3x|Eci1|Fabp5|Fadd|Gstk1|Hint2|Ldha|Mcat|Prdx3|Pygb|Ryr2|Sdhb|Suox|Tnnc1|Tppp|Xdh	18
icm-down-withdcm	ACTS|B1AR69|H2A1K|H3BJQ7|CALM3|ACBP|ODP2|SMYD1|FIBA|LMNB1|NDUAA|CAP2|BMP10|CLH1|KINH|0J0|A0A0R4J|AMACR|E9PV66|RM46|ATAD1|PAIP2
```
결과를 확인해보니 대다수 uniprot id가 어노테이션이 누락되었다. 


## 3. GenCode Uniprot entry 확인

attributes에 잘못된 값을 넣었는지 확인해보기 위해서 GenCode (https://www.gencodegenes.org/mouse/)에서 GRcm39 genome의 Uniprot entry metadata를 찾아보았다.

![image](https://github.com/user-attachments/assets/767f4fa5-6333-4cb1-94d9-e6af8851a365)

맨 아래의 TrEMBL에서 metadata를 클릭하면 다운로드할수있다.

다운로드한 metadata의 uniprot id와 데이터상의 protein id를 대조해보았다.

```python
import pandas as pd

df = pd.read_csv("de-genes_icm-dcm.csv", index_col=0)
annotation_df = pd.read_csv("gencode.vM36.metadata.TrEMBL.gz", sep='\t', header=None, names=['ens_transcript_id', 'uniprot'], compression='gzip', usecols=[0, 1])

df['genes_enst'] = ''
df['counts_enst'] = 0

for idx, row in df.iterrows():
    genes_list = row['genes'].split('|')
    
    genes_enst_list = [annotation_df.loc[annotation_df['uniprot'] == gene, 'ens_transcript_id'].values[0] 
                       if gene in annotation_df['uniprot'].values else 'Nan' 
                       for gene in genes_list]
    
    df.at[idx, 'genes_enst'] = '|'.join(genes_enst_list)
    
    df.at[idx, 'counts_enst'] = len([gene for gene in genes_enst_list if gene != 'Nan'])

df
```
```plain text
	genes	counts	genes_enst	counts_enst
icm-down	ACTS|VDAC3|ACTB|ECHA|SCOT1|ETFA|PRDX3|ACON|TNN...	63	Nan|Nan|Nan|Nan|Nan|Nan|Nan|Nan|Nan|Nan|ENSMUS...	7
dcm-down	ACTB|ECHA|PRDX3|ACON|TNNC1|B1AR69|IDHP|NDUS2|S...	54	Nan|Nan|Nan|Nan|Nan|ENSMUST00000108684.8|Nan|N...	6
icm-down-withdcm	ACTS|B1AR69|H2A1K|H3BJQ7|CALM3|ACBP|ODP2|SMYD1...	22	Nan|ENSMUST00000108684.8|Nan|ENSMUST0000014926...	3
```
마찬가지로 많은 protein id들이 gencode metadata상에 존재하지 않았다. 참고로 45597개 enst id에 대한 어노테이션이 존재한다.

```python
annotation_df
```
```plain text
	ens_transcript_id	uniprot
0	ENSMUST00000061280.17	Q3TGS5
1	ENSMUST00000182114.8	S4R2K3
2	ENSMUST00000182675.8	S4R1A9
3	ENSMUST00000137092.8	Q8BLT5
4	ENSMUST00000129923.2	Q8BLT5
...	...	...
45592	ENSMUST00000178291.2	Q8C9W1
45593	ENSMUST00000164489.3	E9PVT6
45594	ENSMUST00000178335.2	Q8C6S1
45595	ENSMUST00000178337.2	Q3UL49
45596	ENSMUST00000179700.2	Q5XK27
45597 rows × 2 columns
```

## 4. 구글링

예시로 PRDX3에 대한 구글링을 수행해보았다. "PRDX3 mouse protein"으로 검색 결과 아래 데이터가 나왔다(https://www.uniprot.org/uniprotkb/P20108/entry).

![image](https://github.com/user-attachments/assets/e8dc16eb-91f9-4799-8715-fe34b98ef8df)
![image](https://github.com/user-attachments/assets/34f146d1-4511-4ce2-864b-d5e790daa071)

공식 이름은 P20108이고 Synonyms로 PRDX3가 존재한다는것 같은데... 

biomart에서 다른 attributes를 찾아보려했지만 너무 많아서 하나하나 확인이 어려웠다. 그래서 해당 건은 수동으로 큐레이팅하거나 다른 데이터셋을 찾는 방향으로 결정했다.

```r
ensembl <- useMart("ensembl", dataset = "mmusculus_gene_ensembl")
searchAttributes(mart = ensembl)
```
```plain text
A data.frame: 1988 × 3
name	description	page
<chr>	<chr>	<chr>
1	ensembl_gene_id	Gene stable ID	feature_page
2	ensembl_gene_id_version	Gene stable ID version	feature_page
3	ensembl_transcript_id	Transcript stable ID	feature_page
4	ensembl_transcript_id_version	Transcript stable ID version	feature_page
5	ensembl_peptide_id	Protein stable ID	feature_page
6	ensembl_peptide_id_version	Protein stable ID version	feature_page
7	ensembl_exon_id	Exon stable ID	feature_page
8	description	Gene description	feature_page
9	chromosome_name	Chromosome/scaffold name	feature_page
10	start_position	Gene start (bp)	feature_page
11	end_position	Gene end (bp)	feature_page
12	strand	Strand	feature_page
13	band	Karyotype band	feature_page
14	transcript_start	Transcript start (bp)	feature_page
15	transcript_end	Transcript end (bp)	feature_page
16	transcription_start_site	Transcription start site (TSS)	feature_page
17	transcript_length	Transcript length (including UTRs and CDS)	feature_page
18	transcript_tsl	Transcript support level (TSL)	feature_page
19	transcript_gencode_basic	GENCODE basic annotation	feature_page
20	transcript_appris	APPRIS annotation	feature_page
21	transcript_is_canonical	Ensembl Canonical	feature_page
22	external_gene_name	Gene name	feature_page
23	external_gene_source	Source of gene name	feature_page
24	external_transcript_name	Transcript name	feature_page
25	external_transcript_source_name	Source of transcript name	feature_page
26	transcript_count	Transcript count	feature_page
27	percentage_gene_gc_content	Gene % GC content	feature_page
28	gene_biotype	Gene type	feature_page
29	transcript_biotype	Transcript type	feature_page
30	source	Source (gene)	feature_page
⋮	⋮	⋮	⋮
1959	5_utr_end	5' UTR end	sequences
1960	3_utr_start	3' UTR start	sequences
1961	3_utr_end	3' UTR end	sequences
1962	ensembl_transcript_id	Transcript stable ID	sequences
1963	ensembl_transcript_id_version	Transcript stable ID version	sequences
1964	ensembl_peptide_id	Protein stable ID	sequences
1965	ensembl_peptide_id_version	Protein stable ID version	sequences
1966	transcript_biotype	Transcript type	sequences
1967	transcript_version	Version (transcript)	sequences
1968	peptide_version	Version (protein)	sequences
1969	strand	Strand	sequences
1970	transcript_start	Transcript start (bp)	sequences
1971	transcript_end	Transcript end (bp)	sequences
1972	transcription_start_site	Transcription start site (TSS)	sequences
1973	transcript_length	Transcript length (including UTRs and CDS)	sequences
1974	cds_length	CDS Length	sequences
1975	cds_start	CDS start	sequences
1976	cds_end	CDS end	sequences
1977	ensembl_exon_id	Exon stable ID	sequences
1978	exon_chrom_start	Exon region start (bp)	sequences
1979	exon_chrom_end	Exon region end (bp)	sequences
1980	strand	Strand	sequences
1981	rank	Exon rank in transcript	sequences
1982	phase	Start phase	sequences
1983	end_phase	End phase	sequences
1984	cdna_coding_start	cDNA coding start	sequences
1985	cdna_coding_end	cDNA coding end	sequences
1986	genomic_coding_start	Genomic coding start	sequences
1987	genomic_coding_end	Genomic coding end	sequences
1988	is_constitutive	Constitutive exon	sequences
```

