---
date : 2025-07-23
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "HTML #1 프로필 웹페이지 작성 실습 - css 적용"
---

# HTML #2 프로필 웹페이지 작성 실습 - css 적용

#2025-07-23

---

### 1. 구현 조건

- CSS 파일을 별도로 생성하고 HTML 파일에서 링크로 연결
- 태그 선택자, 클래스 선택자, 아이디 선택자를 적용
- Flexbox, Grid 를 적용
- 속성, 값은 임의로 반영
- 최종 결과 화면 캡쳐본과 코드 파일을 ZIP으로 묶어 제출 (이름.zip)

### 2

#html코드

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8" />
  <title>윤소현의 프로필</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>

  <!-- 헤더 -->
  <header>
    <h1>윤소현의 프로필</h1>
  </header>

  <!-- 자기소개 -->
  <section id="intro" class="section-intro infinite-fade">
    <h2>자기소개</h2>
    <p>안녕하세요! 저는 윤소현입니다. 생명공학과 바이오인포메틱스를 전공하였습니다.<br>취미는 넷플릭스, 음악감상, 산책입니다.</p>
  </section>


  <!-- 취미 -->
  <section class="section-hobby infinite-slide-left">
    <h2>취미</h2>
    <ul>
      <li>넷플릭스</li>
      <li>음악감상</li>
      <li>산책</li>
    </ul>
  </section>

  <!-- 넷플릭스 -->
  <section class="section-netflix infinite-slide-right">
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
  <section class="section-blog infinite-zoom">
    <h2>블로그</h2>
    <p><a href="https://yshghid.github.io/" target="_blank">블로그 링크</a>입니다.</p>
    <img src="media/blog.jpg" alt="블로그 이미지" class="blog-image">
  </section>

  <!-- 플레이리스트 -->
  <section class="section-playlist infinite-bounce">
    <h2>플레이리스트</h2>
    <p>자주 듣는 플레이리스트입니다.</p>
    <a href="https://www.youtube.com/watch?v=gmLyLvyLiUU&t=793s" target="_blank">
      <img src="media/playlist.jpg" alt="플레이리스트 이미지" class="playlist-img">
    </a>
  </section>

  <!-- 연락처 -->
  <section class="section-contact animate-fade">
    <h2>Contact</h2>
    <div class="contact-grid">
      <p>📧 이메일: <a href="mailto:yshggid@gmail.com">yshggid@gmail.com</a></p>
      <p>💻 GitHub: <a href="https://github.com/yshghid" target="_blank">https://github.com/yshghid</a></p>
    </div>
  </section>

  <!-- 푸터 -->
  <footer>
    <p>© 2025 윤소현</p>
  </footer>

  <script>
    document.addEventListener("DOMContentLoaded", () => {
      const observer = new IntersectionObserver((entries) => {
        entries.forEach(entry => {
          if (entry.isIntersecting) {
            entry.target.classList.add('animate-active');
            observer.unobserve(entry.target);
          }
        });
      }, { threshold: 0.1 });
  
      document.querySelectorAll('.animate-fade, .animate-slide-left, .animate-slide-right, .animate-slide-up, .animate-zoom-in')
        .forEach(el => observer.observe(el));
    });
  </script>
</body>
</html>

```



#css코드

```html
/* 기본 스타일 */
body {
    font-family: 'Segoe UI', sans-serif;
    line-height: 1.6;
    background-color: #f9f9f9;
    color: #333;
    margin: 0;
    padding: 0;
    scroll-behavior: smooth;
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

  section h2 {
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
  
  /* 넷플릭스 썸네일 (Flexbox 활용) */
  .netflix-thumbnails {
    display: flex;
    flex-wrap: wrap;
    gap: 15px;
    margin-top: 10px;
    justify-content: center;
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
  
  /* 플레이리스트 */
  .playlist-img {
    width: 100%;
    max-width: 500px;
    border-radius: 10px;
    margin-top: 10px;
    box-shadow: 0 4px 10px rgba(0,0,0,0.1);
    transition: transform 0.2s, box-shadow 0.2s;
  }
  
  .playlist-img:hover {
    transform: scale(1.03);
    box-shadow: 0 8px 16px rgba(0,0,0,0.15);
  }
  
  /* Contact Grid */
  .contact-grid {
    display: grid;
    grid-template-columns: 1fr 1fr;
    gap: 10px;
  }
  
  /* 푸터 */
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
  
    .contact-grid {
      grid-template-columns: 1fr;
    }
  }
  
/* 공통 애니메이션 초기 상태 */
.animate-fade,
.animate-slide-left,
.animate-slide-right,
.animate-slide-up,
.animate-zoom-in {
  opacity: 0;
  transform: translateY(20px);
  transition: all 1s ease-out;
}

/* 화면에 보이면 활성화 */
.animate-active {
  opacity: 1;
  transform: none;
}

/* 각각의 추가적인 transform 효과 (선택사항) */
.animate-slide-left { transform: translateX(-50px); }
.animate-slide-right { transform: translateX(50px); }
.animate-slide-up { transform: translateY(50px); }
.animate-zoom-in { transform: scale(0.8); }

.animate-slide-left.animate-active,
.animate-slide-right.animate-active,
.animate-slide-up.animate-active,
.animate-zoom-in.animate-active {
  transform: none;
}

/* 공통 스타일 */
section {
    animation-duration: 3s;
    animation-iteration-count: infinite;
    animation-timing-function: ease-in-out;
  }
  
  /* Fade In-Out 반복 */
  @keyframes fadeLoop {
    0%, 100% { opacity: 0; }
    50% { opacity: 1; }
  }
  .infinite-fade {
    animation-name: fadeLoop;
  }
  
  /* 좌우 슬라이드 반복 */
  @keyframes slideLeftRight {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(-100px); }
    75% { transform: translateX(100px); }
  }
  .infinite-slide-left {
    animation-name: slideLeftRight;
  }
  
  /* 오른쪽에서 왼쪽 반복 */
  @keyframes slideRightLeft {
    0%, 100% { transform: translateX(0); }
    25% { transform: translateX(100px); }
    75% { transform: translateX(-100px); }
  }
  .infinite-slide-right {
    animation-name: slideRightLeft;
  }
  
  /* Zoom 반복 */
  @keyframes zoomInOut {
    0%, 100% { transform: scale(1); }
    50% { transform: scale(0.75); }
  }
  .infinite-zoom {
    animation-name: zoomInOut;
  }
  
  /* Bounce 위아래 */
  @keyframes bounceUpDown {
    0%, 100% { transform: translateY(0px); }
    50% { transform: translateY(-100px); }
  }
  .infinite-bounce {
    animation-name: bounceUpDown;
  }
  
  .section-intro {
    background: linear-gradient(135deg, #f6d365, #fda085); /* 주황 */
  }
  
  .section-hobby {
    background: linear-gradient(135deg, #a1c4fd, #c2e9fb); /* 하늘 */
  }
  
  .section-netflix {
    background: linear-gradient(135deg, #fbc2eb, #a6c1ee); /* 핑크-보라 */
  }
  
  .section-blog {
    background: linear-gradient(135deg, #ffecd2, #fcb69f); /* 베이지-코랄 */
  }
  
  .section-playlist {
    background: linear-gradient(135deg, #cfd9df, #e2ebf0); /* 블루그레이 */
  }
  
  
  .section-contact {
    animation: hueRotateLoop 10s linear infinite;
    background: linear-gradient(135deg, #e0c3fc, #8ec5fc); /* 연보라-파랑 */
    background-size: 200% 200%;
    transition: all 1s ease;
    color: white;
}

/* hue-rotate로 색감 변화 */
@keyframes hueRotateLoop {
  0% { filter: hue-rotate(0deg); }
  50% { filter: hue-rotate(180deg); }
  100% { filter: hue-rotate(360deg); }
}

#intro {
    border: 2px dashed #ff8c00; /* 예시: 주황색 점선 테두리 */
    padding: 35px; /* 여백 살짝 확대 */
  }
```

#실행결과

![image](https://github.com/user-attachments/assets/424c8213-b5cc-4c63-a39c-7cfc00991450)

#링크

[자기소개2.html](https://github.com/yshghid/skala/blob/main/CSS/%EC%9E%90%EA%B8%B0%EC%86%8C%EA%B0%9C/%EC%9E%90%EA%B8%B0%EC%86%8C%EA%B0%9C2.html) [style.css](https://github.com/yshghid/skala/blob/main/CSS/%EC%9E%90%EA%B8%B0%EC%86%8C%EA%B0%9C/style.css)

다음 구조에서 제대로 열린다.

```plain text
자기소개2.html
style.css
media/
├── 증명사진.jpg
├── blog.jpg
├── net1.jpg
├── net2.jpg
├── net3.jpg
├── net4.jpg
└── playlist.jpg
```
