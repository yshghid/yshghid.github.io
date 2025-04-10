---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit"
bookComments: true
---

# [코테] 프로그래머스 알고리즘 고득점 kit

## 목록

*2024-04-09* ⋯ [[해시] 완주하지 못한 선수](https://yshghid.github.io/docs/hobby/study/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [[해시] 전화번호 목록](https://yshghid.github.io/docs/hobby/study/study2/#전화번호-목록)

*2024-04-09* ⋯ [[해시] 의상](https://yshghid.github.io/docs/hobby/study/study2/#의상)

*2024-04-09* ⋯ [[정렬] 완주하지 못한 선수](https://yshghid.github.io/docs/hobby/study/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [[정렬] H-Index](https://yshghid.github.io/docs/hobby/study/study2/#h-index)

*2024-04-09* ⋯ [[스택/큐] 기능개발](https://yshghid.github.io/docs/hobby/study/study2/#기능개발)

*2024-04-10* ⋯ [[해시] 베스트앨범](https://yshghid.github.io/docs/hobby/study/study2/#베스트앨범)

*2024-04-10* ⋯ [[스택/큐] 올바른 괄호](https://yshghid.github.io/docs/hobby/study/study2/#올바른-괄호)

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

---

## 베스트앨범

### 입출력 예

```python

```

### 코드 

```python

```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42579
