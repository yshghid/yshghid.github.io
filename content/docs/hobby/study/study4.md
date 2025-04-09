---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit | 스택/큐"
bookComments: true
---

# [코테] 프로그래머스 알고리즘 고득점 kit | 스택/큐"

## 목록

*2024-04-09* ⋯ [기능개발](https://yshghid.github.io/docs/hobby/study/study2/#기능개발)

*2024-04-09* ⋯ [올바른 괄호](https://yshghid.github.io/docs/hobby/study/study2/#올바른-괄호)

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
```

### 코드

```python
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/12909

