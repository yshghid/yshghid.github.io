---
date : 2025-06-26
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "﹂슈도코드"
bookComments: true
---

# ﹂슈도코드

#2025-06-26

---

### #Clustering

```
def dbscan(sequence, eps, min_samples):
    label = []
    clusters = []
    for nt in sequence:
        neighbors = count_neighbors(sequence, eps)
        if len(neighbors) >= min_samples: 
            label[nt] = 1  #core
            clusters = expand_cluster(sequence, neighbors, eps, min_samples)

        if not in clusters:
            label[nt] = -1 #noise
    return clusters
```

```
def mutclust(sequence, eps_scaler, dim_factor, min_samples):
    hscore = []
    label = []
    clusters = []
    for nt in sequence:
        hscore[nt] = calculate_hscore(sequence)

    ccms = find_ccm(hscore, min_samples)

    for nt in sequence:
        if nt in ccms:
            label[nt] = 1 #core
            clusters = expand_cluster(sequence, eps_scaler, dim_factor)

        if not in clusters:
            label of nt = -1 #noise
    return hscore, ccms, clusters
```
