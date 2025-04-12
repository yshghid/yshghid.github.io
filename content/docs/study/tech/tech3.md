---
date : 2025-04-11
tags: ['2025-04']
categories: ['python, 주식']
bookHidden: true
title: "Kaggle 타이타닉 문제"
bookComments: true
---

# Kaggle 타이타닉 문제

## 목록

*2024-04-12* ⋯ [Kaggle API 사용법](https://yshghid.github.io/docs/study/tech/tech3/#kaggle-api-사용법)

---

## Kaggle API 사용법

### 1. 사전 설정

1. 캐글 설치

```python
!pip install kaggle
```
```plain text
Collecting kaggle
  Downloading kaggle-1.7.4.2-py3-none-any.whl.metadata (16 kB)
Requirement already satisfied: bleach in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (6.1.0)
Requirement already satisfied: certifi>=14.05.14 in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (2024.7.4)
Requirement already satisfied: charset-normalizer in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (3.3.2)
Requirement already satisfied: idna in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (3.7)
Collecting protobuf (from kaggle)
  Downloading protobuf-5.29.4-cp38-abi3-macosx_10_9_universal2.whl.metadata (592 bytes)
Requirement already satisfied: python-dateutil>=2.5.3 in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (2.9.0)
Collecting python-slugify (from kaggle)
  Downloading python_slugify-8.0.4-py2.py3-none-any.whl.metadata (8.5 kB)
Requirement already satisfied: requests in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (2.32.3)
Requirement already satisfied: setuptools>=21.0.0 in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (75.1.0)
Requirement already satisfied: six>=1.10 in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (1.16.0)
Collecting text-unidecode (from kaggle)
  Downloading text_unidecode-1.3-py2.py3-none-any.whl.metadata (2.4 kB)
Collecting tqdm (from kaggle)
  Downloading tqdm-4.67.1-py3-none-any.whl.metadata (57 kB)
Requirement already satisfied: urllib3>=1.15.1 in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (2.2.2)
Requirement already satisfied: webencodings in /opt/anaconda3/envs/workspace/lib/python3.8/site-packages (from kaggle) (0.5.1)
Downloading kaggle-1.7.4.2-py3-none-any.whl (173 kB)
Downloading protobuf-5.29.4-cp38-abi3-macosx_10_9_universal2.whl (417 kB)
Downloading python_slugify-8.0.4-py2.py3-none-any.whl (10 kB)
Downloading text_unidecode-1.3-py2.py3-none-any.whl (78 kB)
Downloading tqdm-4.67.1-py3-none-any.whl (78 kB)
Installing collected packages: text-unidecode, tqdm, python-slugify, protobuf, kaggle
Successfully installed kaggle-1.7.4.2 protobuf-5.29.4 python-slugify-8.0.4 text-unidecode-1.3 tqdm-4.67.1
```
2. Kaggle API 토큰 생성
3. kaggle.json 파일을 다음 경로에 넣기

```bash
cd ~
mkdir .kaggle
cd .kaggle
mv /Users/yshmbid/Desktop/kaggle.json .
chmod 600 kaggle.json # 사용자 권한을 사용자 ID만 읽고 쓸수있게 하겠다는 의미
```

4. titanic 데이터 다운로드

```python
!kaggle competitions download -c titanic
```
bash에서 확인해보면?

```bash
$ pwd
/Users/yshmbid/project/bin
$ ls
1-kaggle.ipynb	titanic.zip
```

titanic.zip이 생겼다!



### 2. 


cf) 머신러닝

- 지도 학습
  - 회귀 문제: 값(연속 값) 예측
  - 분류 문제: 분류 값(이산 값) 예측
- 비지도 학습
  - 군집화(Clustering): 비슷한 데이터를 묶어서 그룹으로 분류하는 기술
  - 이외에도 밀도 추정(기존 데이터를 기반으로 밀도를 추정하는 기법), 차원 축소(이미지 등에서 핵심 특징을 찾아서 이미지 구별) 등의 기술도 있음.
- 강화 학습
  - 최근에는 딥러닝 기반, AI 기술을 주로 사용함
 




> 강의 링크 https://www.inflearn.com/course/%EC%B2%98%EC%9D%8C-%ED%8C%8C%EC%9D%B4%EC%8D%AC-%EB%A8%B8%EC%8B%A0%EB%9F%AC%EB%8B%9D-%EC%9E%85%EB%AC%B8/dashboard

[⏶ 목록](https://yshghid.github.io/docs/study/tech/tech3/#목록)

