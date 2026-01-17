---
date : 2025-06-08
tags: ['2025-06']
categories: ['메모']
bookHidden: true
title: "6월 8일 (+스트레스 받을 이유가 없는이유)"
---

# 6월 8일 (+스트레스 받을 이유가 없는이유)

#2025-06-08

---

> 10:10-10:40 코테
>
> 10:40-11:00 공기업 서칭


#코테

문제: 베스트앨범 https://school.programmers.co.kr/learn/courses/30/lessons/42579

##입출력 예

```plain text
genres = ["classic", "pop", "classic", "classic", "pop"]
plays = [500, 600, 150, 800, 2500]
return = [4, 1, 3, 0]
```

##정답

```python
def solution(genres, plays):
    genre_total = {}       # 장르별 총 재생 수
    genre_songs = {}       # 장르별 (고유 번호, 재생 수) 리스트

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

##개념

```plain text
i) genre_total = {"classic": 1450, "pop": 3100} -> genre_total.items() = [("classic", 1450), ("pop", 3100)]

ii) sorted(genre_total.items(), key=lambda x: x[1], reverse=True) -> [("pop", 3100), ("classic", 1450)]
-> x[1]: 딕셔너리의 value(재생 수)를 기준으로 정렬
-> reverse=True: 내림차순 정렬 
```

즉 이거랑 같음.

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
            result.append(song[0]) #고유번호

    return result
```

#공기업서칭

링크 -  https://www.ncs.go.kr/blind/bl04/RecrtNotifDetail.do?recrtNo=20250221095938

요약 - SAS랑 AICE Associate를 따면 도움됨

##25.2.20 국민건강보험공단_2025년도 제1차 전문인력 채용 - AI

![image](https://github.com/user-attachments/assets/c177b2fa-ab36-4611-91d9-8d97ba9b9fc2)

##25.2.20 국민건강보험공단_2025년도 제1차 전문인력 채용 - 보건의료통계연구

![image](https://github.com/user-attachments/assets/3354b7a0-8512-492b-963f-bacb30ca5778)



> 오늘은 경주갓다가 집에와서 그냥쉴려다가 불안지수가 올라가서 9시에 스카에 왓는데 10시인지금까지 멍때리다가 1시간 남았다... 갑자기 네이버 체험형인턴 낸거 왜연락없지??싶어서 들어갔는데 아직안나온게맞았음 말도안해주고 떨어뜨리는데에 넘마니데여서 또떨어진줄알았네
>
> 아무튼 6-8월까지는 포폴강화를 메인으로 하고 9월부터 졸논쓰면서 본격지원을 할거고 그전까지 포폴강화를 제대로해야 원하는직무를 손에넣을수있을텐데 하는 부담에(실제로 내역량밖이맞기도함) 자꾸 멘탈나가는중인데 사실 내가 손에넣지못하는그림도 충분히 그려지긴한다
> 즉 일어나면 끝장인 그런일은 아닌것이다. 그저그런회사에 갈거긴한데 6-8월이 비니까 비는김에 심심하니까 한다고 생각하자. 어차피그저그런데에 갈거니까 마음편하게먹자.

> 내일할일
>
> 인적성 강의 ppt 프린트
