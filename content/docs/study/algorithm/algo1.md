---
date : 2025-07-02
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "DFS #1 #2 #3"
bookComments: true
---

# DFS #1 #2 #3

#2025-07-02

---

### 1. 숫자 이어 방문하기

#문제 설명

아래 그림처럼 숫자가 노드로 연결되어 있습니다.

```plain text
1 - 2 - 3
```

연결된 노드를 DFS 방식으로 방문하면서 출력하세요.

#입력 형식

```plain text
node = 3
edge = 2
edges = [[1, 2], [2, 3]]
```

#출력 예시

```plain text
1 2 3
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

#graph = {1: [2], 2: [1, 3], 3: [2]}
visited = set()

def dfs(v):
    visited.add(v)
    print(v, end=' ')
    for neighbor in graph[v]:
        if neighbor not in visited:
            dfs(neighbor)

dfs(1)
```

#해설

```plain text
1. graph 딕셔너리 만든다.
2. visited 집합 만든다.
3. dfs 함수 만든다: visited 처리 -> neighbor 찾기 -> neighbor visited 아니면? dfs 수행
4. dfs(1)로 run
```

---

### 2. 알파벳 노드 탐색하기

#문제 설명

다음은 알파벳 노드들이 연결된 무방향 그래프입니다.
시작 노드가 'A'일 때, DFS 순서대로 방문한 노드를 출력하세요.

#입력 예시

```plain text
edges = [['A', 'B'], ['A', 'C'], ['B', 'D']]
```

#출력 예시

```plain text
A B D C
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
    visited.append(v)
    print(v, end=' ')
    for neighbor in graph[v]:
        if neighbor not in visited:
            dfs(neighbor)
dfs('A')
```

---

### 3. 숫자 노드 거꾸로 출력하기

#문제 설명

다음과 같이 연결된 무방향 그래프가 있습니다.

```plain text
1 - 2 - 3 - 4
```

시작 노드를 4로 하여 DFS 순서대로 방문한 노드를 출력하세요.

#입력 예시

```plain text
edges = [[1, 2], [2, 3], [3, 4]]
```

#출력 예시

```plain text
4 3 2 1
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
    print(v, end = ' ')
    for neighbor in graph[v]:
        if neighbor not in visited:
            dfs(neighbor)

dfs(4)
```

#

#출처

https://chatgpt.com/share/68653560-042c-8000-a96b-e790fd14905a








