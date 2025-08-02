---
date : 2025-06-06
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 6일"
---

# 6월 6일

#2025-06-06

---

> 9:30-10:00 코테
> 10:00-10:30: 인적성

#코딩테스트

문제: 폰켓몬 https://school.programmers.co.kr/learn/courses/30/lessons/1845

##입출력 예

```python
nums = [3,1,2,3]
result = 2
```

##정답

```plain text
nums = [3,1,2,3] -> list(set(nums)) = [3,1,2]
N/2 = 2인데 X길이가 2보다 길면 2, 2보다 짧으면 리스트 길이가 정답.
```
```python
def solution(nums):
    nums_list = list(set(nums))
    N = len(nums)
    answer = min(N/2, len(nums_list))
    return answer
```

문제: 전화번호 목록

##입출력 예

```python
phone_book = ["119", "97674223", "1195524421"]
return = false
```

##개념

```plain text
phone_book = ["119", "97674223", "1195524421"] -> phone_book.sort() = ["119", "1195524421", "97674223"]
"1195524421".startwith("119")이면 false
```

##정답

```python
def solution(phone_book):
    phone_book.sort() 
    for i in range(len(phone_book)-1):
        if phone_book[i+1].startswith(phone_book[i]): 
            return False
    return True
```

문제: 의상 https://school.programmers.co.kr/learn/courses/30/lessons/42578

##입출력 예

```python
clothes = [["yellow_hat", "headgear"], ["blue_sunglasses", "eyewear"], ["green_turban", "headgear"]]
return = 5
```

##정답

```plain text
clothes_dict = {"headgear":2, "eyewear":1}
return = 3*2-1 = 5
```
```python
def solution(clothes):
    clothes_dict = {}
    for cloth in clothes:
        if cloth[1] in clothes_dict.keys():
            clothes_dict[cloth[1]] += 1
        else:
            clothes_dict[cloth[1]] = 1

    answer = 1
    for key, value in clothes_dict.items():
        answer = answer*(value+1)
    
    return answer - 1
```



![image](https://github.com/user-attachments/assets/53abb9cb-762b-495c-ba38-7d1ff3e6f419)
