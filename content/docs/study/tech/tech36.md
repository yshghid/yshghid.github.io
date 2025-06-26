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
def DBSCAN(sequence, eps, min_samples):
    cores = []
    clusters = []

    for nt in sequence:
        neighbors = find_neighbors(nt, eps)

        if len(neighbors) >= min_samples:
            label of nt = 1  #core
            append nt in cores

    for core in cores:
        label, clusters = expand_cluster(core, neighbors, eps, min_samples)
        
    label of nt = -1 for nt in sequence if not in clusters #noise not in cluster

    return clusters
```

```python
def MUTCLUST(sequence, eps_scaler, dim_factor, min_samples):
    ccms = []
    hscore = []
    deps = []
    label = []
    clusters = []

    for nt in sequence:
        hscore[nt], deps[nt] = calculate_hscore(nt), calculate_deps(nt)
        append nt in ccms if select_ccm(hscore, deps, min_samples)

    for ccm in ccms:
        label of ccm = 1 #core
        clusters = expand_cluster(ccm, sequence, eps, min_samples, eps_scaler, dim_factor)

    label of nt = -1 for nt in sequence if not in clusters #noise

    return hscore, ccms, clusters
```

```python
#functions used in dbscan()

def expand_cluster(cur_nt, cur_neighbors, min_samples, clusters): #expand cluster of cur_nt
    for ne in cur_neighbors:
        ne_neighbors = find_neighbors(ne, eps)

        if ne_neighbors >= min_samples: 
            label of ne = 0 #border
            append ne in clusters[cur_nt]
            append ne in cur_neighbors
        else: 
            label of nt = -1 #noise in cluster
            append ne in clusters[cur_nt]

    return label, clusters

def find_neighbors(nt, eps):
    for potential_ne in sequence:
        append potential_ne in neighbors if euclidean distance <= eps

    return neighbors
```

```python
#functions used in mutclust()

def expand_cluster(cur_nt, cur_neighbors, min_samples, clusters): #expand cluster of cur_nt
    eps = []
    cur_deps = deps[cur_nt]
    ne = cur_nt

    while cur_deps < min_samples:
        ne = next_ne(ne)
        label of ne = 0 #border
        append ne in clusters[cur_nt]

        ne_deps = deps[ne]
        cur_deps = diminish_deps(cur_deps, ne_deps, dim_factor) #diminish cur_deps by ne_deps
        eps[cur_nt] = cur_deps

    return label, clusters

def calculate_hscore():
    freq, ent, ratio = info.freq, info.ent, info.ratio #frequency, entropy, ratio are pre-calculated
    hscore = np.log2(ratio * ent * 100 + 1)
    return hscore

def calculate_deps(hscore):
    #params
    #EPS_SCALER = 10

    deps = ceil(eps_scaler * hscore)
    return deps

def select_ccm():
    #params
    #MIN_MUTATIONS = 5
    #CCM_MIN_HSCORE_SUM = 0.05
    #CCM_MIN_HSCORE_AVR = 0.01
    #CCM_MIN_HSCORE = 0.03
 
    eps_temp = deps[nt]

    #calculate statistics within eps_temp of nt
    if count of mutation < MIN_MUTATIONS:
        return False
    if sum of hscore < CCM_MIN_HSCORE_SUM:
        return False
    if average of hscore < CCM_MIN_HSCORE_AVR:
        return False
    if min of hscore < CCM_MIN_HSCORE:
        return False
    return True

def next_ne(ne):
    return next nt

def diminish_deps():
    #params
    #EPS_SCALER = 10
    #DIMINISHING_FACTOR = 3
```
