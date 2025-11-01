---
date : 2025-09-22
tags: ['2025-09']
categories: ['career']
bookHidden: true
title: "SKALA 원서: 참여연구 정리"
---

# SKALA 원서: 참여연구 정리

#2025-09-22

---

<mark>#1 SHAP 연구</mark>

- 논문 링크 - https://www.nature.com/articles/s41598-024-75924-x

```
- 연구 목적
  - COVID-19 환자의 질병 진행 과정(악화 단계와 회복 단계)에 따라 차별적으로 발현되는 사이토카인을 규명하고, XAI 기법 SHAP을 기반으로 질병 진행을 설명할 수 있는 바이오마커를 발굴

- 연구 설계
  - 데이터 수집: COVID-19 환자 444명, 건강 대조군 145명으로부터 전자건강기록(EHR)과 혈액 기반 사이토카인 발현 데이터 확보
  - 전처리: 191종의 사이토카인 중 결측치 비율 15% 이하인 166종만 선택, MissForest(Random Forest 기반 결측치 대체) 기법을 사용
  - 병리적 진행 그룹(PPG) 정의: 종양괴사인자 비율(NLR), 젖산탈수소효소(LDH) 지표를 이용해 Deterioration Phase(DP), Recovery Phase(RP)로 구분
  - 모델 구축: Random Forest 분류기를 Healthy vs Severe 환자 데이터로 학습 → 각 사이토카인의 기여도를 SHAP(Shapley Additive Explanations) 값으로 산출
  - 샘플 군집화: SHAP 값 기반 UMAP 차원축소 후 DBSCAN 클러스터링을 적용해 DP/RP 특이적 사이토카인 패턴 탐색
  - 통계 분석: Welch 보정 t-test와 Benjamini–Hochberg 절차를 통해 DP와 RP 간 차별적 발현 사이토카인 식별
  - 네트워크 분석: STRING 데이터베이스로 단백질-단백질 상호작용(PPI) 네트워크 구축 및 KEGG 경로 분석
```

###

<mark>#2 MutClust 연구</mark>

- 논문 링크 - https://link.springer.com/article/10.1186/s13040-025-00476-3

```
- 연구 목적
  - SARS-CoV-2 유전체 상에서 중증도와 관련된 돌연변이 hotspot을 식별하기 위해 밀도 기반 클러스터링 기법을 적용하고, 이를 통해 감염의 병리적 기전을 설명할 수 있는 변이 위치를 규명

- 연구 설계
  - 데이터 수집: GISAID에서 확보한 대규모 SARS-CoV-2 서열 데이터와 환자 임상 중증도 정보
  - 전처리: 서열 정렬(Multiple Sequence Alignment) 및 돌연변이 위치 추출, 변이 발생 빈도 계산
  - 클러스터링: Density-Based Clustering 알고리즘 MutClust을 활용해 변이 hotspot을 탐지
  - 심각도 연관성 분석: hotspot에 포함된 변이들을 환자 중증도 데이터와 매핑해 Severity-associated Hotspot 정의
  - 통계 검증: 변이 분포와 중증도 간의 연관성을 통계적 유의성 검정으로 평가
  - 시각화: hotspot 분포를 게놈 위치 기반 그래프로 표현하고, 중증도 관련 cluster를 강조 표시
```

###

<mark>#3 DHT 약물 연구</mark>

- 논문 링크 - https://link.springer.com/article/10.1007/s10120-025-01626-6

```
- 연구 목적
  - Dihydrotestosterone(DHT)-Androgen receptor(AR) 신호가 EBV 양성 위암에서 DNA 탈메틸화 매개 바이러스 재활성을 유도하여 종양을 억제하는 분자적 기전을 규명

- 연구 설계
  - 세포주 및 처리: EBV 감염 위암 세포주(SNU719, MKN1-EBV)에 DHT 처리 후 AR 의존적 반응 평가
  - 유전자 발현 분석: RNA-seq (Trimmomatic, HISAT2, SAMtools, StringTie, edgeR), 기능 분석(gProfiler)
  - 세포 사멸 측정: CCK-8 cytotoxicity assay, Muse Annexin V & Dead Cell assay
  - 유전자 조작: CRISPR/Cas9 및 shRNA 기반 AR knockdown/knockout 모델 구축
  - 단백질 분석: Western blot (ATM, H2A.X, p53, DNMT3A, EBV 단백질 BZLF1, EBNA1 등)
  - 에피제네틱 분석: 전장 비스펄파이트 시퀀싱(WGBS, Bismark, Bowtie2, Bedtools)으로 DNA 메틸화 패턴 평가
  - 바이러스 활성 측정: EBV DNA copy 수(qPCR), 루시퍼레이스 리포터(BHLF1 promoter)
  - 신호전달 분석: PI3K-Akt, DNA damage response(ATM, γ-H2A.X, p53) 활성 평가
  - 생체 내 검증: 마우스 이종이식 모델(MKN1-EBV, SNU719)에서 종양 성장, 면역세포 침윤, 바이러스 유전자 발현 관찰
  - 임상 데이터 분석: Kaplan–Meier 생존 분석(TCGA PanCancer Atlas, TCGA Nature 2014 dataset)

- 참여 파트
  - 유전자 발현 분석: RNA-seq 데이터 전처리 및 분석
    - Trimmomatic: read quality control
    - HISAT2: reference genome 정렬
    - SAMtools: alignment 처리 및 관리
    - StringTie: transcript 조립 및 정량화
    - edgeR: 발현 차이 분석 (DEG)
    - gProfiler: 기능적 pathway enrichment 분석

  - 에피제네틱 분석: 전장 비스펄파이트 시퀀싱(WGBS) 데이터 전처리 및 분석
    - Bismark: bisulfite read alignment
    - Bowtie2: 시퀀스 매핑
    - Bedtools: 메틸화 패턴 및 genomic feature 매핑
```


#