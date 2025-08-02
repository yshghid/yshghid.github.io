---
date : 2025-07-02
tags: ['2025-07']
categories: ['luck']
bookHidden: true
title: "7월 2일 (오랜만에 루틴)"
---

# 7월 2일 (오랜만에 루틴)

#2025-07-02

---

먼가 잠오지만,, 마음도 쳐지구 해서 독서실옴 2시간만 코테 하고 30분 라디오 듣고 집에 가야겠다.

> 오늘 한일
> - 코테 dfs 5개 풀기
> - 리비전 조금

> 내일 할일
> - 리비전 마저하기
> - 인적성 1회
> - 코테 bfs 풀기

cf) 오늘푼거중에 헷갈린것.

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

아근데 ㅋㅋ ㅠㅠ 도망가고싶다
