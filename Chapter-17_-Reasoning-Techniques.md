# 17장. Reasoning Techniques(추론 기법)

## 17.1 패턴 개요

- **정의**: 에이전트의 **다단계 논리·문제 분해·자기 수정** 강화 기법 집합.
- **왜 필요?** 복잡 문제: 직접 답변 실패 → **명시적 추론 + 리소스 투자**.
- **원리**: "사고 시간" ↑ → 정확도·일관성 ↑ (스케일링 추론 법칙).

### 주요 기법

| 기법 | 설명 | 강점 |
|------|------|------|
| **CoT (Chain-of-Thought)** | 단계별 사고 지시 | 다단계 추론 ↑ |
| **ToT (Tree-of-Thought)** | 분기 탐색·백트래킹 | 대안 평가 |
| **Self-Correction** | 생성물 내부 검토·개선 | 오류 수정 |
| **PALM** | LLM + 코드 실행 | 정확 계산 |
| **ReAct** | 생각 → 행동 → 관찰 루프 | 도구 통합 |

---

## 17.2 응용 사례

| 도메인 | 기법 적용 |
|--------|-----------|
| **멀티홉 Q&A** | CoT + 검색 종합 |
| **수학** | ToT + 코드 실행 |
| **코드 디버깅** | Self-Correction 루프 |
| **전략 계획** | ReAct 피드백 |
| **의료·법률** | PALM + 논리 검증 |

---

## 17.3 고급 기법

- **CoD (Chain-of-Debates)**: 다중 모델 토론 → 편향 ↓.
- **Debate Graph**: 비선형 주장 네트워크.
- **MASS**: MAS 프롬프트·토폴로지 자동 최적화(블록 → 워크플로).
- **Deep Research**: 반복 검색·반성(Perplexity·Gemini).

---

## 17.4 구현: ADK 예제 (ReAct)

```python
search_agent = Agent(tools=[google_search])
code_agent = Agent(code_executor=[BuiltInCodeExecutor])

root_agent = Agent(
    tools=[
        agent_tool(search_agent),
        agent_tool(code_agent)
    ]
)
```


---

## 17.5 구현: LangGraph DeepSearch

- **그래프 루프**: 쿼리 생성 → 웹 연구 → 반성 → 반복 → 답변.

```python
builder = StateGraph(ResearchState)
builder.add_node("generate_query", generate_query)
builder.add_node("web_research", web_research)
builder.add_node("reflection", reflection)
builder.add_conditional_edges("reflection", research_eval, ["web_research", "finish"])
graph = builder.compile()
```


---

## 17.6 스케일링 추론 법칙

- **핵심**: 계산 투자 ↑ → 작은 모델도 고성능.
- **균형**: 모델 크기·지연·비용 최적화.

---

## 17.7 한눈에 보기

- **무엇**: **명시적 추론**(CoT·ToT·ReAct).
- **왜**: 복잡 문제 자율 해결.
- **어떻게**: 루프(생각-행동-관찰) + 다중 에이전트(MASS).
- **언제**: 수학·코드·진단·연구.

---

## 17.8 핵심 정리

- 추론 기법은 **에이전트 자율성 초석**.
- ReAct + Deep Research로 실전 적용.
- 이전 챕터(계획·도구·RAG) 완성.
