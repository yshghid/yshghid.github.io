---
date : 2025-07-19
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "BFS/DFS #1 타겟 넘버"
bookComments: true
---

# BFS/DFS #1 타겟 넘버

#2025-07-19

---


문제: n개의 음이 아닌 정수들이 있습니다. 이 정수들을 순서를 바꾸지 않고 적절히 더하거나 빼서 타겟 넘버를 만들려고 합니다. 사용할 수 있는 숫자가 담긴 배열 `numbers`, 타겟 넘버 `target`이 매개변수로 주어질 때 숫자를 적절히 더하고 빼서 타겟 넘버를 만드는 방법의 수를 return 하도록 solution 함수를 작성해주세요.

제한사항: 주어지는 숫자의 개수는 2개 이상 20개 이하입니다. 각 숫자는 1 이상 50 이하인 자연수입니다. 타겟 넘버는 1 이상 1000 이하인 자연수입니다.

입력: `numbers=[1,1,1,1,1]`, `target=3`

출력: `5`

풀이: 1) answer 만들고 2) dfs 만들고 3) 돌린다.

1:
```python
answer=0
```

2: dfs(index, total)일때 1) 현재 index에서 dfs로 (index+1, total+numbers[index])를 넘겨주거나 dfs로 (index+1, total-numbers[index]를 넘겨주기. 2) index=5(넘침)이면 dfs 하지말고 total==target일때 answer+=1 하기. 3) nonlocal answer 하기.

```python
nonlocal answer
if index == len(numbers):
    if total == target:
        answer += 1
    return
dfs(index + 1, total + numbers[index])  # 현재 숫자를 더함
dfs(index + 1, total - numbers[index])  # 현재 숫자를 뺌
```

3:
```python
dfs(0, 0)
return answer
```

정답:

```python
def solution(numbers, target):
    answer = 0

    def dfs(index, total):
        nonlocal answer
        if index == len(numbers):
            if total == target:
                answer += 1
            return
        dfs(index + 1, total + numbers[index])  # 현재 숫자를 더함
        dfs(index + 1, total - numbers[index])  # 현재 숫자를 뺌

    dfs(0, 0)
    return answer

# 테스트
print(solution([1, 1, 1, 1, 1], 3))  # 출력: 5
```

#

링크: https://school.programmers.co.kr/learn/courses/30/lessons/43165
