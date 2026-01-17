---
date : 2025-07-22
tags: ['2025-07']
categories: ['FE']
bookHidden: true
title: "HTML #1 프로필 웹페이지 작성"
---

# HTML #1 프로필 웹페이지 작성

#2025-07-22

---

### 1

<mark>#구조</mark>

```plain text
/HTML
├── 자기소개1.html
├── 자기소개2.html
└── media/
     ├── 증명사진.jpg
     ├── blog.jpg
     ├── net1.jpg
     ├── net2.jpg
     ├── net3.jpg
     ├── net4.jpg
     └── playlist.jpg
```

<mark>#코드</mark>

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>윤소현의 프로필</title>
</head>
<body>

  <!-- 헤더 -->
  <header>
    <h1>윤소현의 프로필</h1>
  </header>

  <!-- 자기소개 섹션 -->
  <section>
    <h2>자기소개</h2>
    <p>안녕하세요! 저는 윤소현입니다. 생명공학과 바이오인포메틱스를 전공하였습니다. 취미는 넷플릭스, 음악 감상 입니다.</p>
  </section>

  <!-- 정보 목록 섹션 -->
  <section>
    <h2>취미</h2>
    <ul>
      <li>넷플릭스</li>
      <li>음악 감상</li>
      <li>산책</li>
    </ul>
  </section>

  <!-- 넷플릭스 -->
  <section>
    <h2>넷플릭스</h2>
    <p>최근 본 작품:
      <a href="https://www.netflix.com/search?q=%EB%AF%B8%EC%A7%80%EC%9D%98&jbv=82024804" target="_blank">미지의 서울</a> |
      <a href="https://www.netflix.com/search?q=%EC%84%B1%EB%82%9C&jbv=81447461" target="_blank">성난 사람들</a> |
      <a href="https://www.netflix.com/search?q=%EB%8D%B0%EB%B8%94%EC%8A%A4&jbv=81653386" target="_blank">데블스플랜2</a> |
      <a href="https://www.netflix.com/browse?jbv=80994899" target="_blank">보헤미안 랩소디</a>
    </p>

    <div class="netflix-thumbnails">
      <img src="media/net1.jpg" alt="미지의 서울" title="미지의 서울">
      <img src="media/net2.jpg" alt="성난 사람들" title="성난 사람들">
      <img src="media/net3.jpg" alt="데블스플랜2" title="데블스플랜2">
      <img src="media/net4.jpg" alt="보헤미안 랩소디" title="보헤미안 랩소디">
    </div>
  </section>

  <style>
    .netflix-thumbnails {
      display: flex;
      gap: 15px;
      margin-top: 10px;
    }

    .netflix-thumbnails img {
      width: 120px;
      height: auto;
      border-radius: 8px;
      transition: transform 0.2s;
    }

    .netflix-thumbnails img:hover {
      transform: scale(1.05);
    }
  </style>

  <style>
    .tooltip {
      position: relative;
      cursor: pointer;
    }

    .tooltip-img {
      position: absolute;
      top: 1.5em;
      left: 0;
      display: none;
      width: 150px;
      height: auto;
      border: 1px solid #ccc;
      background: #fff;
      padding: 5px;
      z-index: 100;
    }

    .tooltip:hover .tooltip-img {
      display: block;
    }
  </style>

  <!-- 블로그 -->
  <section>
    <h2>블로그</h2>
    <p><a href="https://yshghid.github.io/" target="_blank">블로그 링크</a> 입니다.</p>
    <img src="media/blog.jpg" alt="블로그 이미지" style="width:500px; border-radius:10px;">
  </section>


  <!-- 플레이리스트 -->
  <section>
    <h2>플레이리스트</h2>
    <p>자주 듣는 플레이리스트 입니다.</p>
    <a href="https://www.youtube.com/watch?v=gmLyLvyLiUU&t=793s" target="_blank">
      <img src="media/playlist.jpg" alt="플레이리스트 이미지" style="width:500px; border-radius:10px;">
    </a>
  </section>


  <!-- contact -->
  <section>
    <h2>Contact</h2>
    <p>
      📧 이메일: <a href="mailto:yshggid@gmail.com">yshggid@gmail.com</a><br>
      💻 GitHub: <a href="https://github.com/yshghid" target="_blank">https://github.com/yshghid</a>
    </p>
  </section>

  <!-- 푸터 -->
  <footer>
    <p>© 2025 윤소현</p>
  </footer>

</body>
</html>
```

<mark>#실행결과</mark>

![image](https://github.com/user-attachments/assets/b553a1ba-7cfc-42f4-b056-074ae7628622)
![image](https://github.com/user-attachments/assets/ae4398c5-4948-42f1-97f1-033c614fb13a)

###

### 2

chatgpt로 css 넣은 버전.

<mark>#코드</mark>

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>윤소현의 프로필</title>
  <style>
    /* 기본 스타일 */
    body {
      font-family: 'Segoe UI', sans-serif;
      line-height: 1.6;
      background-color: #f9f9f9;
      color: #333;
      margin: 0;
      padding: 0;
    }

    header, footer {
      background-color: #343a40;
      color: white;
      text-align: center;
      padding: 20px 0;
    }

    header h1 {
      margin: 0;
      font-size: 2rem;
    }

    section {
      background-color: white;
      max-width: 800px;
      margin: 30px auto;
      padding: 30px;
      border-radius: 10px;
      box-shadow: 0 4px 8px rgba(0,0,0,0.05);
    }

    h2 {
      color: #333;
      margin-bottom: 15px;
      border-bottom: 2px solid #eee;
      padding-bottom: 5px;
    }

    ul {
      list-style: circle;
      padding-left: 20px;
    }

    a {
      color: #007acc;
      text-decoration: none;
    }

    a:hover {
      text-decoration: underline;
    }

    /* 넷플릭스 썸네일 */
    .netflix-thumbnails {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
      margin-top: 10px;
    }

    .netflix-thumbnails img {
      width: 150px;
      border-radius: 8px;
      transition: transform 0.2s, box-shadow 0.2s;
      box-shadow: 0 2px 4px rgba(0,0,0,0.1);
    }

    .netflix-thumbnails img:hover {
      transform: scale(1.05);
      box-shadow: 0 4px 8px rgba(0,0,0,0.2);
    }

    /* 블로그 이미지 */
    .blog-image {
      width: 100%;
      max-width: 500px;
      border-radius: 10px;
      margin-top: 10px;
      box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    }

    /* footer */
    footer p {
      margin: 0;
      font-size: 0.9rem;
    }

    /* 반응형 */
    @media (max-width: 600px) {
      .netflix-thumbnails {
        flex-direction: column;
        align-items: center;
      }

      .netflix-thumbnails img {
        width: 80%;
      }
    }
        /* 추가 */
    .playlist-img {
    width: 100%;
    max-width: 500px;
    border-radius: 10px;
    margin-top: 10px;
    box-shadow: 0 4px 10px rgba(0, 0, 0, 0.1);
    transition: transform 0.2s, box-shadow 0.2s;
    }

    .playlist-img:hover {
    transform: scale(1.03);
    box-shadow: 0 8px 16px rgba(0, 0, 0, 0.15);
    }
  </style>
</head>
<body>

  <!-- 헤더 -->
  <header>
    <h1>윤소현의 프로필</h1>
  </header>

  <!-- 자기소개 -->
  <section>
    <h2>자기소개</h2>
    <p>안녕하세요! 저는 윤소현입니다. 생명공학과 바이오인포메틱스를 전공하였습니다.<br>취미는 넷플릭스, 음악감상, 산책입니다.</p>
  </section>

  <!-- 취미 -->
  <section>
    <h2>취미</h2>
    <ul>
      <li>넷플릭스</li>
      <li>음악감상</li>
      <li>산책</li>
    </ul>
  </section>

  <!-- 넷플릭스 -->
  <section>
    <h2>넷플릭스</h2>
    <p>최근 본 작품:
      <a href="https://www.netflix.com/search?q=%EB%AF%B8%EC%A7%80%EC%9D%98&jbv=82024804" target="_blank">미지의 서울</a> |
      <a href="https://www.netflix.com/search?q=%EC%84%B1%EB%82%9C&jbv=81447461" target="_blank">성난 사람들</a> |
      <a href="https://www.netflix.com/search?q=%EB%8D%B0%EB%B8%94%EC%8A%A4&jbv=81653386" target="_blank">데블스플랜2</a> |
      <a href="https://www.netflix.com/browse?jbv=80994899" target="_blank">보헤미안 랩소디</a>
    </p>
    <div class="netflix-thumbnails">
      <img src="media/net1.jpg" alt="미지의 서울" title="미지의 서울">
      <img src="media/net2.jpg" alt="성난 사람들" title="성난 사람들">
      <img src="media/net3.jpg" alt="데블스플랜2" title="데블스플랜2">
      <img src="media/net4.jpg" alt="보헤미안 랩소디" title="보헤미안 랩소디">
    </div>
  </section>

  <!-- 블로그 -->
  <section>
    <h2>블로그</h2>
    <p><a href="https://yshghid.github.io/" target="_blank">블로그 링크</a>입니다.</p>
    <img src="media/blog.jpg" alt="블로그 이미지" class="blog-image">
  </section>

  <!-- 플레이리스트 -->
  <section>
    <h2>플레이리스트</h2>
    <p>자주 듣는 플레이리스트입니다.</p>
    <a href="https://www.youtube.com/watch?v=gmLyLvyLiUU&t=793s" target="_blank">
      <img src="media/playlist.jpg" alt="플레이리스트 이미지" class="playlist-img">
    </a>
  </section>

  <!-- 연락처 -->
  <section>
    <h2>Contact</h2>
    <p>
      📧 이메일: <a href="mailto:yshggid@gmail.com">yshggid@gmail.com</a><br>
      💻 GitHub: <a href="https://github.com/yshghid" target="_blank">https://github.com/yshghid</a>
    </p>
  </section>

  <!-- 푸터 -->
  <footer>
    <p>© 2025 윤소현</p>
  </footer>

</body>
</html>
```

<mark>#실행결과</mark>

![image](https://github.com/user-attachments/assets/7b99daaf-966b-442e-9eb7-9228ae338676)
![image](https://github.com/user-attachments/assets/9246ad30-33b8-4235-bab6-f3528972efaa)
![image](https://github.com/user-attachments/assets/7d3c10cd-016c-437a-a588-fbc5e0ad7331)

###

<mark>#cf</mark>

챗지피티 돌리니까 확이뻐지긴하지만 그래두... naive 버전이 더 정감가서 좋다.

#