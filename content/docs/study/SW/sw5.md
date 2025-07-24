---
date : 2025-07-23
tags: ['2025-07']
categories: ['SW']
bookHidden: true
title: "Java #1 쇼핑몰 주문 처리 실습"
---

# Java #1 쇼핑몰 주문 처리 실습

#2025-07-23

---

#문제

당신은 온라인 쇼핑몰의 개발자로, 고객 주문을 처리하는 프로그램을 작성하고 있습니다. 주문 처리 과정에서는 여러 조건을 고려해야 합니다. 예를 들어, 상품의 재고 여부, 고객의 회원 등급, 주문 금액, 배송 옵션 등을 확인하여 적절한 메시지와 할인율을 적용해야 합니다. 아래의 세부 조건에 맞도록 JavaScript 함수를 구현하고, 최종 결과를 console.log 또는 alert로 출력해보세요.

#세부 조건

상품 재고 확인
- 재고가 1개 이상일 경우: 주문을 진행한다.
- 재고가 0개일 경우: 품절 메시지를 출력한다.

회원 등급에 따른 할인율 적용
- VIP 회원: 10% 할인
- Gold 회원: 5% 할인
- 그 외 회원: 할인 없음

주문 금액에 따른 추가 할인 (기본 할인 적용 이후 기준)
- 100,000원 이상: 5,000원 추가 할인
- 200,000원 이상: 15,000원 추가 할인

배송 옵션에 따른 배송비 처리
- 배송 옵션이 "fast"인 경우: 배송비 3,000원 추가
- 배송 옵션이 "standard" 또는 미선택인 경우: 배송비 없음

최종 출력 메시지
- 주문이 정상적으로 처리된 경우: "주문이 정상적으로 완료되었습니다." 메시지와 함께 최종 결제 금액 출력
- 재고가 없는 경우: "죄송합니다. 해당 상품은 품절입니다." 메시지 출력

HTML 인터페이스 구현 조건
- 상품 재고와 주문 금액은 숫자만 입력 가능하도록 설정할 것 (type="number" 사용)
- 회원 등급과 배송 옵션은 Drop-down List 형태로 구현할 것 (<select> 태그 사용)
- 버튼 클릭 시 모든 입력값의 유효성을 검사한 후 함수가 호출되도록 할 것
- 함수 실행 결과는 alert()을 사용해 사용자에게 보여줄 것

CSS를 이용하여 페이지의 스타일을 간단히 꾸밀 것 (예: 선택자 `button`, 속성 `background-color`, 값 `green` 등 사용).

#파일 구조

```
shopping-order-system/
├── index.html         # 메인 HTML 파일 (입력 폼 및 버튼)
├── style.css          # CSS 스타일 파일 (디자인 및 레이아웃)
└── script.js          # JavaScript 로직 파일 (주문 처리 함수)
```

#결과

![image](https://github.com/user-attachments/assets/145ea5af-1ae6-4cc6-b8bf-3415d9d3fb06)
![image](https://github.com/user-attachments/assets/f2087716-da4d-4af9-b44d-8699d056b21d)
![image](https://github.com/user-attachments/assets/1bd26353-8111-445f-92bd-467e8a236d2a)
![image](https://github.com/user-attachments/assets/1e061c66-52f2-4b0b-a4e1-0d310944c85d)

#
