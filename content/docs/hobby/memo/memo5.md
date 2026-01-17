---
date : 2025-06-14
tags: ['2025-06']
categories: ['메모']
bookHidden: true
title: "6월 14일"
---

# 6월 14일

#2025-06-14

---

> 오늘한일
> - 코테 공책 정리
> - SK AX 원서
> - 운동
> - 코테 3개

#코테

문제: 같은 숫자는 싫어 https://school.programmers.co.kr/learn/courses/30/lessons/12906

##입출력 예

```plain text
arr = [1,1,3,3,0,1,1]
answer = [1,3,0,1]
```

##정답

```python
def solution(arr):
    answer = [arr[0]]
    for i in arr:
        if i != answer[-1]:
            answer.append(i)
    return answer
```

문제: 기능개발 https://school.programmers.co.kr/learn/courses/30/lessons/42586

##입출력 예

```plain text
progresses = [95, 90, 99, 99, 80, 99]	
speeds = [1, 1, 1, 1, 1, 1]	
return = [1, 3, 2]
```

##정답

```plain text
n=1 -> [96, 91, 100, 100, 81, 100]
n=5 -> [100, 95, 100, 100, 85, 100] -> 배포 -> [100, 95, 100, 100, 85, 100].pop(0) -> [95, 100, 100, 85, 100]
n=10 -> [100, 100, 100, 90, 100] -> 배포 -> [90, 100]
n=20 -> [100, 100] -> 배포 -> []
```
```python
def solution(progresses, speeds):
    answer = []
    while progresses:
        for i in range(len(progresses)):
            progresses[i] += speeds[i]

        count = 0
        while progresses and progresses[0] >= 100:
            progresses.pop(0)
            speeds.pop(0)
            count += 1
        
        if count > 0:
            answer.append(count)
    
    return answer
```

문제: 올바른 괄호 https://school.programmers.co.kr/learn/courses/30/lessons/12909

##입출력 예

```plain text
s = "(())()"
answer = true
```

##정답

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

#기업정보

[AI/IT·Digital] 미래에셋증권 2025 상반기 채용연계형 인턴(신입사원) 모집 https://jasoseol.com/recruit/96221

우대사항: x

![image](https://github.com/user-attachments/assets/ca196b3a-6857-4bf0-ba2e-c99d869ed0b4)

[바이오의료연구센터-의료분야 국책과제 보조] 한국산업기술시험원 2025년 2분기 2차수 한국산업기술시험원 위촉 계약직(행정,연구직) 공개모집 https://jasoseol.com/recruit/96072

NCS 없고 면접만 있어서 내보면 좋을듯

![image](https://github.com/user-attachments/assets/acb2b726-0e24-490c-b5d2-0958c684343b)

![image](https://github.com/user-attachments/assets/f25b231d-ab98-4dcc-8b8a-cf12b88f26c0)

> 오늘은 미뤄왔던 '수분크림이랑 썬크림사기'를했는데 썬크림 1+1이라길래 아무생각없이 2통 집었는데 1통에 2개입이어서 4개산사람이돼버림 그리고 SK AX 신경쓰기싫어서 그냥안낼려다가 갑자기 내고싶어져서 1시간만에 대충써서 내버렸다 그래서 리비전작업을 못했다 내일하지뭐...
