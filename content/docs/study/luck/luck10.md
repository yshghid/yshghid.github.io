---
date : 2025-06-15
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 15일"
---

# 6월 15일

#2025-06-15

---

> 오늘한일
> - 인적성 1회
> - 코테 1개
> - 리비전작업

#코테

문제: 완전범죄 https://school.programmers.co.kr/learn/courses/30/lessons/389480

##입출력 예

```plain text
info = [[1, 2], [2, 3], [2, 1]]
n = 4
m = 4
result = 2
```

##정답

```python
def solution(info, n, m):
    from collections import deque

    N = len(info)
    visited = [[False] * m for _ in range(n)]
    visited[0][0] = True
    queue = deque()
    queue.append((0, 0, 0))  # (i번째 물건까지 처리, A의 누적 흔적, B의 누적 흔적)

    while queue:
        idx, a_sum, b_sum = queue.popleft()
        if idx == N:
            return a_sum  # 모든 물건을 훔쳤고, A의 흔적 누적 최소값 리턴

        a_cost, b_cost = info[idx]

        # A가 훔치는 경우
        if a_sum + a_cost < n and not visited[a_sum + a_cost][b_sum]:
            visited[a_sum + a_cost][b_sum] = True
            queue.append((idx + 1, a_sum + a_cost, b_sum))

        # B가 훔치는 경우
        if b_sum + b_cost < m and not visited[a_sum][b_sum + b_cost]:
            visited[a_sum][b_sum + b_cost] = True
            queue.append((idx + 1, a_sum, b_sum + b_cost))

    return -1  # 모든 경우 탐색했는데도 실패한 경우
```

##틀린이유
```plain text
1행: A 1개, B 2개
3개 다 가능하다고 가정. 

[[1, 2], [2, 3], [2, 1]] -> [[2, 1], [1, 2], [2, 3]] (b가작은순으로 sorting)

N = len(info) = 3
0-> m-1 = 3 (o)
1-> 3-2 = 1 (o)
2 -> 1-3<0이므로 안함. n-2 = 2 (o)
답: 2
```
에서 3개 다 가능하다고 가정<< 이 아니라 3개 다 훔치는게 문제였음 3개가안되면 2개만 훔치는게아니라 그냥 -1 하고 끝내면됨

```python
def solution(info, n, m):
    N = len(info)
    
    for _ in range(N):
        cur_m, cur_n, cur_N = m, n, len(info)
        A_flag, B_flag = False, False
        
        for i in range(cur_N):
            info.sort(key=lambda x : x[1])
            
            cur_a = info[i][0]
            cur_b = info[i][1]
            
            while not A_flag: 
                while not B_flag:
                    if cur_m > cur_b:
                        cur_m -= cur_b
                    else:
                        B_flag = True #B가잡힘
                        break
                
                if cur_n > cur_a:
                    cur_n -= cur_a
                else:
                    A_flag = True #A가잡힘
                    break
        
        if A_flag and B_flag: #둘다 잡힘
            info.pop(-1)
        
        else: #A나 B가 안잡힘
            break
        
    if A_flag and B_flag:
        return -1
    else: 
        return cur_n
```

> 오늘은 오전에 인적성이랑 코테 풀고 오후에 리비전 조금하구 집와서 저녁으로 치킨먹고 운동갔다가 씻고 밤에 코테랑 인적성 공책정리를했다.
