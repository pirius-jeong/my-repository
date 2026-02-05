# 14장. Knowledge Retrieval (RAG, 지식 검색)

## 14.1 RAG 패턴 개요

- **정의**: LLM 응답 전에 **외부 지식 검색 → 프롬프트 증강**으로 정확성·시의성 강화하는 패턴이다.
- **왜 필요?** LLM 한계(정적 지식·환각): 최신·전문 데이터 통합 → **검증 가능 응답**.
- **흐름**: 쿼리 → 검색(의미론적) → 청크 증강 → LLM 생성 → 인용.

### 핵심 개념

| 개념 | 설명 | 예시 |
|------|------|------|
| **임베딩** | 텍스트 → 벡터(의미 공간 매핑) | "고양이" ≈ "새끼 고양이" |
| **청킹** | 문서 → 관리 가능 조각 | 섹션·단락 분할 |
| **벡터 DB** | 임베딩 저장·의미 검색 | Pinecone·Weaviate |
| **검색** | 키워드(BM25) + 의미(거리) 하이브리드 | FAISS·HNSW |

---

## 14.2 RAG 변형

| 변형 | 특징 | 용도 |
|------|------|------|
| **GraphRAG** | 지식 그래프(노드·에지) 활용 | 관계 복잡 질문(재무·유전자) |
| **Agentic RAG** | 에이전트 추론(검증·종합·도구) | 충돌 조정·다단계 쿼리 |

- **Agentic 장점**: 출처 우선순위·모순 해결·외부 도구(웹 검색).
- **과제**: 복잡성·비용·지연 ↑.

---

## 14.3 응용 사례

- **엔터프라이즈 Q&A**: 내부 문서·정책 검색.
- **고객 지원**: FAQ·매뉴얼 기반.
- **콘텐츠 추천**: 의미 유사 선호도.
- **뉴스 요약**: 실시간 피드 검색.

---

## 14.4 구현: Google ADK 예제

### 14.4.1 Google 검색 RAG

```python
search_agent = Agent(
    name="연구_보조원",
    instruction="주제 조사 시 Google 검색 도구 사용",
    tools=[google_search]
)
```


### 14.4.2 Vertex AI RAG

```python
memory_service = VertexAiRagMemoryService(
    rag_corpus="projects/.../ragCorpora/...",
    similarity_top_k=5,
    vector_distance_threshold=0.7
)
```


---

## 14.5 구현: LangChain + LangGraph 예제

- **전체 파이프라인**: 로드 → 청킹 → 임베딩(Weaviate) → 검색 → 생성.

```python
# 청킹·저장
chunks = text_splitter.split_documents(docs)
vectorstore = Weaviate.from_documents(chunks, OpenAIEmbeddings())

# 그래프 워크플로
workflow = StateGraph(RAGGraphState)
workflow.add_node("retrieve", retrieve_node)  # 벡터 검색
workflow.add_node("generate", generate_node)  # LLM + 프롬프트
app = workflow.compile()

# 실행
result = app.invoke({"question": "대통령 발언?"})
```


---

## 14.6 과제 및 팁

- **과제**: 청킹 품질·분산 정보·모순·비용·업데이트.
- **팁**: 하이브리드 검색·최신화 스케줄링·임계값 튜닝.

---

## 14.7 한눈에 보기

- **무엇**: **검색 → 증강 → 생성**으로 LLM 지식 확장.
- **왜**: 환각 ↓, 최신·도메인 지식 ↑, 인용 가능.
- **어떻게**: 임베딩·벡터 DB·Agentic/Graph 변형, ADK·LangChain 구현.
- **언제**: 사실 기반 Q\&A·지원·추천.

---

## 14.8 핵심 정리

- RAG는 **LLM을 실전 지식 도구로 승격**시킨다.
- 기본 + Graph/Agentic으로 복잡성 대응.
- 이전 패턴(도구·메모리·에이전트)과 결합 시 완벽.
