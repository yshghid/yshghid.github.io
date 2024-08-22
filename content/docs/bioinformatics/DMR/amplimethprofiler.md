---
author: "kinda"
date: 2024-07-28
title: "[Paper] ampliMethProfiler"
categories: ["Works"]
tags: ["DMR"]
---

# [Paper] ampliMethProfiler: CpG methylation profiles of targeted deep bisulfite sequenced amplicons

## Abstract

**Background**

CpG sites in an individual molecule may exist in a binary state (methylated or unmethylated) and each individual DNA molecule, containing a certain number of CpGs, is a combination of these states defining an epihaplotype. Classic quantification based approaches to study DNA methylation are intrinsically unable to fully represent the complexity of the underlying methylation substrate. Epihaplotype based approaches, on the other hand, allow methylation profiles of cell populations to be studied at the single molecule level.

For such investigations, next-generation sequencing techniques can be used, both for quantitative and for epihaplotype analysis. Currently available tools for methylation analysis lack output formats that explicitly report CpG methylation profiles at the single molecule level and that have suited statistical tools for their interpretation.

**Results**

Here we present ampliMethProfiler, a python-based pipeline for the extraction and statistical epihaplotype analysis of amplicons from targeted deep bisulfite sequencing of multiple DNA regions.

**Conclusions**

ampliMethProfiler tool provides an easy and user friendly way to extract and analyze the epihaplotype composition of reads from targeted bisulfite sequencing experiments. ampliMethProfiler is written in python language and requires a local installation of BLAST and (optionally) QIIME tools. It can be run on Linux and OS X platforms. The software is open source and freely available at http://amplimethprofiler.sourceforge.net.

## Background

Locus-specific DNA methylation analysis is used widely in many research fields. Traditionally, Sanger sequencing was used as the standard technique to quantify the methylation state of a specific bisulfite-treated locus at single nucleotide resolution. Nowadays, next-generation sequencing techniques are used for high-throughput sequencing of bisulfite polymerase chain reaction (PCR) amplicons obtaining many thousands of sequences in a single sequencing run [1, 2]. In such a way, the methylation heterogeneity of a given locus can be studied at the single molecule level.

With high-throughput sequencing of bisulfite PCR amplicons, it is possible to investigate methylation diversity in a sample by looking directly at methylation profiles (epihaplotypes) of the individual cells in a population, rather than considering a single profile where CpG methylation is analyzed as a mixture of methylated and unmethylated CpGs [3]. Analysis of epihaplotype diversity is applicable to fields as diverse as carcinogenesis, developmental biology and plant biology [4–6].

Using this high-throughput approach, the epihaplotypes of the pool of cells that comprise the study sample can be treated as a population of haploid organisms. When considered in this way, notions and techniques derived from other fields, such as population genetics, ecology and metagenomics can be incorporated into protocols. In particular, several metrics, statistical methods and tools developed to analyze population structure can be easily imported and adapted for the analysis of methylation profiles generated from deep targeted sequencing. It is, therefore, important to develop tools that are able to extract locus-specific NGS methylation data in a format that can be easily imported into already available statistical tools, and that allow a user-friendly, basic statistical interpretation of this particular kind of data.

Here, we present ampliMethProfiler, a pipeline for the extraction and analysis of methylation profiles at the single molecule level from deep targeted bisulfite sequencing of multiple DNA regions. This tool provides functions to demultiplex, filter and extract methylation profiles directly from FASTA files. Among the various output formats that are available for the representation of methylation profile composition, ampliMethProfiler provides the Biological Observation Matrix (BIOM) [7] format, which allows the user to directly import methylation profiles into a wide range of meta-genomics analysis software tools. Also, several core analyses of the epihaplotype population structure of input samples can be automatically performed by the pipeline using a local installation of QIIME software [8].

## Implementation

**Input data**

AmpliMethProfiler (Fig. 1) requires three types of input files: a file containing the reads from the sequencer in FASTA format, a comma-separated file containing information on the sequenced regions, and a FASTA file containing the reference sequences of the analyzed regions. Optionally, a file containing metadata associated with each sample can be provided to enable the tool to perform a series of basic EpiHaplotype based Analyses (EHAs) on the pipeline outcome.

**Demultiplexing and filtering**

Reads from targeted bisulfite sequencing of multiple regions are demultiplexed by comparing their 5′ and 3′ ends with a list of provided PCR primers. The demultiplexing procedure is based on a user-provided percentage of similarity between the 5′ or 3′ end of a read sequence and the corresponding PCR primer sequences. Reads are filtered out if no match is found between at least one of the read ends or if, given a user-provided threshold, their length does not match.

**Extraction of methylation profiles**

First, amplicons from targeted bisulfite sequencing are aligned to the corresponding bisulfite-converted reference sequence using the locally installed BLASTn program [9]. Then, the tool inspects the C and CpG aligned positions for each input read. Bisulfite efficiency for each aligned read is computed as the percentage of conversion of non-CpG cytosine residues (green Cs in the reference sequence in the example below) to thymine residues (green Ts in the reference and bisulfite-converted reference sequences in the example below). If the percentage of non-CpG deaminated C residues (red Cs in the read sequence in the example below) over the total number of non-CpG C residues is below the given threshold, the read is discarded. In this latter case, positions for which residues other than C or T (A, G) or gaps are found are excluded from the assay (purple characters in the read sequence in the example below). A user provided threshold defines the minimum percentage of reference non-CpG cytosine residues to be assayed to consider the bisulfite efficiency estimate valid; if this percentage is below the given threshold the read is discarded. The methylation profile for each aligned read is determined by evaluating the deamination of CpG sites as a result of the bisulfite treatment.

For each CpG position in the aligned reference sequence (green Cs in the bisulfite-converted reference sequence in the example below), the corresponding position in the aligned read sequence is inspected. If a C is found in that position, then that site is considered methylated; if a T is found, then the site is considered unmethylated; and if alignment gaps or other bases (A or G) are found, the methylation state of the CpG site is reported as uncertain (marked in purple in the example below).

Methylation percentages for each site are then computed as the number of non-deaminated bases mapped on that site over the total number of non-ambiguously mapped positions. The same procedure is used to compute bisulfite efficiency for all C (non-CpG) sites. Then, the abundance of each distinct methylation pattern is evaluated for each sample. Such reports are created by counting, for each of the possible 2NCpG epihaplotypes (where NCpG stands for the number of CpG sites in the analyzed region), the number of passing filter reads containing the pattern.

**EpiHaplotype based analysis**

A series of exploratory EHAs are performed on the sample profile abundances obtained in the previous steps. These analyses are performed starting from the BIOM file containing methylation profile abundances and a metadata file reporting the characteristics for each analyzed sample. A local installation of biom tool [7] and QIIME software suite are employed for this purpose.

Three kinds of analyses are performed to summarize sample epihaplotype composition:

i) A series of summary statistics on the number of passing filter profiles in each sample are performed using the “biom summarize-table” command;
ii) A summary of samples’ taxonomic composition, computed as the abundance of profiles stratified by the number of methylated CpGs, is performed through QIIME’s summarize_taxa_through_plots.py module; and
iii) A heatmap, comparing relative abundances of methylation profiles between samples, where profiles (rows) are clustered by UPGMA hierarchical clustering, is created with QIIME’s make_otu_heatmap.py script.

Within-sample diversity (Alpha diversity), for samples and groups of samples in the study, is evaluated using QIIME’s alpha_rarefaction.py workflow, which performs the following steps:

1. Generate rarefied profile abundance tables for each sample (multiple_rarefactions.py);
2. Compute measures of alpha diversity for each rarefied OTU table (alpha_diversity.py);
3. Collate alpha diversity results (collate_alpha.py); and
4. Generate alpha rarefaction plots (make_rarefaction_plots.py).

The between-sample diversity (Beta diversity) between all pairs of samples in the study is evaluated using QIIME’s beta_diversity_through_plots.py workflow, which performs the following steps:

1. Rarefy profile abundance tables to remove sampling depth heterogeneity (single_rarefaction.py);
2. Compute beta diversity metrics (beta_diversity.py) using Bray–Curtis dissimilarity between methylation profile abundances of samples;
3. Run Principal Coordinates Analysis (principal_coordinates.py);
4. Generate 3D Emperor PCoA plots (make_emperor.py) and 2D PCoA plots (make_2d_plots.py); and
5. Compare distances within and between groups of samples using boxplots (make_distance_boxplots.py).

## Results

**ampliMethProfiler pipeline**

The ampliMethProfiler pipeline is composed of three functional modules (Fig. 1), implemented in three python modules: preprocessFasta.py, methProfiles.py, qiime_analysis.py. The preprocessFasta.py module generates, for each sequenced region, a quality filtered FASTA file containing the reads from that region that passed filtering. Importantly, it creates a new FASTA file for each analyzed region, whose entries are annotated with the ID of the region and of the sample. The methProfiles.py module runs on each demultiplexed, filtered FASTA file generated by preprocessFasta.py and computes CpG methylation profile matrices, profile counts and several summary and quality statistics. For each analyzed region, methProfile.py returns the following output files.

**Summary and quality statistics file**

This file contains information about the number of reads that pass the filtering, the methylation percentage of each C in CpG sites, and the bisulfite efficiency for each C in non-CpG sites (Fig. 2a).






