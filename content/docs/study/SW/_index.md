---
weight: 17
title: "SW"
bookComments: false
type: docs
---

# SW

#####


*2025-08-04* ⋯ Docker #5 kubernetes 환경에 나의 앱을 배포해보자

[1. 작업 정보 #1 작업 위치 $ pwd /Users/yshmbid/rde/config/workspace/exec-template #2 파일 구조  #3 이전 실습과의 차이  1. cicd.sh를 쓴다. 2. deploy 디렉토리를 쓴다.  3. docker-build.sh와 docker-push.sh에서 amd였던걸 arm으로 바꿔줬는데 이걸다시 amd로 바꿔준다. 1. cicd.sh 작성 ⋯](https://yshghid.github.io/docs/study/sw/sw18/)

---

*2025-08-04* ⋯ Docker #4 자신의 Frontend (HTML, JS, CSS) 개발 코드를 컨테이너로 만들고 이것을 실행시켜 보자

[조건 1. nginx:alpine 이미지를 사용 2. 노출 Port는80 3. nginx를실행하는방식은 -nginx -g daemon off; 4. nginx의 routing 설정은 default.conf에 설정한다. 1. 작업 위치 $ pwd /Users/yshmbid/rde/config/workspace/exec-template $ ls Dockerfile default.config/workspace/exec-template ⋯](https://yshghid.github.io/docs/study/sw/sw17/)

---

*2025-08-04* ⋯ Docker #3 

[1. 레지스트리에 접속하고 이미지를 pull/push하기 # Docker 로그인 $ docker login https://{실습링크}.com # ID: * # Password: * $ Login Succeeded # 이미지 Pull (이미지 내려받기): 예를 들어 container-linux:1.1 이미지를 다운로드 $ docker pull {실습링크}.com/{실습id}/container-linux ⋯](https://yshghid.github.io/docs/study/sw/sw16/)

---

*2025-08-01* ⋯ Docker #2 작년 작업 복기: netmhcpan image 불러와서 패키지 돌리기
 
[1 2024.11.24 MutClust 작업중에 netmhcpan을 돌려야되는 상황이 왓었는데 netmhcpan이 유료였나 그래서 패키지 다운은 안되고.. 서버 뒤지다가 아래 README.txt 파일 발견해서 결과물 저장까진 했던 기억이있다. 1. 도커 컨테이너 생성 docker hub에서 ‘* ’ 이미지 다운로드 (* : 링크 블라인드 처리) 링크 : https://hub.docker. ⋯](https://yshghid.github.io/docs/study/sw/sw15/)

---

*2025-08-01* ⋯ Docker #1 Python 실행 컨테이너 만들기


[1 RDE 런처 실행 RDE #1 Local PC에서 RDE 환경 구성에서 Harbor registry로부터 RdE Container download를 수행했고 아이콘을 클릭해서 RDE 런처를 실행한다. 1. 웹 서비스 실행 컨테이너 만들기 #1 /config/workspace/cloud/container/00.container-linux 경로로 이동 cd /config/workspace/ ⋯](https://yshghid.github.io/docs/study/sw/sw14/)

---

*2025-07-31* ⋯ SQL #6 AI 서비스 리뷰 시스템

[1. 문제 AI 서비스 리뷰 시스템: 키워드 기반 텍스트 필터링과 AI 기반 방식의 비교를 통해 유사도 기반 검색에 대한 개념 이해 - 테이블 개요	. Day 3 – ai_service_creator_ranking.sql	. 주제: AI 서비스 리뷰 (WITH (CTE) + 집계로 인기 기획자 추출)	. 목적: CTE(Common Table Expression)로 집계 ⋯](https://yshghid.github.io/docs/study/sw/sw13/)

---

*2025-07-31* ⋯ HTML #2 SKCT 공부용 메모장+계산기 만들기

[1. 문제 SKCT는 응시화면이 아래와같이 돼잇는데 연습하기 불편한거같애서 html로 만들어봣다 2. SKCT 공부용 메모장+계산기 #파일구조 /skct ├── index.html └── script.js #활용 요렇게 문제옆에 띄워놓고 쓰면됨 ㅎㅎㅎ 3. 수정사항 3-1. 메모장 1. 메모장 ↔ 그림판 전환 버튼 - 메모장일때는 '🎨 그림판', 그림판일때는 ⋯](https://yshghid.github.io/docs/study/sw/sw12/)

---

*2025-07-30* ⋯ SQL #5 소셜미디어 포스트 리뷰 시스템 

[1. 문제 JSONB 기반의 메타정보 필드 설계 + 검색 + AI 분석 연계 - 테이블 개요 . Day 2 – jsonb_metadata_sql_practice.sql	. 주제: 소셜미디어 포스트 리뷰	. 목적: 포스트에 대한 사용자 평가 + 해시태그/속성을 JSONB로 저장하여 AI 추천/필터 기반 만들기 - 실습 준비 . 특정 메타 속성 포함 검색(JSONB 검색 쿼리 실습) ⋯](https://yshghid.github.io/docs/study/sw/sw11/)

---

*2025-07-30* ⋯ SQL #4 AI 피드백 분석 시스템의 테이블 정규화

[1. 문제 AI 피드백 분석 시스템의 테이블 정규화 - 시나리오	. 여러분은 AI 피드백 분석 시스템을 위한 데이터 모델링을 맡았습니다. 현재는 여러 실험 데이터를 한 테이블에 모아두었지만, 벡터 임베딩 처리, 학습데이터 전처리, RAG 문서 기반 검색 등을 고려해 정규화 설계가 필요합니다. 비정규 테이블 예시: Day 2 – 정규화와 ⋯](https://yshghid.github.io/docs/study/sw/sw10/)

---

*2025-07-29* ⋯ SQL #3 스키마 분리와 AI 분석

[생각 정리 1. AI 분석이 들어갈 때 왜 별도 스키마로 나누는 것이 유리할까요? 2. 스키마 vs. 테이블 분리, 어떤 방식이 어떤 상황에 적합할까요? 3. 향후 pgvector 또는 AI 모델 결과를 넣기 위해 어떻게 테이블을 확장할 수 있을까요? 1. AI 분석이 들어갈 때 왜 별도 스키마로 나누는 것이 유리할까요? AI 분석이 포함될 때 ⋯](https://yshghid.github.io/docs/study/sw/sw9/)

---

*2025-07-29* ⋯ SQL #2 학사 관리 시스템 설계 - 스키마 분리 및 멀티 프로젝트 설계

[1. 문제 이전에 만든 ERD를 기반으로 PostgreSQL 로 스키마 분리 및 멀티 프로젝트 설계합니다. - 주제   . 서울캠퍼스/제주캠퍼스별 학사 관리 시스템 (Learning Management System)  동일한 학사관리 시스템 구조를 기반으로, 캠퍼스에 따라 데이터를 스키마 단위로  분리 설계하고 향후 AI 분석 결과의 멀티 벡터 저장 구조로 ⋯](https://yshghid.github.io/docs/study/sw/sw8/)

---


*2025-07-29* ⋯ SQL #1 학사 관리 시스템 설계 - 엔터티 도출 및 ERD 작성

[1. 문제 AI 기반 학사 관리 시스템 (Learning Management System) 설계를 위한 엔터티 도출 및 ERD 작성 실습입니다. - 요구사항 . 교육과정, 수강생, 과정운영자, 강사, 과정 설명 텍스트, Review 등으로 구성 . 과정 설명 텍스트는 향후 AI 임베딩 대상이므로 충분한 길이와 자유 텍스트로 정의 - 순서 . 학사관리시스템  ⋯](https://yshghid.github.io/docs/study/sw/sw7/)

---

*2025-07-24* ⋯ Hugo blog #3 Markdown HTML 렌더링 문제

[Hugo book Theme는 원래 위 코드를 작성하면 아래처럼 토글이 나온다. 어느날부터 갑자기 토글이든 문단나누기든 다 안먹어서, 근데 원인을 몰라서 그냥 shortcode 기능 없는대로 쓰다가, 너무 불편해서 좀 찾아봤고 hugo.toml에 다음 내용 넣어준 뒤로는 잘 작동했다. 근데 이후에 html 관련 포스팅을 작성했는데 넣어준 코드가 다 깨졌다. ⋯](https://yshghid.github.io/docs/study/sw/sw6/)

---

*2025-07-23* ⋯ JavaScript #1 쇼핑몰 주문 처리 과제

[#문제 당신은 온라인 쇼핑몰의 개발자로, 고객 주문을 처리하는 프로그램을 작성하고 있습니다. 주문 처리 과정에서는 여러 조건을 고려해야 합니다. 예를 들어, 상품의 재고 여부, 고객의 회원 등급, 주문 금액, 배송 옵션 등을 확인하여 적절한 메시지와 할인율을 적용해야 합니다. 아래의 세부 조건에 맞도록 JavaScript 함수를 구현하고 ⋯](https://yshghid.github.io/docs/study/sw/sw5/)

---

*2025-07-22* ⋯ [HTML #1 프로필 웹페이지 작성 과제](https://yshghid.github.io/docs/study/sw/sw2/)

#

---

*2025-07-22* ⋯ NPM 과 PIP 명령어 목록

[1. NPM (Node Package Manager) 패키지 설치 npm install <패키지명> - 패키지 설치 npm install -g <패키지명> - 전역 설치 npm install --save-dev <패키지명> - 개발 의존성으로 설치 npm install - package.json의 모든 의존성 설치 패키지 관리 npm uninstall <패키지명> - 패키지 제거 ⋯](https://yshghid.github.io/docs/study/sw/sw1/)

---

*2025-07-21* ⋯ RDE #1 Local PC에서 RDE 환경 구성

[1 #1 Docker Desktop 설치 링크 - https://www.docker.com/products/docker-desktop/ #2 RdE Container download Harbor registry로부터 이미지 다운로드 (* 에 이미지 경로) docker pull * 다운로드 확인하면? 잘들어가있다! 2 #1 Local RDE 설치하기 https://mattermost.*.com 접속해서 다운로드. #2 실행 아이콘 클릭해서 실행 ⋯](https://yshghid.github.io/docs/study/sw/sw3/)

---

*2025-04-09* ⋯ Github #2 Ubuntu 20.04 brownout 오류

[블로그 수정하는데 갑자기 처음보는 오류가 발생,, 찾아보니 ubuntu-20.04 GitHub Actions runner가 2025년 4월 15일에 지원 종료함에 따라 workflow에서 runs-on: ubuntu-20.04를 사용중이라면 runs-on: ubuntu-22.04로 수정하라는 내용이었다. jobs: deploy: runs-on: ubuntu-22.04 gh-pages.yml에 들어가서 runs-on: ⋯](https://yshghid.github.io/docs/study/tech/study1/)

---

*2024-12-31* ⋯ Github #1 There was an error committing your changes: File could not be edited 오류

[1. 갑자기 모든 파일의 수정이 안되고 page deployment도 오류가 났다. 2. 브라우저 캐시 문제인가 해서 방문기록이랑 캐시를 모두 삭제해보았다. 그래도 오류가 났다. 3. 구글링하니까 내 경우랑 맞아떨어지는 한국인 블로그글이 있어서 시키는대로 https://www.githubstatus.com/에 들어가봤다. 블로그 글이랑 같은 창이 떴는데 그냥 ⋯](https://yshghid.github.io/docs/study/tech/cs5/)


---

*2024-12-31* ⋯ Hugo blog #2 Favicon 변경, Giscus 댓글창 추가

[Favicon 변경  Hugo-book 테마의 github에서 README 파일을 읽어보면, logo와 favicon 이미지의 경로 정보를 찾을 수 있다. (logo 정보) (favicon 정보) 확인 결과 static 디렉토리에 각각 logo.png, favicon.png로 저장해두면 반영되는것 같다. 참고로 Hugo-book 테마의 오리지널 웹사이트는 아래와 같이 디자인되어있고 ⋯](https://yshghid.github.io/docs/study/tech/cs2/)

---

*2024-12-31* ⋯ Hugo blog #1 사이트 생성, 깃허브 배포

[Hugo 설치 $ brew install hugo $ hugo version hugo v0.131.0+extended darwin/arm64 BuildDate=2024-08-02T09:03:48Z VendorInfo=brew Hugo v0.112.0 이상인지 확인하면 된다. Hugo 사이트 생성 작업하고 싶은 위치에 Hugo 디렉토리를 만들어준다. $ mkdir Hugo $ cd Hugo Hugo로 들어가서 ⋯](https://yshghid.github.io/docs/study/tech/cs1/)


