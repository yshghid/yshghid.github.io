+++
title = "github blog 커스텀하기: 폰트크기 수정"
categories = ["etc"]
+++

# github blog 커스텀하기: 폰트크기 수정

hugo-book 테마는 깔끔한 흰색배경이 포인트라서 css를 딱히 커스텀해줄건 없긴하지만... 

1. 코드창의 폰트사이즈가 조금 작으면 더 이쁠것같아서 수정해주려하고
2. 하이퍼링크는 파란색, 방문한링크는 보라색으로 뜨는데 보라색이 뭔가 맘에안들어서 진한 남색 정도로 바꿔줄것이다.

## 1. 코드 폰트크기 수정

[hugo-book 테마의 extra customization을 보면](https://github.com/alex-shpak/hugo-book/tree/master?tab=readme-ov-file#extra-customisation) assets/_custom.scss 경로에 커스텀하려는 부분만 적어주면 되는듯하다.

[hugo-book/assets를 보면](https://github.com/alex-shpak/hugo-book/tree/master/assets) scss 파일이 많다. 그중 _markdown.scss를 보면 코드관련 설정이 있다. 

```scss
  code {
    direction: ltr;
    unicode-bidi: embed;
    padding: 0 $padding-4;
    background: var(--gray-200);
    border-radius: $border-radius;
    font-size: 0.875em;
  }
```

여기서 font-size를 0.775em으로 수정해줄것이다. 블로그 리포지토리에 assets/_custom.scss를 생성해서 해당 내용을 추가해줬다.

```scss
//markdown
.markdown {
  code {
    font-size: 0.775em;
  }
}
```

![image](https://github.com/user-attachments/assets/53f34a7b-c2be-43ff-bf36-7c7c9287d7db)

나란히놓고 보면 성공적으로 작아졌다.

## 2. 하이퍼링크 색깔 수정

hugo-book/assets/_defaults.scss를 보면 방문한 링크에 대한 색깔설정이 있다. 

```scss
@mixin theme-light {
  --gray-100: #f8f9fa;
  --gray-200: #e9ecef;
  --gray-500: #adb5bd;

  --color-link: #0055bb;
  --color-visited-link: #8440f1;

  --body-background: white;
  --body-font-color: black;

  --icon-filter: none;

  --hint-color-info: #6bf;
  --hint-color-warning: #fd6;
  --hint-color-danger: #f66;
}
```

color-visited-link가 #8440f1로 되어있는데, 이 보라색이 너무 튀어서 #2e1594로 낮춰주려고 한다. 이는 variable이어서 assets/_custom.scss가 아닌 _variables.scss에 넣어줘야한다. 

```scss
//defaults
@mixin theme-light {
  --color-visited-link: #2e1594;
}
```

![image](https://github.com/user-attachments/assets/2ae92af5-0b77-47d6-a874-d9c8a2f18bc0)

성공적으로 남색이 되었다!
