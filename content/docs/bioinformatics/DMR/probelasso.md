---
author: "kinda"
date: 2024-07-28
title: "[DMR/논문] Probe Lasso: A novel method to rope in differentially methylated regions with 450K DNA methylation data"
categories: ["Bioinformatics"]
tags: ["DMR"]
---

# [DMR/논문] Probe Lasso: A novel method to rope in differentially methylated regions with 450K DNA methylation data

## Abstract
The speed and resolution at which we can scour the genome for DNA methylation changes has improved immeasurably in the last 10 years and the advent of the Illumina 450K BeadChip has made epigenome-wide association studies (EWAS) a reality. The resulting datasets are conveniently formatted to allow easy alignment of significant hits to genes and genetic features, however; methods that parse significant hits into discreet differentially methylated regions (DMRs) remain a challenge to implement. In this paper we present details of a novel DMR caller, the Probe Lasso: a flexible window based approach that gathers neighbouring significant-signals to define clear DMR boundaries for subsequent in-depth analysis. The method is implemented in the R package ChAMP (Morris et al., 2014) and returns sets of DMRs according to user-tuned levels of probe filtering (e.g., inclusion of sex chromosomes, polymorphisms) and probe-lasso size distribution. Using a sub-sample of colon cancer- and healthy colon-samples from TCGA we show that Probe Lasso shifts DMR calling away from just probe-dense regions, and calls a range of DMR sizes ranging from tens-of-bases to tens-of-kilobases in scale. Moreover, using TCGA data we show that Probe Lasso leverages more information from the array and highlights a potential role of hypomethylated transcription factor binding motifs not discoverable using a basic, fixed-window approach.

**Keywords**

Differentially methylated regions; DNA methylation; Epigenetics; EWAS; Illumina 450K BeadChip

## 1. Introduction

DNA methylation is an essential epigenetic modification for normal mammalian development. It refers to the addition of a methyl group at the 5′ position of cytosine nucleotides (C) to form 5-methylcytosine (mC) and in mammalian cells occurs predominantly at CpG dinucleotides. CpG dinucleotides are underrepresented in mammalian genomes but the majority of these loci (70–80%) in a given cell population exhibit high levels of methylation (mClocus: >85%). CpGs that remain constitutively unmethylated tend to cluster into CpG-rich regions called CpG islands (CGIs). Curiously, pluripotent stem cells harbour an additional 33% of mC at non-CG (CpH) dinucleotides, however; this epigenetic mark is less stable and, consequently, these loci often exist as partially methylated (mClocus: 25–50%). Although in theory every methylated cytosine has the potential to become de-methylated, less than 22% of autosomal CpGs are dynamically regulated 
[2]
. Nevertheless, the prevailing pattern of variation in DNA methylation leaves a cell-specific stamp, which together with other epigenetic alterations such as histone modifications and non-coding RNAs, contribute to a series of exquisitely coordinated mechanisms that control gene expression both temporarily and spatially.

Correct acquisition of DNA methylation in proliferating cells is governed by the DNA methyltransferases (DNMT), a family of three catalytically-active enzymes comprising maintenance (DNMT1) and de novo (DNMT3a and DNMT3b) functions. Perturbation of these genes in mouse results in a range of detrimental phenotypes, which highlights the indispensable role of DNA methylation in normal development. These phenotypes include genome-wide partial methylation loss, developmental delay, erroneous germline imprints, sterility and embryonic lethality.

Given the clear importance of DNA methylation, concerted efforts are underway to understand the impact of more subtle DNA methylation differences on normal development and disease. Our understanding is gradually coming into focus due to a number of high-information content methylation technologies that have emerged in the last 5 years (reviewed in 
[3]
). These include whole genome bisulfite sequencing (WGBS; 
[4]
, 
[5]
), methylated immunoprecipitation sequencing (MeDIP-seq; 
[6]
), reduced-representation bisulfite sequencing (RRBS; 
[7]
) and the Illumina Infinium Human Methylation 450K BeadChip (herein termed, “450K BeadChip”; 
[8]
). All these platforms are capable of generating genome-wide or whole genome methylation profiles (“methylomes”) and deliver high-information content, albeit with different foci 
[9]
. For example, although WGBS is not amenable to studying large cohorts (due to the number of reads required to cover each cytosine with sufficient depth), it can resolve entire methylomes at single nucleotide-resolution; on the other hand, 450K BeadChips only assay approx. 1.8% of CpGs but are highly amenable to studying large cohorts – a critical requirement for statistical power; MeDIP and RRBS lie somewhere in between. All the aforementioned technologies with the exception of MeDIP-seq, use bisulfite converted DNA to resolve mC at single base-resolution; in contrast, MeDIP uses an antibody to enrich for the methylated fraction of the genome and provides a region-based “consensus” methylation level, with resolution concomitant with sequence insert size 
[10]
.

Because sequence-based approaches often provide broad and uninterrupted methylomic coverage it is not surprising that these technologies are responsible for identifying the bulk of differentially methylated regions (DMRs) that distinguish cell-, tissue- and disease-specific phenotypes. DMRs are discrete genomic sequences that harbour a distinct methylation signature across a number of CpGs (and/or non-CpGs) capable of distinguishing one phenotype from another. Their identification and utility have far-reaching implications for clinical applications because they ultimately reduce the scale of the genome to a handful of regions; once DMRs are validated and replicated it paves the way for time-, cost- and effort-effective assays that will inform subsequent functional studies and deliver diagnostic tools.

Even though the majority of DMRs have been identified using sequencing-based methods, the majority of methylomes have been generated using the 450K BeadChip; for example, the latest version of the MARMAL-AID database 
[11]
 contains 450K data for more than 9000 samples from nearly 200 different tissues and almost 100 different diseases. For technical manufacturing reasons though, the coverage of CpGs on 450K BeadChips has to be restricted. As a result, and possibly for historical reasons, CpG distribution on the 450K BeadChip is skewed towards CGIs and genes. Moreover contiguous CpGs are not always covered. This has therefore opened up the challenge to implement a comprehensive algorithm for DMR calling on 450K BeadChip datasets. A simple approach is to count significant signals emanating from a fixed-size sliding-window. This way a DMR could be defined if a window (or contiguous windows) of certain size capture a specified number of significantly associated probes. As discussed above however, this is contentious due to the distribution of CpGs and risks restricting DMR calling to regions most heavily probed. There are a number of DMR calling methods within the public domain that have application to the 450K BeadChip. These include ‘Bump Hunting’ 
[12]
, ‘Block Finding’ 
[13]
, ‘AClust’ 
[14]
, and ‘DMRcate’ 
[15]
.

Here we introduce an alternative DMR calling method, the Probe Lasso. Probe Lasso utilises a flexible window (“probe-lasso”) based on probe density to gather neighbouring significant-signals to define clear DMR boundaries. The principal motive for developing this algorithm is to redirect subsequent analysis away from just probes/regions located in promoters/CGIs, which the array is skewed toward and leverage information from putatively important, but largely ignored, intergenic regions. To illustrate this we benchmark Probe Lasso against a fixed window approach. Probe Lasso shares similarities with another DMR calling method, ‘Comb-p’ 
[16]
 although there are notable differences; in particular, Comb-p uses auto-correlation data to first correct individual probe p-values, then defines DMRs based on peaks of corrected p-values. In contrast, Probe Lasso gathers neighboring significant signals from probes in regions that can extend according to the probe’s genomic/epigenomic annotation and then uses auto-correlation information to combine the p-values of probes within a DMR.

2. Materials and methods
2.1. Preprocessing and methylation-variable position (MVP) calling
Probe Lasso is implemented within the Bioconductor package ChAMP 
[1]
, and relies on a series of objects created using this package. The following provides a brief description of a typical workflow using ChAMP. Raw data (.idat files) are loaded using the champ.load function to derive a list object that contains, among other things, methylation levels (‘beta’) of probes for samples specified in a sample sheet (‘pd’) and detection p-values (‘detP’) for each probe. We remove samples with call rates (i.e., detP <0.01) less than 98%, and then remove probes that do not provide complete information across all samples. Beta values are inter-array normalised using one of a variety of publically available procedures with the champ.norm function and subject to singular variable decomposition (SVD) analysis with champ.svd to identify potential confounding factors. MVPs are then identified for appropriate contrasts using champ.mvp, which implements the limma package 
[17]
 and the resulting object is used for DMR calling using champ.lasso.

2.2. Dependencies
To call DMRs effectively champ.lasso requires each probe to have genetic and epigenetic feature annotation and polymorphism data. Genetic and epigenetic feature annotation is maintained in the Bioconductor package IlluminaHumanMethylation450kmanifest and contains information such as chromosome, mapping position, nearby genes and/or CGIs; polymorphism data is held in the Bioconductor package Illumina450ProbeVariants.db, which contains allele frequency information of variants within a probe, within 10 bp of target locus or at target locus for four different ancestry groups (African, American, Asian and European) derived from 1000 Genomes Project 
[18]
 data.

2.3. Probe Lasso rationale
Fig. 1
A illustrates that probe spacing on the 450K BeadChip is not uniform with regard to gene feature: probes within 200 bp of a transcription start site (“TSS200”) are most densely spaced whereas probes in 3′ UTRs and intergenic regions (“IGRs”) are least-densely spaced. Unsurprisingly, given the definition of CGIs and their derivatives 
[8]
, 
Fig. 1
B reveals that probe density decreases the further a probe maps from a CGI (CGI → shore → shelf → open sea). Furthermore, probe spacing at a specific gene feature covaries with its CGI relation (herein termed “genetic/epigenetic feature”), which diversifies probe spacing even more (
Fig. 1
C). Taken together, these figures show that gathering neighbouring significant signals on the 450K BeadChip requires a dynamic calling framework.

