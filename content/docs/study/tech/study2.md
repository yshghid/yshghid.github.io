---
date : 2025-04-09
tags: ['2025-04']
categories: ['python']
bookHidden: true
title: "프로그래머스 알고리즘 고득점 kit - 해시, 정렬"
bookComments: true
---

# 프로그래머스 알고리즘 고득점 kit - 해시, 정렬

## 목록

*2024-04-09* ⋯ [[해시] 완주하지 못한 선수](https://yshghid.github.io/docs/study/tech/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [[해시] 전화번호 목록](https://yshghid.github.io/docs/study/tech/study2/#전화번호-목록)

*2024-04-09* ⋯ [[해시] 의상](https://yshghid.github.io/docs/study/tech/study2/#의상)

*2024-04-09* ⋯ [[정렬] 완주하지 못한 선수](https://yshghid.github.io/docs/study/tech/study2/#완주하지-못한-선수)

*2024-04-09* ⋯ [[정렬] H-Index](https://yshghid.github.io/docs/study/tech/study2/#h-index)

*2024-04-10* ⋯ [[해시] 베스트앨범](https://yshghid.github.io/docs/study/tech/study2/#베스트앨범)

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

## 베스트앨범

### 입출력 예

```python
genres = ["classic", "pop", "classic", "classic", "pop"]
plays = [500, 600, 150, 800, 2500]
return = [4, 1, 3, 0]
```

### 개념

```python
genres = ["classic", "pop", "classic", "classic", "pop"]
plays = [500, 600, 150, 800, 2500]
n=1 genre_songs = 'classic':[(500,0)]
n=2 genre_songs = 'classic': [(500, 0)], 'pop': [(600, 1)]
```

### 코드 

```python
def solution(genres, plays):
    genre_total = {}       # 장르별 총 재생 수
    genre_songs = {}       # 장르별 (재생 수, 고유 번호) 리스트

    for i in range(len(genres)):
        genre = genres[i]
        play = plays[i]

        # 1. 총 재생 수 누적
        if genre in genre_total:
            genre_total[genre] += play
        else:
            genre_total[genre] = play

        # 2. 장르별 노래 정보 저장
        if genre in genre_songs:
            genre_songs[genre].append((play, i))
        else:
            genre_songs[genre] = [(play, i)]

    # 3. 장르를 총 재생 수 기준으로 정렬
    sorted_genres = sorted(genre_total.items(), key=lambda x: x[1], reverse=True)

    result = []
    for genre, _ in sorted_genres:
        # 4. 각 장르 내에서 노래를 재생 수 기준 내림차순, 고유번호 오름차순 정렬
        songs = sorted(genre_songs[genre], key=lambda x: (-x[0], x[1]))
        # 5. 최대 두 개까지 수록
        for song in songs[:2]:
            result.append(song[1])

    return result
```

> 문제 링크 https://school.programmers.co.kr/learn/courses/30/lessons/42579

