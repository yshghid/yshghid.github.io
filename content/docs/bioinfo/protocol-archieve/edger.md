# edgeR

## 들어가며

> 본 포스팅은 아래 내용을 바탕으로 작성되었다. 
> - [Bioconductor **edgeR**](https://bioconductor.org/packages/release/bioc/html/edgeR.html)
> - [RNA Sequence Analysis in R: *edgeR*](https://web.stanford.edu/class/bios221/labs/rnaseq/lab_4_rnaseq.html)

## 1. edgeR, baySeq 설치


```
> if (!require("BiocManager", quietly = TRUE))
    install.packages("BiocManager")
> BiocManager::install("edgeR")
> BiocManager::install("baySeq") #example dataset
```
```
> R.version
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
```

## 2. DE analysis
