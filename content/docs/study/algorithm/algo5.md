---
date : 2025-07-04
tags: ['2025-07']
categories: ['코테']
bookHidden: true
title: "BFS 공부"
---

# BFS 공부

---

### 1. 기본로직

```plain text
1. graph 만든다
2. 인접 리스트 정렬
3. bfs 만든다.
i) visited 만들고 / queue 만들어서 start만 넣는다. 
ii) queue가 빌때까지 다음을 수행
: queue의 첫번째값 v를 꺼냄. v가 미방문이면? visited에 v추가하고. v의 이웃을 봣을때 미방문이면? queue에 추가.
4. bfs 돌린다.
```

참고 문제: [BFS #1](https://yshghid.github.io/docs/study/algorithm/algo3/#1-%eb%b0%a9%eb%ac%b8-%ec%88%9c%ec%84%9c-%ec%b6%9c%eb%a0%a5%ed%95%98%ea%b8%b0)

#
