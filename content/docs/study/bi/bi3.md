---
date : 2025-01-02
weight: 602
tags: ['2025-01']
categories: ['BI']
bookHidden: true
title: "[코드] Pathway enrichment bubble plot of GO terms"
---

# [코드] Pathway enrichment bubble plot of GO terms

##### 2025.01.02

---

## Load package

```R
library(ggplot2)
```

## Set path
```R
setwd("/data/home/ysh980101/2307_kallisto")
getwd()
```
```plain text
'/data1/home/ysh980101/2307_kallisto'
```

## Draw bubble plot
```R
condition <- '150_con'
gpsource <- 'REAC'

df <- read.csv(paste0("./sleuth_ward/gprofiler/gprofiler_",condition,"_termsize.csv"))
df <- df[df$source == gpsource, ]
df$reg_type <- 'up'
df$nlog <- abs(df$negative_log10_of_adjusted_p_value)
df <- df[order(df$negative_log10_of_adjusted_p_value), ]

ggplot(df, aes(x = reorder(term_name, nlog), y = negative_log10_of_adjusted_p_value, size = intersection_size, color = nlog)) +
  geom_point(alpha = 0.6) +
  theme(axis.text.y = element_text(angle = 0, vjust = 0.5, hjust=1)) +
  labs(title = paste0("Bubble Plot - ",gpsource," / ",condition),
       x = "Term",
       y = "-log10(p-adj)",
       size = "Intersection Size",
       color = "-log10(p-adj)") +
  scale_size(range = c(1,10)) +
  scale_color_gradient2(low = "blue", mid = "white", high = "red") +
  coord_flip()
ggsave(filename = paste0("./bubble_plot_",condition,"_",gpsource,".png"), width =9, height = 9)
```
![image](https://github.com/user-attachments/assets/ce18a119-8f8b-48e7-91a8-88a09ed96041)
