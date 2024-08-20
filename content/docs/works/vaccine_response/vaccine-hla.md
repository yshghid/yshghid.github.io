---
author: "kinda"
date: 2024-08-19
title: "[Paper] Immune response stability differential splicing of HLA"
categories: ["Works"]
tags: ["vaccine-response"]
---

# [Paper] Immune response stability to the SARS‐CoV‐2 mRNA vaccine booster is influenced by differential splicing of HLA genes

## Abstract

Many molecular mechanisms that lead to the host antibody response to COVID‐19 vaccines remain largely unknown. In this study, we used serum antibody detection combined with whole blood RNA‐ based transcriptome analysis to investigate variability in vaccine response in healthy recipients of  a booster (third) dose schedule of the mRNA BNT162b2 vaccine against COVID‐19. The cohort was divided into two groups: (1) low‐stable individuals, with antibody concentration anti‐SARS‐CoV IgG S1 below 0.4 percentile at 180 days after boosting vaccination; and (2) high‐stable individuals, with antibody values greater than 0.6 percentile of the range in the same period (median 9525 [185–80,000] AU/mL). Differential gene expression, expressed single nucleotide variants and insertions/deletions, differential splicing events, and allelic imbalance were explored to broaden our understanding of the immune response sustenance. Our analysis revealed a differential expression of genes with immunological functions in individuals with low antibody titers, compared to those with higher antibody titers, underscoring the fundamental importance of the innate immune response for boosting immunity. Our findings also provide new insights into the determinants of the immune response variability to the SARS‐CoV‐2 mRNA vaccine booster, highlighting the significance of differential splicing regulatory mechanisms, mainly concerning HLA alleles, in delineating vaccine immunogenicity.

Keywords SARS-CoV-2, Vaccine response, Whole blood transcriptome, Immune response variability, Admixed population, Human leukocyte antigen

> **dataset**
> - mRNA BNT162b2 백신 접종된 healthy recipients
> - 백신 접종 후 생성된 항체(serum antibody detection) 데이터
> - 유전자 발현(whole blood RNA‐based transcriptome analysis) 데이터
> - 분석: 백신에 대한 면역 반응 비교

> **group**
> - Low‐stable individuals: 접종후 180일에 항-SARS-CoV IgG S1 항체 농도가 낮은(0.4% 이하) 그룹
> - High‐stable individuals: 접종후 180일에 항체 농도가 높은(0.6% 이상) 그룹

> **result summary**
> - Differential gene expression: 낮은 항체농도 그룹과 높은 항체농도 그룹 간에 선천성 면역기능 유전자의 발현이 다름. 
> - SNV
> - Differential splicing events: HLA 대립유전자와 관련된 차별적 스플라이싱 조절 메커니즘이 다름, 따라서 백신 면역원성(면역 반응을 유도하는 능력)을 구분하는 데 중요한 역할을 한다.
> - Allelic imbalance


## Introduction

The coronavirus disease 2019 (COVID-19) pandemic, caused by the severe acute respiratory syndrome coro- navirus 2 (SARS-CoV-2), posed an unprecedented burden upon global healthcare systems with concurrently substantial economic disruptions, as evidenced by prolonged lockdown measures1. COVID-19 vaccines were developed, tested, and approved in record time, substantially reducing the number of cases, hospitalizations, and deaths worldwide2. This success, particularly in regions with high vaccination coverage, thereby ratifies that efficacious vaccination approaches harbor the potential to deal with emerging viruses.

SARS-CoV-2 vaccines encompass a broad spectrum of approaches, including inactivated viral particles, live attenuated vaccines, viral vectors encoding the viral spike protein, mRNA constructs, and adjuvanted spike protein subunits3. Nonetheless, despite the administration of more than six billion doses of COVID-19 vaccines, the high effectiveness of the COVID-19 vaccines reaching up to 95%4, and the fact that the latest viral variants exhibit reduced lethality among immunized individuals, SARS-CoV-2 transmission persisted even at a low speed.

Besides, many COVID-19 cases occur in vaccinated individuals5. Escape from the immune response or a sub- optimal immune response can partially contribute to virus spread, favoring new variants’ emergence. However, a comprehensive understanding of the factors underlying differentiated host immune responses to COVID-19 vaccines, even among immunocompetent individuals, is still in progress.

Consistent variations in vaccine immunogenicity are a recurring phenomenon for different viruses. One pivotal aspect requiring thorough characterization concerns host differences in sustaining immunogenicity elicited by vaccines over time and the identification of genetic drivers influencing the immune response to COVID-19 vaccines has been the focus of several studies. Indeed, investigations into the spectral individual variation in functional immune response have revealed a notable genetic connection with vaccination response. Twin studies have provided insights by highlighting that monozygotic twins exhibit lesser variability than dizygotic twins in their vaccine-induced responses under controlled environmental conditions6. Furthermore, the host’s genetic background, primarily characterized by single nucleotide variants (SNVs) in genes encoding human leukocyte antigen (HLA) classes I and II, cytokines, cytokine receptors, and genes involved in innate immune response (e.g., Toll-like receptors), can partly elucidate the interindividual variability in the immune response to vaccines, such as measles and rubella7–12, hepatitis B7,13–15, influenza16, smallpox17, or Bacillus anthracis18. Besides, differ- ent ethnic groups living in the same geographical location exhibit diverse immune responses to vaccination or antibodies’ decline, suggesting a genetic modulatory influence in the dynamics of vaccine-induced responses19.

Additional factors influencing immune response variability include intrinsic host issues (e.g. age, gender, comorbidities, body mass index, micronutrients, microbiota, preexisting immunity) and extrinsic elements (e.g. smoking, alcohol consumption, exercise, psychological stress, and toxins exposure)19. Furthermore, compelling evidence indicates the existence of ethnic diversity in vaccine-induced antibody levels20. Thus, efforts directed towards the comprehensive understanding of the wide-ranging spectrum of immune responses observed among healthy individuals and the intricate mechanisms underlying this variability may help to develop more effec- tive immunogenic vaccines and overwhelm vaccine failures. Indeed, high-throughput methodologies, such as RNA-based transcriptome analyses, can be gold standard tools to explore this landscape, since they reflect both intrinsic and extrinsic factors affecting humoral, cellular, innate, cytokine, and adaptive immune responses21. Transcriptomic analysis in blood, an accessible tissue reflective of immune system dynamics22, can be employed to discern potential signatures of vaccine-induced responses or to characterize their minor expression or complete absence in less responsive individuals.

> **백신 면역 반응 변동성에 기여하는 요인 (factors influencing immune response variability)**
> - 백신에 대한 면역 반응의 개인 간 variation이 큰데 기여하는 요인은?
> - 요인1: HLA gene에서 발생하는 단일 뉴클레오타이드 변이(SNVs)
> - 요인2: 사이토카인, 사이토카인 수용체 및 선천 면역 반응에 관여하는 유전자
> - 요인3: 연령, 성별, 동반 질환, 체질량 지수, 미량 영양소, 마이크로바이옴, 기존 면역(내적 요인) / 흡연, 음주, 운동, 심리적 스트레스, 독소 노출(외적 요인)
> - 요인4: 인종

On a global scale, Brazil emerged as a prominent hub for SARS-CoV-2 spread during pandemics, marked by elevated counts of both cases and deaths23 (https://coronavirus.jhu.edu/map.html). Conversely, it boasts a robust immunization program, resulting in significant adherence to SARS-CoV-2 vaccination compared to many other countries. Among the COVID-19 vaccines distributed in Brazil, CoronaVac (Sinovac), a whole inactivated virus vaccine, received regulatory approval from the Brazilian Health Regulatory Agency (ANVISA) in January 2021 and then spread as one of the most globally employed vaccines. The Covishield vaccine was the second vaccine to be administered in the country. It was developed by the University of Oxford in partnership with the pharmaceutical company AstraZeneca, using the modified chimpanzee adenovirus ChAdOx1 as a vector. Subsequently, in response to the emergence of several Variants of Concern (VOCs), notably the Delta (B.1.617.2) and Omicron variants, the Brazilian government introduced the administration of a booster dose of the mRNA BNT162b2 vaccine (BioNTech/Pfizer) to those who had completed the primary vaccination schedule at least six months earlier, aiming to reinforce immune protection against COVID-1924,25.

SARS-CoV-2 BNT162b2 vaccine (Pfizer-Biontech) is an mRNA vaccine engineered with lipid nanoparticles and nucleoside modifications, designed to encode a full-length, prefusion-stabilized SARS-CoV-2 spike protein anchored within the viral membrane. It has demonstrated safety and efficacy in preventing COVID-1926. Recently, an observational retrospective investigation was conducted to assess the antibody response at intervals of 120 and 180 days after the BNT162b2 vaccine in 1.115 subjects, evidencing that the second dose of this vaccine allows a satisfactory antibody response27. Furthermore, booster vaccination enables higher protection against SARS- CoV-2 variants than is achieved with a primary series of vaccination, although antibody titers naturally decrease over time, requiring additional boosting28,29. However, studies investigating the impact of genetic regulation of the immune response to the BNT162b2 vaccine and the antibodies’ stability have been few and ethnically restricted.

To better understand SARS-CoV-2 vaccine-induced immunogenicity and why some individuals sustain anti- body titers after receiving the booster dose better than others, we analyzed the antibody response elicited by the BNT162b2 vaccine. We also carried out bulk RNA-based transcriptome analysis from whole blood 180 days after vaccination boosting. Additionally, our systematic bioinformatic analysis showed a potential role for innate immune regulatory mechanisms in maintaining humoral response after vaccination boosting. Besides, we found high correlations between differential alternative splicing events and vaccine response, mainly concerning HLA genes. These genes hold the potential to serve as predictors of vaccine response, highlighting the valuable role of molecular profiling in improving the accuracy of vaccine response prediction.

> - 개인의 백신 면역반응을 예측할수있을까? 본 논문에서는 BNT162b2 백신에 대한 면역 반응의 유전적 조절(transcript 데이터)과 항체 안정성의 영향(항체 데이터)을 연구함.
> - 선천 면역 조절 메커니즘이 백신 부스터접종 후의 체액성 면역 반응을 유지하는 데 중요한 역할을 할 수 있음을 발견함.
> - HLA 유전자와 관련된 차별적 스플라이싱 이벤트와 백신 반응 간의 높은 상관관계가 발견되었고 따라서 백신반응을 예측하는데 중요한 역할을 할수있음.

## Methods

**Study participants and sample collection**

Within the context of the Brazilian COVID-19 vaccination campaign, 5,345 individuals from an ethically mixed Brazilian population from Rio de Janeiro (Brazil) were recruited at Rio de Janeiro State University for SARS- CoV-2 IgG evaluation (data not published). 

The primary vaccination schedule in both groups was accomplished by Coronavac (Sinovac) or Covishield (AstraZeneca). Between April and July 2022, twenty healthy adult volunteers’ recipients of a booster (third dose) regimen for the SARS-CoV-2 BNT162b2 vaccine were selected for further evaluation on day 180 after the boost- ing vaccination (IgG S1 median [min–max] AU/mL, Sinovac: 10,797 [283–80,000], n = 348 and AstraZeneca: 8919 [185–80,000], n = 448). Individuals with systemic conditions, such as cancer, diabetes, and obesity, were not included in this study (Table 1). Within this cohort, individuals were classified into two age- and sex-matched groups of ten individuals, according to the titers/concentrations of SARS-CoV-2 antibodies low-stable (1), in which individuals had antibodies titers/concentrations 180 days less than 7000 AU/mL (percentile < 0.4) after boosting vaccination; and high-stable (2), with antibodies titers compatible with the population in the same period (over 12,800 AU/mL—percentile > 0.6) (Fig. 1).

The collection of serum specimens for SARS-CoV-2 antibodies analysis and whole blood samples for RNA analysis occurred at the same time, 180 days after the BNT162b2 booster dose. Serum samples for antibody analysis were collected employing vacutainer clot-activator tubes (BD Biosciences, San Jose, CA). Peripheral blood samples for RNA-seq analysis were collected using TempusTM Blood RNA Tube (Thermo Fisher Inc.).

> **샘플 정보**
> - 샘플1: Coronavac(시노백) 또는 Covishield(아스트라제네카) 백신 (1차)접종자 5,345명
> - 샘플2: SARS-CoV-2 BNT162b2(화이자) 백신 부스터(3차)접종자 20명
> - 샘플2: Low-stable 그룹 10명(부스터접종 180일째에 SARS-CoV-2 항체 농도가 7,000 AU/mL 이하)(0.4% 미만) / High-stable 그룹 10명(항체 농도가 12,800 AU/mL 이상)(0.6% 이상)
> - 샘플1 데이터셋: serum specimens for SARS-CoV-2 antibodies analysis (추정)
> - 샘플2 데이터셋: serum specimens for antibodies analysis / whole blood samples for RNA analysis
> - 샘플2 디자인
> - ![image](https://github.com/user-attachments/assets/810d8f60-fcba-42d1-a210-9c96e0d56735)

**Serological assessment of SARS‐CoV‐2 IgG antibodies**

Serum specimens underwent comprehensive analysis to discern the qualitative and quantitative presence of IgG antibodies directed against the nucleoprotein or spike protein of SARS-CoV-2. The automated immunoassay technology was employed, specifically the SARS-CoV-2 IgG and SARS-CoV-2 IgG II Quant assays by Abbott Diagnostics (Abbott Parl, IL). The technique harnessed paramagnetic microparticles coated with either the nucleoprotein or the receptor binding domain (RBD) situated within the S1 subunit of the spike protein. This analytical protocol utilized chemiluminescence (CMIA) processes and outcome measurements are reported as an index for nucleoprotein detection or as arbitrary units per mL (AU/mL) for the spike protein, with a diagnostic threshold set above 1.4 for nucleoprotein reactivity and above 50.0 AU/mL for spike protein reactivity.

**RNA extraction and whole blood transcriptome**

Total RNA was extracted from whole peripheral blood samples stored in TempusTM Blood RNA Tubes using Tem- pusTM Spin RNA Isolation Kit (Thermo Fisher Scientific, San Jose, CA, USA) followed by treatment with TURBO DNA-freeTM Kit (Thermo Fisher Scientific, San Jose, CA, USA). Qubit 2.0 Fluorometer with the Qubit RNA Assay Kit (Life Technologies, Carlsbad, CA, USA), and TapeStation 2200 (Agilent Technologies, Santa Clara, CA, USA) were employed to assess the concentration, purity, and integrity of the RNA samples. Only those samples with an RNA integrity number (RIN) greater than 7.0 were used for subsequent analysis. An average of 0.5 μg of total RNA was utilized for library construction through the Illumina Stranded Total RNA Prep, and rRNA depletion was performed with Ribo-Zero Plus (Illumina, San Diego, CA, USA), following the manufacturer’s guidelines. RNA-Seq libraries were sequenced in an Illumina NextSeq 550 platform (75 bp paired-end reads). The quality of sequenced reads was assessed with FastQC30, and trimming was carried out using BBDuk (https://sourceforge. net/projects/bbmap). STAR tool version 2.7 was employed for the alignment of the reads onto the human genome reference (GRCh38.p14)31. The sequencing metrics were assessed with RNA-SeQC software32.

**Identification of differentially expressed genes, alternative splicing events, and functional enrichment**

We compared the groups exhibiting low and high stability concentrations of antibodies to identify genes with baseline expression that could potentially serve as predictors for the BNT162b2 vaccination outcome. Differen- tial gene expression analysis was performed through the DEGRE package for R33, which identifies Differentially Expressed Genes (DEGs) in a pairwise manner and considers the insertion of the individuals’ age as random effects in the experimental design. It also has a preprocessing step responsible for filtering genes that could impair DEGs’ inference. For DEGs’ inference, DEGRE uses a Generalized Linear Mixed Model (GLMM) with a negative binomial distribution. Due to the limited number of samples, a stringent threshold for DEGs was set with adjusted p-values or q-values < 0.005.
To detect Differential Alternative Splicing Events (DASE) from the RNA-Seq data, the replicate multivariate analysis of transcript splicing (rMATS v.4.1.2)34 was employed. This approach identified different alternative splicing events including skipped exon (SE), alternative 5’ splice site (A5SS), alternative 3’ splice site (A3SS), mutually exclusive exons (MXE), and retained intron (RI) events. A significance threshold for alternative splicing events was set with a false discovery rate (FDR) < 0.01. 35–37

Combined enrichment analysis of DEGs and DASE genes was conducted using Enrichr software incorporating Reactome (RT), Kyoto Encyclopedia of Genes and Genomes (KEGG), Gene Ontology (GO) Consortium38–40 and DisGeNET41. Stringent adjusted p-values < 0.05 indicated significant enrichments and only the top 10 significant terms were considered.

**Co‐expression modules in tissue‐specific networks and interactome analysis**

The HumanBase tool (https://hb.flatironinstitute.org/) was employed to identify coherent gene clusters in blood tissue-specific networks from the DEGs and DASE genes. Genes within a cluster share local network neighbor- hoods, forming a cohesive, specific functional module with systematic association. The approach is based on shared k-nearest-neighbors (SKNN) and the Louvain community-finding algorithm. It can mitigate the impact of highly connected genes and highlight the local network structure by establishing connections between genes that are likely to be functionally clustered.

Additionally, data from the Biological General Repository for Interaction Datasets—BioGrid concerning curated protein and genetic interactions from humans were used to construct an interaction network from the DEGs and DASE genes. Then, information about interactions with SARS-CoV-2 viral proteins, obtained from the BioGRID COVID-19 Coronavirus Curation Project, was integrated into the network. The weights of the nodes were calculated by adding the inverse of the log2 Fold-Change value (1/log2FC). The software Gephi 0.943 was employed to visualize interactions.

> **method**
> - DEG분석: DEGRE 사용(qv<0.005)
> - DASE(Differential Alternative Splicing Events) 분석: rMATS 사용(fdr<0.01)
> - Enrichment 분석: Enrichr 사용(pv<0.05)
> - Co-expression module 식별: HumanBase 사용(혈액 조직 특이적 네트워크에서 DEGs와 DASEGs에서 패턴이 일관된 유전자 클러스터를 식별)
> - 상호작용 네트워크: BioGRID 사용(sars-cov-2단백질과 human단백질 사이 상호작용 네트워크)
> - 상호작용 네트워크의 노드가중치 할당: 1/log2FC 할당
> - 상호작용 네트워크 시각화: Gephi v.0.9.4 사용

**Variant calling and allelic imbalance analysis**

In the context of analyses pertaining to expressed SNVs (eSNVs) and insertions/deletions (indels), an allelic imbalance analysis was conducted based on a computational pipeline, PipASE, designed for the detection of Allele-Specific Expression (ASE) within transcriptome data44. Initially, the sequencing quality parameters were assessed for each FASTQ file using FastQC (https://www.bioinformatics.babraham.ac.uk/projects/fastqc/). Next, bad-formed reads were removed using Trimmomatic45. The filtered reads were aligned to the human GRCh38. p14 reference genome assembly using STAR v3.7 software31. Subsequently, the mapped sequences underwent additional processing using SAMtools, which involved sorting, indexing, and read selection based on mapping quality parameters (MAPQ ≥ 30) in BAM files46. Then, we masked duplicate reads and performed variant calling in RNA-seq data using MarkedDuplicates and HaplotypeCaller from GATK v4.1, respectively47,48. We used ASEReadCounter to determine the read counts for reference and alternative alleles in each position49.

The differential expression of genetic variants across the human genome was calculated by the reference allele ratio (ref ratio) in each sample using the following equation: ref ratio = (# of reads with the reference allele)/(# of reads with the reference allele + # of reads with the alternative allele). We required coverage of at least twelve reads per variant site for differential ASE analysis.

**Splice site alteration and haplotype identification around the identified eSNVs from DASE genes**

In silico analysis was performed to investigate splicing site alterations around the eSNVs found in the DASE genes. The prediction analysis was performed using ESEfinder and NNSplice tools, with corresponding prediction score thresholds and sequence lengths to reach a sensitivity, and specificity ≥ 80%50,51. To perform read-based phasing analysis the HapCUT2 was used with default parameters accessing germline WES BAM files and respec- tive VCFs files. The analysis limitation includes the infeasibility of linking distant variants in haplotypes, since Illumina technology generated short read lengths (100–250 bases)52.

**Sequence‐based HLA typing using RNA‐seq data**

The HLA alleles identification was performed directly from RNA-Seq reads in each sequence. First, RNA-Seq reads in fastq format were mapped to the human chromosome 6 (GRCh38.p14) using bowtie253. The mapped sequences were assembled into 200 bp contigs using the TASR tool54 and aligned to HLA reference sequences using the NCBI BLAST + 2.13.0 package (https://blast.ncbi.nlm.nih.gov/Blast.cgi). The following alignment parameters were used: -b 5 -v 5. The HLA reference sequences of classes I and II genotypes were retrieved in fasta format from the IMGT/HLA database. After alignment, the selected sequences were used to predict HLA alleles in the HLAminer tool with the default parameters55.

> **method (2)**
> - eSNV 식별: GATK v4.1의 MarkedDuplicates, HaplotypeCaller 사용
> - ASEG(allele specific expresed gene) 분석: PipASE 사용 (allelic imbalance에 의한 차등발현비율을 측정함)
> - ASEG의 eSNV들의 스플라이싱 사이트 변화 예측: ESEfinder, NNSplice 사용
> - HLA 유전자형 분석: HLAminer 사용

> **분석 summary**
> RNA-seq 데이터를 기반으로 유전자 변이(eSNVs)를 분석하고, 대립형질 불균형(ASEG)을 분석함. 이를 통해 BNT162b2 백신 반응과 관련된 유전자 변이를 식별하고 이 변이가 백신 효과에 미친 영향을 분석함.
> DASE 유전자에서 발견된 변이가 스플라이싱 사이트에 미치는 영향을 예측하고 HLA 대립유전자 분석을 통해 백신 반응에 중요한 유전자들을 분석함.

**Institutional review board statement**

The study was conducted in accordance with and under the approval of the Pedro Ernesto University Hospital Ethical Committee code: CAAE 0135320.0.0000.5259 version, approved on 01 Sept 2021 version 4).

**Informed consent statement**

Informed consent was obtained from all subjects involved in the study or their representatives. Written informed consent was obtained from the participants to publish this paper.


