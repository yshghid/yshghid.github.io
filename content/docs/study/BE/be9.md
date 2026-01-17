---
date : 2025-10-13
tags: ['2025-10']
categories: ['BE']
bookHidden: true
title: "Langchain #2 LangGraph 기반 Multi-Agent + Agentic RAG 시스템"
---

# Langchain #2 LangGraph 기반 Multi-Agent + Agentic RAG 시스템 

#2025-10-13

---

### 1. 실습 개요

- 목적
  - AI 헬스케어 스타트업의 투자 가치를 평가하기 위해 입력된 스타트업 정보에서 '경쟁사 유무를 자동 판별'하고, 판별 결과에 따라 워크플로우를 동적으로 분기하여 'Multi-Agent 시스템(10개 전문 에이전트)'이 각자의 역할(정보 수집, 기술력 분석, 시장성 평가, 경쟁사 비교)을 순차적으로 수행하며, 외부 문서(시장 보고서, 기술 리뷰, 규제 정보)를 'RAG 시스템(FAISS + OpenAI Embeddings)'으로 검색하여 LLM 분석에 참조 컨텍스트를 제공하고, 'Scorecard Method 가중치 평가 방식'으로 6개 항목(창업자/팀, 시장성, 제품/기술력, 경쟁 우위, 실적, 투자조건)을 정량화하여 10점 만점 투자 점수를 산출한 뒤, 전체 프로세스를 'LangGraph 기반 상태 관리 워크플로우'로 자동화하고, 최종적으로 분석 결과를 Executive Summary, 기술력/시장성 평가, 경쟁 분석, 투자 판단을 포함한 전문적인 'Word/PDF 형식의 투자 평가 보고서'로 생성

- 실습 설계
  - 경쟁사 유무 판별: LLM 기반 스타트업 정보 분석 → YES/NO 판단 → 워크플로우 분기점 결정
  - Multi-Agent 기반 분석: InvestmentAgents 클래스 내 10개 전문 에이전트(경쟁사 정보 수집, 기술력 분석, 시장성 평가, 경쟁사 비교 등) 독립 실행
  - RAG 시스템: FAISS 벡터스토어 + OpenAIEmbeddings를 활용해 외부 문서(PDF, TXT) 검색 → 기술력/시장성 분석 시 컨텍스트 제공
  - Scorecard Method: 창업자/팀(30%), 시장성(25%), 제품/기술력(15%), 경쟁 우위(10%), 실적(10%), 투자조건(10%) 가중치 평가 → 10점 만점 점수 산출
  - LangGraph 워크플로우: StateGraph로 노드(10개 에이전트) 및 조건부 엣지 정의 → 경쟁사 유무에 따라 동적 경로 분기 → 순차 실행 자동화
  - Word/PDF 투자 평가 보고서 생성: python-docx + ReportLab 활용 → 마크다운 파싱 → 투자 점수 강조 → Executive Summary, 기술력/시장성 평가, 경쟁 분석, 투자 판단 포함 전문 보고서 출력


### 2. 실습 코드

<mark>프로젝트 구조</mark>
```plain text
project/
├── config.py                 # 환경 설정
├── models.py                 # 데이터 모델 정의
├── rag_system.py             # RAG 시스템
├── agents.py                 # 투자 평가 에이전트
├── graph.py                  # LangGraph 워크플로우
├── report_generator.py       # 보고서 생성 (Word/PDF)
├── main.py                   # 메인 실행 파일
└── requirements.txt          # 의존성 패키지
```

###

<mark>#1 config.py</mark>

```python
# config.py 
"""환경 설정 및 상수 정의"""

import os
from dotenv import load_dotenv

# 환경 변수 로드
load_dotenv()

# API 설정
OPENAI_API_KEY = os.getenv("OPENAI_API_KEY")

# 모델 설정
LLM_MODEL = "gpt-4o-mini"
LLM_TEMPERATURE_RAG = 0.3
LLM_TEMPERATURE_AGENT = 0.5

# RAG 설정
CHUNK_SIZE = 1000
CHUNK_OVERLAP = 200
RETRIEVAL_K = 4

# 출력 디렉토리
OUTPUT_DIR = "outputs"

# RAG 문서 경로
RAG_DOC_PATHS = [
    "data/rag_docs/02-18-25_CDER AI Discussion Paper_v2.1.pdf",
    "data/rag_docs/BRG-Report-AI-and-The-Future-of-Healthcare.pdf",
    "data/rag_docs/CB-Insights-Report-The-Generative-AI-in-Healthcare-Market-Map.pdf",
    "data/rag_docs/high-performance_medicine_the_convergence_of_human_and_artificial_intelligence_nature_medicine_volume_25_january_2019.pdf"
]
```

###

<mark>#2 models.py</mark>

```python
# models.py
"""데이터 모델 정의"""

from typing import TypedDict, Annotated, List, Optional
import operator


class InvestmentState(TypedDict):
    """투자 평가 상태"""
    startup_name: str
    startup_info: str
    has_competitor: Optional[bool]
    competitor_info: Optional[str]
    
    # 타겟 스타트업 분석
    tech_analysis: Optional[str]
    market_analysis: Optional[str]
    
    # 경쟁사 분석
    competitor_tech_analysis: Optional[str]
    competitor_market_analysis: Optional[str]
    
    # 비교 및 판단
    comparison_result: Optional[str]
    investment_decision: Optional[str]
    investment_score: Optional[float]
    
    # 최종 보고서
    final_report: Optional[str]
    
    # 메타 정보
    messages: Annotated[List[str], operator.add]
    iteration: int
```

###

<mark>#3 rag_system.py</mark>

```python
# rag_system.py
"""RAG 시스템 - 문서 기반 정보 검색"""

from typing import List
from langchain_openai import ChatOpenAI, OpenAIEmbeddings
from langchain_community.vectorstores import FAISS
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_core.documents import Document
from pypdf import PdfReader
from config import (
    OPENAI_API_KEY, 
    LLM_MODEL, 
    LLM_TEMPERATURE_RAG,
    CHUNK_SIZE,
    CHUNK_OVERLAP,
    RETRIEVAL_K
)


class RAGSystem:
    """RAG 시스템 - 문서 기반 정보 검색"""
    
    def __init__(self, api_key: str = OPENAI_API_KEY):
        self.llm = ChatOpenAI(
            model=LLM_MODEL,
            temperature=LLM_TEMPERATURE_RAG,
            api_key=api_key
        )
        self.embeddings = OpenAIEmbeddings(api_key=api_key)
        self.vectorstore = None
        
    def load_documents(self, file_paths: List[str]) -> int:
        """문서 로드 및 벡터 스토어 생성"""
        docs = []
        for path in file_paths:
            content = ""
            try:
                if path.lower().endswith(".pdf"):
                    with open(path, 'rb') as f:
                        reader = PdfReader(f)
                        for page in reader.pages:
                            content += page.extract_text() or ""
                else:
                    with open(path, 'r', encoding='utf-8') as f:
                        content = f.read()
                
                if content:
                    docs.append(Document(
                        page_content=content,
                        metadata={"source": path}
                    ))
            except Exception as e:
                print(f"문서 로드 실패 {path}: {e}")
        
        text_splitter = RecursiveCharacterTextSplitter(
            chunk_size=CHUNK_SIZE,
            chunk_overlap=CHUNK_OVERLAP
        )
        splits = text_splitter.split_documents(docs)
        
        self.vectorstore = FAISS.from_documents(splits, self.embeddings)
        return len(splits)
    
    def retrieve(self, query: str, k: int = RETRIEVAL_K) -> str:
        """관련 문서 검색"""
        if not self.vectorstore:
            return ""
        
        docs = self.vectorstore.similarity_search(query, k=k)
        return "\n\n".join([doc.page_content for doc in docs])
```

###

<mark>#4 agents.py</mark>

```python
# agents.py
"""투자 평가 에이전트"""

from typing import List
from langchain_openai import ChatOpenAI
from langchain_core.prompts import ChatPromptTemplate
from models import InvestmentState
from rag_system import RAGSystem
from config import OPENAI_API_KEY, LLM_MODEL, LLM_TEMPERATURE_AGENT


class InvestmentAgents:
    """투자 평가 에이전트들"""
    
    def __init__(self, api_key: str = OPENAI_API_KEY):
        self.llm = ChatOpenAI(
            model=LLM_MODEL,
            temperature=LLM_TEMPERATURE_AGENT,
            api_key=api_key
        )
        self.rag = RAGSystem(api_key)
    
    def setup_rag(self, doc_paths: List[str]):
        """RAG 시스템 초기화"""
        num_chunks = self.rag.load_documents(doc_paths)
        print(f"✅ RAG 시스템 초기화 완료: {num_chunks}개 청크 생성")
    
    def check_competitor(self, state: InvestmentState) -> InvestmentState:
        """경쟁사 유무 판별"""
        prompt = ChatPromptTemplate.from_messages([
            ("system", """당신은 헬스케어 시장 분석 전문가입니다.
            주어진 스타트업 정보를 분석하여 직접적인 경쟁사가 있는지 판단하세요.
            
            판단 기준:
            - 동일한 기술 영역 (예: 의료영상 AI, 진단 AI, 신약개발 AI 등)
            - 유사한 타겟 질환 또는 적용 분야
            - 경쟁 관계가 명시된 경우
            
            명확한 경쟁사가 언급되어 있으면 'YES', 없으면 'NO'로만 답하세요."""),
            ("user", "스타트업 정보:\n{info}")
        ])
        
        response = self.llm.invoke(
            prompt.format_messages(info=state["startup_info"])
        )
        
        has_competitor = "YES" in response.content.upper()
        state["has_competitor"] = has_competitor
        state["messages"].append(
            f"경쟁사 유무: {'있음' if has_competitor else '없음'}"
        )
        
        return state
```

###

#5 graph.py

```python
# graph.py
"""LangGraph 워크플로우 구성"""

from typing import List
from langgraph.graph import StateGraph, END
from models import InvestmentState
from agents import InvestmentAgents


def create_investment_graph(api_key: str, doc_paths: List[str]):
    """투자 평가 그래프 생성"""
    agents = InvestmentAgents(api_key)
    agents.setup_rag(doc_paths)
    
    # 그래프 정의
    workflow = StateGraph(InvestmentState)
    
    # 노드 추가
    workflow.add_node("check_competitor", agents.check_competitor)
    workflow.add_node("collect_startup", agents.collect_startup_info)
    workflow.add_node("collect_competitor", agents.collect_competitor_info)
    workflow.add_node("analyze_tech", agents.analyze_technology)
    workflow.add_node("analyze_competitor_tech", agents.analyze_competitor_technology)
    workflow.add_node("evaluate_market", agents.evaluate_market)
    workflow.add_node("evaluate_competitor_market", agents.evaluate_competitor_market)
    workflow.add_node("compare", agents.compare_competitors)
    workflow.add_node("decide", agents.make_investment_decision)
    workflow.add_node("report", agents.generate_report)
    
    # 엣지 정의
    workflow.set_entry_point("check_competitor")
    
    # 경쟁사 유무에 따른 분기
    def route_by_competitor(state: InvestmentState):
        if state["has_competitor"] is None:
            return "collect_startup"
        elif state["has_competitor"]:
            return "collect_competitor"
        else:
            return "collect_startup"
    
    workflow.add_conditional_edges(
        "check_competitor",
        route_by_competitor,
        {
            "collect_startup": "collect_startup",
            "collect_competitor": "collect_competitor"
        }
    )
    
    # 경쟁사 없는 경로
    workflow.add_edge("collect_startup", "analyze_tech")
    workflow.add_edge("analyze_tech", "evaluate_market")
    
    # 경쟁사 있는 경로
    workflow.add_edge("collect_competitor", "analyze_competitor_tech")
    workflow.add_edge("analyze_competitor_tech", "evaluate_competitor_market")
    
    # 병합 지점 (경쟁사 비교 또는 투자 판단)
    def route_to_comparison(state: InvestmentState):
        if state.get("competitor_market_analysis"):
            return "compare"
        else:
            return "decide"
    
    workflow.add_conditional_edges(
        "evaluate_market",
        route_to_comparison,
        {
            "compare": "compare",
            "decide": "decide"
        }
    )
    
    workflow.add_edge("evaluate_competitor_market", "compare")
    workflow.add_edge("compare", "decide")
    workflow.add_edge("decide", "report")
    workflow.add_edge("report", END)
    
    return workflow.compile()
```

###

#6 report_generator.py

```python
"""보고서 생성 - Word/PDF"""

import os
from typing import Dict
from datetime import datetime
from docx import Document as DocxDocument
from docx.shared import Pt, Inches, RGBColor
from docx.enum.text import WD_PARAGRAPH_ALIGNMENT
from reportlab.lib.pagesizes import letter
from reportlab.lib.styles import getSampleStyleSheet, ParagraphStyle
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.enums import TA_CENTER
from reportlab.lib import colors
from config import OUTPUT_DIR


def save_report_as_docx(result: Dict, startup_name: str) -> str:
    """Word 문서로 보고서 저장"""
    doc = DocxDocument()

    # 문서 제목
    title = doc.add_heading('AI 스타트업 투자 평가 보고서', 0)
    title.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER

    # 부제목
    subtitle = doc.add_paragraph(f'{startup_name}')
    subtitle.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER
    subtitle_format = subtitle.runs[0]
    subtitle_format.font.size = Pt(16)
    subtitle_format.font.color.rgb = RGBColor(70, 130, 180)

    # 날짜
    date_para = doc.add_paragraph(f'평가일: {datetime.now().strftime("%Y년 %m월 %d일")}')
    date_para.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER
    date_para.runs[0].font.size = Pt(11)

    doc.add_paragraph()

    # 투자 점수 강조
    score_para = doc.add_heading('투자 평가 점수', level=2)
    score_value = doc.add_paragraph(f"{result['investment_score']:.1f} / 10.0")
    score_value.runs[0].font.size = Pt(24)
    score_value.runs[0].font.bold = True
    score_value.runs[0].font.color.rgb = RGBColor(220, 20, 60) if result['investment_score'] >= 7.0 else RGBColor(255, 140, 0)
    score_value.alignment = WD_PARAGRAPH_ALIGNMENT.CENTER

    doc.add_paragraph()

    # 보고서 본문 파싱
    report_content = result['final_report']
    lines = report_content.split('\n')

    for line in lines:
        line = line.strip()
        if not line:
            doc.add_paragraph()
            continue

        if line.startswith('# '):
            doc.add_heading(line[2:], level=1)
        elif line.startswith('## '):
            doc.add_heading(line[3:], level=2)
        elif line.startswith('### '):
            doc.add_heading(line[4:], level=3)
        elif line.startswith('- '):
            p = doc.add_paragraph(line[2:], style='List Bullet')
            p.runs[0].font.size = Pt(11)
        elif line.startswith('**') and line.endswith('**'):
            p = doc.add_paragraph()
            run = p.add_run(line[2:-2])
            run.bold = True
            run.font.size = Pt(11)
        else:
            p = doc.add_paragraph(line)
            p.runs[0].font.size = Pt(11)

    # 파일 저장
    output_path = f"{OUTPUT_DIR}/{startup_name}_투자평가보고서.docx"
    os.makedirs(OUTPUT_DIR, exist_ok=True)
    doc.save(output_path)

    return output_path


def save_report_as_pdf(result: Dict, startup_name: str) -> str:
    """PDF 문서로 보고서 저장"""
    output_path = f"{OUTPUT_DIR}/{startup_name}_투자평가보고서.pdf"
    os.makedirs(OUTPUT_DIR, exist_ok=True)

    doc = SimpleDocTemplate(output_path, pagesize=letter)
    story = []
    styles = getSampleStyleSheet()

    # 커스텀 스타일
    title_style = ParagraphStyle(
        'CustomTitle',
        parent=styles['Title'],
        fontSize=24,
        textColor=colors.HexColor('#2C3E50'),
        spaceAfter=12,
        alignment=TA_CENTER
    )

    subtitle_style = ParagraphStyle(
        'CustomSubtitle',
        parent=styles['Normal'],
        fontSize=16,
        textColor=colors.HexColor('#4682B4'),
        spaceAfter=6,
        alignment=TA_CENTER
    )

    heading1_style = ParagraphStyle(
        'CustomHeading1',
        parent=styles['Heading1'],
        fontSize=16,
        textColor=colors.HexColor('#2C3E50'),
        spaceAfter=12,
        spaceBefore=12
    )

    heading2_style = ParagraphStyle(
        'CustomHeading2',
        parent=styles['Heading2'],
        fontSize=14,
        textColor=colors.HexColor('#34495E'),
        spaceAfter=10,
        spaceBefore=10
    )

    normal_style = ParagraphStyle(
        'CustomNormal',
        parent=styles['Normal'],
        fontSize=11,
        spaceAfter=6
    )

    # 제목
    story.append(Paragraph("AI 스타트업 투자 평가 보고서", title_style))
    story.append(Spacer(1, 0.1*Inches))

    # 부제목
    story.append(Paragraph(startup_name, subtitle_style))
    story.append(Spacer(1, 0.1*Inches))

    # 날짜
    date_text = f"평가일: {datetime.now().strftime('%Y년 %m월 %d일')}"
    story.append(Paragraph(date_text, normal_style))
    story.append(Spacer(1, 0.3*Inches))

    # 투자 점수
    score_style = ParagraphStyle(
        'Score',
        parent=styles['Normal'],
        fontSize=20,
        textColor=colors.HexColor('#DC143C') if result['investment_score'] >= 7.0 else colors.HexColor('#FF8C00'),
        alignment=TA_CENTER,
        spaceAfter=12
    )
    story.append(Paragraph("투자 평가 점수", heading2_style))
    story.append(Paragraph(f"<b>{result['investment_score']:.1f} / 10.0</b>", score_style))
    story.append(Spacer(1, 0.3*Inches))

    # 보고서 본문
    report_content = result['final_report']
    lines = report_content.split('\n')

    for line in lines:
        line = line.strip()
        if not line:
            story.append(Spacer(1, 0.1*Inches))
            continue

        # HTML 특수 문자 이스케이프
        line = line.replace('&', '&amp;').replace('<', '&lt;').replace('>', '&gt;')

        if line.startswith('# '):
            story.append(Paragraph(line[2:], heading1_style))
        elif line.startswith('## '):
            story.append(Paragraph(line[3:], heading2_style))
        elif line.startswith('### '):
            story.append(Paragraph(line[4:], heading2_style))
        elif line.startswith('- '):
            story.append(Paragraph(f"• {line[2:]}", normal_style))
        elif line.startswith('**') and line.endswith('**'):
            story.append(Paragraph(f"<b>{line[2:-2]}</b>", normal_style))
        else:
            story.append(Paragraph(line, normal_style))

    # PDF 빌드
    doc.build(story)

    return output_path
```

### 

<mark>#7 main.py</mark>

```python
# main.py
"""메인 실행 파일"""

from typing import Dict, List
from models import InvestmentState
from graph import create_investment_graph
from report_generator import save_report_as_docx, save_report_as_pdf
from config import OPENAI_API_KEY, RAG_DOC_PATHS


def evaluate_startup(
    startup_name: str,
    startup_info: str,
    api_key: str = OPENAI_API_KEY,
    doc_paths: List[str] = RAG_DOC_PATHS
) -> Dict:
    """스타트업 투자 평가 실행"""
    
    print(f"\n{'='*60}")
    print(f"🏥 {startup_name} 투자 평가 시작")
    print(f"{'='*60}\n")
    
    # 그래프 생성
    graph = create_investment_graph(api_key, doc_paths)
    
    # 초기 상태
    initial_state = InvestmentState(
        startup_name=startup_name,
        startup_info=startup_info,
        has_competitor=None,
        competitor_info=None,
        tech_analysis=None,
        market_analysis=None,
        competitor_tech_analysis=None,
        competitor_market_analysis=None,
        comparison_result=None,
        investment_decision=None,
        investment_score=None,
        final_report=None,
        messages=[],
        iteration=0
    )
    
    # 실행
    result = graph.invoke(initial_state)
    
    # 결과 출력
    print(f"\n{'='*60}")
    print("📊 평가 결과")
    print(f"{'='*60}")
    print(f"투자 점수: {result['investment_score']:.1f}점")
    print(f"\n진행 과정:")
    for msg in result['messages']:
        print(f"  {msg}")
    
    return result


if __name__ == "__main__":
    # 스타트업 정보
    STARTUP_NAME = "Qure.ai"
    STARTUP_INFO = """
    Qure.ai는 인도 기반 의료 AI 스타트업으로, 의료 영상 진단을 위한 
    딥러닝 솔루션을 개발합니다.
    
    주요 제품:
    - qXR: 흉부 X-ray 자동 분석
    - qER: 응급실 뇌 CT 스캔 분석
    - qCT: 폐 결절 및 병변 탐지
    
    실적:
    - 70개국 이상 진출
    - 3,000개 이상 의료기관 도입
    - FDA 승인 획득
    
    경쟁사:
    - Zebra Medical Vision (이스라엘)
    - Lunit (한국)
    """
    
    # 평가 실행
    result = evaluate_startup(
        startup_name=STARTUP_NAME,
        startup_info=STARTUP_INFO
    )
    
    # 최종 보고서 저장
    if result['final_report']:
        # Word 파일 생성
        docx_path = save_report_as_docx(result, STARTUP_NAME)
        print(f"\n✅ Word 보고서 저장 완료: {docx_path}")

        # PDF 파일 생성
        pdf_path = save_report_as_pdf(result, STARTUP_NAME)
        print(f"✅ PDF 보고서 저장 완료: {pdf_path}")
```

### 

#8 