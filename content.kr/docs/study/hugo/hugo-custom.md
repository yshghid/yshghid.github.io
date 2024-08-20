+++
title = "github blog 커스텀하기: favicon, giscus 댓글창 추가"
menu = "main"
categories = ["Study"]
tags = ["hugo"]
+++

# github blog 커스텀하기: favicon, giscus 댓글창 추가

## 1. favicon 설정

Hugo-book 테마의 github에서 README 파일을 읽어보면, [logo](https://github.com/alex-shpak/hugo-book#site-configuration)와 [favicon 이미지](https://github.com/alex-shpak/hugo-book#extra-customisation)의 경로 정보를 찾을 수 있다.
확인 결과 `static` 디렉토리에 각각 `logo.png`, `favicon.png`로 저장해두면 반영되는것 같다.

참고로 Hugo-book 테마의 오리지널 웹사이트는 아래와 같이 디자인되어있고

![image](https://github.com/user-attachments/assets/1028de04-3335-4db4-bbf7-bb101c54e8a2)

최상단 탭에 들어가는 이미지가 logo.png, 블로그 이름 옆에 들어가는 이미지(위 이미지에는 없음)가 favicon.png이다.

먼저 static 디렉토리에 넣고 싶은 로고와 파비콘을 logo.png, favicon.png 로 저장해준다. 다음으로, hugo.toml 파일을 열어 아래 내용을 추가해준다.

```toml
  # (Optional, default none) Set the path to a logo for the book. If the logo is
  # /static/logo.png then the path would be 'logo.png'
  BookLogo = 'logo.png'
```

추가된 내용을 로컬 저장소에도 pull 해준다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog
$ git pull

$ hugo server
```

hugo server로 확인해보면, 설정한 로고와 파비콘이 잘 들어간것을 확인할 수 있다.

## 2. Giscus 댓글창 추가

Giscus 댓글 시스템을 Hugo 기반 블로그에 연동하기 위해서는 Giscus에 블로그 리포지토리를 연결한 후, js script를 작성하여 블로그 리포지토리의 layouts 디렉토리에 저장하면 된다고 한다.

이때 연결할 리포지토리는 다음 3가지 조건을 만족해야 한다.

1. Public이어야 함.
2. giscus 앱이 설치되어 있어야 함.
3. Discussions 기능이 해당 저장소에서 활성화되어 있어야 함.

**공개 저장소 확인**

연결하려는 블로그 리포지토리(<username>.github.io)의 Settings > General의 맨 하단을 보면 Danger Zone에서 public인지 private인지 확인이 가능하다. private라면 public으로 설정해준다. 

**giscus 설치**

https://github.com/apps/giscus 에 접속하여 install, configure를 진행하면 쉽게 설치된다. Repository access는 All repositories로 설정하고 save를 눌러준다. 

**Discussion 기능 활성화**

블로그 리포지토리의 Settings > General을 스크롤해보면 Discussions 체크박스가 생긴 것을 확인할 수 있다. 이를 체크해준후 위로 스크롤해보면 상단에 Discussions 탭이 생긴것을 확인할수있다.

**블로그를 Giscus로 연결**

이제 블로그 리포지토리가 Giscus에 연결할 3가지 조건을 만족하였고 블로그를 Giscus로 연결해주면 된다. 연결해주려면 js 스크립트를 작성하여 layouts/partials/docs/comments.html에 추가해주어야한다. comments.html 저장 경로는 테마마다 다를수있으며 [hugo-book theme에서 해당파일이 존재하는 경로](https://github.com/alex-shpak/hugo-book/blob/master/layouts/partials/docs/comments.html)를 확인후 사용해줬다.

js 스크립트는 https://giscus.app/ko에서 파라미터를 선택하면 적절하게 생성해준다! 해당 내용을 복사해서 블로그 리포지토리에 작성해주었다. 

```bash
$ pwd
/Users/yshmbid/Hugo/blog
$ mkdir -p layouts/partials
$ touch comments.html #들어가서 js스크립트 작성
```

그리고 hugo.toml을 확인해준다. BookComments를 false로 설정할경우 댓글창이 생성되지 않는다. 

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ touch hugo.toml #들어가서 아래 내용 추가해주기
```
```toml
  # (Optional, default true) Enables comments template on pages
  # By default partials/docs/comments.html includes Disqus template
  # See https://gohugo.io/content-management/comments/#configure-disqus
  # Can be overwritten by same param in page frontmatter
  BookComments = true
```

설정이 완료되었다면 hugo server로 확인해준다.

```
$ pwd
/Users/yshmbid/Hugo/blog

$ hugo server
```

![image](https://github.com/user-attachments/assets/f0ca503c-c910-4c81-ba04-a79282bbc6f7)

예쁘게 들어가있는걸 확인할수있다!

## 마무리

아래와같이 대문사진에도 댓글창이 뜨면 조금 거슬리므로... 

![image](https://github.com/user-attachments/assets/d26e9ca1-5d5a-42c6-9d46-e2e97120afbb)

포스트마다 들어가서 수정해주면 더 이뻐진다. [README의 Posting 파라미터](https://github.com/alex-shpak/hugo-book/tree/master?tab=readme-ov-file#page-configuration)를 확인후 bookComments = false를 넣어줬다.

![image](https://github.com/user-attachments/assets/1ee10c7f-b4f0-49b7-9d66-4eea41a20ecd)

깔끔해졌다. ㅎㅎ

### 참고한 블로그 및 문서 

1. HUGO/comments 공식 문서 - https://gohugo.io/content-management/comments/#configure-disqus
2. GISCUS 공식 문서 - https://giscus.app/ko
3. https://kzeoh.github.io/posts/make-blog3/
4. https://github.com/Integerous/Integerous.github.io?tab=readme-ov-file
