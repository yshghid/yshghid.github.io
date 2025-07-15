---
weight: 20
title: "깃허브"
bookComments: false
bookHidden: true
type: docs
---

# 깃허브

## 2025

*04-09* ⋯ 깃허브 오류 Ubuntu 20.04 brownout

[블로그 수정하는데 갑자기 처음보는 오류가 발생,, 찾아보니 ubuntu-20.04 GitHub Actions runner가 2025년 4월 15일에 지원 종료함에 따라 workflow에서 runs-on: ubuntu-20.04를 사용중이라면 runs-on: ubuntu-22.04로 수정하라는 내용이었다. jobs: deploy: runs-on: ubuntu-22.04 gh-pages.yml에 들어가서 runs-on: ubuntu-20.04를 runs-on: ubuntu-22.04로 바꿔주니까 ⋯](https://yshghid.github.io/docs/study/tech/study1/)


## 2024

*12-31* ⋯ 깃허브 오류 There was an error committing your changes: File could not be edited

[1. 갑자기 모든 파일의 수정이 안되고 page deployment도 오류가 났다. 2. 브라우저 캐시 문제인가 해서 방문기록이랑 캐시를 모두 삭제해보았다. 그래도 오류가 났다. 3. 구글링하니까 내 경우랑 맞아떨어지는 한국인 블로그글이 있어서 시키는대로 https://www.githubstatus.com/에 들어가봤다. 블로그 글이랑 같은 창이 떴는데 그냥 기다려야된다길래 그냥 기다림. 5. 2시간 뒤에 들어가니까 이 창으로 바뀌었다. ⋯](https://yshghid.github.io/docs/study/tech/cs5/)

---

*12-31* ⋯ Favicon 변경, Giscus 댓글창 추가

[Favicon 변경  Hugo-book 테마의 github에서 README 파일을 읽어보면, logo와 favicon 이미지의 경로 정보를 찾을 수 있다. (logo 정보) (favicon 정보) 확인 결과 static 디렉토리에 각각 logo.png, favicon.png로 저장해두면 반영되는것 같다. 참고로 Hugo-book 테마의 오리지널 웹사이트는 아래와 같이 디자인되어있고 최상단 탭에 들어가는 이미지가 logo.png, 블로그 이름 옆에 들어가는 ⋯](https://yshghid.github.io/docs/study/tech/cs2/)

---

*12-31* ⋯ 사이트 생성, 깃허브 배포

[Hugo 설치 $ brew install hugo $ hugo version hugo v0.131.0+extended darwin/arm64 BuildDate=2024-08-02T09:03:48Z VendorInfo=brew Hugo v0.112.0 이상인지 확인하면 된다. Hugo 사이트 생성 작업하고 싶은 위치에 Hugo 디렉토리를 만들어준다. $ mkdir Hugo $ cd Hugo Hugo로 들어가서 hugo 사이트 틀을 생성해준다. 나는 blog라는 이름으로 생성하였다.](https://yshghid.github.io/docs/study/tech/cs1/)

#
