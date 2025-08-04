---
weight: 14
title: "Bioinformatics"
bookComments: false
type: docs
---

# Bioinformatics


*2025-08-03* ⋯ EBVaGC DHT 연구 #1 method contribution 정리

[0. 제목 Dihydrotestosterone-androgen receptor signaling suppresses EBV-positive gastric cancer through DNA demethylation-mediated viral reactivation 1. 참여 파트 Materials and Methods 2. *RNA-seq analysis └── 13. *Whole genome bisulfite ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi32/)

---

*2025-07-28* ⋯ methylKit: DMR 분석

[1. Load packages library("methylKit") library("genomation") library("GenomicRanges") 2. Set path setwd("/data/home/ysh980101/2309_5-aza/Bismark/sorted_n") getwd() '/data1/home/ysh980101/2309_5-aza/Bismark/sorted_n' 3. Load data # Define ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi31/)

---

*2025-07-28* ⋯ MAFFT #2 MAFFT 실행

[1. Objective Influenza의 Reference squence는 길이가 fix되어있지만, 각 sequence는 삽입/탈락 mutation이 일어남에 따라 모두 길이가 같지 않다. 이 길이를 맞춰주는 padding을 하기 위해 MAFFT를 이용해 정렬(Multiple Sequence Alignment)한다. 2. MAFFT 실행 bash script #data /Influenza └── ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi30/)

---
        

*2025-07-28* ⋯ MAFFT #1 Fasta 파일 전처리

[1. Load package import pandas as pd import numpy as np import os import matplotlib.pyplot as plt import random os.sys.path.append("/data/home/ysh980101/2410/Mutclust2") from Bin.sc import * 2. Objective Influenza type A의 H1N1 strain의 fasta 파일을 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi29/)

---

*2025-07-28* ⋯ Selenium: Influenza fasta 파일 크롤링

[1. Load package import pandas as pd import numpy as np import os 2. Set path os.chdir('/Users/yshmbid/Desktop/workspace/gisaid') os.getcwd() '/Users/yshmbid/Desktop/workspace/gisaid' 3. Run crawling # ChromeDriver 경로를 설치하고 Service 객체로 전달 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi28/)

---

*2025-07-23* ⋯ [netMHCpan #4 결과 확인 및 heatmap 시각화](https://yshghid.github.io/docs/study/bioinformatics/bi27/)

#

---

*2025-07-23* ⋯ [netMHCpan #3 HLA-peptide affinity 분석](https://yshghid.github.io/docs/study/bioinformatics/bi26/)

#

---

*2025-07-23* ⋯ [netMHCpan #2 HLA-I 펩타이드 추출](https://yshghid.github.io/docs/study/bioinformatics/bi24/)

#

---

*2025-07-23* ⋯ netMHCpan #1 환자 시퀀스 생성

[1. Load package import pandas as pd import numpy as np import os import sys import re sys.path.append('/data/home/ysh980101/2409/bin') from mhc_epitope import * 2. Load data import pandas as pd import os 데이터 로드 sequence_df = make_sequence_df() ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi25/)

---

*2025-07-12* ⋯ EdgeR: DE 분석

[Load package suppressMessages({ library("DESeq2") library(pheatmap) library(withr) #library(tidyverse) library(RColorBrewer) library(gplots) library(dplyr) }) Set path setwd("/data/home/ysh980101/2307_EBV") getwd() '/data1/home/ysh980101/2307_EBV' ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi23/)

---

*2025-05-28* ⋯ 연구실 bashrc 스크립트

[#local #alias cobi2='ssh -p 5290 ysh980101@155.230.28.211' alias cobi2="ssh -p 3160 ysh980101@155.230.110.91" alias cobi3="ssh -p 7777 ysh980101@155.230.110.92" alias cobi4="ssh -p 4712 ysh980101@155.230.110.93" # >>> conda initialize >>> # !! Contents ⋯](https://yshghid.github.io/docs/study/tech/tech11/)

---

*2025-04-21* ⋯ TopHat2, HTSeq, Rsubread: RNA-seq 전처리 파이프라인 비교

[비교 의의 Traditional 방법은 TopHat2+HTseq 조합이지만 오류도 넘 많이나고 Rsubread를 쓰면 빠르고 깔끔한데 왜 써야하지..? 싶어서 동일한 데이터(pair-end fastq)로 돌려봄. HTseq에서 아래 코드를 수행할때 파라미터가 많은데 뭐가 다르게나오는지 모르겠어서 실험해봄. Cases
Rsubread 사용 HTSeq 사용, ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi9/)

---

*2025-04-21* ⋯ Bismark: WGBS 전처리

[1. Build Index $ bowtie2-build Homo_sapiens.GRCh38.dna.toplevel.fa GRCh38 -p 40 2. Bam Sorting & Indexing $ samtools sort KEB01_1_bismark_bt2_pe.bam -o KEB01_1_bismark_bt2_pe.sorted.bam $ samtools index KEB01_1_bismark_bt2_pe.sorted.bam ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi6/)

---

*2025-04-21* ⋯ ChIP-seq 전처리

[1. Trimming #chipseq_trimming.sh #!/bin/bash setting envs export bdir="/data3/projects/2022_KNU_EBV" export hg38_bowtieidx="/data3/PUBLIC_DATA/ref_genomes/homo_sapiens/hg38/hg38_bowtie_idx/hg38.fa" export hg38_bwaidx="/data3/PUBLIC_DATA/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi5/)

---

*2025-04-21* ⋯ Rsubread, edgeR: RNA-seq 전처리

[가장 오류 적게나는 조합! 1. Align RNA-seq #Load Packages library(Rsubread) library(org.Mm.eg.db) library(gridExtra) library(reshape2) #Set Path indir = "/data/home/ysh980101/2504/mirna/data" outdir = "/data/home/ysh980101/2504/mirna/data" ⋯ ](https://yshghid.github.io/docs/study/bioinformatics/bi8/)

---

*2025-04-21* ⋯ TopHat, SAMtools, HTSeq: RNA-seq 전처리

[1. TopHat 실행 $ tophatpy -o tophat_out_33-1 --no-mixed -p 40 \ $ /data3/PUBLIC_DATA/ref_genomes/homo_sapiens/GRCh38/Homo_sapiens.GRCh38.dna.toplevel \ $ /data/home/ysh980101/2306_tophat/data/Bowtie2Index/5-AZA_33-1_1.fastq \ $ /data/home/ysh980101/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi7/)

---

*2025-04-21* ⋯ Gprofiler/ggplot2: Enrichment 분석, 버블 플롯

[Load Package library(ggplot2) Set Path setwd("/data-blog/bi3") getwd() '/data-blog/bi3' Functional Enrichment Bubble Plot condition <- '150_con' gpsource <- 'GO:BP' #gpsource <- 'REAC' df_c1 <- read.csv(paste0("./sleuth_ward/ ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi3/)

---

*2025-04-21* ⋯ Sleuth 작업

[Load Package, Run Sleuth require("sleuth") packageVersion("sleuth") library("gridExtra") library("cowplot") library("biomaRt") library(readr) setwd("/data/home/ysh980101/2307_kallisto") getwd() sample_id <- dir(file.path("./")) ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi2/)

---

*2025-04-21* ⋯ Kallisto Pseudoalignment 작업

[1. Build Index $ kallisto index -i transcripts_cDNA.idx Homo_sapiens.GRCh38.cdna.all.fa.gz 2. Pseudoalign $ kallisto quant -i transcripts_cDNA.idx -o output_150-1 -t 40 ../2306_tophat/data/Bowtie2Index/5-AZA_150-1_1_edited.fastq ../2306 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi4/)

---

*2024-12-31* ⋯ EndNote 사용법

[1. EndNote 설치 및 계정 설정 계정 설정: 공식 웹사이트에서 End note 계정을 생성한다. 설치: 나의 경우 여기에서 다운로드해줬다. 2. 레퍼런스 추가 방법 Google Scholar에 논문 제목을 검색해서 인용>EndNote를 클릭하면 .enw 파일이 다운로드된다. 3. 레퍼런스 관리 Endnote에 접속한다. Collect>Import References로 들어간다 ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi16/)

---

*2024-12-31* ⋯ EBV RNA-seq 전처리

[분석 목적 제공받은 fastq를 human genome에 매핑해서 전처리, 분석 후 DE 결과 보냄 DE 분석시에 EBV 유전자도 포함해달라는 요청 해야하는것 fastq를 EBV genome에 매핑해서 전처리, EBV count 생성 human count에 EBV count를 붙이기 통합 count로 DE 분석 재수행 1. Alignment Load package, Set Path ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi11/)

---

*2024-12-31* ⋯ DESeq2: DE 분석

[suppressMessages({ library("DESeq2") library(pheatmap) library(withr) #library(tidyverse) library(RColorBrewer) library(gplots) library(dplyr) }) Set path setwd("/data-blog/bi1") getwd() '/data-blog/bi1' Run DESeq2 S1 <- '33' S2 <- '150' ⋯](https://yshghid.github.io/docs/study/bioinformatics/bi1/)

#

### 생물정보학 메모장

[﹂밀도 기반 클러스터링 연구](https://yshghid.github.io/docs/study/tech/tech17/)

[﹂항생제 예후 예측 연구](https://yshghid.github.io/docs/study/tech/tech19/)

#
