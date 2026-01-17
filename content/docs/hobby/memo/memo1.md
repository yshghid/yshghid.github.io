---
date : 2025-06-05
tags: ['2025-06']
categories: ['메모']
bookHidden: true
title: "6월 5일 (특이점:외부에쫌많이 흔들림)"
---

# 6월 5일 (특이점:외부에쫌많이 흔들림)

#2025-06-05

---

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

```plain text
i) Counter(participant) -> {'leo':1, 'kiki':1, 'eden':1}
ii) Counter(participant) - Counter(completion) -> {'leo':1}
iii) 답은? 위를 X로 봣을때 list(X.keys())[0]
```

```python
from collections import Counter

def solution(participant, completion):
  answer = Counter(participant) - Counter(completion)
  return list(answer.keys())[0]
```

> 오늘한일
> 1. 빅분기, sqld, 정처기, 컴활 시험일정 확인
> 2. 항생제 작업
> 3. 코테-인적성-NCS 1회

> 오늘회고
>
> - 오늘은 잠을 2시간밖에못잤는데 무력하기까지해서 잠을 덜잔 핑계로 시간을 버려도되는날이라고 생각해버리려했는데 아무리생각해도 이건 퇴마의문제가아니라 내일이되면 현실이 더 악화되는것 아닌가??라는걸 깨달아버리고 다급하게 스카에 와서 계획을다잡았다
> - 노력만 하지말고 계획을 이행하기. 고통은 최소화하기. 
