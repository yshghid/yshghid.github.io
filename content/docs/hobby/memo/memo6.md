---
date : 2025-06-15
tags: ['2025-06']
categories: ['메모']
bookHidden: true
title: "6월 15일"
---

# 6월 15일

#2025-06-15

---

> 오늘할일
> - 인적성 1회 - 언어: p.66-80 / 수리: p.153-166 / 추리: p.234-241 / 공간지각: p.322-329
> - 코테 1문제 
> - 리비전작업

#인적성

##풀이문항

언어 - 15/15, 12/15

수리 - 7/15, 3/7

추리 - 12/15, 8/12

공간지각 - 6/10, 3/6

##간단리뷰

언어 - 11번: 기반식 고인돌이랑 개석식 고인돌 비교해야대는데 탁자식이랑 기반식을 비교하는 실수를함 / 16번: 근거를 안찾고 느낌으로 배열했는데 다시읽어보니까 "여러 학자"를 받는단어가없으니 (가)가 1번이 안됨 근데 빨리읽어서 그거까지 안봣다 / 20번: (나)랑 (라)는좀 헷갈릴만한듯 다시풀기

수리 - 3,4번: 이건걍쉬웟는데 그마저도 암산을잘못함 / 7번: 항생제 판매량 보랬는데 전체를봄 1:30만에 풀수준이엇는데,, / 16번: 397.7 -> 439.9가 112% 증가냐는 문제였는데 400 -> 440으로 어림하니까 12%길래 넘어갓는데 10.6%여서 틀린선지엿음 너무어림하면안댄다 그리고 다시풀긴햇는데 3분걸림

추리 - 1,2번: 다시보니 암산해놓음 손으로쓰자 / 8번: 공식안쓰고 밴다이어그램으로그냥했는데 실수나오는거보니 공식쓰는게 낫나?? / 10번: 찬성팀 2명인걸 안읽음

공간지각 - 1번: 3번틀린거같앗는데 다시보니아님.. 1번이틀렷다. / 3번: 어이없음 이걸어케품 / 7번: 갑자기접는방향이 헷갈림 담에다시봐야할듯

##총평

언어 2:00, 수리 2:00, 추리 2:00, 공간지각 3:00으로 제한두고 했는데 수리랑 공간지각은 각각 3:00, 4:00으로 제한두는게 나을듯 특히 자료해석은 정말 혼돈의 카오스였다,,

그리고 연습장에풀고 책에 체크만했는데 체크도안하는게 조을거같음

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

오늘은 > 오전에 인적성이랑 코테 풀고 오후에 리비전 조금하구 집와서 저녁으로 치킨먹고 운동갔다가 씻고 밤에 코테랑 인적성 공책정리를했다.
