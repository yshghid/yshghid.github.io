---
weight: 18
title: "생물정보학"
bookComments: false
type: docs
---

# 생물정보학

## 2025

*07-12* ⋯ EdgeR: DE 분석

[Load package suppressMessages({ library("DESeq2") library(pheatmap) library(withr) #library(tidyverse) library(RColorBrewer) library(gplots) library(dplyr) }) Set path setwd("/data/home/ysh980101/2307_EBV") getwd() '/data1/home/ysh980101/2307_EBV' ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi23/)

---

*05-28* ⋯ Bash 쉘 스크립트 (로컬, 서버)

[#local #alias cobi2='ssh -p 5290 ysh980101@155.230.28.211' alias cobi2="ssh -p 3160 ysh980101@155.230.110.91" alias cobi3="ssh -p 7777 ysh980101@155.230.110.92" alias cobi4="ssh -p 4712 ysh980101@155.230.110.93" # >>> conda initialize >>> # !! Contents ⋯](https://yshghid.github.io/docs/study/tech/tech11/)

---

*04-21* ⋯ TopHat2, HTSeq, Rsubread: RNA-seq 전처리 파이프라인 비교

[비교 의의 Traditional 방법은 TopHat2+HTseq 조합이지만 오류도 넘 많이나고 Rsubread를 쓰면 빠르고 깔끔한데 왜 써야하지..? 싶어서 동일한 데이터(pair-end fastq)로 돌려봄. HTseq에서 아래 코드를 수행할때 파라미터가 많은데 뭐가 다르게나오는지 모르겠어서 실험해봄. Cases
Rsubread 사용 HTSeq 사용, ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi9/)

---

*04-21* ⋯ Bismark: WGBS 전처리

[1. Build Index $ bowtie2-build Homo_sapiens.GRCh38.dna.toplevel.fa GRCh38 -p 40 2. Bam Sorting & Indexing $ samtools sort KEB01_1_bismark_bt2_pe.bam -o KEB01_1_bismark_bt2_pe.sorted.bam $ samtools index KEB01_1_bismark_bt2_pe.sorted.bam ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi6/)

---

*04-21* ⋯ ChIP-seq 전처리

[1. Trimming #chipseq_trimming.sh #!/bin/bash setting envs export bdir="/data3/projects/2022_KNU_EBV" export hg38_bowtieidx="/data3/PUBLIC_DATA/ref_genomes/homo_sapiens/hg38/hg38_bowtie_idx/hg38.fa" export hg38_bwaidx="/data3/PUBLIC_DATA/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi5/)

---

*04-21* ⋯ Rsubread, edgeR: RNA-seq 전처리

[가장 오류 적게나는 조합! 1. Align RNA-seq #Load Packages library(Rsubread) library(org.Mm.eg.db) library(gridExtra) library(reshape2) #Set Path indir = "/data/home/ysh980101/2504/mirna/data" outdir = "/data/home/ysh980101/2504/mirna/data" ⋯ ](https://yshghid.github.io/docs/study/bioinformatics/bi8/)

---

*04-21* ⋯ TopHat, SAMtools, HTSeq: RNA-seq 전처리

[1. TopHat 실행 $ tophatpy -o tophat_out_33-1 --no-mixed -p 40 \ $ /data3/PUBLIC_DATA/ref_genomes/homo_sapiens/GRCh38/Homo_sapiens.GRCh38.dna.toplevel \ $ /data/home/ysh980101/2306_tophat/data/Bowtie2Index/5-AZA_33-1_1.fastq \ $ /data/home/ysh980101/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi7/)

---

*04-21* ⋯ Gprofiler/ggplot2: Enrichment 분석, 버블 플롯

[Load Package library(ggplot2) Set Path setwd("/data-blog/bi3") getwd() '/data-blog/bi3' Functional Enrichment Bubble Plot condition <- '150_con' gpsource <- 'GO:BP' #gpsource <- 'REAC' df_c1 <- read.csv(paste0("./sleuth_ward/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi3/)

---

*04-21* ⋯ Sleuth 작업

[Load Package, Run Sleuth require("sleuth") packageVersion("sleuth") library("gridExtra") library("cowplot") library("biomaRt") library(readr) setwd("/data/home/ysh980101/2307_kallisto") getwd() sample_id <- dir(file.path("./")) ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi2/)

---

*04-21* ⋯ Kallisto Pseudoalignment 작업

[1. Build Index $ kallisto index -i transcripts_cDNA.idx Homo_sapiens.GRCh38.cdna.all.fa.gz 2. Pseudoalign $ kallisto quant -i transcripts_cDNA.idx -o output_150-1 -t 40 ../2306_tophat/data/Bowtie2Index/5-AZA_150-1_1_edited.fastq ../2306 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi4/)


## 2024

*12-31* ⋯ EndNote 사용법

[1. EndNote 설치 및 계정 설정 계정 설정: 공식 웹사이트에서 End note 계정을 생성한다. 설치: 나의 경우 여기에서 다운로드해줬다. 2. 레퍼런스 추가 방법 Google Scholar에 논문 제목을 검색해서 인용>EndNote를 클릭하면 .enw 파일이 다운로드된다. 3. 레퍼런스 관리 Endnote에 접속한다. Collect>Import References로 들어간다 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi16/)

---

*12-31* ⋯ EBV RNA-seq 전처리

[분석 목적 제공받은 fastq를 human genome에 매핑해서 전처리, 분석 후 DE 결과 보냄 DE 분석시에 EBV 유전자도 포함해달라는 요청 해야하는것 fastq를 EBV genome에 매핑해서 전처리, EBV count 생성 human count에 EBV count를 붙이기 통합 count로 DE 분석 재수행 1. Alignment Load package, Set Path ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi11/)

---

*12-31* ⋯ DESeq2: DE 분석

[suppressMessages({ library("DESeq2") library(pheatmap) library(withr) #library(tidyverse) library(RColorBrewer) library(gplots) library(dplyr) }) Set path setwd("/data-blog/bi1") getwd() '/data-blog/bi1' Run DESeq2 S1 <- '33' S2 <- '150' ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi1/)

#

### 생물정보학 메모장

[﹂밀도 기반 클러스터링 연구](https://yshghid.github.io/docs/study/tech/tech17/)

[﹂항생제 예후 예측 연구](https://yshghid.github.io/docs/study/tech/tech19/)

#
