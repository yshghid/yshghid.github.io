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

[완주하지 못한 선수](https://yshghid.github.io/docs/hobby/study/study2/#완주하지-못한-선수)

[전화번호 목록](https://yshghid.github.io/docs/hobby/study/study2/#전화번호-목록)

[의상](https://yshghid.github.io/docs/hobby/study/study2/#의상)

[베스트앨범](https://yshghid.github.io/docs/hobby/study/study2/#베스트앨범)

---

## 완주하지 못한 선수

##### 2025-04-09

---

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

---

## 전화번호 목록

##### 2025-04-09

---

## 의상

##### 2025-04-09

---

## 베스트앨범

##### 2025-04-09

---
