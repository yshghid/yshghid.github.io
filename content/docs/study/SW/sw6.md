---
date : 2025-07-24
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "Markdown 내부에서 HTML 허용"
---

# Markdown 내부에서 HTML 허용

#2025-07-24

---

```plain text
<details>
  <summary> 토글 </summary>
    토글 내용
</details>
```

<details>
  <summary> 토글 </summary>
    토글 내용
</details>

Hugo book Theme는 [원래](https://github.com/alex-shpak/hugo-book/blob/master/exampleSite/content.en/docs/shortcodes/details.md) 위 코드를 작성하면 아래처럼 토글이 나온다.

어느날부터 갑자기 토글이든 문단나누기든 다 안먹어서, 근데 원인을 몰라서 그냥 shortcode 기능 없는대로 쓰다가, 너무 불편해서 좀 찾아봤고 `hugo.toml`에 다음 내용 넣어준 뒤로는 잘 작동했다.

```toml
[markup]
  [markup.goldmark]
    [markup.goldmark.renderer]
      unsafe = true
```

근데 이후에 [html 관련 포스팅](https://yshghid.github.io/docs/study/sw/sw5/)을 작성하는데 넣어준 코드가 다 깨졌다. 

근데 심지어 html 코드 뿐만아니라 plain text 처리된 코드들도 다 깨졌다. (위에서 보다시피 이 글에선 잘 나오는데..)

![image](https://github.com/user-attachments/assets/74612054-0dc2-41f0-bfa4-c2a07747c60f)

실제 호스팅 화면은 이렇게 나오고 (댓글창도 없어짐)

![image](https://github.com/user-attachments/assets/edbe7f19-7d7a-46c4-baaf-aa3f90a16823)

md 파일은 깨졌다.

html이라는 글자 자체가 문제인가해서 html이라는 글자를 모두 제거해보고
