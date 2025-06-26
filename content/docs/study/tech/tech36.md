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

```python
def dbscan(sequence, eps, min_samples):
    label[nt] = -1 for nt in sequence
    clusters = []

    for nt in sequence:
        neighbors = count_neighbors(nt, eps)
        if len(neighbors) >= min_samples: 
            label[nt] = 1  #core
            label, clusters = expand_cluster(nt, neighbors, eps, min_samples)
        
    #noise(not in cluster) if label[nt] = -1
    return clusters
```

```python
def mutclust(sequence, eps_scaler, dim_factor, min_samples):
    hscore = []
    eps = []
    label = []
    clusters = []

    for nt in sequence:
        hscore[nt] = calculate_hscore(nt)
        eps[nt] = calculate_eps(nt)

    ccms = find_ccm(hscore, min_samples)

    for nt in sequence:
        if nt in ccms:
            label[nt] = 1 #core
            clusters = expand_cluster(nt, sequence, eps, min_samples, eps_scaler, dim_factor)

        if not in clusters:
            label of nt = -1 #noise

    return hscore, ccms, clusters
```

```python
#functions used in dbscan()

def expand_cluster(cur_nt, cur_neighbors, min_samples, clusters)
    #expand cluster of cur_nt / based on cur_neighbors, min_samples / and return clusters

    label[ne] = 0 for ne in cur_neighbors

    for ne in cur_neighbors:
        ne_neighbors = count_neighbors(ne, eps)
        if ne_neighbors >= min_samples: #border
            append ne in clusters[nt]
            append ne in neighbors
        else: #noise (in cluster)
            append ne in clusters[nt]

    return clusters
```
