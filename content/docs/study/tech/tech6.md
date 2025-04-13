---
date : 2025-03-30
tags: ['2025-03']
categories: ['github']
bookHidden: true
title: "GitHub Pages 강제 재빌드/재배포하기"
bookComments: true
---

# GitHub Pages 강제 재빌드/재배포하기

![image](https://github.com/user-attachments/assets/7f018149-56e9-42ea-a0f1-2accca0f4995)

1. Github에서 코드 수정하면 pages build and deployment가 됏었는데 assets/_variables.scss 수정은 코드가 문제인진 모르겠지만 변경사항이 없다고 판단한건지 자동 빌드/배포가 안됏다.
2. 이유를 찾으면 좋겠지만!! 귀찮아서 그냥 강제 재빌드 방법을 찾음.

```bash
git commit --allow-empty -m "Trigger rebuild"
git push origin main
```
