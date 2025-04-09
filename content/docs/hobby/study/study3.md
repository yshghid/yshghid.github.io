---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit | 정렬"
bookComments: true
---

# [코테] 프로그래머스 알고리즘 고득점 kit | 정렬

## 목록

*2024-04-09* ⋯ [완주하지 못한 선수](https://yshghid.github.io/docs/hobby/study/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [H-Index](https://yshghid.github.io/docs/hobby/study/study2/#h-index)

---

## 가장 큰 수

### 입출력 예

```python
numbers = [3, 30, 34, 5, 9] #numbers의 원소는 0 이상 1,000 이하
return = "9534330"
```

### 개념

```plain text
[3, 30, 34, 5, 9]
-> 그냥 sort시: [3, 30, 34, 5, 9]
-> 원하는 정렬: [34, 3, 30 5, 9]
-> x*3하면: [333, 303030, 343434, 555, 999] 정렬: [34, 3, 30, 5, 9]
```

### 코드

```python
def solution(numbers):
  numbers_str = list(map(str,numbers))
  numbers_str.sort(key = lambda x: x*3, reverse=True)
  if numbers_str[0] == '0': #numbers에 0 또는 양의 정수-> 다 0인 경우
    return '0'
  return ''.join(numbers_str)
  
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42746

---

## H-Index


### 입출력 예

```python
citations	= [3, 0, 6, 1, 5]
return = 3
```

### 코드

```python
def solution(citations):
  citations.sort(reverse=True)
  for idx, citation in enumerate(citations):
    if citation < idx+1:
      return idx
  return len(citations)

#[4,3,3,2] -> 4번이상 인용된 논문 1편, 3번이상 인용된 논문 2편, 3번이상 인용된 논문 3편, 2번이상 인용된 논문 4편 -> 3이다. 
#[0,0,0] -> 0번이상 인용된 논문 1편 -> 0이다
#[5,5,5] -> 5번이상 인용된 논문 1편,2편,3편 -> 3번이상 인용 논문 3편 -> 3이다. 
  
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42747
