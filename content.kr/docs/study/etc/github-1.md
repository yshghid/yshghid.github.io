---
author: "kinda"
date: 2024-08-19
title: "[github] mac 터미널로 깃허브 디렉토리 내 모든파일 다운받기"
categories: ["Study"]
tags: ["etc"]
---

# [github] mac 터미널로 깃허브 디렉토리 내 모든파일 다운받기

"https://github.com/heineman/LearningAlgorithms/tree/main/algs" 디렉토리 내 모든 파일을 터미널 명령어로 다운받기. 

**방법1**
```
curl -s https://api.github.com/repos/heineman/LearningAlgorithms/contents/algs | jq -r '.[] | .download_url' | wget -i -
```

**방법2**
```
wget -qO- https://api.github.com/repos/heineman/LearningAlgorithms/contents/algs | jq -r '.[] | .download_url' | xargs -n 1 wget
```
