---
date : 2025-07-03
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "BFS #1"
bookComments: true
---

# BFS #1

#2025-07-03

---

### 1. 방문 순서 출력하기

#문제 설명

정점의 개수 n, 간선의 개수 m, 시작 정점 s가 주어진다.
이후 m개의 간선 정보(정점 a, 정점 b)가 주어진다.
인접한 정점들을 오름차순으로 방문한다고 할 때, BFS로 방문한 정점의 순서를 출력하시오.

#입력 형식

```plain text
5 4 1
1 2
1 3
2 4
3 5
```

#출력 예시

```plain text
1 2 3 4 5
```

#정답

```python
from collections import deque

# 입력
n, m, s = map(int, input().split())  # 정점 개수, 간선 개수, 시작 정점
graph = {i: [] for i in range(1, n+1)}

# 간선 입력
for _ in range(m):
    a, b = map(int, input().split())
    graph[a].append(b)
    graph[b].append(a)  # 양방향 그래프

# 인접 리스트 정렬 (오름차순 방문)
for node in graph:
    graph[node].sort()

# BFS 함수
def bfs(start):
    visited = set()
    queue = deque([start])
    visited_order = []

    while queue:
        v = queue.popleft()
        if v not in visited:
            visited.add(v)
            visited_order.append(v)
            for neighbor in graph[v]:
                if neighbor not in visited:
                    queue.append(neighbor)
    return visited_order

# 출력
order = bfs(s)
print(" ".join(map(str, order)))
```

#풀이

```plain text

```
