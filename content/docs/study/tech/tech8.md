---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit - 스택/큐"
bookComments: true
---

# 프로그래머스 알고리즘 고득점 kit - 스택/큐

## 목록

*2024-04-09* ⋯ [[스택/큐] 기능개발](https://yshghid.github.io/docs/study/tech/study2/#기능개발)

*2024-04-10* ⋯ [[스택/큐] 올바른 괄호](https://yshghid.github.io/docs/study/tech/study2/#올바른-괄호)

*2024-04-10* ⋯ [[스택/큐] 프로세스](https://yshghid.github.io/docs/study/tech/study2/#프로세스)

*2024-04-10* ⋯ [[스택/큐] 다리를 지나는 트럭](https://yshghid.github.io/docs/study/tech/study2/#다리를-지나는-트럭)

---

## 기능개발

### 입출력 예

```python
progresses = [93, 30, 55]
speeds = [1, 30, 5]
return = [2, 1]
```

### 개념
```python
progresses = [99,99,97] speeds = [1,1,1]이면
cnt=0 progresses = [100,100,98] -> cnt=1 -> cnt=2 -> answer = [2]
cnt=0 progresses = [99] -> cnt=0, answer = [2]
cnt=0 progresses = [100] -> cnt=1 -> answer = [1]
cnt=0 progresses = [] -> 종료
```

### 코드

```python
def solution(progresses, speeds):
  answer = []
  while progresses:
    for i in range(len(progresses)):
      progresses[i] += speeds[i]
    cnt = 0
    while progresses and progresses[0] >= 100:
      progresses.pop(0)
      speeds.pop(0)
      cnt+=1
    if cnt>0:
      answer.append(cnt)
  return answer
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42586

---

## 올바른 괄호

### 입출력 예

```python
s = ")()("
return = false
```

### 코드

```python
def solution(s):
    count = 0
    for char in s:
        if char == '(':
            count += 1
        else:  # char == ')'
            count -= 1
        if count < 0:
            return False
    return count == 0
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/12909

---

## 프로세스

### 입출력 예

```python
priorities = [2, 1, 3, 2]
location = 2
return = 1
```

### 개념

```python
queue = deque([(0,2),(1,1),(2,3),(3,2)])

order=0
queue=[(1,1),(2,3),(3,2)] -> (0,2)에서 2<3 -> order=0
queue=[(2,3),(3,2),(0,2)] -> (1,1)에서 1<3 -> order=0
queue=[(3,2),(0,2),(1,1)] -> (2,3)에서 3은 max -> order=1 -> location=3 -> return 1
```

### 코드 

```python
from collections import deque

def solution(priorities, location):
    queue = deque([(i, p) for i, p in enumerate(priorities)])
    order = 0  # 실행 순서

    while queue:
        current = queue.popleft()
        # 뒤에 더 높은 우선순위가 있다면 다시 뒤로 보내기
        if any(current[1] < item[1] for item in queue):
            queue.append(current)
        else:
            order += 1
            # 현재 프로세스가 내가 찾는 위치라면 순서 반환
            if current[0] == location:
                return order
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42587

---

## 다리를 지나는 트럭

### 입출력 예

```python
bridge_length = 2
weight = 10
truck_weights = [7,4,5,6]
```

### 코드 

```python
def solution(bridge_length, weight, truck_weights):
    trucks = deque([(i, 0) for i in truck_weights])
    bridge = []
    time = 0
    total_weight = 0

    while bridge:
        cur_truck = trucks.leftpop()
        if total_weight += cur_truck <= weight and len(bridge) < bridge_length:
            bridge.append(cur_truck)
        time += 1
        bridge = t[1]+=1 for t in bridge
        

    return answer
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42583
