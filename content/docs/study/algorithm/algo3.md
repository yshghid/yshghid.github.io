---
date : 2025-07-03
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "BFS #1 #2"
bookComments: true
---

# BFS #1 #2

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
1. graph 만든다
2. 인접 리스트 정렬
3. bfs 만든다.
i) visited 만들고 queue 만들어서 start만 넣는다. answer인 visited_order 만든다. 
ii) queue가 빌때까지 다음을 수행: queue의 첫번째값 v를 꺼냄. v가 미방문이면? visited와 visited_order에 v추가하고. v의 이웃을 봣을때 미방문이면? queue에 추가.
(dfs는 미방문이면? dfs(u) 였는데 bfs는 queue에 추가.)
4. bfs 돌린다.
```

#

### 2. 모든 노드까지의 최단 거리

#문제 설명

노드 개수 n, 에지 개수 m이 주어질때 1번 정점에서 시작하여, 각 정점까지의 최소 간선 수(거리)를 구하시오.
정점은 1번부터 n번까지 있으며, 양방향 간선으로 연결되어 있다.

#입력 형식

```plain text
6 5
1 2
1 3
2 4
3 5
5 6
```

#출력 예시

```plain text
1번에서 1번까지 거리: 0
1번에서 2번까지 거리: 1
1번에서 3번까지 거리: 1
1번에서 4번까지 거리: 2
1번에서 5번까지 거리: 2
1번에서 6번까지 거리: 3
```

#설명

BFS는 모든 간선의 가중치가 동일할 때 최단 거리를 구하는 데 사용됩니다. 큐를 이용해 한 단계씩 거리 정보를 갱신하며 탐색합니다.

#정답

```python
from collections import deque

n, m = map(int, input().split())
graph = {i:[] for i in range(1, n+1)}

for _ in range(m):
    a, b = map(int, input().split())
    graph[a].append(b)
    graph[b].append(a) #양방향 그래프

# 거리 저장용 리스트 (초기값 -1: 방문하지 않음)
distance = [-1] * (n + 1)
start = 1
distance[start] = 0

# BFS 실행
queue = deque([start])

while queue:
    v = queue.popleft()
    for neighbor in graph[v]:
        if distance[neighbor] == -1:  # 아직 방문하지 않은 노드
            distance[neighbor] = distance[v] + 1
            queue.append(neighbor)

# 결과 출력
for i in range(1, n+1):
    print(f"1번에서 {i}번까지 거리: {distance[i]}")
```

#풀이

```plain text
1. graph 만들기
2. 노드 6개라치면 distance = [-1, 0, -1, -1, -1, -1, -1] 만들기 (1번 노드는 시작이니깐 0)
3. queue 만들기
4. queue가 빌때까지 다음을 수행: 첫번째값 v 꺼내고 이웃을봤을때 미방문이면 이웃의 distance는 v의 distance+1로 설정하고 queue에 u를 추가.
```

#

#출처 https://chatgpt.com/share/6866853c-5e10-8000-af91-f5b708d579ad
