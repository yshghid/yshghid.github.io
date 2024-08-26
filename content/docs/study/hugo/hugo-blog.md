---
author: "kinda"
date: 2024-07-28
title: "Hugo로 github blog 개설하기"
categories: ["hugo"]
tags: ["2024"]
---

## 1. 사전 설치

```bash
$ brew install hugo

$ hugo version
hugo v0.131.0+extended darwin/arm64 BuildDate=2024-08-02T09:03:48Z VendorInfo=brew
```

Hugo v0.112.0 이상인지 확인하면 된다.

## 2. 사이트 생성

작업하고 싶은 위치에 Hugo 디렉토리를 만들어준다.

```bash
$ pwd
/Users/yshmbid

$ mkdir Hugo
$ cd Hugo
```

Hugo로 들어가서 hugo 사이트 틀을 생성해준다. 나는 blog라는 이름으로 생성하였다.

```bash
$ pwd
/Users/yshmbid/Hugo

$ hugo new site blog
```

blog 디렉토리에 빈 Git 저장소를 초기화한다.

```bash
$ cd blog
$ pwd
/Users/yshmbid/Hugo/blog

$ git init
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint:
hint: 	git config --global init.defaultBranch <name>
hint:
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint:
hint: 	git branch -m <name>
/Users/yshmbid/Hugo/blog/.git/ 안의 빈 깃 저장소를 다시 초기화했습니다
```

위에서 `Using 'master' as the name for the initial branch.` 언급이 나온다. 여기서 확인해줘야 할 부분이 있다.

레포지토리 생성 페이지에서 Add a README file.을 체크하면 This will set main as the default branch.라는 안내가 뜬다. 이를 통해 default가 main임을 확인할수있다.
따라서 이와같은 경우에는 master가 아닌 main로 바꿔줘야 한다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ git config --global init.defaultBranch main
$ git branch -m main
```

다음으로 테마를 골라주고, 선택한 테마를 Git 서브모듈로 프로젝트에 추가한다. https://themes.gohugo.io/ 에서 Hugo 템플릿을 고를 수 있고, 나는 hugo-book이라는 테마를 사용했다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ git submodule add https://github.com/alex-shpak/hugo-book themes/hugo-book
```

다음으로, 블로그의 기본 설정들을 세팅해준다. blog 디렉토리 내 파일들은 대략적으로 아래와 같이 구성되어 있다.

```bash
$ ls
archetypes	data		i18n		resources
assets		hugo.toml	layouts		static
content		public		themes
```

이 중에서 `content`와 `hugo.toml`만 수정할것이다. (이 단계에서 존재하지 않아도 상관없다!) `content`에는 작성한 게시물이 들어가고, `hugo.toml`에는 기본 세팅을 위한 config 변수들이 들어간다.

`content`와 `hugo.toml` 수정은 테마별로 내용이 다를 수 있으며 해당 테마가 배포된 깃허브의 README 파일을 확인하는 것을 추천한다.

**`content` 수정**

![image](https://github.com/user-attachments/assets/01fcd512-3d57-4158-95a9-3dfee431d0ee)

hugo-book 테마의 경우에는 사이트 빌드에 대해 [이와 같이](https://github.com/alex-shpak/hugo-book/tree/master?tab=readme-ov-file#creating-site-from-scratch) 언급하고 있다. 해당 테마는 국가별로 여러 content 디렉토리가 존재해서, 그 중 main이 되는 content.en의 내용만을 시키는대로 복사해준다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ cp -R themes/hugo-book/exampleSite/content.en/* ./content
```

**`hugo.toml` 수정**

`hugo.toml`에 선택한 테마를 설정해주고 열어서 확인해본다.

```bash
$ echo "theme = 'hugo-book'" >> hugo.toml
$ view hugo.toml
  1 baseURL = 'https://example.org/'
  2 languageCode = 'en-us'
  3 title = 'My New Hugo Site'
  4 theme = 'hugo-book'
```

여기서 base가 되는 내용만 수정해줬고,

```bash
  1 baseURL = 'https://yshghid.github.io/'
  2 languageCode = 'en-us'
  3 title = 'Bioinfo Cookbook'
  4 theme = 'hugo-book'
  # i를 누르면 편집모드로 전환된다.
  # 편집이 끝났으면 esc를 누르고 :wq!를 입력하면 완료된다.
```

hugo-book 테마에서 제공하는 [기본 configuration 파라미터들](https://github.com/alex-shpak/hugo-book/tree/master?tab=readme-ov-file#site-configuration)이 있어서 해당 내용을 복사하고 그대로 붙여주었다. 

기본적인 설정이 끝났으므로 로컬에서 실행시켜보자! http://localhost:1313에 접속하면 local 환경에서 어떻게 실행 중인지 확인할수있다.

```bash
$ hugo server
```

![image](https://github.com/user-attachments/assets/bdb165a6-35f2-4bb4-80da-95e32ee1cb15)

이렇게 뜨면 성공이고

변경 사항을 픽스하려면 hugo를 수행해서 public 디렉토리에 static site 코드를 생성해준다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ hugo
Start building sites …
hugo v0.131.0+extended darwin/arm64 BuildDate=2024-08-02T09:03:48Z VendorInfo=brew

WARN  Expand shortcode is deprecated. Use 'details' instead.

                   | EN
-------------------+-----
  Pages            | 58
  Paginator pages  |  0
  Non-page files   |  0
  Static files     | 78
  Processed images |  0
  Aliases          | 11
  Cleaned          |  0

Total in 66 ms
```

## 3. 사이트 배포

hugo로 만든 static site를 github page를 활용해서 배포할것이다. 이를 위해서 `<user-id>.github.io` 리포지토리를 생성해준다.

이때 Add a README file 을 선택할 경우 push 할때 오류가 날 수 있으므로 체크 해제해서 생성해주는게 좋다.

다음으로, .github/workflows 경로에 gh-pages.yml 파일을 만들어준다. gh-pages.yml은 GitHub Actions 워크플로우를 정의하여 코드가 커밋되거나 푸시될 때 자동으로 Hugo 사이트를 빌드하고 GitHub Pages에 배포할 수 있도록 하는 파일이다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ mkdir -p .github/workflows
$ cd .github/workflows
$ touch gh-pages.yml
```

[HUGO 공식 문서](https://gohugo.io/hosting-and-deployment/hosting-on-github/)에서 직접 작성해줘도 되고, 아래 코드를 복사해서 사용해줘도된다. 

```
name: github pages

on:
  push:
    branches:
      - main  # Set a branch to deploy
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-20.04
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Create .nojekyll
        run: echo '' > .nojekyll

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
          # extended: true

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v4
        if: github.ref == 'refs/heads/main'
        with:
          github_token: ${{ secrets.GH_TOKEN }}
          publish_dir: ./public
```

여기서도 line 6의 main를 확인해주자! default가 master라면 master로 바꿔줘야 한다.

코드를 사용하려면 GH_TOKEN를 정의해줘야 한다. 리포지토리의 Settings -> Secretes and Variables -> Actions 에서 Repository secretes와 Repository variables를 생성해준다. Secret 자리에 토큰을 입력해주면 된다.

그리고 `- name: Create .nojekyll` `run: echo '' > .nojekyll`이라는 부분이 있는데, 나의 경우 page build and deployment에서 자꾸 jekyll로 빌드하는 오류가 발생해서 넣어준것이다. 이부분을 빼도되고, 만약 넣었다면 아래의 .nojekyll 파일을 생성해줘야한다. 

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ touch .nojekyll
```

내용은 작성할필요 없는데 파일은 있어야한다. 작성이 완료되었다면, 생성한 `<user-id>.github.io` 리포지토리에 연결한 후 커밋, 푸시해준다.

```bash
$ pwd
/Users/yshmbid/Hugo/blog

$ git remote add origin https://github.com/yshghid/yshghid.github.io.git
$ git add .
$ git commit -m "first commit"
$ git push -u origin main
```

## 마무리

이로써 블로그 생성과 배포는 끝이지만!! 추가로 확인하면 좋은 부분이 있다.

1. Actions: 리포지토리의 Actions에서 초록색 체크박스가 뜨는지 확인해주자! 오류가 난다면 해당 오류의 로그를 읽어보고 그에 맞게 수정해주면 된다.
2. Source: 리포지토리의 Settings -> Pages -> Build and deployment의 Source를 보면, 공식 문서에서는 Deploy from a branch에서 Github Actions로 바꿔주라고 나온다. 바꿔도 상관없으나 나는 그냥 뒀다. 그리고 Branch를 보면, 보통은 gh-pages 브랜치가 기본 Github Pages 브랜치로 설정되어 있지만 혹시 안되어 있다면 gh-pages로 바꿔주면 된다.
3. 구조: blog 디렉토리가 아래와 같은 구조를 띤다면 제대로 작성된 것이다.

```bash
/Users/yshmbid/Hugo/blog
├── hugo.toml
├── content/
├── layouts/
├── static/
└── .github/
    └── workflows/
        └── gh-pages.yml
```

### 참고한 블로그 및 문서

1. HUGO 공식 문서 - https://gohugo.io/getting-started/quick-start/
2. HUGO 공식 문서2 - https://gohugo.io/hosting-and-deployment/hosting-on-github/
3. hugo-book github - https://github.com/alex-shpak/hugo-book.git
4. https://c11oud.tistory.com/entry/GitHub-깃허브-블로그-만들기1
5. https://github.com/Integerous/Integerous.github.io
6. https://kzeoh.github.io/posts/make-blog/

