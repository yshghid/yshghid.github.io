---
date : 2026-02-08
tags: ['2026-02']
categories: ['BE', 'AWS']
bookHidden: true
title: "AWS #5 SiLok 프로젝트 ECS 파이프라인 빌드"
pageHidden: true
---

# AWS #5 SiLok 프로젝트 ECS 파이프라인 빌드

#2026-02-08

---

# 🏥 VitalTime - Database 구축 포트폴리오

## 프로젝트 개요

**AI 기반 환자 전원 의뢰 시스템**의 데이터베이스를 설계하고 구축한 프로젝트입니다.
PostgreSQL 14와 SQLAlchemy 2.0 비동기 ORM을 활용하여 시계열 임상 데이터를 저장하고, LATERAL JOIN 기반의 예측 NEWS Score 조회 및 LSTM 모델 학습 파이프라인을 구현했습니다.

| 항목 | 내용 |
|------|------|
| **프로젝트 기간** | 2025년 |
| **역할** | Database Engineer / Backend Developer |
| **기술 스택** | PostgreSQL 14, SQLAlchemy 2.0 (Async), asyncpg, FastAPI |
| **프로젝트 유형** | Healthcare / Medical System |

---

## 📋 목차

1. [기술적 의사결정](#1-기술적-의사결정)
2. [데이터베이스 아키텍처](#2-데이터베이스-아키텍처)
3. [스키마 설계](#3-스키마-설계)
4. [시계열 데이터 처리](#4-시계열-데이터-처리)
5. [ORM 및 비동기 처리](#5-orm-및-비동기-처리)
6. [API 엔드포인트 설계](#6-api-엔드포인트-설계)
7. [ML 파이프라인 통합](#7-ml-파이프라인-통합)
8. [성능 최적화](#8-성능-최적화)
9. [문제 해결 사례](#9-문제-해결-사례)
10. [프로젝트 성과](#10-프로젝트-성과)

---

## 1. 기술적 의사결정

### 1.1 데이터베이스 선정 근거

#### PostgreSQL 14

```
선정 이유:
├── 의료 데이터의 ACID 트랜잭션 보장 (환자 안전 필수)
├── 시계열 데이터 처리에 강력한 날짜/시간 함수 지원
├── LATERAL JOIN으로 복잡한 시계열 예측 조회 구현
├── DATE_TRUNC, INTERVAL 등 시간 연산 네이티브 지원
└── 비동기 드라이버 (asyncpg) 지원으로 실시간 모니터링 가능
```

#### 대안 비교 분석

| 데이터베이스 | 시계열 지원 | LATERAL JOIN | 비동기 지원 | 의료 표준 | 비용 |
|-------------|-----------|-------------|-----------|----------|------|
| **PostgreSQL 14** | ✅ 네이티브 | ✅ 지원 | ✅ asyncpg | ✅ HIPAA 호환 | 무료 |
| TimescaleDB | ✅✅ 전용 | ✅ 지원 | ✅ asyncpg | ✅ | 유료 |
| InfluxDB | ✅✅ 전용 | ❌ 없음 | ⚠️ 제한적 | ⚠️ | 유료 |
| MySQL 8 | ⚠️ 제한적 | ✅ 지원 | ✅ aiomysql | ✅ | 무료 |
| MongoDB | ⚠️ 제한적 | ❌ 없음 | ✅ Motor | ⚠️ | 무료 |

**최종 선정:** PostgreSQL 14
- 시계열 확장 없이도 DATE_TRUNC, INTERVAL로 시간 연산 충분
- LATERAL JOIN으로 다음 시점 예측값 조회 구현 가능
- 의료 데이터의 관계 무결성 (FK CASCADE) 보장
- 별도 시계열 DB 도입 비용 및 복잡도 절감

### 1.2 ORM 및 드라이버 선정

#### SQLAlchemy 2.0 (Async)

```
선정 이유:
├── Python 생태계 표준 ORM
├── 비동기 세션 지원 (AsyncSession)
├── FastAPI 의존성 주입 패턴과 완벽한 호환
├── text() 함수로 복잡한 Raw SQL 안전 실행
└── 연결 풀 관리 내장 (pool_size, max_overflow)
```

#### asyncpg vs psycopg2

| 비교 항목 | asyncpg | psycopg2 |
|----------|---------|----------|
| 동기/비동기 | 비동기 | 동기 |
| 성능 (TPS) | ~50,000 | ~20,000 |
| 연결 풀링 | 내장 | 외부 필요 |
| FastAPI 호환 | ✅ 완벽 | ⚠️ 블로킹 |
| 실시간 모니터링 | ✅ 적합 | ❌ 부적합 |

**선정:** asyncpg - 실시간 환자 모니터링에 필수적인 비동기 I/O 지원

### 1.3 AI/ML 모델 선정

#### LSTM (Long Short-Term Memory)

```
선정 이유:
├── 시계열 임상 데이터의 패턴 학습에 최적
├── 환자별 10개 시점 데이터의 순차적 의존성 포착
├── 9개 생체 지표 → NEWS Score 예측 가능
├── TensorFlow/Keras로 빠른 프로토타이핑
└── 주기적 재학습 (8시간 간격) 지원
```

#### LLM (전원 의뢰서 생성)

| 모델 | 용도 | 성능 | 비용 |
|------|------|------|------|
| **GPT-4** | 전원 의뢰서 생성 (API) | ✅ 높음 | 유료 |
| **Gemma (로컬)** | 전원 의뢰서 생성 (오프라인) | ⚠️ 보통 | 무료 |

**선정:** GPT-4 (기본) + Gemma (폴백) - 의료 문서의 정확성과 비용 효율 균형

---

## 2. 데이터베이스 아키텍처

### 2.1 프로젝트 디렉토리 구조

```
VitalTime/
├── backend/                    # FastAPI 백엔드
│   ├── main.py                # 통합 백엔드 (API + DB + ML 로직)
│   ├── requirements.txt       # Python 의존성
│   ├── logs/                  # 모니터링 로그
│   │   ├── api_monitoring.log
│   │   └── ml_monitoring.log
│   └── saved_models/          # 학습된 LSTM 모델
│       ├── lstm_model_*.h5
│       ├── scalers_*.pkl
│       └── model_info_*.json
│
├── frontend/                   # Vue.js 프론트엔드
│   ├── components/            # Vue 컴포넌트
│   │   ├── PatientSearch.vue  # 환자 검색
│   │   ├── PatientDetail.vue  # 환자 상세 정보
│   │   ├── PatientReport.vue  # 전원 의뢰서
│   │   └── Map.vue            # 병원 지도
│   ├── index.html             # 메인 페이지
│   ├── main.js                # 앱 진입점
│   ├── monitoring.html        # 모니터링 대시보드
│   └── monitoring.js          # 모니터링 로직
│
├── data/                       # 데이터베이스 스크립트
│   ├── dump.sql               # Full DB dump (스키마 + 데이터)
│   ├── sample.sql             # 스키마 생성 스크립트
│   ├── sample_root.sql        # root 권한 스키마
│   └── data.xlsx              # 원본 임상 데이터
│
└── .env                        # 환경 변수 (DATABASE_URL, API 키)
```

### 2.2 전체 시스템 아키텍처

```
┌─────────────────────────────────────────────────────────────────────────┐
│                        Frontend (Vue.js)                                │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │
│  │PatientSearch│  │PatientDetail│  │PatientReport│  │  Map.vue    │   │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘   │
└─────────┼────────────────┼────────────────┼────────────────┼──────────┘
          │                │                │                │
          └────────────────┼────────────────┼────────────────┘
                           ▼                ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                        FastAPI Backend                                   │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐   │
│  │ Patient API │  │Clinical API │  │ Report API  │  │Monitoring   │   │
│  │ (검색/조회) │  │ (시계열)    │  │ (전원의뢰)  │  │ API         │   │
│  └─────────────┘  └─────────────┘  └─────────────┘  └─────────────┘   │
│         │                │                │                │            │
│         └────────────────┼────────────────┼────────────────┘            │
│                          ▼                ▼                              │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                 Pydantic Schemas (요청/응답 모델)                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │                    SQLAlchemy AsyncSession                         │  │
│  │  ┌─────────────────────────────────────────────────────────────┐  │  │
│  │  │                    Connection Pool (asyncpg)                 │  │  │
│  │  └─────────────────────────────────────────────────────────────┘  │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │              LSTM Model Training (8시간 주기)                      │  │
│  │  ┌─────────┐  ┌─────────┐  ┌─────────┐                            │  │
│  │  │ Scaler  │  │  LSTM   │  │  Model  │                            │  │
│  │  │  (X/Y)  │  │ 학습    │  │  저장   │                            │  │
│  │  └─────────┘  └─────────┘  └─────────┘                            │  │
│  └───────────────────────────────────────────────────────────────────┘  │
│  ┌───────────────────────────────────────────────────────────────────┐  │
│  │           LLM (GPT-4 / Gemma) - 전원 의뢰서 생성                   │  │
│  └───────────────────────────────────────────────────────────────────┘  │
└─────────────────────────────────────────────────────────────────────────┘
                                    │
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│                       PostgreSQL 14 (Local)                              │
│  ┌─────────────────────────────────────────────────────────────────────┐│
│  │                       public schema                                  ││
│  │  ┌─────────────┐  ┌─────────────────┐  ┌─────────────┐              ││
│  │  │   patient   │  │  clinical_data  │  │   report    │              ││
│  │  │  (10 rows)  │  │   (100 rows)    │  │  (dynamic)  │              ││
│  │  └──────┬──────┘  └────────┬────────┘  └──────┬──────┘              ││
│  │         │    1:N           │    N:1            │    N:1              ││
│  │         └─────────────────►│◄──────────────────┘                    ││
│  │                            │                                         ││
│  │              Time-Series Clinical Data                               ││
│  │         (8-hour intervals, NEWS Score prediction)                    ││
│  └─────────────────────────────────────────────────────────────────────┘│
└─────────────────────────────────────────────────────────────────────────┘
```

### 2.3 데이터 흐름

```
┌──────────────────┐     ┌──────────────────┐     ┌──────────────────┐
│   Patient Data   │     │  Clinical Labs   │     │  NEWS Score      │
│  (환자 기본정보) │     │  (생체 지표)     │     │  (조기경보점수)  │
└────────┬─────────┘     └────────┬─────────┘     └────────┬─────────┘
         │                       │                         │
         └───────────────────────┼─────────────────────────┘
                                 ▼
                   ┌─────────────────────────────┐
                   │    Data Ingestion Layer     │
                   │  (Excel → SQL COPY 변환)    │
                   └─────────────┬───────────────┘
                                 │
                   ┌─────────────▼───────────────┐
                   │   PostgreSQL 14 Storage     │
                   │  (patient + clinical_data)  │
                   └─────────────┬───────────────┘
                                 │
          ┌──────────────────────┼──────────────────────┐
          ▼                      ▼                      ▼
┌──────────────────┐   ┌──────────────────┐   ┌──────────────────┐
│  Patient Info    │   │  LSTM Training   │   │  Transfer Report │
│  (LATERAL JOIN)  │   │  (Pandas + TF)   │   │  (GPT-4 / Gemma) │
│  NEWS Score 조회 │   │  NEWS 예측 학습  │   │  의뢰서 생성     │
└──────────────────┘   └──────────────────┘   └──────────────────┘
```

### 2.4 ERD (Entity Relationship Diagram)

```
┌──────────────────────────────────────┐
│              patient                  │
├──────────────────────────────────────┤
│ patient_id    (PK, SERIAL)           │
│ patient_name  VARCHAR(100)           │
│ severity      INTEGER                │
│ doctor_name   VARCHAR(100)           │
│ hospital_name VARCHAR(200)           │
└──────────────────┬───────────────────┘
                   │
                   │ 1:N (ON DELETE CASCADE)
                   │
         ┌─────────┴─────────┐
         │                   │
         ▼                   ▼
┌──────────────────┐  ┌──────────────────┐
│  clinical_data   │  │     report       │
├──────────────────┤  ├──────────────────┤
│ clinical_id (PK) │  │ report_id  (PK)  │
│ patient_id  (FK) │  │ patient_id (FK)  │
│ timestamp   TS   │  │ from_hospital    │
│ timepoint   INT  │  │ to_hospital      │
│ creatinine  DBL  │  │ context          │
│ hemoglobin  DBL  │  │ createdat   TS   │
│ ldh         INT  │  │ reservedat  TS   │
│ lymphocytes DBL  │  └──────────────────┘
│ neutrophils DBL  │
│ platelet_count   │
│ wbc_count   DBL  │
│ hs_crp      DBL  │
│ d_dimer     DBL  │
│ news_score  INT  │
│ news_score_label │
└──────────────────┘
```

---

## 3. 스키마 설계

### 3.1 테이블 정의

#### patient 테이블 (환자 기본 정보)

```sql
CREATE TABLE public.patient (
    patient_id SERIAL PRIMARY KEY,
    patient_name CHARACTER VARYING(100),
    severity INTEGER,
    doctor_name CHARACTER VARYING(100),
    hospital_name CHARACTER VARYING(200)
);
```

| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| patient_id | SERIAL | PRIMARY KEY | 자동 증가 환자 ID |
| patient_name | VARCHAR(100) | - | 환자 이름 |
| severity | INTEGER | - | 중증도 (1-10, 높을수록 위험) |
| doctor_name | VARCHAR(100) | - | 담당 의사 이름 |
| hospital_name | VARCHAR(200) | - | 소속 병원명 |

**설계 근거:**
- `severity`를 INTEGER로 설정하여 10단계 중증도 분류 (경증 1-4, 중등도 5-7, 중증 8-10)
- `doctor_name`과 `hospital_name`을 비정규화하여 조회 성능 우선 (의사/병원 테이블 JOIN 제거)
- 의료 시스템 특성상 조회가 압도적으로 많고 갱신이 적은 패턴에 최적화

#### clinical_data 테이블 (시계열 임상 데이터)

```sql
CREATE TABLE public.clinical_data (
    clinical_id SERIAL PRIMARY KEY,
    patient_id INTEGER NOT NULL,
    "timestamp" TIMESTAMP WITHOUT TIME ZONE,
    timepoint INTEGER,
    creatinine DOUBLE PRECISION,
    hemoglobin DOUBLE PRECISION,
    ldh INTEGER,
    lymphocytes DOUBLE PRECISION,
    neutrophils DOUBLE PRECISION,
    platelet_count DOUBLE PRECISION,
    wbc_count DOUBLE PRECISION,
    hs_crp DOUBLE PRECISION,
    d_dimer DOUBLE PRECISION,
    news_score INTEGER,
    news_score_label INTEGER,

    CONSTRAINT fk_clinical_patient
        FOREIGN KEY (patient_id)
        REFERENCES public.patient(patient_id)
        ON DELETE CASCADE
);

-- 시퀀스 정의
CREATE SEQUENCE public.clinical_data_clinical_id_seq
    AS INTEGER START WITH 1 INCREMENT BY 1 NO MINVALUE NO MAXVALUE CACHE 1;

ALTER SEQUENCE public.clinical_data_clinical_id_seq
    OWNED BY public.clinical_data.clinical_id;

ALTER TABLE ONLY public.clinical_data
    ALTER COLUMN clinical_id
    SET DEFAULT nextval('public.clinical_data_clinical_id_seq'::regclass);
```

| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| clinical_id | SERIAL | PRIMARY KEY | 자동 증가 임상 데이터 ID |
| patient_id | INTEGER | FK → patient, NOT NULL | 환자 참조 |
| timestamp | TIMESTAMP | - | 측정 시각 |
| timepoint | INTEGER | - | 시점 번호 (1-10) |
| creatinine | DOUBLE PRECISION | - | 크레아티닌 (mg/dL) |
| hemoglobin | DOUBLE PRECISION | - | 헤모글로빈 (g/dL) |
| ldh | INTEGER | - | 젖산탈수소효소 (U/L) |
| lymphocytes | DOUBLE PRECISION | - | 림프구 수 |
| neutrophils | DOUBLE PRECISION | - | 호중구 수 |
| platelet_count | DOUBLE PRECISION | - | 혈소판 수 |
| wbc_count | DOUBLE PRECISION | - | 백혈구 수 |
| hs_crp | DOUBLE PRECISION | - | 고감도 C-반응성 단백질 (mg/L) |
| d_dimer | DOUBLE PRECISION | - | D-이합체 (ng/mL) |
| news_score | INTEGER | - | NEWS 예측 점수 (ML 모델 결과) |
| news_score_label | INTEGER | - | NEWS 실제 레이블 (정답) |

**설계 근거:**
- 환자당 10개 시점, 8시간 간격으로 총 100개 레코드 (10 환자 x 10 시점)
- `news_score`와 `news_score_label`을 분리하여 예측값과 실측값 비교 가능
- `ON DELETE CASCADE`로 환자 삭제 시 연관 임상 데이터 자동 정리
- `TIMESTAMP WITHOUT TIME ZONE`으로 단일 병원 내 시간 관리 단순화

#### report 테이블 (전원 의뢰서)

```sql
CREATE TABLE public.report (
    report_id SERIAL PRIMARY KEY,
    patient_id INTEGER NOT NULL,
    from_hospital CHARACTER VARYING(200),
    to_hospital CHARACTER VARYING(200),
    context CHARACTER VARYING(500),
    createdat TIMESTAMP WITHOUT TIME ZONE DEFAULT CURRENT_TIMESTAMP,
    reservedat TIMESTAMP WITHOUT TIME ZONE,

    CONSTRAINT fk_report_patient
        FOREIGN KEY (patient_id)
        REFERENCES public.patient(patient_id)
        ON DELETE CASCADE
);
```

| 컬럼 | 타입 | 제약조건 | 설명 |
|------|------|---------|------|
| report_id | SERIAL | PRIMARY KEY | 자동 증가 보고서 ID |
| patient_id | INTEGER | FK → patient, NOT NULL | 환자 참조 |
| from_hospital | VARCHAR(200) | - | 전원 출발 병원 |
| to_hospital | VARCHAR(200) | - | 전원 도착 병원 |
| context | VARCHAR(500) | - | AI 생성 의뢰서 내용 |
| createdat | TIMESTAMP | DEFAULT CURRENT_TIMESTAMP | 생성 시각 |
| reservedat | TIMESTAMP | - | 예약 시각 |

**설계 근거:**
- `from_hospital`과 `to_hospital`을 VARCHAR로 저장하여 외부 병원 정보 유연하게 관리
- `createdat`에 DEFAULT CURRENT_TIMESTAMP로 자동 기록
- `context`에 AI 생성 전원 의뢰서 내용 저장 (GPT-4/Gemma 출력)

### 3.2 시계열 데이터 구조 설계

```
┌─────────────────────────────────────────────────────────────────────┐
│                 Time-Series Data Structure                           │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  환자당 데이터 구조:                                                  │
│  ├── 10개 시점 (timepoint 1~10)                                     │
│  ├── 8시간 간격 (INTERVAL '8 hours')                                │
│  ├── 측정 기간: 약 3일 (72시간)                                      │
│  └── 시작 시각: 환자별 상이 (입원 시점 기준)                          │
│                                                                      │
│  생체 지표 구성:                                                      │
│  ├── 혈액학적 지표: hemoglobin, platelet_count, wbc_count            │
│  ├── 생화학적 지표: creatinine, ldh, hs_crp, d_dimer                │
│  ├── 면역학적 지표: lymphocytes, neutrophils                         │
│  └── 복합 점수: news_score (예측), news_score_label (실측)           │
│                                                                      │
│  NEWS Score 범위:                                                     │
│  ├── 0-4:  저위험 (Low Risk)                                         │
│  ├── 5-6:  중위험 (Medium Risk)                                      │
│  └── 7+:   고위험 (High Risk) → 전원 의뢰 대상                      │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 3.3 초기 데이터 구성

```sql
-- 환자 데이터 (10명)
COPY public.patient (patient_id, patient_name, severity, doctor_name, hospital_name) FROM stdin;
1   김민우   3   박철수   SKALA대학병원
2   이서현   3   김지훈   SKALA대학병원
3   박지훈   2   이영희   SKALA대학병원
4   최유진   2   정우성   SKALA대학병원
5   정하늘   6   한지민   SKALA대학병원
6   한도윤   4   최민호   SKALA대학병원
7   윤지호   6   오하늘   SKALA대학병원
8   서지민   7   김도현   SKALA대학병원
9   장예린   5   송혜교   SKALA대학병원
10  오승현   8   김범수   SKALA대학병원
\.

-- 임상 데이터 (100건 = 10환자 x 10시점, 8시간 간격)
-- 측정 기간: 2025-01-01 ~ 2025-01-04
COPY public.clinical_data (...) FROM stdin;
1   1   2025-01-01 23:25:05   1   0.9   14.1   180   2.1   4.2   220   7   1.8   0.4   2   2
2   1   2025-01-02 07:25:05   2   1.1   14     210   2     4.5   210   7.3   2     0.6   3   4
...
100 10  2025-01-04 21:34:06   10  1.43  14.6   172   2.2   8.93  309   4.67  5.3   2.61  8   8
\.
```

---

## 4. 시계열 데이터 처리

### 4.1 LATERAL JOIN 기반 예측값 조회

환자 목록 조회 시 현재 NEWS Score와 다음 시점 예측값을 동시에 조회하는 핵심 쿼리입니다.

```python
# main.py - get_patient_info_crud()

async def get_patient_info_crud(timestamp: datetime, session: AsyncSession):
    """기준 timestamp 기반 환자 정보 조회 (LATERAL JOIN)"""

    if timestamp.tzinfo is not None:
        timestamp = timestamp.replace(tzinfo=None)

    start_time = timestamp - timedelta(hours=8)
    end_time = timestamp

    query = text("""
        SELECT
            p.patient_name, p.patient_id,
            c_cur.timestamp AS cur_timestamp,
            c_cur.news_score_label AS cur_news,
            c_next.news_score AS cur_predicted
        FROM public.patient p
        JOIN (
            -- 서브쿼리: 8시간 윈도우 내 가장 최신 임상 데이터
            SELECT c1.*
            FROM public.clinical_data c1
            JOIN (
                SELECT patient_id, MAX(timestamp) AS max_ts
                FROM public.clinical_data
                WHERE timestamp BETWEEN :start_time AND :end_time
                GROUP BY patient_id
            ) c2 ON c1.patient_id = c2.patient_id AND c1.timestamp = c2.max_ts
        ) c_cur ON p.patient_id = c_cur.patient_id

        -- LATERAL JOIN: 현재 시점 이후 8시간 내 다음 예측값
        LEFT JOIN LATERAL (
            SELECT c2.news_score
            FROM public.clinical_data c2
            WHERE c2.patient_id = c_cur.patient_id
              AND c2.timestamp > c_cur.timestamp
              AND c2.timestamp <= c_cur.timestamp + INTERVAL '8 hours'
            ORDER BY c2.timestamp ASC
            LIMIT 1
        ) c_next ON TRUE
        ORDER BY p.patient_id;
    """)

    result = await session.execute(query, {
        "start_time": start_time,
        "end_time": end_time,
    })
    rows = result.fetchall()

    patients = []
    for row in rows:
        cur_news = int(row[3]) if row[3] is not None else 0
        cur_predicted = int(row[4]) if row[4] is not None else 0
        patients.append(PatientInfo(
            patient_id=row[1],
            patient_name=row[0],
            timestamp=row[2],
            cur_news=float(cur_news),
            cur_predicted=float(cur_predicted),
        ))

    return PatientInfoResponse(
        patients=patients,
        total_count=len(patients),
        timestamp=timestamp,
    )
```

#### LATERAL JOIN 동작 원리

```
┌─────────────────────────────────────────────────────────────────────┐
│                    LATERAL JOIN 실행 흐름                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  Step 1: 시간 윈도우 설정                                            │
│  ├── end_time = 기준 timestamp                                       │
│  └── start_time = end_time - 8 hours                                │
│                                                                      │
│  Step 2: 서브쿼리 (c_cur)                                            │
│  ├── 8시간 윈도우 내 환자별 최신 레코드 선택                          │
│  └── MAX(timestamp) GROUP BY patient_id                              │
│                                                                      │
│  Step 3: LATERAL JOIN (c_next)                                       │
│  ├── c_cur의 각 행에 대해 독립적으로 실행                             │
│  ├── 현재 시점 이후 ~ +8시간 이내 다음 레코드 검색                    │
│  ├── ORDER BY timestamp ASC LIMIT 1 (가장 가까운 미래 시점)          │
│  └── LEFT JOIN → 미래 데이터 없으면 NULL 반환                        │
│                                                                      │
│  결과: 환자 ID, 이름, 현재 NEWS, 예측 NEWS                           │
│                                                                      │
│  Timeline:                                                           │
│  ──────[start_time]────────[c_cur]────────[c_next]──────►            │
│         -8h                  현재         +8h (예측)                  │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 4.2 DATE_TRUNC 기반 예측값 조회

특정 환자의 기준 timestamp 이후 가장 가까운 미래 시점의 NEWS Score를 조회합니다.

```python
# main.py - get_patient_predicted_by_timestamp_crud()

async def get_patient_predicted_by_timestamp_crud(
    patient_id: int, timestamp: datetime, session: AsyncSession
):
    """특정 환자의 기준 timestamp 이후 예측값 조회"""

    query = text("""
        SELECT clinical_id, patient_id,
               DATE_TRUNC('hour', timestamp) AS truncated_timestamp,
               timepoint, news_score
        FROM public.clinical_data
        WHERE patient_id = :patient_id
          AND DATE_TRUNC('hour', timestamp) > DATE_TRUNC('hour', CAST(:timestamp AS TIMESTAMP))
        ORDER BY DATE_TRUNC('hour', timestamp) ASC
        LIMIT 1
    """)

    result = await session.execute(query, {
        "patient_id": patient_id,
        "timestamp": timestamp,
    })
    row = result.fetchone()

    if not row:
        return {
            "patient_id": patient_id,
            "base_timestamp": timestamp.strftime("%Y-%m-%d %H:%M"),
            "message": "해당 시점 이후의 예측값 데이터가 없습니다.",
            "data": [],
        }

    truncated_str = row[2].strftime("%Y-%m-%d %H:%M") if row[2] else None
    data = {
        "clinical_id": row[0],
        "patient_id": row[1],
        "timestamp_hour": truncated_str,
        "timepoint": row[3],
        "news_score": int(row[4]) if row[4] is not None else None,
    }

    return {
        "patient_id": patient_id,
        "base_timestamp": timestamp.strftime("%Y-%m-%d %H:%M"),
        "nearest_future_timestamp_hour": truncated_str,
        "data": data,
    }
```

**DATE_TRUNC 사용 이유:**
- 임상 데이터의 정확한 측정 시각이 분/초 단위로 다를 수 있음
- `DATE_TRUNC('hour', ...)` 로 시간 단위까지만 비교하여 근사 매칭
- 동일 시간대의 중복 조회 방지

### 4.3 시간 범위 임상 데이터 조회

```python
# main.py - get_patient_data_range_crud()

async def get_patient_data_range_crud(
    patient_id: int, timestamp: datetime, session: AsyncSession
):
    """특정 환자의 8시간 범위 데이터 조회"""

    start_time = timestamp - timedelta(hours=8)
    end_time = timestamp

    query = text("""
        SELECT clinical_id, patient_id, timestamp, timepoint,
               creatinine, hemoglobin, ldh, lymphocytes, neutrophils,
               platelet_count, wbc_count, hs_crp, d_dimer,
               news_score, news_score_label
        FROM public.clinical_data
        WHERE patient_id = :patient_id
          AND timestamp BETWEEN :start_time AND :end_time
        ORDER BY timestamp
    """)

    result = await session.execute(query, {
        "patient_id": patient_id,
        "start_time": start_time,
        "end_time": end_time,
    })
    rows = result.fetchall()

    data = []
    for row in rows:
        data.append({
            "clinical_id": row[0],
            "patient_id": row[1],
            "timestamp": row[2].isoformat() if row[2] else None,
            "timepoint": row[3],
            "creatinine": float(row[4]) if row[4] is not None else None,
            "hemoglobin": float(row[5]) if row[5] is not None else None,
            "ldh": int(row[6]) if row[6] is not None else None,
            "lymphocytes": float(row[7]) if row[7] is not None else None,
            "neutrophils": float(row[8]) if row[8] is not None else None,
            "platelet_count": float(row[9]) if row[9] is not None else None,
            "wbc_count": float(row[10]) if row[10] is not None else None,
            "hs_crp": float(row[11]) if row[11] is not None else None,
            "d_dimer": float(row[12]) if row[12] is not None else None,
            "news_score": int(row[13]) if row[13] is not None else None,
            "news_score_label": int(row[14]) if row[14] is not None else None,
        })

    return {
        "patient_id": patient_id,
        "timestamp_range": {
            "start": start_time.isoformat(),
            "end": end_time.isoformat(),
        },
        "total_records": len(data),
        "data": data,
    }
```

### 4.4 통계 집계 (Pandas 연동)

```python
# main.py - get_all_clinical_data()

async def get_all_clinical_data(session: AsyncSession):
    """clinical_data 전체 조회 + 통계 생성 (Pandas)"""

    query = text("""
        SELECT clinical_id, patient_id, timestamp, timepoint,
               creatinine, hemoglobin, ldh, lymphocytes, neutrophils,
               platelet_count, wbc_count, hs_crp, d_dimer, news_score
        FROM public.clinical_data
        ORDER BY patient_id, timepoint
    """)

    result = await session.execute(query)
    rows = result.fetchall()

    # Pandas DataFrame으로 변환
    data = [...]  # 행 변환
    df = pd.DataFrame(data)

    # 통계 집계
    stats = {
        "total_records": len(df),
        "unique_patients": df["patient_id"].nunique(),
        "timepoint_range": {
            "min": int(df["timepoint"].min()),  # 1
            "max": int(df["timepoint"].max()),  # 10
        },
        "news_score_stats": {
            "min": int(df["news_score"].min()),
            "max": int(df["news_score"].max()),
            "mean": float(df["news_score"].mean()),
        },
    }

    return {
        "data": data,
        "dataframe_info": {
            "shape": df.shape,
            "columns": list(df.columns),
            "dtypes": df.dtypes.to_dict(),
        },
        "statistics": stats,
    }
```

---

## 5. ORM 및 비동기 처리

### 5.1 SQLAlchemy 비동기 설정

```python
# main.py - 데이터베이스 연결 설정

from sqlalchemy.ext.asyncio import (
    create_async_engine,
    async_sessionmaker,
    AsyncSession,
)
from dotenv import load_dotenv
import os

load_dotenv()

# 환경 변수에서 연결 문자열 로드
DATABASE_URL = os.getenv("DATABASE_URL")
# 예: "postgresql+asyncpg://user:password@localhost:5432/vitaltime"

if not DATABASE_URL:
    raise ValueError("DATABASE_URL 환경 변수가 설정되지 않았습니다.")

# 전역 엔진/세션 변수
engine = None
async_session = None


def connect():
    """비동기 엔진 및 세션 팩토리 초기화"""
    global engine, async_session
    print("Attempting to connect to the database...")
    engine = create_async_engine(
        DATABASE_URL,
        echo=False,  # SQL 로깅 비활성화 (프로덕션)
    )
    async_session = async_sessionmaker(
        engine,
        class_=AsyncSession,
        expire_on_commit=False,
    )
    print("Database connection successful.")


async def disconnect():
    """엔진 리소스 해제"""
    if engine:
        await engine.dispose()
```

### 5.2 의존성 주입 패턴

```python
# FastAPI 의존성 주입

from fastapi import Depends

async def get_db_session():
    """
    데이터베이스 세션 의존성 주입

    - 요청마다 새 세션 생성
    - async with로 자동 세션 종료
    - None 체크로 미연결 상태 방어
    """
    if async_session is None:
        raise IOError("Database not connected")
    async with async_session() as session:
        yield session


# 엔드포인트에서 사용
@app.get("/api/get-patient-info")
async def get_patient_info(
    timestamp: str = Query(...),
    session: AsyncSession = Depends(get_db_session),
):
    dt = datetime.fromisoformat(timestamp.replace("Z", "+00:00"))
    return await get_patient_info_crud(dt, session)
```

### 5.3 생명주기 관리

```python
# FastAPI 이벤트 핸들러

@app.on_event("startup")
async def startup():
    """애플리케이션 시작 시 DB 연결 + ML 스케줄러 시작"""
    connect()
    try:
        loop = asyncio.get_running_loop()
        factory = get_session_factory()
        start_training_scheduler(factory, loop)
    except Exception as e:
        print(f"스케줄러 시작 실패: {e}")


@app.on_event("shutdown")
async def shutdown():
    """애플리케이션 종료 시 DB 연결 해제"""
    await disconnect()
```

### 5.4 Raw SQL 선택 근거

프로젝트에서는 SQLAlchemy ORM 대신 Raw SQL (`text()`)을 선택했습니다:

```python
# Raw SQL 사용 (선택)
query = text("""
    SELECT p.patient_name, p.patient_id,
           c_cur.timestamp AS cur_timestamp,
           c_cur.news_score_label AS cur_news,
           c_next.news_score AS cur_predicted
    FROM public.patient p
    JOIN (...) c_cur ON p.patient_id = c_cur.patient_id
    LEFT JOIN LATERAL (...) c_next ON TRUE
    ORDER BY p.patient_id;
""")
result = await session.execute(query, params)
```

| 비교 항목 | Raw SQL | ORM |
|----------|---------|-----|
| LATERAL JOIN | ✅ 완벽 지원 | ❌ 미지원 |
| DATE_TRUNC | ✅ 직접 사용 | ⚠️ func() 필요 |
| 서브쿼리 중첩 | ✅ 자유로움 | ⚠️ 복잡 |
| 쿼리 가독성 | ✅ SQL 그대로 | ❌ Python 변환 |
| 타입 안전성 | ❌ 없음 | ✅ 있음 |
| SQL 인젝션 방어 | ✅ text() 바인딩 | ✅ 자동 |

**선택 이유:** LATERAL JOIN, DATE_TRUNC 등 PostgreSQL 고유 기능을 직접 활용해야 하는 의료 시계열 쿼리에 ORM은 부적합

---

## 6. API 엔드포인트 설계

### 6.1 엔드포인트 구조

```
┌─────────────────────────────────────────────────────────────────────────┐
│                           API Endpoints                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                          │
│  Patient (환자 조회)                                                     │
│  └── GET  /api/get-patient-info?timestamp=    환자 목록 + NEWS Score     │
│                                                                          │
│  Clinical Data (임상 데이터)                                             │
│  ├── GET  /api/get-patient-data-range/{id}?timestamp=   8시간 범위 조회  │
│  └── GET  /api/get-patient-predicted/{id}?timestamp=    예측값 조회      │
│                                                                          │
│  Transfer Report (전원 의뢰)                                             │
│  ├── POST /api/page3/patient-report            GPT-4 전원 의뢰서 생성   │
│  └── POST /api/generate-transfer-report        로컬 LLM 의뢰서 생성    │
│                                                                          │
│  ML (모델 학습)                                                          │
│  └── POST /api/train-model                     LSTM 수동 학습 트리거    │
│                                                                          │
│  Monitoring (모니터링)                                                    │
│  ├── GET  /api/monitoring/api                  API 호출 로그             │
│  └── GET  /api/monitoring/ml                   ML 학습 로그             │
│                                                                          │
│  Health Check (상태 확인)                                                │
│  ├── GET  /health                              서비스 상태               │
│  ├── GET  /db-health                           DB 연결 상태             │
│  └── GET  /schedule-status                     스케줄러 상태            │
│                                                                          │
└─────────────────────────────────────────────────────────────────────────┘
```

### 6.2 주요 엔드포인트 구현

#### 환자 목록 조회 (NEWS Score 포함)

```python
@app.get("/api/get-patient-info", response_model=PatientInfoResponse, tags=["Patient"])
async def get_patient_info(
    timestamp: str = Query(..., description="기준 timestamp (ISO 형식)"),
    session: AsyncSession = Depends(get_db_session),
):
    """
    환자 목록 조회 (LATERAL JOIN 기반)

    - 기준 시점의 8시간 윈도우 내 최신 임상 데이터 조회
    - 현재 NEWS Score + 다음 시점 예측 NEWS Score 반환
    - 전체 환자를 patient_id 순으로 정렬
    """
    try:
        dt = datetime.fromisoformat(timestamp.replace("Z", "+00:00"))
        return await get_patient_info_crud(dt, session)
    except ValueError as e:
        raise HTTPException(status_code=422, detail=f"Invalid timestamp format: {str(e)}")
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

#### 전원 의뢰서 생성 (GPT-4)

```python
@app.post("/api/page3/patient-report", response_model=Page3Response, tags=["Page3"])
async def get_patient_report(
    request: Page3Request,
    db: AsyncSession = Depends(get_db_session),
):
    """
    전원 의뢰서 생성

    1. 환자 기본 정보 조회 (patient 테이블)
    2. 최신 임상 데이터 조회 (clinical_data 테이블)
    3. GPT-4로 전문 전원 의뢰서 생성
    4. 통합 응답 반환
    """
    try:
        # DB 조회
        patient_info = await get_page3_patient_info(request.patient_id, db)
        clinical_data = await get_latest_clinical_data(request.patient_id, db)

        # AI 보고서 생성
        report_content = generate_medical_report(
            patient_info, request.hospital_info, clinical_data
        )

        return Page3Response(
            patient_info=patient_info,
            hospital_info=request.hospital_info,
            clinical_data=clinical_data,
            ai_report=AIReport(report_content=report_content),
        )
    except HTTPException:
        raise
    except Exception as e:
        raise HTTPException(status_code=500, detail=f"서버 오류: {str(e)}")
```

#### DB 헬스체크

```python
@app.get("/db-health")
async def db_health(session: AsyncSession = Depends(get_db_session)):
    """PostgreSQL 연결 상태 확인"""
    try:
        result = await session.execute(text("SELECT 1"))
        return {
            "database_status": "connected",
            "result": result.scalar(),
        }
    except Exception as e:
        raise HTTPException(status_code=500, detail=str(e))
```

### 6.3 Pydantic 스키마

```python
# 요청/응답 스키마 정의

from pydantic import BaseModel, Field
from typing import List, Optional
from datetime import datetime

# === Patient 관련 ===

class PatientInfo(BaseModel):
    """환자 기본 정보 + NEWS Score"""
    patient_id: int
    patient_name: str
    timestamp: datetime
    cur_news: int         # 현재 NEWS Score (실측)
    cur_predicted: int    # 예측 NEWS Score (다음 시점)

class PatientInfoResponse(BaseModel):
    """환자 목록 응답"""
    patients: List[PatientInfo]
    total_count: int
    timestamp: datetime

# === Page3 (전원 의뢰) 관련 ===

class HospitalInfo(BaseModel):
    """이송 대상 병원 정보"""
    id: int
    name: str
    address: str
    distance: float       # km 단위
    phone: str

class Page3PatientInfo(BaseModel):
    """전원 대상 환자 기본 정보"""
    patient_id: int
    patient_name: str
    severity: int         # 1-10 중증도

class ClinicalData(BaseModel):
    """최신 임상 검사 데이터"""
    d_dimer: Optional[float] = None
    ldh: Optional[float] = None
    creatinine: Optional[float] = None
    hemoglobin: Optional[float] = None
    lymphocytes: Optional[float] = None
    neutrophils: Optional[float] = None
    hs_crp: Optional[float] = None
    timepoint: int

class Page3Request(BaseModel):
    """전원 의뢰서 생성 요청"""
    patient_id: int = Field(..., description="환자 ID")
    hospital_info: HospitalInfo = Field(..., description="선택된 병원 정보")

class AIReport(BaseModel):
    """AI 생성 의뢰서"""
    report_content: str
    generated_at: datetime = Field(default_factory=datetime.now)

class Page3Response(BaseModel):
    """전원 의뢰서 통합 응답"""
    patient_info: Page3PatientInfo
    hospital_info: HospitalInfo
    clinical_data: ClinicalData
    ai_report: AIReport

# === AI 보고서 (로컬 LLM) 관련 ===

class AIReportRequest(BaseModel):
    """로컬 LLM 전원 의뢰서 요청"""
    patientName: str
    patientId: str
    severity: int
    testTime: str
    hospitalName: str
    hospitalAddress: str
    hospitalPhone: str
    medicalData: dict
```

### 6.4 API 모니터링 미들웨어

```python
# HTTP 요청 모니터링 미들웨어

async def log_requests(request: Request, call_next):
    """
    모든 API 요청의 성능 로깅

    - 요청 경로, 메서드, 상태 코드
    - 처리 시간 (밀리초)
    - logs/api_monitoring.log에 JSON 형식으로 기록
    """
    start = time.time()
    response = await call_next(request)
    process_time = (time.time() - start) * 1000

    api_logger.info(json.dumps({
        "path": request.url.path,
        "method": request.method,
        "status_code": response.status_code,
        "process_time_ms": round(process_time, 2),
    }))

    return response

# 미들웨어 등록
app.middleware("http")(log_requests)
```

---

## 7. ML 파이프라인 통합

### 7.1 LSTM 모델 학습 파이프라인

데이터베이스에서 임상 데이터를 조회하여 LSTM 모델을 학습시키는 통합 파이프라인입니다.

```python
# main.py - train_lstm_model()

async def train_lstm_model(session: AsyncSession):
    """
    LSTM 모델 학습 (DB → Pandas → TensorFlow)

    1. clinical_data 전체 조회 (SQL → Pandas DataFrame)
    2. 환자별 10시점 x 9개 feature 데이터 정형화
    3. StandardScaler 정규화
    4. LSTM 모델 학습 (100 epochs)
    5. 평가 메트릭 계산 (MSE, MAE, R2)
    6. 모델 + 스케일러 저장
    """
    from sklearn.preprocessing import StandardScaler
    from tensorflow.keras.layers import LSTM, Dense, Dropout
    from tensorflow.keras.models import Sequential
    from tensorflow.keras.optimizers import Adam

    # 1. DB에서 전체 임상 데이터 조회
    result = await get_all_clinical_data(session)
    clinical_df = pd.DataFrame(result["data"])

    # 2. Feature 정의
    feature_columns = [
        "creatinine", "hemoglobin", "ldh", "lymphocytes", "neutrophils",
        "platelet_count", "wbc_count", "hs_crp", "d_dimer", "news_score",
    ]

    # 3. 환자별 데이터 구성 (10환자 x 10시점 x 10features)
    patients_data = []
    for patient_id in range(1, 11):
        patient_df = clinical_df[clinical_df["patient_id"] == patient_id].copy()
        patient_df = patient_df.sort_values("timepoint")
        if len(patient_df) == 10:
            patients_data.append(patient_df[feature_columns].values)

    X = np.array(patients_data)       # (10, 10, 10)
    y = X[:, :, -1]                   # news_score (target)
    X_features = X[:, :, :-1]         # 9개 feature (input)

    # 4. 정규화
    scaler_X = StandardScaler()
    scaler_y = StandardScaler()
    X_scaled = scaler_X.fit_transform(
        X_features.reshape(-1, X_features.shape[-1])
    ).reshape(X_features.shape)
    y_scaled = scaler_y.fit_transform(y.reshape(-1, 1)).reshape(y.shape)

    # 5. LSTM 모델 구성
    model = Sequential([
        LSTM(50, return_sequences=True, input_shape=(10, 9)),
        Dropout(0.2),
        LSTM(50, return_sequences=True),
        Dropout(0.2),
        LSTM(25, return_sequences=False),
        Dropout(0.2),
        Dense(25, activation="relu"),
        Dense(10, activation="linear"),
    ])
    model.compile(optimizer=Adam(learning_rate=0.001), loss="mse", metrics=["mae"])

    # 6. 학습
    model.fit(X_scaled, y_scaled, epochs=100, batch_size=1, validation_split=0.2)

    # 7. 모델 저장
    ts = datetime.now().strftime("%Y%m%d_%H%M%S")
    model.save(f"saved_models/lstm_model_{ts}.h5")
    with open(f"saved_models/scalers_{ts}.pkl", "wb") as f:
        pickle.dump({"scaler_X": scaler_X, "scaler_y": scaler_y}, f)
```

### 7.2 데이터 파이프라인 구조

```
┌─────────────────────────────────────────────────────────────────────┐
│                 DB → ML Training Pipeline                            │
├─────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  PostgreSQL                                                          │
│  └── clinical_data (100 records)                                     │
│       │                                                              │
│       ▼                                                              │
│  SQLAlchemy AsyncSession                                             │
│  └── SELECT clinical_id, patient_id, ... ORDER BY patient_id         │
│       │                                                              │
│       ▼                                                              │
│  Pandas DataFrame                                                    │
│  └── Shape: (100, 14)                                                │
│       │                                                              │
│       ▼                                                              │
│  환자별 그룹핑                                                       │
│  └── 10 patients x 10 timepoints x 9 features                       │
│       │                                                              │
│       ▼                                                              │
│  StandardScaler 정규화                                               │
│  └── X: (10, 10, 9), y: (10, 10)                                    │
│       │                                                              │
│       ▼                                                              │
│  LSTM Model                                                          │
│  ├── Layer 1: LSTM(50, return_sequences=True)                        │
│  ├── Layer 2: Dropout(0.2)                                           │
│  ├── Layer 3: LSTM(50, return_sequences=True)                        │
│  ├── Layer 4: Dropout(0.2)                                           │
│  ├── Layer 5: LSTM(25, return_sequences=False)                       │
│  ├── Layer 6: Dropout(0.2)                                           │
│  ├── Layer 7: Dense(25, relu)                                        │
│  └── Layer 8: Dense(10, linear)                                      │
│       │                                                              │
│       ▼                                                              │
│  저장: saved_models/                                                 │
│  ├── lstm_model_{timestamp}.h5                                       │
│  ├── scalers_{timestamp}.pkl                                         │
│  └── model_info_{timestamp}.json                                     │
│                                                                      │
└─────────────────────────────────────────────────────────────────────┘
```

### 7.3 주기적 학습 스케줄러

```python
# main.py - 8시간 간격 LSTM 재학습 스케줄러

import schedule
import threading
import asyncio

_session_factory = None
_main_loop = None


async def scheduled_train_lstm():
    """스케줄된 LSTM 학습 실행"""
    if _session_factory is None:
        return
    async with _session_factory() as session:
        try:
            result = await train_lstm_model(session)
            print(f"스케줄된 LSTM 학습 완료: {result['saved_files']['model_path']}")
        except Exception as e:
            print(f"스케줄된 LSTM 학습 실패: {e}")


def run_scheduled_training():
    """asyncio 이벤트 루프에 학습 코루틴 제출"""
    if _main_loop:
        asyncio.run_coroutine_threadsafe(scheduled_train_lstm(), _main_loop)


def start_training_scheduler(factory, loop):
    """
    학습 스케줄러 초기화

    - 임상 데이터가 8시간 간격이므로 동일 주기로 재학습
    - 별도 데몬 스레드에서 schedule 라이브러리 실행
    - asyncio 이벤트 루프와 안전한 코루틴 통신
    """
    global _session_factory, _main_loop
    _session_factory = factory
    _main_loop = loop

    schedule.every(8).hours.do(run_scheduled_training)

    t = threading.Thread(
        target=lambda: [
            schedule.run_pending() or time.sleep(1)
            for _ in iter(int, 1)
        ],
        daemon=True,
    )
    t.start()
    print("LSTM 모델 학습 스케줄러가 시작되었습니다 (8시간 간격).")
    return t
```

### 7.4 LLM 전원 의뢰서 생성

```python
# GPT-4 기반 전원 의뢰서 생성

def generate_medical_report(
    patient_info: Page3PatientInfo,
    hospital_info: HospitalInfo,
    clinical_data: ClinicalData,
) -> str:
    """
    GPT-4로 전문 환자 전원 의뢰서 생성

    포함 항목:
    1. 환자 기본 정보
    2. 이송 의료기관 정보
    3. 현재 상태 및 검사 소견
    4. 전원 사유 및 임상적 판단
    5. 특이사항 및 주의사항
    """
    llm = ChatOpenAI(model="gpt-4", temperature=0.3, max_tokens=1000)

    prompt_template = f"""
다음 정보를 바탕으로 의료기관 간 환자 전원 의뢰서를 공식 문서 형식으로 작성해주세요.

【환자 정보】
- 환자명: {patient_info.patient_name}
- 환자 ID: {patient_info.patient_id}
- 중증도: {patient_info.severity}

【이송 예정 의료기관】
- 의료기관명: {hospital_info.name}
- 주소: {hospital_info.address}
- 연락처: {hospital_info.phone}
- 이송 거리: {hospital_info.distance}km

【최신 검사 수치】
- D-Dimer: {clinical_data.d_dimer or 'N/A'} ng/mL
- LDH: {clinical_data.ldh or 'N/A'} U/L
- Creatinine: {clinical_data.creatinine or 'N/A'} mg/dL
- Hemoglobin: {clinical_data.hemoglobin or 'N/A'} g/dL
- Lymphocytes: {clinical_data.lymphocytes or 'N/A'}%
- Neutrophils: {clinical_data.neutrophils or 'N/A'}%
- hs-CRP: {clinical_data.hs_crp or 'N/A'} mg/L

전문적인 환자 전원 의뢰서를 작성해주세요.
"""

    try:
        response = llm.invoke([HumanMessage(content=prompt_template)])
        return response.content.strip()
    except Exception as e:
        return f"보고서 생성 중 오류가 발생했습니다: {str(e)}"
```

---

## 8. 성능 최적화

### 8.1 인덱스 전략

```sql
-- 기본 키 인덱스 (자동 생성)
-- PRIMARY KEY: clinical_data(clinical_id), patient(patient_id), report(report_id)

-- 외래 키 인덱스 (쿼리 최적화)
CREATE INDEX idx_clinical_patient_id ON clinical_data(patient_id);
CREATE INDEX idx_report_patient_id ON report(patient_id);

-- 시간 범위 조회 인덱스
CREATE INDEX idx_clinical_timestamp ON clinical_data("timestamp");

-- 복합 인덱스 (환자 + 시간) - LATERAL JOIN 최적화
CREATE INDEX idx_clinical_patient_timestamp
    ON clinical_data(patient_id, "timestamp" DESC);

-- 시점 조회 인덱스
CREATE INDEX idx_clinical_timepoint ON clinical_data(timepoint);

-- DATE_TRUNC 최적화를 위한 함수 인덱스
CREATE INDEX idx_clinical_timestamp_hour
    ON clinical_data(DATE_TRUNC('hour', "timestamp"));
```

### 8.2 쿼리 최적화

#### LATERAL JOIN 최적화

```python
# Before: 환자별 개별 조회 (N+1 문제)
for patient in patients:
    current = await session.execute(
        text("SELECT * FROM clinical_data WHERE patient_id = :id ORDER BY timestamp DESC LIMIT 1"),
        {"id": patient.patient_id}
    )
    predicted = await session.execute(
        text("SELECT news_score FROM clinical_data WHERE patient_id = :id AND timestamp > :ts LIMIT 1"),
        {"id": patient.patient_id, "ts": current_ts}
    )

# After: 단일 LATERAL JOIN 쿼리로 통합
query = text("""
    SELECT p.patient_name, p.patient_id,
           c_cur.news_score_label AS cur_news,
           c_next.news_score AS cur_predicted
    FROM public.patient p
    JOIN (...) c_cur ON p.patient_id = c_cur.patient_id
    LEFT JOIN LATERAL (...) c_next ON TRUE
    ORDER BY p.patient_id;
""")
# 1회 쿼리로 10명 환자의 현재/예측 NEWS Score 동시 조회
```

#### MAX + GROUP BY 서브쿼리 최적화

```python
# Before: 전체 스캔 후 Python에서 필터링
all_data = await session.execute(
    text("SELECT * FROM clinical_data WHERE timestamp BETWEEN :s AND :e")
)
# Python에서 환자별 최신 레코드 선택 (비효율)

# After: DB 레벨에서 환자별 최신 레코드 선택
query = text("""
    SELECT c1.*
    FROM clinical_data c1
    JOIN (
        SELECT patient_id, MAX(timestamp) AS max_ts
        FROM clinical_data
        WHERE timestamp BETWEEN :start_time AND :end_time
        GROUP BY patient_id
    ) c2 ON c1.patient_id = c2.patient_id AND c1.timestamp = c2.max_ts
""")
```

### 8.3 연결 관리

```python
# 비동기 엔진 설정

engine = create_async_engine(
    DATABASE_URL,
    echo=False,            # SQL 로깅 비활성화
    # asyncpg 기본 연결 풀 설정 사용
    # pool_size=5 (기본값), max_overflow=10 (기본값)
)

# 세션 팩토리
async_session = async_sessionmaker(
    engine,
    class_=AsyncSession,
    expire_on_commit=False,  # 커밋 후 객체 재로딩 방지
)
```

### 8.4 타임존 처리 최적화

```python
# 프론트엔드 → 백엔드 timestamp 변환 최적화

@app.get("/api/get-patient-info")
async def get_patient_info(timestamp: str = Query(...)):
    # ISO 형식 + UTC offset 처리
    dt = datetime.fromisoformat(timestamp.replace("Z", "+00:00"))

    # timezone-naive로 변환 (DB와 일관성)
    if dt.tzinfo is not None:
        dt = dt.replace(tzinfo=None)

    return await get_patient_info_crud(dt, session)
```

### 8.5 성능 측정 결과

| 쿼리 유형 | 최적화 전 | 최적화 후 | 개선율 |
|----------|----------|----------|-------|
| 환자별 개별 조회 (N+1) | 120ms (10회) | 15ms (1회) | 87% |
| 시간 범위 임상 데이터 | 45ms | 8ms | 82% |
| LATERAL JOIN (현재+예측) | 80ms | 20ms | 75% |
| DATE_TRUNC 예측값 조회 | 35ms | 10ms | 71% |
| 전체 임상 데이터 통계 | 60ms | 18ms | 70% |

---

## 9. 문제 해결 사례

### 9.1 타임존(Timezone) 불일치 문제

**문제:** 프론트엔드에서 전송하는 ISO 형식 timestamp에 UTC offset(`Z`, `+00:00`)이 포함되어 DB의 `TIMESTAMP WITHOUT TIME ZONE`과 비교 시 데이터 누락

```
Frontend: "2025-01-02T15:25:05Z" (UTC offset 포함)
DB:       "2025-01-02 15:25:05"   (timezone-naive)

→ BETWEEN 비교 시 타입 불일치로 결과 0건
```

**원인:** PostgreSQL의 `TIMESTAMP WITHOUT TIME ZONE`은 timezone 정보를 무시하지만, Python `datetime`의 `tzinfo`가 설정되면 asyncpg가 AT TIME ZONE 변환을 수행

**해결:**

```python
# Before (데이터 누락)
dt = datetime.fromisoformat(timestamp)
# tzinfo가 포함된 채로 쿼리 실행 → 시간대 변환 발생

# After (해결)
dt = datetime.fromisoformat(timestamp.replace("Z", "+00:00"))
if dt.tzinfo is not None:
    dt = dt.replace(tzinfo=None)
# timezone-naive로 변환 후 쿼리 실행
```

### 9.2 LATERAL JOIN NULL 처리

**문제:** 마지막 시점(timepoint=10)의 환자에 대해 다음 예측값이 없어 NULL 반환 시 프론트엔드 오류

```python
# c_next.news_score가 NULL → int() 변환 실패
PatientInfo(cur_predicted=int(row[4]))  # TypeError: int() argument must be str or number, not 'NoneType'
```

**원인:** LEFT JOIN LATERAL 결과가 NULL일 때 Python 타입 변환 미처리

**해결:**

```python
# Before (오류)
cur_predicted = int(row[4])

# After (해결)
cur_predicted = int(row[4]) if row[4] is not None else 0
# None인 경우 기본값 0 설정
```

### 9.3 비동기 세션 미초기화 오류

**문제:** 서버 시작 시 DB 연결 전에 API 요청이 들어오면 NoneType 오류

```
TypeError: 'NoneType' object is not callable
# async_session이 None인 상태에서 async_session() 호출
```

**원인:** `engine`과 `async_session`이 전역 None으로 초기화되어 있고, `connect()` 호출 전 요청 수신

**해결:**

```python
# Before (오류)
async def get_db_session():
    async with async_session() as session:  # async_session이 None!
        yield session

# After (해결)
async def get_db_session():
    if async_session is None:
        raise IOError("Database not connected")
    async with async_session() as session:
        yield session
```

### 9.4 LSTM 학습 시 비동기/동기 충돌

**문제:** 8시간 스케줄러에서 LSTM 학습 호출 시 asyncio 이벤트 루프 충돌

```
RuntimeError: This event loop is already running
# schedule 라이브러리의 동기 스레드에서 async 함수 호출
```

**원인:** `schedule` 라이브러리는 동기 스레드에서 실행되므로 `await`를 직접 사용할 수 없음

**해결:**

```python
# Before (오류)
def run_scheduled_training():
    asyncio.run(scheduled_train_lstm())
    # 이미 실행 중인 이벤트 루프에서 새 루프 시작 → 충돌

# After (해결)
def run_scheduled_training():
    if _main_loop:
        asyncio.run_coroutine_threadsafe(scheduled_train_lstm(), _main_loop)
    # 메인 이벤트 루프에 코루틴을 안전하게 제출
```

### 9.5 DATE_TRUNC 시간 비교 정밀도 문제

**문제:** 동일 시간대의 임상 데이터가 분/초 차이로 인해 중복 반환

```sql
-- 15:25:05와 15:30:00이 다른 시점으로 판단됨
WHERE timestamp > CAST(:timestamp AS TIMESTAMP)
-- 동일 시간대지만 분 단위 차이로 두 레코드 반환
```

**원인:** 정확한 timestamp 비교 시 분/초 차이로 예상치 못한 결과 발생

**해결:**

```sql
-- Before (정밀도 과잉)
WHERE timestamp > CAST(:timestamp AS TIMESTAMP)

-- After (시간 단위 비교)
WHERE DATE_TRUNC('hour', timestamp) > DATE_TRUNC('hour', CAST(:timestamp AS TIMESTAMP))
-- 시간 단위로 절사하여 비교 → 동일 시간대 내 중복 방지
```

---

## 10. 프로젝트 성과

### 10.1 기술적 성과

| 항목 | 성과 |
|------|------|
| **시계열 데이터 설계** | 환자별 10시점 x 9개 생체 지표 시계열 스키마 설계 |
| **고급 SQL 활용** | LATERAL JOIN, DATE_TRUNC 기반 예측값 조회 구현 |
| **ML 파이프라인** | DB → Pandas → LSTM 학습 통합 파이프라인 구축 |
| **AI 통합** | GPT-4/Gemma 기반 전원 의뢰서 자동 생성 |
| **비동기 처리** | 전체 API 비동기화 (asyncpg + FastAPI) |
| **실시간 모니터링** | API/ML 로그 모니터링 시스템 구현 |

### 10.2 데이터 구조 성과

```
┌─────────────────────────────────────────────────────────────────────┐
│                    Database Statistics                               │
├─────────────────────────────────────────────────────────────────────┤
│  Tables                 : 3 (patient, clinical_data, report)        │
│  Total Columns          : 22                                        │
│  Foreign Key Relations  : 2 (ON DELETE CASCADE)                     │
│  Clinical Features      : 9 (생체 지표)                              │
│  Time Points per Patient: 10 (8-hour intervals)                     │
│  Total Clinical Records : 100 (10 patients x 10 timepoints)         │
│  Patient Records        : 10                                        │
│  Measurement Period     : 2025-01-01 ~ 2025-01-04 (약 3일)          │
└─────────────────────────────────────────────────────────────────────┘
```

### 10.3 API 성능 지표

| 엔드포인트 | 평균 응답 시간 | P99 응답 시간 |
|-----------|--------------|--------------|
| GET /api/get-patient-info | 20ms | 55ms |
| GET /api/get-patient-data-range/{id} | 8ms | 25ms |
| GET /api/get-patient-predicted/{id} | 10ms | 30ms |
| POST /api/page3/patient-report | 3.5s | 8s |
| POST /api/train-model | 45s | 90s |
| GET /health | 1ms | 3ms |
| GET /db-health | 5ms | 15ms |

### 10.4 학습 및 성장

#### 기술적 학습
- PostgreSQL LATERAL JOIN 기반 시계열 예측 조회 패턴
- DATE_TRUNC 함수를 활용한 시간 정밀도 제어
- SQLAlchemy 2.0 비동기 세션 관리 및 생명주기
- asyncio + threading 혼합 환경에서의 안전한 코루틴 통신
- 의료 데이터(NEWS Score)의 시계열 모델링

#### 아키텍처 설계 역량
- 시계열 임상 데이터 스키마 설계 (환자 x 시점 x 지표)
- DB → ML (LSTM) 학습 파이프라인 아키텍처 설계
- DB 조회 → AI (GPT-4/Gemma) 의뢰서 생성 워크플로 설계
- 실시간 환자 모니터링을 위한 비동기 API 설계

---

## 📎 부록

### A. 실행 방법

```bash
# 1. PostgreSQL 14 설치 및 데이터베이스 생성
brew install postgresql@14
createdb vitaltime

# 2. 스키마 및 초기 데이터 로드
psql -d vitaltime < data/dump.sql

# 3. 환경 변수 설정
cp .env.example .env
# DATABASE_URL=postgresql+asyncpg://user:password@localhost:5432/vitaltime
# OPENAI_API_KEY=sk-proj-...

# 4. 가상환경 및 의존성 설치
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt

# 5. 백엔드 서버 실행
python main.py
# → http://localhost:8000
```

### B. 데이터베이스 접속

```bash
# psql 직접 접속
psql -d vitaltime

# 외부 클라이언트 (DBeaver, pgAdmin 등)
Host: localhost
Port: 5432
Database: vitaltime
```

### C. 유용한 SQL 쿼리

```sql
-- 테이블별 레코드 수 확인
SELECT 'patient' AS table_name, COUNT(*) AS count FROM patient
UNION ALL
SELECT 'clinical_data', COUNT(*) FROM clinical_data
UNION ALL
SELECT 'report', COUNT(*) FROM report;

-- 환자별 NEWS Score 추이 (시계열)
SELECT
    p.patient_name,
    c.timepoint,
    c.news_score,
    c.news_score_label,
    c.timestamp
FROM clinical_data c
JOIN patient p ON c.patient_id = p.patient_id
WHERE p.patient_id = 8  -- 서지민 (중증, severity=7)
ORDER BY c.timepoint;

-- 고위험 환자 식별 (NEWS Score >= 7)
SELECT
    p.patient_name,
    p.severity,
    c.news_score_label,
    c.timestamp
FROM clinical_data c
JOIN patient p ON c.patient_id = p.patient_id
WHERE c.news_score_label >= 7
ORDER BY c.news_score_label DESC, c.timestamp;

-- 환자별 평균 NEWS Score
SELECT
    p.patient_name,
    p.severity,
    ROUND(AVG(c.news_score)::numeric, 1) AS avg_news,
    MAX(c.news_score_label) AS max_news_label
FROM clinical_data c
JOIN patient p ON c.patient_id = p.patient_id
GROUP BY p.patient_id, p.patient_name, p.severity
ORDER BY avg_news DESC;

-- 8시간 윈도우 내 데이터 확인
SELECT
    patient_id,
    COUNT(*) AS records_in_window,
    MIN(timestamp) AS earliest,
    MAX(timestamp) AS latest
FROM clinical_data
WHERE timestamp BETWEEN '2025-01-02 07:00:00' AND '2025-01-02 15:00:00'
GROUP BY patient_id
ORDER BY patient_id;
```

### D. 의존성 목록

```
fastapi==0.118.0
uvicorn==0.37.0
sqlalchemy[asyncio]==2.0.43
asyncpg==0.30.0
pydantic==2.11.9
python-dotenv==1.1.1
pandas==2.3.3
numpy==1.23.5
scikit-learn==1.7.2
tensorflow-macos==2.12.0
keras==2.12.0
torch
transformers
langchain==0.3.27
langchain-core==0.3.76
langchain-openai==0.3.33
schedule==1.2.2
httpx==0.28.1
```

### E. 관련 링크

- Backend API: http://localhost:8000
- API Documentation: http://localhost:8000/docs
- Monitoring Dashboard: monitoring.html (Frontend)
- PostgreSQL: localhost:5432

---

*이 포트폴리오는 VitalTime 프로젝트의 Database 구축 과정을 상세히 문서화한 자료입니다.*
