---
date : 2025-06-17
tags: ['2025-06']
categories: ['python']
bookHidden: true
title: "#2 베스트앨범"
bookComments: true
---

# #2 베스트앨범

#2025-06-17

---

## 문제

#문제 설명

스트리밍 사이트에서 장르 별로 가장 많이 재생된 노래를 두 개씩 모아 베스트 앨범을 출시하려 합니다. 노래는 고유 번호로 구분하며, 노래를 수록하는 기준은 다음과 같습니다.

1. 속한 노래가 많이 재생된 장르를 먼저 수록합니다.
2. 장르 내에서 많이 재생된 노래를 먼저 수록합니다.
3. 장르 내에서 재생 횟수가 같은 노래 중에서는 고유 번호가 낮은 노래를 먼저 수록합니다.

노래의 장르를 나타내는 문자열 배열 genres와 노래별 재생 횟수를 나타내는 정수 배열 plays가 주어질 때, 베스트 앨범에 들어갈 노래의 고유 번호를 순서대로 return 하도록 solution 함수를 완성하세요.

#제한사항

- genres[i]는 고유번호가 i인 노래의 장르입니다.
- plays[i]는 고유번호가 i인 노래가 재생된 횟수입니다.
- genres와 plays의 길이는 같으며, 이는 1 이상 10,000 이하입니다.
- 장르 종류는 100개 미만입니다.
- 장르에 속한 곡이 하나라면, 하나의 곡만 선택합니다.
- 모든 장르는 재생된 횟수가 다릅니다.

#입출력 예

| genres | plays | return |
| --- | --- | --- |
| ["classic", "pop", "classic", "classic", "pop"] | [500, 600, 150, 800, 2500] | [4, 1, 3, 0] |

#입출력 예 설명

classic 장르는 1,450회 재생되었으며, classic 노래는 다음과 같습니다.

- 고유 번호 3: 800회 재생
- 고유 번호 0: 500회 재생
- 고유 번호 2: 150회 재생

pop 장르는 3,100회 재생되었으며, pop 노래는 다음과 같습니다.

- 고유 번호 4: 2,500회 재생
- 고유 번호 1: 600회 재생

따라서 pop 장르의 [4, 1]번 노래를 먼저, classic 장르의 [3, 0]번 노래를 그다음에 수록합니다.

- 장르 별로 가장 많이 재생된 노래를 최대 두 개까지 모아 베스트 앨범을 출시하므로 2번 노래는 수록되지 않습니다.

#정답

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
            genre_songs[genre].append((i, play))
        else:
            genre_songs[genre] = [(i, play)]

    # 3. 장르를 총 재생 수 기준으로 정렬
    sorted_genres = sorted(genre_total.items(), key=lambda x: x[1], reverse=True)

    result = []
    for genre, _ in sorted_genres:
        # 4. 각 장르 내에서 노래를 재생 수 기준 내림차순, 고유번호 오름차순 정렬
        songs = sorted(genre_songs[genre], key=lambda x: (-x[1], x[0]))
        # 5. 최대 두 개까지 수록
        for song in songs[:2]:
            result.append(song[0])

    return result
```



## 풀이

#단계별로보기

1. 장르별 재생수 딕셔너리
2. 장르별 곡 딕셔너리 (인덱스와 재생수)
3. 장르별 재생수 딕셔너리를 내림차순 정렬
4. 장르별 곡 딕셔너리를 곡 재생수 내림차순, 고유번호 오름차순 정렬
5. 2개까지 result에 수록

#1

```python
for i in range(len(genres)):
    genre = genres[i]
    play = plays[i]

    if genre in genre_total:
        genre_total[genre] += play
    else:
        genre_total[genre] = play
```
```plain text
장르별 재생수 딕셔너리 genre_total를 채움
결과:
genre_total = {'classic': _, 'pop': _}
```

#2

```python
    if genre in genre_songs:
        genre_songs[genre].append((i, play))
    else:
        genre_songs[genre] = [(i, play)]
```
```plain text
장르별 곡 딕셔너리 genre_songs를 채움
결과:
genre_songs = {'classic': [(0, _), (1, _), (2, _)], 'pop': [(3, _), (4, _)]}
```

#3

```python
sorted_genres = sorted(genre_total.items(), key=lambda x: x[1], reverse=True)
```
```plain text
genre_total를 내림차순 정렬
```

#4

```python
for genre, _ in sorted_genres:
    songs = sorted(genre_songs[genre], key=lambda x: (-x[1], x[0]))
```
```plain text
genre_songs를 곡 재생수 내림차순, 고유번호 오름차순 정렬
```

#5

```python
    for song in songs[:2]:
        result.append(song[0])
```
```plain text
2개까지 result에 수록
```
