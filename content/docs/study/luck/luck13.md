---
date : 2025-06-17
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 17일"
---

# 6월 17일

#2025-06-17

---

오늘할일
- 인적성 1회
- 코테 1문제
- 리비전작업
- 컴활강의듣기

### 1. 인적성

풀이문항
- 언어 - 4/4, 3/4 추리 - 5/5, 4/5 추리 - 5/5, 5/5 공간지각 - 1/4, 1/1

총평

- 오늘 kt 모고를 풀었는데 공간지각빼고는 풀만햇다 공간지각은 규칙찾긴햇는데 넘늦게찾음... 담에다시풀어보기. 

### 2. 코테

문제: 연결 요소의 개수 구하기 https://school.programmers.co.kr/learn/courses/30/lessons/43162

정답

```python
def solution(n, computers):
    visited = [False] * n

    def dfs(node):
        visited[node] = True
        for next_node in range(n):
            if computers[node][next_node] == 1 and not visited[next_node]:
                dfs(next_node)

    network_count = 0
    for i in range(n):
        if not visited[i]:
            dfs(i)
            network_count += 1

    return network_count
```

정답풀이

```plain text
n = 3
computers = [[1, 1, 0], 
             [1, 1, 0], 
             [0, 0, 1]]	
return = 2

1. visited = [False] * n
2. 현재 idx에서 탐색법: visited[idx] = True 하고
-> for next_node in range(n): 연결이고 미방문이면 다음탐색
(-> computers[idx][next_node] == 1 이고 not visited[next_node]이면 dfs(next_node))
3. 전체 탐색
for i in range(n): 미방문이면 dfs(i), 끝나면 cnt+=1
```

(내풀이)

```plain text
n = 3
computers = [[1, 1, 0], 
             [1, 1, 0], 
             [0, 0, 1]]	
return = 2

1. visited = [0, 0, 0]
2. idx=0: 방문안함 cnt+=1
visited = [1, 0, 0]
connected = [1, 1, 0] -> 1인 인덱스는 1
3. 
visited = [1, 1, 0] 
connected = [1, 1, 0] -> 1인 인덱스 없음 -> 끝
4. idx=1: 방문함 pass
5. idx=2: 방문안함 cnt+=1
visited = [1, 1, 1]
connected = [0, 0, 1] -> 1인 인덱스 없음 -> 끝
```
