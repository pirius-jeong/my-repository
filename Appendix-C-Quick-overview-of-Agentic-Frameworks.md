# 부록 C. Quick Overview of Agentic Frameworks(에이전트 프레임워크 개요)

## C.1 LangChain & LangGraph

| 프레임워크 | 특징 | 용도 |
|------------|------|------|
| **LangChain** | LCEL 체인(DAG, 선형 파이프) | RAG·요약·추출 |
| **LangGraph** | 노드·에지 그래프(순환·상태) | 다중 에이전트·계획-실행 |

**비교**

| 특징 | LangChain | LangGraph |
|------|-----------|-----------|
| 워크플로 | 선형(DAG) | 순환 그래프 |
| 상태 | 실행당 없음 | 명시적 객체 |
| 용도 | 예측 시퀀스 | 동적 에이전트 |

**LangGraph 예제 (병렬)**
```python
class State(TypedDict):
    topic: str
    joke: str
    story: str
    poem: str
    combined_output: str

# 노드: LLM 호출 (joke/story/poem)
# 에지: START → 병렬 → aggregator → END
graph = StateGraph(State)
# ... 빌드 & invoke({"topic": "고양이"})
```


---

## C.2 Google ADK

- **설명**: 다중 에이전트 오케스트레이션(순차·병렬 에이전트).
- **예**: `LlmAgent(model="gemini", tools=[google_search])`
- **강점**: 프로덕션·배포 중심.

---

## C.3 CrewAI

- **설명**: 역할·태스크·크루(순차/계층 워크플로).
- **강점**: 인간 팀 모방(페르소나·목표).
- **예**: `@crew(agents=self.agents, tasks=self.tasks, process=Process.sequential)`

---

## C.4 기타 프레임워크

| 프레임워크 | 초점 |
| :-- | :-- |
| **AutoGen (MS)** | 대화 협업 |
| **LlamaIndex** | RAG·데이터 검색 |
| **Haystack** | 검색 파이프라인(엔터프라이즈) |
| **MetaGPT** | SOP 기반 역할(소프트웨어 개발) |
| **SuperAGI** | 수명주기 관리(GUI) |
| **Semantic Kernel (MS)** | 플러그인·플래너(.NET/Python) |
| **Strand Agent (AWS)** | 가벼운 SDK(MCP 통합) |


---

## C.5 선택 가이드

- **선형**: LangChain
- **순환 에이전트**: LangGraph
- **팀 협업**: CrewAI·ADK
- **검색 중심**: LlamaIndex·Haystack

---

## C.6 핵심 정리

- **다양성**: 저수준(LangGraph) ~ 고수준(CrewAI).
- **추천**: 프로젝트 복잡도 따라 선택.
- **트렌드**: 상태·그래프·협업 ↑.
