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
    clusters = []

    for nt in sequence:
        neighbors = find_neighbors(nt, eps)
        if len(neighbors) >= min_samples: 
            label of nt = 1  #core
            label, clusters = expand_cluster(nt, neighbors, eps, min_samples)
        
    if not in clusters:
        label of nt = -1 #noise
    return clusters
```

```python
def mutclust(sequence, eps_scaler, dim_factor, min_samples):
    hscore = []
    deps = []
    label = []
    clusters = []

    for nt in sequence:
        hscore[nt] = calculate_hscore(nt)
        deps[nt] = calculate_deps(nt)

    ccms = find_ccm(hscore, deps, min_samples)

    for nt in sequence:
        if nt in ccms:
            label of nt = 1 #core
            clusters = expand_cluster(nt, sequence, eps, min_samples, eps_scaler, dim_factor)

        if not in clusters:
            label of nt = -1 #noise (not in cluster)

    return hscore, ccms, clusters
```

```python
#functions used in dbscan()

def expand_cluster(cur_nt, cur_neighbors, min_samples, clusters): #expand cluster of cur_nt
    label of ne = 0 for ne in cur_neighbors

    for ne in cur_neighbors:
        ne_neighbors = find_neighbors(ne, eps)
        if ne_neighbors >= min_samples: #border
            append ne in clusters[cur_nt]
            append ne in cur_neighbors
        else: 
            label of nt = -1 #noise (in cluster)
            append ne in clusters[cur_nt]

    return clusters

def find_neighbors(nt, eps):
    for potential_ne in sequence:
        append potential_ne in neighbors if euclidean_distance <= eps
    return neighbors
```

```python
#functions used in mutclust()

def expand_cluster(cur_nt, cur_neighbors, min_samples, clusters): #expand cluster of cur_nt
    eps = []
    cur_deps = deps[cur_nt]
    cur_ne = cur_nt

    while cur_deps < min_samples:
        cur_ne = next_ne(cur_ne)
        append cur_ne in clusters[cur_nt]

        ne_deps = deps[cur_ne]
        cur_deps = diminish_deps(cur_deps, ne_deps) #diminish cur_deps by ne_deps
        eps[cur_ne] = cur_deps

    return clusters

def calculate_hscore():

def calculate_deps():

def find_ccm():
```
