
# AI Startup Investment Evaluation Agent

본 프로젝트는 **인공지능 스타트업의 투자 가능성**을 자동으로 평가하는 멀티 에이전트 + Agentic RAG 시스템입니다. PDF/텍스트 근거 기반 평가와 스코어카드 방식의 의사결정을 결합하여 **투자 유망 / 조건부 / 보류**를 산출합니다.

> LangGraph로 구성된 상태 기계(StateGraph) 위에 기술/시장/경쟁 분석 노드를 배치하고, FAISS 기반 RAG로 IR·보고서·논문 등 자료를 근거로 활용합니다.

---

## Overview

- **Objective** : AI 스타트업의 기술력, 시장성, 리스크를 기준으로 투자 적합성 분석
- **Method** : AI Agent + Agentic RAG (근거 우선 평가)
- **Tools** : LangGraph, LangChain, OpenAI API, FAISS, PyPDF, python-docx, reportlab

---

## Features

- PDF 자료 기반 정보 추출 (IR, 기사, 리포트 등)
- 투자 기준별 판단 분류 (시장성, 팀, 기술력, 경쟁우위, 실적, 밸류에이션)
- Scorecard Method 기반 **가중 합산 점수** 및 최종 의견 산출
- **최종 투자 보고서** 생성 (DOCX, PDF 내보내기)

---

## Tech Stack

| Category   | Details                      |
|------------|------------------------------|
| Framework  | LangGraph, LangChain, Python |
| LLM        | GPT-4o-mini via OpenAI API   |
| Retrieval  | FAISS, Chroma (옵션)         |
| Parsing    | PyPDF, python-docx           |
| Report     | python-docx, reportlab       |

---

## Agents

- **Agent A**: 기술 경쟁력 평가 (혁신성, 차별성, 특허/지재권, 구현 가능성)
- **Agent B**: 시장성/팀/규제/진입장벽 평가 및 요약
- (옵션) 경쟁사 에이전트: 경쟁사 기술/시장성 동등 기준 비교

---

## Architecture

```
(check_competitor) → collect_startup ─→ analyze_tech ─→ evaluate_market ─╮
                   └→ collect_competitor → analyze_competitor_tech → evaluate_competitor_market ─→ compare ─→ decide ─→ report → END
                                                                                               ╰──────────────────────────────────────╯
```
> LangGraph 상태 전이로 경쟁사 유무에 따른 분기 및 병합이 구현되어 있습니다.

(그래프 이미지는 `docs/architecture.png`로 교체해 주세요.)

---

## Directory Structure

```
├── data/                      # 스타트업 PDF/텍스트 문서 (RAG 입력)
│   └── rag_docs/
├── agents/                    # 평가 기준별 Agent 모듈 (선택적 분리 지점)
├── prompts/                   # 프롬프트 템플릿
├── outputs/                   # 평가 결과 및 보고서(DOCX/PDF)
├── app.py                     # 실행 스크립트 (엔드 투 엔드)
├── README.md                  # 본 문서
└── requirements.txt           # 의존성 리스트
```

---

## Quickstart

### 1) 환경 변수
프로젝트 루트에 `.env` 파일을 생성하고 OpenAI 키를 설정합니다.
```
OPENAI_API_KEY=sk-...
```

### 2) 의존성 설치
```
pip install -r requirements.txt
```
예시(직접 설치):
```
pip install langchain-core langchain-community langchain-openai faiss-cpu pypdf python-docx reportlab python-dotenv
```

### 3) 데이터 준비
- `data/rag_docs/` 폴더에 IR 자료/리포트/PDF 등을 넣습니다. (예: 3~10개)

### 4) 실행
`app.py`의 샘플 파라미터(스타트업명, 문서 경로)를 확인/수정한 후 실행합니다.
```
python app.py
```

실행 결과:
- 콘솔에 **진행 로그** 및 **최종 점수** 출력
- `outputs/` 폴더에 **DOCX** / **PDF 보고서** 생성

---

## Evaluation Flow (노드 기준)

1. **check_competitor**: 스타트업 정보로 경쟁사 유무 YES/NO 판정  
2. **collect_startup / collect_competitor**: 대상/경쟁사 기초 정보 수집  
3. **analyze_tech / analyze_competitor_tech**: RAG 문맥을 포함한 기술력 평가  
4. **evaluate_market / evaluate_competitor_market**: 시장성/규제/진입장벽 평가  
5. **compare**: 기술·시장 포지셔닝 비교 및 차별화 포인트 정리  
6. **decide**: Scorecard Method 가중 합산으로 **최종 점수 & 의견(Invest/Conditional/Hold)**  
7. **report**: Executive Summary 포함 **최종 보고서** 생성 (DOCX/PDF)

---

## Scoring (Scorecard Method)

| 항목 | 비중 | 예시 지표 |
|------|-----:|-----------|
| 창업자/팀 | 30% | 전문성, 도메인 적합성, 실행력 |
| 시장성 | 25% | TAM/SAM/SOM, 성장률, 수요/페인포인트 |
| 제품/기술력 | 15% | 혁신성, 차별성, 구현 가능성 |
| 경쟁 우위 | 10% | 진입장벽, 특허/데이터 모수, 네트워크 효과 |
| 실적 | 10% | 매출/계약/배포 수, 임상/규제 진행도 |
| 투자조건 | 10% | 밸류에이션, 딜 구조, 보호조건 |

- **≥ 7.0**: INVEST (투자 추천)  
- **5.0 ~ 7.0**: CONDITIONAL (조건부)  
- **< 5.0**: HOLD (보류)

---

## Outputs

- `outputs/{startup}_투자평가보고서.docx`  
- `outputs/{startup}_투자평가보고서.pdf`  
- 보고서 구성: Executive Summary → 기업개요 → 기술/시장성 → 경쟁분석 → Scorecard → 결론/제언

---

## Configuration Tips

- `chunk_size=1000, chunk_overlap=200` (텍스트 스플리터)  
- `k=4` (RAG 문서 검색 상위 k)  
- 민감한 데이터/비공개 PDF 사용 시 로컬 실행 권장  
- Chroma로 교체 시 `persist_directory` 지정

---

## Contributors

- **김철수**: Prompt Engineering, Agent Design  
- **최영희**: PDF Parsing, Retrieval Agent

---

## License

본 프로젝트는 연구/실습 목적 예제로 제공됩니다. 상업적 사용 또는 재배포 시 관련 라이선스를 확인하세요.
