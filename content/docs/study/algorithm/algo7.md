---
date : 2025-07-19
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "BFS/DFS #2 네트워크"
bookComments: true
---

# BFS/DFS #2 네트워크

#2025-07-19

---

문제: 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 이때 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다. 컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

제한사항: 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다. 각 컴퓨터는 0부터 `n-1`인 정수로 표현합니다. i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다. computer[i][i]는 항상 1입니다.

입력: `n=3`, `computers=[[1, 1, 0], [1, 1, 0], [0, 0, 1]]`

출력: `2`

풀이: 1) visited 만들고 2) dfs 만들고 3) 돌린다

1:

```python
visited = [False] * n
```

2: dfs(node)일때 1) 현재 node를 visited 처리하고 2) 이웃 노드 next_node가 not visited이면 dfs(next_node)하기.

```python
def dfs(node):
    visited[node] = True
    for next_node in range(n):
        if computers[node][next_node] == 1 and not visited[next_node]:
            dfs(next_node)
```

3: n개 컴퓨터에 대해서 not visited이면 dfs로 visit하고 answer+=1하기.

```python
answer = 0
for i in range(n):
    if not visited[i]:
        dfs(i)
        answer += 1
```

정답:

```python
def solution(n, computers):
    visited = [False] * n
    
    def dfs(node):
        visited[node] = True
        for next_node in range(n):
            if computers[node][next_node] == 1 and not visited[next_node]:
                dfs(next_node)
                
    answer = 0
    for i in range(n):
        if not visited[i]:
            dfs(i)
            answer += 1
            
    return answer
```

#

링크: https://school.programmers.co.kr/learn/courses/30/lessons/43162
