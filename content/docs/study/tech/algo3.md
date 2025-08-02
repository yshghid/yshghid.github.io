---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#4 완전범죄"
bookComments: true
---

# #4 완전범죄

#2025-06-19

---

### 1. 문제

#문제 설명

A도둑과 B도둑이 팀을 이루어 모든 물건을 훔치려고 합니다. 단, 각 도둑이 물건을 훔칠 때 남기는 흔적이 누적되면 경찰에 붙잡히기 때문에, 두 도둑 중 누구도 경찰에 붙잡히지 않도록 흔적을 최소화해야 합니다.

물건을 훔칠 때 조건은 아래와 같습니다.

- 물건 i를 훔칠 때,
    - A도둑이 훔치면 `info[i][0]`개의 A에 대한 흔적을 남깁니다.
    - B도둑이 훔치면 `info[i][1]`개의 B에 대한 흔적을 남깁니다.
- 각 물건에 대해 A도둑과 B도둑이 남기는 흔적의 개수는 1 이상 3 이하입니다.

경찰에 붙잡히는 조건은 아래와 같습니다.

- A도둑은 자신이 남긴 흔적의 누적 개수가 `n`개 이상이면 경찰에 붙잡힙니다.
- B도둑은 자신이 남긴 흔적의 누적 개수가 `m`개 이상이면 경찰에 붙잡힙니다.

각 물건을 훔칠 때 생기는 흔적에 대한 정보를 담은 2차원 정수 배열 `info`, A도둑이 경찰에 붙잡히는 최소 흔적 개수를 나타내는 정수 `n`, B도둑이 경찰에 붙잡히는 최소 흔적 개수를 나타내는 정수 `m`이 매개변수로 주어집니다. 두 도둑 모두 경찰에 붙잡히지 않도록 모든 물건을 훔쳤을 때, **A도둑이 남긴 흔적의 누적 개수의 최솟값**을 return 하도록 solution 함수를 완성해 주세요. 만약 어떠한 방법으로도 두 도둑 모두 경찰에 붙잡히지 않게 할 수 없다면 -1을 return해 주세요.

#제한사항

- 1 ≤ `info`의 길이 ≤ 40
    - `info[i]`는 물건 `i`를 훔칠 때 생기는 흔적의 개수를 나타내며, [`A에 대한 흔적 개수`, `B에 대한 흔적 개수`]의 형태입니다.
    - 1 ≤ `흔적 개수` ≤ 3
- 1 ≤ `n` ≤ 120
- 1 ≤ `m` ≤ 120


#입출력 예

| info | n | m | result |
| --- | --- | --- | --- |
| [[1, 2], [2, 3], [2, 1]] | 4 | 4 | 2 |
| [[1, 2], [2, 3], [2, 1]] | 1 | 7 | 0 |
| [[3, 3], [3, 3]] | 7 | 1 | 6 |
| [[3, 3], [3, 3]] | 6 | 1 | -1 |


### 2. 정답

```python
def solution(info, n, m):
    import heapq

    N = len(info)
    visited = [[False] * m for _ in range(n)]
    heap = []
    heapq.heappush(heap, (0, 0, 0))  # (a_sum, idx, b_sum)

    while heap:
        a_sum, idx, b_sum = heapq.heappop(heap)
        if idx == N:
            return a_sum  # 최소 a_sum에 도달한 경로

        a_cost, b_cost = info[idx]

        # A가 훔치는 경우
        next_a = a_sum + a_cost
        if next_a < n and not visited[next_a][b_sum]:
            visited[next_a][b_sum] = True
            heapq.heappush(heap, (next_a, idx + 1, b_sum))

        # B가 훔치는 경우
        next_b = b_sum + b_cost
        if next_b < m and not visited[a_sum][next_b]:
            visited[a_sum][next_b] = True
            heapq.heappush(heap, (a_sum, idx + 1, next_b))

    return -1

```

### 3. 단계별로 보기

1. visited array 만들고
2. heap 만들고
3. (A누적흔적, 몇번째물건인지, B누적흔적) 넣고 돌리기 시작
4. (idx=N이면 끝)
5. A가 훔치면 (next_a, idx + 1, b_sum) 넣고 B가 훔치면 (a_sum, idx + 1, next_b) 넣고.
6. (0, 0, 0) 보고 암것도 안넣은거면 A도 B도 못훔친거니까 -1 반환





---

(아래 내용은 틀림... 어쩐지 이해안대더라 ㅂㄷㅂㄷ)

#정답

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

#단계별로보기

1. visited array 만들고
2. queue 만들고
3. (몇번째물건인지, A누적흔적, B누적흔적) 넣고 돌리기 시작
4. (idx=N이면 끝)
5. A가 훔치면 (idx+1, a_sum+a_cost, b_cost) 넣고 B가 훔치면 (idx+1, a_cost, b_sum+b_cost) 넣고.
6. 안넣은거면 A도 B도 못훔친거니까 -1 반환

##1 
```python
visited = [[False] * m for _ in range(n)]
```
```plain text
visited = [[False, False, False, False], [False, False, False, False], [False, False, False, False], [False, False, False, False]]

시작은
visited = [[True, False, False, False], [False, False, False, False], [False, False, False, False], [False, False, False, False]]
```

##2

```python
queue = deque()
```

##3

```python
idx, a_sum, b_sum = queue.popleft()
```
```plain text
idx=0, a_sum=0, b_sum=0
```

##4
```python
if idx == N:
    return a_sum  # 모든 물건을 훔쳤고, A의 흔적 누적 최소값 리턴
```

##5

```python
# A가 훔치는 경우
if a_sum + a_cost < n and not visited[a_sum + a_cost][b_sum]:
    visited[a_sum + a_cost][b_sum] = True
    queue.append((idx + 1, a_sum + a_cost, b_sum))

# B가 훔치는 경우
if b_sum + b_cost < m and not visited[a_sum][b_sum + b_cost]:
    visited[a_sum][b_sum + b_cost] = True
    queue.append((idx + 1, a_sum, b_sum + b_cost))
```
```plain text
a_sum + a_cost < n and not visited[a_sum + a_cost][b_sum]
에서 not visited가 아니라 visited이면 A가 안훔치냐?
```

##예시

(A 도둑의 누적 흔적 수(a_sum)가 최소가 되도록 보장하는 로직은 어디지?)

```plain text
info = [[1, 3], [2, 1], [3, 1]]
n = 10  # A가 잡히는 기준
m = 4   # B가 잡히는 기준

<초기 상태>
BFS Queue: [(0, 0, 0)] → 아직 아무것도 훔치지 않음
visited[0][0] = True

<1단계: idx=0, a_sum=0, b_sum=0>
가능한 선택:
A가 1번 훔침 → (1, 1, 0) → visited[1][0] = True
B가 1번 훔침 → (1, 0, 3) → visited[0][3] = True
→ Queue: [(1, 1, 0), (1, 0, 3)]

<2단계: idx=1, a_sum=1, b_sum=0>
A가 1,2번 훔침 → (2, 3, 0) → visited[3][0] = True
A가 1번 B가 2번 훔침 → (2, 1, 1) → visited[1][1] = True
→ Queue: [(1, 0, 3), (2, 3, 0), (2, 1, 1)]

<2-2단계: idx=1, a_sum=0, b_sum=3>
A가 2번 B가 1번 훔침 → (2, 2, 3) → visited[2][3] = True
B가 1,2번 훔침 → (2, 0, 4) → (b_sum=4 ≥ m=4) → 불가
→ Queue: [(2, 3, 0), (2, 1, 1), (2, 2, 3)]

<3단계: idx=2, a_sum=3, b_sum=0>
A가 1,2,3번 훔침 → (3, 6, 0) → visited[6][0] = True
A가 1,2번 B가 3번 훔침 → (3, 3, 1) → visited[3][1] = True
→ Queue: [(2, 1, 1), (2, 2, 3), (3, 6, 0), (3, 3, 1)]

<3-2단계: idx=2, a_sum=1, b_sum=1
A가 1,3번 B가 2번 훔침 → (3, 4, 1) → visited[4][1] = True
A가 1번 B가 2,3번 훔침 → (3, 1, 2) → visited[1][2] = True
→ Queue: [(2, 2, 3), (3, 6, 0), (3, 3, 1), (3, 4, 1), (3, 1, 2)] ← 여기서 idx==3 도달 → 정답은 A

<결론 - A 도둑의 누적 흔적 수(a_sum)가 최소가 되도록 보장하는 로직은 어디인가?>
Queue: [(2, 2, 3), (3, 6, 0), (3, 3, 1), (3, 4, 1), (3, 1, 2)]
하고 다음 단계가 마지막 idx=2 데이터 를 pop으로 queue에서 제거하는것.
- queue에서 첫번째 값이 idx=3인거중에 A 흔적이 최소인 값인가?
- #A가 훔침 이 #B가 훔침보다 빨리 수행되는데
- 어떻게 A 도둑의 누적 흔적 수(a_sum)가 최소가 되도록 보장하는 로직이 됨??
```

