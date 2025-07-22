---
date : 2025-07-21
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "RDE #1 Local PC에서 RDE 환경 구성"
---

# RDE #1 Local PC에서 RDE 환경 구성

#2025-07-22

---

### 1

#1 Docker Desktop 설치

링크 - https://www.docker.com/products/docker􏰂desktop/

#2 RdE Container download

Harbor registry로부터 이미지 다운로드 (*에 이미지 경로)

```bash
docker pull *
```

다운로드 확인하면?

![image](https://github.com/user-attachments/assets/f17604e3-47b4-4cd4-87e2-eb5037a60e7d)

잘들어가있다!

### 2

#1 Local RDE 설치하기

https://mattermost.*.com 접속해서 다운로드.

#2 실행

![image](https://github.com/user-attachments/assets/e2d206b7-5969-4f68-840f-67e1bf343b31)

아이콘 클릭해서 실행

```bash
=============================================
            RDE Launcher 시작 중...
=============================================

시작 시간: 2025-07-22 16:55:56
작업 디렉토리: /Users/yshmbid/rde
실행 파일: rde-launcher-macos-arm64

로그 파일: /Users/yshmbid/rde/rde-launcher.log

작업 디렉토리로 이동했습니다.
실행 파일 확인 완료: rde-launcher-macos-arm64

=============================================
            RDE Launcher 실행 중...
=============================================

프로세스를 시작합니다...

설정 파일 로드 중...
컨테이너 타입 설정이 없습니다. 기본값 'docker'을 사용합니다.

...
VS Code 서버가 성공적으로 시작되었습니다!
SSH config가 이미 최신 상태입니다.
SSH 키가 성공적으로 복사되었습니다.
✅ VS Code 컨테이너가 성공적으로 시작되었습니다.
   - 접속 URL: http://localhost:8443/vscode
✅ 명령 실행 완료.

=============================================
            실행이 성공적으로 완료되었습니다!
=============================================

완료 시간: 2025-07-22 16:56:27
실행 시간: 00:00:31
종료 코드: 0

로그 파일 위치: /Users/yshmbid/rde/rde-launcher.log
로그 파일을 열려면 'open "/Users/yshmbid/rde/rde-launcher.log"' 명령을 사용하세요.

완료! 계속하려면 Enter 키를 누르세요...
(base) yshmbid:~ yshmbid$ 
```

성공적으로 실행!

### 3. Local vscode에서 RDE 접속하기

vscode > extensions > Remote-SSH 설치

![image](https://github.com/user-attachments/assets/b074fa52-4982-4725-9e08-81b0222cb48c)

이때 원격접속은 /config로 설정.

![image](https://github.com/user-attachments/assets/b58fa6db-46e9-4b95-9b11-4f3e1f517452)

접속완료 화면!

