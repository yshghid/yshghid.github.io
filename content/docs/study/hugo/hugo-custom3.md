---
author: "kaya"
date: 2024-08-26
title: "github blog customize: archive 창 수정"
categories: ["etc"]
tags: ["2024-07"]
weight: 43
---

# github blog customize: archive 창 수정

![image](https://github.com/user-attachments/assets/33023e26-586b-4908-b05f-78b713d669c0)

hugo-book 테마에서 tag, category에 따른 archive창을 볼수있는데 위와같이 구성돼있다.

내용없이 제목만 뜨게 만들고싶어서 [hugo-book 테마의 layout 디렉토리](https://github.com/alex-shpak/hugo-book/tree/master/layouts)를 찾아보니 [taxonomy/taxonomy.html](https://github.com/alex-shpak/hugo-book/blob/master/layouts/taxonomy/taxonomy.html)과 [posts/list.html](https://github.com/alex-shpak/hugo-book/blob/master/layouts/posts/list.html)이 관여하고있는듯해서, 내 리포지토리에 taxonomy/taxonomy.html과 layouts/posts/list.html을 생성해서 각각 다음과 같이 작성해주었다.

```html
{{ define "main" }}
  {{ range sort .Paginator.Pages }}
  <article class="markdown book-post">
    <h2>
      <a href="{{ .RelPermalink }}">{{ partial "docs/title.html" . }}</a>
    </h2>
    {{ partial "docs/post-meta" . }}

  </article>
  {{ end }}

  {{ template "_internal/pagination.html" . }}
{{ end }}

{{ define "toc" }}
  {{ partial "docs/taxonomy" . }}
{{ end }}
```

참고로 위코드는 원래코드에서 아래 내용을 뺀것이다.

```html
    <p>
      {{- .Summary -}}
      {{ if .Truncated }}
        <a href="{{ .RelPermalink }}">...</a>
      {{ end }}
    </p>
```

열어보면 archive 창에서 본문내용이 사라지고 보다 깔끔해진것을 확인할수있다!

![image](https://github.com/user-attachments/assets/87755cef-c869-4009-8ef5-af0ed9eecdc5)

