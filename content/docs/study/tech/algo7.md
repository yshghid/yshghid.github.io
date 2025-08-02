---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#3 네트워크"
bookComments: true
---

# #3 네트워크

#2025-06-17

---

## 문제

#문제 설명

네트워크란 컴퓨터 상호 간에 정보를 교환할 수 있도록 연결된 형태를 의미합니다. 예를 들어, 컴퓨터 A와 컴퓨터 B가 직접적으로 연결되어있고, 컴퓨터 B와 컴퓨터 C가 직접적으로 연결되어 있을 때 컴퓨터 A와 컴퓨터 C도 간접적으로 연결되어 정보를 교환할 수 있습니다. 따라서 컴퓨터 A, B, C는 모두 같은 네트워크 상에 있다고 할 수 있습니다.

컴퓨터의 개수 n, 연결에 대한 정보가 담긴 2차원 배열 computers가 매개변수로 주어질 때, 네트워크의 개수를 return 하도록 solution 함수를 작성하시오.

#제한사항

- 컴퓨터의 개수 n은 1 이상 200 이하인 자연수입니다.
- 각 컴퓨터는 0부터 `n-1`인 정수로 표현합니다.
- i번 컴퓨터와 j번 컴퓨터가 연결되어 있으면 computers[i][j]를 1로 표현합니다.
- computer[i][i]는 항상 1입니다.

#입출력 예

| n | computers | return |
| --- | --- | --- |
| 3 | [[1, 1, 0], [1, 1, 0], [0, 0, 1]] | 2 |
| 3 | [[1, 1, 0], [1, 1, 1], [0, 1, 1]] | 1 |

#입출력 예 설명

예제 #1

아래와 같이 2개의 네트워크가 있습니다.

![image](https://github.com/user-attachments/assets/d1554ccd-4f40-431e-b883-8d2b2f4cfd84)

예제 #2

아래와 같이 1개의 네트워크가 있습니다.

![image](https://github.com/user-attachments/assets/c60df42c-443b-4ddc-bcfe-3b88c6a61a2e)

#정답

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

## 풀이

#단계별로보기

1. visited array 만들고
2. dfs 함수 만들고
3. 돌린다

##1 
```python
visited = [False] * n
```
```plain text
visited = [False, False, False, False, False]
```

##2

```python
def dfs(node):
    visited[node] = True
    for next_node in range(n):
        if computers[node][next_node] == 1 and not visited[next_node]:
            dfs(next_node)
```
```plain text
현재 idx에서 탐색법: visited[idx] = True 하고
-> for next_node in range(n): 연결이고 미방문이면 다음탐색
(-> computers[idx][next_node] == 1 이고 not visited[next_node]이면 dfs(next_node))
```

##3

```python
for i in range(n):
    if not visited[i]:
        dfs(i)
        network_count += 1
```
```plain text
전체 idx에서 탐색:
-> for i in range(n): 미방문이면 dfs(i), 끝나면 cnt+=1
```

