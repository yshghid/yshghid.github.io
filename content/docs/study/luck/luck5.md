---
date : 2025-06-09
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 9일"
---

# 6월 9일

#2025-06-09

---

> 5:45-6:15 코테
>
> 6:15-6:45 SAS, AICA 시험 확인


#코딩테스트

문제: 가장 큰 수 https://school.programmers.co.kr/learn/courses/30/lessons/42746

##입출력 예

```plain text
numbers = [3, 30, 34, 5, 9]
return = '9534330'
```

##정답

```plain text
numbers.sort() -> [3, 5, 9, 30, 34]
numbers = [str(i] for i in numbers] -> numbers.sort() = [3, 30, 34, 5, 9] -> 원하는 모양 = [30, 3, 34, 5, 9]인데 길이가 3자리 이하임을 이용하기.

numbers = [i*3 for i in numbers -> [333, 555, 999, 303030, 343434]
numbers.sort() = [303030, 333, 343434, 555, 999]
answer = 9534330
```

```python
def solution(numbers):
    numbers = list(map(str, numbers))
    numbers.sort(key=lambda x: x*3, reverse=True)
    return str(int(''.join(numbers)))
```

문제: H-index https://school.programmers.co.kr/learn/courses/30/lessons/42747

##입출력 예

```plain text
citations = [3, 0, 6, 1, 5]	
return = 3
```

##정답

```python
def solution(citations):
    citations.sort(reverse=True) 
    for idx, c in enumerate(citations):
        if idx + 1 > c:
            return idx
    return len(citations)
```

> 오늘한일
> - 코테 2개
> - 항생제 작업
