---
date : 2025-07-02
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "DFS #4 #5"
bookComments: true
---

# DFS #4 #5

#2025-07-02

---

### 4. 연결 요소 개수 구하기

#문제 설명

노드가 여러 개 있고, 일부 노드만 연결되어 있을 수 있습니다.
이 그래프에서 연결 요소의 개수를 구하세요.
(연결 요소: DFS로 모두 도달할 수 있는 노드 묶음)

#입력 형식

```plain text
node = 6  
edges = [[1, 2], [2, 3], [4, 5]]
```

#출력 예시

```plain text
3
```

#정답

```python
graph = {}

for a, b in edges:
    if a not in graph:
        graph[a] = []
    if b not in graph:
        graph[b] = []
    graph[a].append(b)
    graph[b].append(a)

visited = set()

def dfs(v):
    visited.add(v)
    for u in graph[v]:
        if u not in visited:
            dfs(u)
cnt = 0
for v in range(1, node+1):
    if v not in visited:
        dfs(v)
        cnt += 1

print(cnt)
```

---

### 5. 특정 노드와 연결된 노드 수 구하기

#문제 설명

DFS를 사용해 시작 노드에서 도달할 수 있는 노드가 총 몇 개인지 구하세요.
(자기 자신 포함)

#입력 형식

```plain text
node = 6  
edges = [[1, 2], [2, 3], [4, 5]]  
start = 2
```

#출력 예시

```plain text
3
```

#정답

```python
graph = {}

for a, b in edges:
    if a not in graph:
        graph[a] = []
    if b not in graph:
        graph[b] = []
    graph[a].append(b)
    graph[b].append(a)

visited = set()

def dfs(v):
    visited.add(v)
    for u in graph[v]:
        if u not in visited:
            dfs(u)

dfs(start)
print(len(visited))
```

#

#출처

https://chatgpt.com/share/68653560-042c-8000-a96b-e790fd14905a
