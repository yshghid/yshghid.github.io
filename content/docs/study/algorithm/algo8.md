---
date : 2025-07-21
tags: ['2025-07']
categories: ['python']
bookHidden: true
title: "BFS/DFS #3 게임 맵 최단거리"
bookComments: true
---

# BFS/DFS #3 게임 맵 최단거리

#2025-07-21

---


문제: ROR 게임은 두 팀으로 나누어서 진행하며, 상대 팀 진영을 먼저 파괴하면 이기는 게임입니다. 따라서, 각 팀은 상대 팀 진영에 최대한 빨리 도착하는 것이 유리합니다. 지금부터 당신은 한 팀의 팀원이 되어 게임을 진행하려고 합니다. 다음은 5 x 5 크기의 맵에, 당신의 캐릭터가 (행: 1, 열: 1) 위치에 있고, 상대 팀 진영은 (행: 5, 열: 5) 위치에 있는 경우의 예시입니다.

![image](https://github.com/user-attachments/assets/0c2834d7-07d7-4c5a-b6c6-9058de58a3da)

위 그림에서 검은색 부분은 벽으로 막혀있어 갈 수 없는 길이며, 흰색 부분은 갈 수 있는 길입니다. 캐릭터가 움직일 때는 동, 서, 남, 북 방향으로 한 칸씩 이동하며, 게임 맵을 벗어난 길은 갈 수 없습니다. 아래 예시는 캐릭터가 상대 팀 진영으로 가는 두 가지 방법을 나타내고 있습니다.

첫 번째 방법은 11개의 칸을 지나서 상대 팀 진영에 도착했습니다.

![image](https://github.com/user-attachments/assets/4280f689-ea5d-49b4-a9f9-e19a7add56f1)

두 번째 방법은 15개의 칸을 지나서 상대팀 진영에 도착했습니다.

![image](https://github.com/user-attachments/assets/78219ce7-72f6-4933-bc80-b128a08d74a9)


위 예시에서는 첫 번째 방법보다 더 빠르게 상대팀 진영에 도착하는 방법은 없으므로, 이 방법이 상대 팀 진영으로 가는 가장 빠른 방법입니다.

만약, 상대 팀이 자신의 팀 진영 주위에 벽을 세워두었다면 상대 팀 진영에 도착하지 못할 수도 있습니다. 예를 들어, 다음과 같은 경우에 당신의 캐릭터는 상대 팀 진영에 도착할 수 없습니다.

![image](https://github.com/user-attachments/assets/c10bcaf2-f734-4a14-9b85-85fa7657dc29)

게임 맵의 상태 maps가 매개변수로 주어질 때, 캐릭터가 상대 팀 진영에 도착하기 위해서 지나가야 하는 칸의 개수의 최솟값을 return 하도록 solution 함수를 완성해주세요. 단, 상대 팀 진영에 도착할 수 없을 때는 -1을 return 해주세요.

제한사항: maps는 n x m 크기의 게임 맵의 상태가 들어있는 2차원 배열로, n과 m은 각각 1 이상 100 이하의 자연수입니다. n과 m은 서로 같을 수도, 다를 수도 있지만, n과 m이 모두 1인 경우는 입력으로 주어지지 않습니다. maps는 0과 1로만 이루어져 있으며, 0은 벽이 있는 자리, 1은 벽이 없는 자리를 나타냅니다. 처음에 캐릭터는 게임 맵의 좌측 상단인 (1, 1) 위치에 있으며, 상대방 진영은 게임 맵의 우측 하단인 (n, m) 위치에 있습니다.

입력: `maps=[[1,0,1,1,1],[1,0,1,0,1],[1,0,1,1,1],[1,1,1,0,1],[0,0,0,0,1]]`

출력: `11`

풀이: 1) queue 만든다 2) queue에서 값을 가져오고 앞뒤좌우에 대해서 탐색. 처음 방문하는 위치면 해당 위치를 큐에 추가. 3) 거리 받기. 

1: queue 만든다.

```python
queue = deque()
queue.append((0, 0))
```

n, m, dx, dy 만든다.

```python
n = len(maps)
m = len(maps[0])

dx = [-1, 1, 0, 0]
dy = [0, 0, -1, 1]
```

2

```python
while queue:
    x, y = queue.popleft()
    
    for i in range(4):
        nx = x + dx[i]
        ny = y + dy[i]
        
        if maps[nx][ny] == 1:
            maps[nx][ny] = maps[x][y] + 1
            queue.append((nx, ny))
```

예외처리: 맵을 벗어나는 경우, 벽이거나 이미 방문한 곳이면 무시

```python
if nx < 0 or ny < 0 or nx >= n or ny >= m:
    continue

if maps[nx][ny] == 0:
    continue
```

3

```python
return maps[n-1][m-1] if maps[n-1][m-1] != 1 else -1
```

정답:

```python
from collections import deque

def solution(maps):
    n = len(maps)
    m = len(maps[0])
    
    # 상, 하, 좌, 우 방향 정의
    dx = [-1, 1, 0, 0]
    dy = [0, 0, -1, 1]

    # BFS를 위한 큐 초기화
    queue = deque()
    queue.append((0, 0))  # 시작 위치 (0,0)

    # BFS 수행
    while queue:
        x, y = queue.popleft()
        
        for i in range(4):  # 네 방향으로 탐색
            nx = x + dx[i]
            ny = y + dy[i]

            # 맵을 벗어나는 경우 무시
            if nx < 0 or ny < 0 or nx >= n or ny >= m:
                continue

            # 벽이거나 이미 방문한 곳이면 무시
            if maps[nx][ny] == 0:
                continue

            # 처음 방문하는 길이면 거리 갱신 후 큐에 추가
            if maps[nx][ny] == 1:
                maps[nx][ny] = maps[x][y] + 1
                queue.append((nx, ny))

    # 도착 지점의 값이 1이면 도달하지 못한 것, 아니면 거리 리턴
    return maps[n-1][m-1] if maps[n-1][m-1] != 1 else -1

```


#

링크: https://school.programmers.co.kr/learn/courses/30/lessons/1844
