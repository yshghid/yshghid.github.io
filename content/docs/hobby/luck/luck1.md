---
date : 2025-06-05
tags: ['2025-06']
categories: ['luck']
bookHidden: true
title: "6월 1주 (0605-0611)"
---

# 6월 1주 (0605-0611)

#2025-06-05

---

> 오늘한일
> 1. 빅분기, sqld, 정처기, 컴활 시험일정 확인
> 2. 항생제 작업
> 3. 코테-인적성-NCS 1회

#코딩테스트

문제: 완주하지 못한 선수 https://school.programmers.co.kr/learn/courses/30/lessons/42576?language=python3

##입출력 예

```python
participant = ["leo", "kiki", "eden"]	
completion = ["eden", "kiki"]	
return = "leo"
```

##개념

```python
Counter(["leo", "kiki", "eden"]) -> {'leo':1, 'kiki':1, 'eden':1}
Counter(["leo", "kiki", "eden"]) - Counter(["kiki", "eden"]) -> {'leo':1} (key별로 value를 빼서 0이나 음수되면 제거)
```

##정답

i) Counter(participant) -> {'leo':1, 'kiki':1, 'eden':1}
ii) Counter(participant) - Counter(completion) -> {'leo':1}
iii) 답은? 위를 X로 봣을때 list(X.keys())[0]

```python
from collections import Counter

def solution(participant, completion):
  answer = Counter(participant) - Counter(completion)
  return list(answer.keys())[0]
```



> 오늘의 다짐
> - 외부에 흔들리지말기
> - 계획을 지키고 나서 우울해하기
