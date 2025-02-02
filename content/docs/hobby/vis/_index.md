---
weight: 19
bookComments: false
title: "♡✧。"
---

# ♡✧。

생각 조각 모음

## 2월 

2월 1주
  - 트랜스포머의 인코더와 디코더 [>>](yshghid/github.io/docs/hobby/vis/#트랜스포머의-인코더와-디코더)


## 아카이브

### 1월

  - BOBBY Lucky man 앨범 [>>](yshghid.github.io/docs/hobby/vis/#bobby-lucky-man-앨범)

---

## 트랜스포머의 인코더와 디코더

##### 2025.02.01

---

트랜스포머의 인코더.

- 셀프 어텐션은 입력 단어 사이 연관성을 어텐션 점수로 계산하는 것. 입력 단어를 Q, K, V벡터로 변환하고, Q와 K벡터의 내적을 구하고, 소프트맥스를 적용하여 확률로 변환하고, V벡터를 이 확률로 가중합하여 최종 어텐션 점수를 얻는다.
- 단어 간의 관계는 여러 종류가 있는데 1개의 헤드를 사용하면 1가지 관계만 학습될것임. 멀티 헤드 어텐션을 수행해서 다양한 관계 패턴을 동시에 학습한다. 관계의 종류로는 문법적 관계(cat-sat은 주어-동사), 의미적 관계(cat-mat은 동물-사물이 놓인곳), 위치적 관계(on-mat)가 있다. 각 헤드에서 나온 결과를 병합하고 최종 선형 변환해서 하나의 벡터를 얻는다.
 
- 단어의 임베딩 벡터 + sin/cos으로 계산된 위치 인코딩 = 최종 입력 벡터. 이 작업을 Residual Connection라고 함.
- Residual Connection하고 Layer Normalization 해준다. 그래야 기울기 소실이나 기울기 폭발이 일어나지 않는다.
- 위 입력 벡터를 FFN이 받아서 선현 변환 -> ReLU 활성화 함수 적용 -> 선형 변환 -> 출력.

트랜스포머의 디코더.

- 핵심 연산은 MMS. 똑같이 Q,K,V벡터로 변환하고 어텐션 스코어 계산하고, 마스킹할 단어의 스코어를 −∞로 설정하여 Softmax에서 0이 되게 해서 최종 출력 벡터 생성한다.
- 다음으로 인코더에서 나온 K, V벡터와 디코더에서 나온 Q벡터를 활용해서 multi head attention을 수행한다. 이를 cross attention이라고 하는데 이를 통해서 디코더가 인코더의 정보를 반영해서 다음 토큰을 예측할수있게 한다.
- 다음으로 FFN. 두개의 완전 연결층으로 구성. 입력 -> 중간차원 -> 활성화함수 ReLU or GELU -> 출력.
- 다음으로 똑같이 Residual Connection하고 Layer Normalization 해준다.
- 마지막 디코더 블록에서 나온 결과는 완전 연결층(dense layer)을 거쳐 변환 -> softmax를 적용해서 단어 확률 분포 계산 -> 가장 확률이 높은 단어를 출력함.


(정리한 공부: 구글 BERT의 정석 - 트랜스포머 입문 [>>](https://yshghid.github.io/docs/study/cs/cs15/))

---

## BOBBY Lucky man 앨범

##### 2025.01.31

---

https://www.youtube.com/watch?v=ToCeMyOBcLA&t=2102s
![image](https://github.com/user-attachments/assets/260fe9d7-b5ef-46ae-a228-564471815095)


요즘 맨날 essential같은 플리모음만 듣다가 오랜만에 바비 플리가 떠서 틀었는데 넘 좋다!

1

바비 앨범 다들었었는데 Lucky man이 내 최애앨범이었음.
1. 일단 노래가 좋고
2. 어쩌다가 노래 소개하는 vlive 영상을 봤는데 이 앨범 나온게 결혼하기 전이었나 결혼한 직후인가 그랬고 본인이 이런 노래를 낼수있도록 강렬한 감정을 누군가가 선사해줬고 이런 경험을 할수있었던 자기가 lucky man이어서 이렇게 지었다고 했는데 자기 감정선 설명을 너무 잘해서 호감이었음. 그리고 설명 듣고 들으니까 노래가 더 좋게들렸다
   
   https://www.youtube.com/watch?v=1t4sVHqRfVQ
   
   원래 공식 영상도 있었는데 다 내려가고 없넹... 외국인이 번역해놓은 불법으로 추정되는 영상만 남아있다. 벌써 4년전이라니

이중 최애곡 4개는
- 7 새벽에 (In THE DaRk)
- 9 Ur SOUL Ur BodY (feat. dk)
- RaiNinG (feat. JU-NE)
- 내려놔 (Let iT Go)

2

몰랐는데 1년 전에 정규 3집을 냈음

3

![image](https://github.com/user-attachments/assets/28ebf98e-3cde-49d6-8eea-219a228c354c)

이 사진 맘에든다 웹툰 문유 느낌남
