---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit | 해시"
bookComments: true
---

# [코테] 프로그래머스 알고리즘 고득점 kit | 해시

## 목록

*2024-04-09* ⋯ [완주하지 못한 선수](https://yshghid.github.io/docs/hobby/study/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [전화번호 목록](https://yshghid.github.io/docs/hobby/study/study2/#전화번호-목록)

*2024-04-09* ⋯ [의상](https://yshghid.github.io/docs/hobby/study/study2/#의상)

*2024-04-09* ⋯ [베스트앨범](https://yshghid.github.io/docs/hobby/study/study2/#베스트앨범)

---

## 완주하지 못한 선수

### 입출력 예

```python
participant = ["leo", "kiki", "eden"]	
completion = ["eden", "kiki"]	
return = "leo"
```

### 개념

```python
Counter(["leo", "kiki", "eden"]) -> {'leo':1, 'kiki':1, 'eden':1}
Counter(["leo", "kiki", "eden"]) - Counter(["kiki", "eden"]) -> {'leo':1} (key별로 value를 빼서 0이나 음수되면 제거)
```

### 코드

```python
from collections import Counter

def solution(participant, completion):
  answer = Counter(participant) - Counter(completion)
  return list(answer.keys())[0]
```

### 코드2

```python
def solution(participant, completion):
  participant.sort()
  completion.sort()
  for p,c in zip(participant, completion):
    if p != c:
      return p
  return participant[-1]
```
> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42576

---

## 전화번호 목록

### 입출력 예

```python
phone_book = ["119", "97674223", "1195524421"]
return = False
```

### 코드 - 정렬+startwith

```python
def solution(phone_book):
  phone_book.sort()
  for i in range(len(phone_book)-1):
    if phone_book[i+1].startwith(phone_book[i]:
      return False
  return True
```

### 코드2 - 해시

```python
def solution(phone_book):
  phone_dict = {}

  for number in phone_book:
    phone_dict[number] = True

  for number in phone_book: #3번
    for i in range(1,len(number)): # "1195524421"면 10번
      prefix = number[:i]
      if prefix in phone_dict: # number[:3]이 "119"인데 있으니까 False # prefix가 phone_dict의 key에 있는지만 봄
        return False
  return True
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42577

---

## 의상

### 입출력 예

```python
clothes = [["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ["green_turban", "headgear"]]
return = 5
```

### 코드 

```python
def solution(clothes):
  clothes_dict = {}
  for item, kind in clothes:
    clothes_dict[kind] = clothes_dict.get(kind,0)+1 # value 또는 0 받음
  answer = 1
  for kind in clothes_dict: # key만 받음
    answer *= (clothes_dict[kind]+1) #모자2 안경1이면 3*2-1=5 출력
  return answer-1
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42578

---

## 베스트앨범

### 입출력 예

```python

```

### 코드 

```python

```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42579
