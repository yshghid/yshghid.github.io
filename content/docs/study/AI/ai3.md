---
date : 2025-07-19
tags: ['2025-07']
categories: ['AI', 'RAG']
bookHidden: true
title: "RAG #3 자동 대화 이력 관리"
pageHidden: true
---

# RAG #3 자동 대화 이력 관리

#2025-07-19

---

### 1. 자동 대화 이력 관리

`ChatPromptTemplate`을 통해 시스템 메시지를 포함하는 프롬프트를 만든다. 시스템 메시지는 모델에게 “너는 금융 상담사야”라고 역할을 부여하는 것이다. 이어지는 `("placeholder", "{messages}")`는 실제 사용자의 질문과 AI의 답변이 이 자리에 채워질 것이라는 의미다. 이 프롬프트는 `chat = ChatOpenAI(model="gpt-4o-mini")`와 연결되는데, 이는 OpenAI의 gpt-4o-mini 모델을 사용하는 챗 인터페이스이다. 이 프롬프트와 모델을 `prompt | chat`이라는 LCEL 표현으로 묶으면, 하나의 체인이 만들어진다. 이 체인은 주어진 메시지 목록을 받아, GPT 모델에 전달하고 응답을 생성하는 구조다.


```python
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "당신은 금융 상담사입니다. 모든 질문에 최선을 다해 답변하십시오."),
        ("placeholder", "{chat_history}"),
        ("human", "{input}"),
    ]
)
```

이제 이 체인을 사용해 실제 대화를 진행한다. 첫 번째 방법은 단순히 리스트로 과거 대화 내용을 전달하는 것이다. 사람이 “저축을 늘리려면 어떻게 해야 하나요?”라고 묻고, AI가 답하고, 사용자가 다시 “방금 뭐라고 했나요?”라고 재확인 질문을 던진다. 이 때 invoke() 메서드로 전체 메시지 리스트를 넘기면, 모델은 이 대화를 바탕으로 응답을 생성한다.

```python
# 프롬프트와 모델을 연결하여 체인 생성
chain = prompt | chat

# 이전 대화를 포함한 메시지 전달
ai_msg = chain.invoke(
    {
        "messages": [
            ("human", "저축을 늘리기 위해 무엇을 할 수 있나요?"),
            ("ai", "저축 목표를 설정하고, 매달 자동 이체로 일정 금액을 저축하세요."),
            ("human", "방금 뭐라고 했나요?"), 
        ],
    }
)
print(ai_msg.content)  # 챗봇의 응답 출력
```
```plain text
저축을 늘리기 위해 먼저 저축 목표를 명확히 설정하고, 매달 자동 이체를 통해 일정 금액을 저축하는 것을 추천했습니다. 이를 통해 꾸준하게 저축할 수 있는 습관을 기를 수 있습니다. 도움이 필요하신 부분이 있으면 말씀해 주세요!
```

하지만 이 방식은 매번 메시지를 수동으로 넘겨야 하므로 실전에서는 불편하다.

그래서 등장하는 것이 ChatMessageHistory다. 이 클래스는 이전 대화 내용을 메모리에 저장하고 관리하는 도구이다. add_user_message()와 add_ai_message()를 통해 메시지를 하나씩 저장할 수 있고, 이후에는 .messages를 통해 전체 대화 내용을 꺼낼 수 있다. 이 메시지를 체인에 넘기면, 마치 인간과 대화하듯 연속적인 문맥이 반영된 응답이 나온다. 이 방식은 훨씬 유연하며, 체인과 연결해서 반복적으로 사용할 수 있다.

```python
# <ChatMessageHistory를 사용한 메시지 관리>
from langchain_community.chat_message_histories import ChatMessageHistory

# 대화 이력 저장을 위한 클래스 초기화
chat_history = ChatMessageHistory()

# 사용자 메시지 추가
chat_history.add_user_message("저축을 늘리기 위해 무엇을 할 수 있나요?")
chat_history.add_ai_message("저축 목표를 설정하고, 매달 자동 이체로 일정 금액을 저축하세요.")

# 새로운 질문 추가 후 다시 체인 실행
chat_history.add_user_message("방금 뭐라고 했나요?")
ai_response = chain.invoke({"messages": chat_history.messages})
print(ai_response.content)  
```
```plain text
저축을 늘리기 위해 저축 목표를 설정하고, 매달 자동 이체로 일정 금액을 저축하는 것이 좋다고 말씀드렸습니다. 이렇게 하면 규칙적으로 저축할 수 있는 습관을 기를 수 있습니다. 추가적으로, 소비를 줄이는 방법이나 불필요한 지출을 점검하는 것도 저축을 늘리는 데 도움이 됩니다. 더 구체적인 조언이 필요하시다면 말씀해 주세요!
```

하지만 여전히 문제는 있다. 대화가 길어지면 모델의 입력 토큰 수가 초과될 수 있고, 세션 별로 기억을 구분해야 하는 요구도 발생한다. 이를 해결하기 위해 RunnableWithMessageHistory가 사용된다. 이 클래스는 체인을 감싸고, 특정 세션 ID에 따라 메시지 기록을 저장하거나 불러올 수 있도록 해준다. 즉, 사용자 A와 B가 서로 다른 대화를 동시에 해도, 각각의 대화가 독립적으로 기억되는 것이다. RunnableWithMessageHistory는 어떤 키를 기준으로 입력 메시지와 대화 이력을 구분할 것인지도 명시할 수 있다. 예를 들어 사용자의 질문은 "input"이라는 키에, 이전 대화는 "chat_history"라는 키에 저장된다.

```python
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

# 시스템 메시지와 대화 이력을 사용하는 프롬프트 템플릿 정의
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "당신은 금융 상담사입니다. 모든 질문에 최선을 다해 답변하십시오."),
        ("placeholder", "{chat_history}"), 
        ("human", "{input}"), 
    ]
)
```

이제 세션 기반 체인을 실행해보면, 처음 사용자가 "저축을 늘리려면 어떻게 해야 하나요?"라고 질문하고, 이후 "내가 방금 뭐라고 했나요?"라고 하면, 모델은 그 이전에 했던 말을 기억하고 그대로 요약해서 말해준다. 이건 LLM이 마치 실제 사람처럼, “방금 내가 뭐라고 했지?”라는 질문에 자연스럽게 답한다.

```python
# 대화 이력을 관리할 체인 설정
chat_history = ChatMessageHistory()
chain = prompt | chat

# RunnableWithMessageHistory 클래스를 사용해 체인을 감쌉니다
chain_with_message_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: chat_history,  
    input_messages_key="input",
    history_messages_key="chat_history", 
)

# 질문 메시지 체인 실행
chain_with_message_history.invoke(
    {"input": "저축을 늘리기 위해 무엇을 할 수 있나요?"},
    {"configurable": {"session_id": "unused"}},
).content

# 새로운 입력 메시지를 추가하고 체인을 실행
chain_with_message_history.invoke(
    {"input": "내가 방금 뭐라고 했나요?"}, 
    {"configurable": {"session_id": "unused"}} 
).content
```
```plain text
'저축을 늘리기 위한 몇 가지 방법을 소개합니다:\n\n1. **예산 수립하기**: 월별 예산을 세워 수입과 지출을 명확하게 파악하세요. 고정 지출과 변동 지출을 분리한 후, 불필요한 지출을 줄이는 방법을 찾아보세요.\n\n2. **비상금 마련하기**: 예기치 않은 상황에 대비해 최소 3~6개월 분의 생활비를 비상금으로 저축하세요. 이를 통해 갑작스러운 지출에 대응할 수 있습니다.\n\n3. **정기 저축**: 월급이 들어오는 즉시 일정 금액을 저축 계좌로 자동 이체하면, 저축을 더 쉽게 할 수 있습니다.\n\n4. **소비 습관 점검**: 취미나 외식 등에서의 지출 패턴을 분석하고, 필요한 부분만 소비하도록 합니다. 더 건강한 소비 습관을 기르는 것이 중요합니다.\n\n5. **금융 상품 활용**: 고금리 저축 계좌나 재테크 상품을 통해 더욱 높은 이자를 받을 수 있도록 다양한 금융 상품을 고려해보세요.\n\n6. **부가 소득 창출**: 아르바이트나 프리랜스 일 등을 통해 추가 수입을 마련하여 저축에 활용할 수 있습니다.\n\n7. **목표 설정하기**: 구체적인 저축 목표를 세우고, 그 목표를 달성하기 위한 계획을 수립하세요. 자금 목표를 명확히 하면 저축 의욕이 더 높아질 수 있습니다.\n\n8. **비교 구매하기**: 물건을 구매할 때 여러 판매처를 비교하고 최저가를 찾아보세요. 세일이나 할인 이벤트를 활용하는 것도 좋습니다.\n\n9. **신용카드 사용 조절**: 신용카드 사용을 최소화하고, 필요한 경우 한정된 카드만 사용하는 것이 도움이 됩니다. 카드 사용 시 지출 내역을 꼼꼼히 기록하세요.\n\n이러한 방법들을 통해 저축 습관을 들이고, 재정적으로 더 안정된 미래를 준비할 수 있습니다.'

'당신은 "저축을 늘리기 위해 무엇을 할 수 있나요?"라고 질문하셨습니다. 저축을 늘리기 위한 다양한 방법을 제안해드렸습니다. 추가적인 질문이나 더 알고 싶은 내용이 있으시면 말씀해 주세요!'
```

하지만 대화가 길어지면 또 다른 문제가 생긴다. 너무 많은 대화가 누적되면 입력 제한 때문에 모델이 다 받아들이지 못할 수 있다. 이를 해결하기 위해 trim_messages가 등장한다. 이 함수는 과거 메시지 중 일부만 남기고 나머지는 제거하는데, 예제에서는 "last" 전략을 사용하고 최대 2개의 메시지만 유지하도록 설정했다. 메시지를 트리밍한 후에는 그 줄어든 메시지를 기반으로 다시 체인을 실행할 수 있다. 이 방식은 마치 인간이 “최근 이야기만 기억하고 과거는 까먹는” 것과 같은 전략이다. 이걸 통해 시스템은 메모리 부담을 줄일 수 있고, 짧고 효율적인 대화를 유지할 수 있다.

```python
# <메시지 트리밍 예제>
from langchain_core.messages import trim_messages
from langchain_core.runnables import RunnablePassthrough
from operator import itemgetter

# 메시지 트리밍 유틸리티 설정
trimmer = trim_messages(strategy="last", max_tokens=2, token_counter=len)

# 트리밍된 대화 이력과 함께 체인 실행
chain_with_trimming = (
    RunnablePassthrough.assign(chat_history=itemgetter("chat_history") | trimmer)
    | prompt
    | chat
)

# 트리밍된 대화 이력을 사용하는 체인 설정
chain_with_trimmed_history = RunnableWithMessageHistory(
    chain_with_trimming,
    lambda session_id: chat_history,
    input_messages_key="input",
    history_messages_key="chat_history",
)

# 새로운 대화 내용 추가 후 체인 실행
chain_with_trimmed_history.invoke(
    {"input": "저는 5년 내에 집을 사기 위해 어떤 재정 계획을 세워야 하나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "finance_session_1"}}  # 세션 ID 설정
)

# 새로운 입력 메시지를 추가하고 체인을 실행
chain_with_trimmed_history.invoke(
    {"input": "내가 방금 뭐라고 했나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "finance_session_1"}}  # 세션 ID 설정
).content
```
```plain text
AIMessage(content='5년 내에 집을 구매하기 위한 재정 계획을 세우는 것은 중요한 단계입니다. 다음은 효과적인 계획을 위한 몇 가지 단계입니다:\n\n1. **목표 설정**:\n   - 구매할 집의 가격을 예상합니다. 지역에 따라 다양한 가격대가 있으므로, 관심 있는 지역의 평균 집값을 조사하세요.\n\n2. **다운 페이먼트 계산**:\n   - 일반적으로 집값의 10-20%를 다운 페이먼트로 요구합니다. 예를 들어, 3억 원짜리 집의 경우, 3천만 원에서 6천만 원 정도가 필요합니다.\n\n3. **예산 작성**:\n   - 현재의 소득과 지출을 파악하여 저축할 수 있는 금액을 계산하세요. 매달 얼마를 저축할 수 있는지 확인하는 것이 중요합니다.\n\n4. **저축 계획**:\n   - 5년 동안 필요한 다운 페이먼트를 목표로 매월 저축해야 할 금액을 계산합니다. 예를 들어, 3천만 원의 다운 페이먼트를 위해 5년(60개월) 동안 저축해야 할 경우, 매월 약 50만 원을 저축해야 합니다.\n\n5. **저축 계좌 선택**:\n   - 일반 저축계좌, 고이율 저축계좌, 또는 적금 상품을 고려하여 이자를 통해 저축액을 늘리세요.\n\n6. **부동산 시장 연구**:\n   - 주택 시장의 변동성을 이해하고, 특정 지역의 부동산 가격 상승률을 고려해 적절한 시기에 구매할 수 있도록 조사하세요.\n\n7. **전문가 상담**:\n   - 부동산 상담사나 재정 상담사와 상담하여 개인의 상황에 맞는 구체적인 전략을 세우는 것이 좋습니다.\n\n8. **신용 점수 관리**:\n   - 대출을 받을 때 유리한 조건을 위해 신용 점수를 관리하세요. 신용 점수를 높이기 위해서는 연체 없이 빚을 갚고, 신용 카드 사용을 적절히 관리하는 것이 중요합니다.\n\n위의 단계를 따라주시고, 필요에 따라 개인의 재정 상황에 맞게 조정하면 좋습니다. 추가 질문이 있으시면 언제든지 말씀해 주세요!', additional_kwargs={'refusal': None}, response_metadata={'token_usage': {'completion_tokens': 494, 'prompt_tokens': 126, 'total_tokens': 620, 'completion_tokens_details': {'accepted_prediction_tokens': 0, 'audio_tokens': 0, 'reasoning_tokens': 0, 'rejected_prediction_tokens': 0}, 'prompt_tokens_details': {'audio_tokens': 0, 'cached_tokens': 0}}, 'model_name': 'gpt-4o-mini-2024-07-18', 'system_fingerprint': 'fp_13eed4fce1', 'finish_reason': 'stop', 'logprobs': None}, id='run-79fd2e0c-15a7-410a-81bb-fb7021567658-0', usage_metadata={'input_tokens': 126, 'output_tokens': 494, 'total_tokens': 620, 'input_token_details': {'audio': 0, 'cache_read': 0}, 'output_token_details': {'audio': 0, 'reasoning': 0}})

'당신은 "저는 5년 내에 집을 사기 위해 어떤 재정 계획을 세워야 하나요?"라고 말씀하셨습니다. 이 질문에 대해 저는 집 구매를 위한 재정 계획의 단계들을 설명해드렸습니다. 추가로 궁금하신 점이나 더 알고 싶은 내용이 있으시면 말씀해 주세요!'
```

더 고급 기능으로는 대화 내용을 ‘요약’해서 기억하는 방식이 있다. summarize_messages() 함수는 현재까지의 대화 내용을 GPT에게 전달해 요약을 요청하고, 요약된 내용을 새로운 시스템 메시지로 저장한다. 이 방식은 메시지를 전부 저장하는 대신 핵심 요약만 기억하게 만드는 전략이다. 마치 비서에게 “지금까지의 대화 요약 좀 해줘”라고 말한 뒤, 그 요약 내용을 기반으로 다음 대화를 이어가는 방식이다. 예를 들어 “저에게 어떤 재정적 조언을 해주셨나요?”라는 질문을 받았을 때, 모델은 이전 대화 전체를 기억하는 대신 그 요약된 내용을 참조하여 응답하게 된다. 이 접근법은 특히 긴 세션을 유지하면서도 중요한 맥락을 잃지 않도록 하기 위해 유용하다.

```python
# <이전 대화 요약 내용 기반으로 답변하기>
def summarize_messages(chain_input):
    stored_messages = chat_history.messages
    if len(stored_messages) == 0:
        return False
    # 대화를 요약하기 위한 프롬프트 템플릿 설정
    summarization_prompt = ChatPromptTemplate.from_messages(
        [
            ("placeholder", "{chat_history}"), 
            (
                "user",
                "이전 대화를 요약해 주세요. 가능한 한 많은 세부 정보를 포함하십시오.", 
            ),
        ]
    )

    # 요약 체인 생성 및 실행
    summarization_chain = summarization_prompt | chat
    summary_message = summarization_chain.invoke({"chat_history": stored_messages})
    chat_history.clear()
    chat_history.add_message(summary_message) 
    return True

# 대화 요약을 처리하는 체인 설정
chain_with_summarization = (
    RunnablePassthrough.assign(messages_summarized=summarize_messages)
    | chain_with_message_history
)

# 요약된 대화를 기반으로 새로운 질문에 응답
print(chain_with_summarization.invoke(
    {"input": "저에게 어떤 재정적 조언을 해주셨나요?"}, 
    {"configurable": {"session_id": "unused"}} 
).content)
```
```plain text
아래는 일반적인 재정적 조언입니다. 이 조언들은 개인의 재정 상황에 따라 조정이 필요할 수 있습니다:

1. **예산 작성**: 매달의 수입과 지출을 기록하여 전체적인 재정 상태를 파악합니다. 이를 통해 불필요한 지출을 줄일 수 있습니다.

2. **저축 계획**: 목표 저축액을 설정하고, 매달 일정 금액을 저축 계좌로 자동 이체하도록 설정하여 저축을 습관화 합니다.

3. **비상 자금 확보**: 3~6개월의 생활비에 해당하는 금액을 비상금으로 마련해 두면 예기치 못한 지출에 대처할 수 있습니다.

4. **부채 관리**: 고금리 부채부터 우선적으로 상환하고, 부채 상황을 체계적으로 관리하여 재정적 부담을 줄입니다.

5. **신용 점수 관리**: 신용 점수를 주기적으로 확인하고, 연체 없이 청구서를 납부하여 긍정적인 신용 기록을 유지합니다.

6. **투자 고려**: 장기적인 재정 목표에 맞춰 적절한 투자 상품을 찾아 투자합니다. 주식, 채권, 펀드 등 다양한 옵션을 조사한 후 자신의 위험 감수 수준에 맞는 곳에 투자하십시오.

7. **전문가 상담**: 필요할 경우 재정 상담사와 상담하여 맞춤형 재정 계획을 세우는 것이 좋습니다.

8. **목표 설정**: 단기 및 장기 목표를 명확히 설정하여 그에 맞는 세부 계획을 수립합니다. 예를 들어, 주택 구매나 자녀 교육비 마련과 같은 목표입니다.

이 조언들이 도움이 되었으면 좋겠습니다. 재정적 상황이나 목표가 더 구체적이라면, 그에 맞춰 더욱 세부적인 조언을 드릴 수 있습니다! 어떤 부분에 대해 더 알고 싶으신가요?
```

### 2. 정리

```python
!pip install -q python-dotenv langchain-community langchain-core langchain langchain-openai langchain-chroma

# <이전 대화를 포함한 메시지 전달>
from langchain_core.prompts import ChatPromptTemplate
from langchain_openai import ChatOpenAI

chat = ChatOpenAI(model="gpt-4o-mini")

# 프롬프트 템플릿 정의: 금융 상담 역할
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "당신은 금융 상담사입니다. 사용자에게 최선의 금융 조언을 제공합니다."),
        ("placeholder", "{messages}"),  # 대화 이력 추가
    ]
)

# 프롬프트와 모델을 연결하여 체인 생성
chain = prompt | chat

# 이전 대화를 포함한 메시지 전달
ai_msg = chain.invoke(
    {
        "messages": [
            ("human", "저축을 늘리기 위해 무엇을 할 수 있나요?"),  # 사용자의 첫 질문
            ("ai", "저축 목표를 설정하고, 매달 자동 이체로 일정 금액을 저축하세요."),  # 챗봇의 답변
            ("human", "방금 뭐라고 했나요?"),  # 사용자의 재확인 질문
        ],
    }
)
print(ai_msg.content)  # 챗봇의 응답 출력

# <ChatMessageHistory를 사용한 메시지 관리>
from langchain_community.chat_message_histories import ChatMessageHistory

# 대화 이력 저장을 위한 클래스 초기화
chat_history = ChatMessageHistory()

# 사용자 메시지 추가
chat_history.add_user_message("저축을 늘리기 위해 무엇을 할 수 있나요?")
chat_history.add_ai_message("저축 목표를 설정하고, 매달 자동 이체로 일정 금액을 저축하세요.")

# 새로운 질문 추가 후 다시 체인 실행
chat_history.add_user_message("방금 뭐라고 했나요?")
ai_response = chain.invoke({"messages": chat_history.messages})
print(ai_response.content)  # 챗봇은 이전 메시지를 기억하여 답변합니다.

# < RunnableWithMessageHistory를 사용한 메시지 관리>
from langchain_core.prompts import ChatPromptTemplate
from langchain_core.runnables.history import RunnableWithMessageHistory
from langchain_community.chat_message_histories import ChatMessageHistory

# 시스템 메시지와 대화 이력을 사용하는 프롬프트 템플릿 정의
prompt = ChatPromptTemplate.from_messages(
    [
        ("system", "당신은 금융 상담사입니다. 모든 질문에 최선을 다해 답변하십시오."),
        ("placeholder", "{chat_history}"),  # 이전 대화 이력
        ("human", "{input}"),  # 사용자의 새로운 질문
    ]
)

# 대화 이력을 관리할 체인 설정
chat_history = ChatMessageHistory()
chain = prompt | chat

# RunnableWithMessageHistory 클래스를 사용해 체인을 감쌉니다
chain_with_message_history = RunnableWithMessageHistory(
    chain,
    lambda session_id: chat_history,  # 세션 ID에 따라 대화 이력을 불러오는 함수
    input_messages_key="input",  # 입력 메시지의 키 설정
    history_messages_key="chat_history",  # 대화 이력의 키 설정
)

# 질문 메시지 체인 실행
chain_with_message_history.invoke(
    {"input": "저축을 늘리기 위해 무엇을 할 수 있나요?"},
    {"configurable": {"session_id": "unused"}},
).content

# 새로운 입력 메시지를 추가하고 체인을 실행
chain_with_message_history.invoke(
    {"input": "내가 방금 뭐라고 했나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "unused"}}  # 세션 ID 설정
).content

# <메시지 트리밍 예제>
from langchain_core.messages import trim_messages
from langchain_core.runnables import RunnablePassthrough
from operator import itemgetter

# 메시지 트리밍 유틸리티 설정
trimmer = trim_messages(strategy="last", max_tokens=2, token_counter=len)

# 트리밍된 대화 이력과 함께 체인 실행
chain_with_trimming = (
    RunnablePassthrough.assign(chat_history=itemgetter("chat_history") | trimmer)
    | prompt
    | chat
)

# 트리밍된 대화 이력을 사용하는 체인 설정
chain_with_trimmed_history = RunnableWithMessageHistory(
    chain_with_trimming,
    lambda session_id: chat_history,
    input_messages_key="input",
    history_messages_key="chat_history",
)

# 새로운 대화 내용 추가 후 체인 실행
chain_with_trimmed_history.invoke(
    {"input": "저는 5년 내에 집을 사기 위해 어떤 재정 계획을 세워야 하나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "finance_session_1"}}  # 세션 ID 설정
)

# 새로운 입력 메시지를 추가하고 체인을 실행
chain_with_trimmed_history.invoke(
    {"input": "내가 방금 뭐라고 했나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "finance_session_1"}}  # 세션 ID 설정
).content

# <이전 대화 요약 내용 기반으로 답변하기>
def summarize_messages(chain_input):
    stored_messages = chat_history.messages
    if len(stored_messages) == 0:
        return False
    # 대화를 요약하기 위한 프롬프트 템플릿 설정
    summarization_prompt = ChatPromptTemplate.from_messages(
        [
            ("placeholder", "{chat_history}"),  # 이전 대화 이력
            (
                "user",
                "이전 대화를 요약해 주세요. 가능한 한 많은 세부 정보를 포함하십시오.",  # 요약 요청 메시지
            ),
        ]
    )

    # 요약 체인 생성 및 실행
    summarization_chain = summarization_prompt | chat
    summary_message = summarization_chain.invoke({"chat_history": stored_messages})
    chat_history.clear()  # 요약 후 이전 대화 삭제
    chat_history.add_message(summary_message)  # 요약된 메시지를 대화 이력에 추가
    return True

  # 대화 요약을 처리하는 체인 설정
chain_with_summarization = (
    RunnablePassthrough.assign(messages_summarized=summarize_messages)
    | chain_with_message_history
)

# 요약된 대화를 기반으로 새로운 질문에 응답
print(chain_with_summarization.invoke(
    {"input": "저에게 어떤 재정적 조언을 해주셨나요?"},  # 사용자의 질문
    {"configurable": {"session_id": "unused"}}  # 세션 ID 설정
).content)
```

#

#출처

책 RAG 마스터: 랭체인으로 완성하는 LLM 서비스: 멀티모달, 그래프 RAG, 에이전트, 파인튜닝까지


