# 16장. Resource-Aware Optimization(리소스 인식 최적화)

## 16.1 패턴 개요

- **정의**: 에이전트가 **연산·시간·비용 자원 동적 관리**로 예산 내 목표 달성하는 패턴이다.
- **왜 필요?** LLM 비용·지연 문제 → **효율성 최적화**(품질 vs 자원 균형).
- **비유**: 예산 내 여행 계획(저비용 vs 고급).

### 핵심 전략

| 전략 | 설명 |
|------|------|
| **동적 모델 선택** | 쿼리 복잡도 따라 LLM 라우팅(Flash vs Pro) |
| **폴백** | 주요 모델 실패 시 저비용 대체 |
| **라우터 에이전트** | 쿼리 분류 → 적합 모델 할당 |
| **Critique Agent** | 응답 평가·피드백 → 개선 |

---

## 16.2 응용 사례

| 사례 | 최적화 |
|------|--------|
| **비용 LLM** | 복잡: Pro / 단순: Flash |
| **실시간** | 빠른 경로 우선 |
| **에너지** | 에지: 저전력 모델 |
| **안정성** | 백업 모델 전환 |
| **데이터** | 요약 검색 |
| **작업 할당** | 부하 균형 |

---

## 16.3 구현: Google ADK 예제

```python
# 에이전트 정의 (다른 모델)
gemini_pro_agent = Agent(model="gemini-2.5-pro")  # 복잡
gemini_flash_agent = Agent(model="gemini-2.5-flash")  # 단순

# 라우터 에이전트
class QueryRouterAgent(BaseAgent):
    async def _run_async_impl(self, context):
        query_len = len(context.current_message.text.split())
        if query_len < 20:
            response = await gemini_flash_agent.run_async(...)  # 저비용
        else:
            response = await gemini_pro_agent.run_async(...)  # 고성능
```


---

## 16.4 구현: OpenAI + Google 검색 예제

- **분류 → 모델 + 검색**: 단순(GPT-4o-mini) / 추론(o1-mini) / 검색(GPT-4o + Google).

```python
def handle_prompt(prompt: str) -> dict:
    classification = classify_prompt(prompt)  # LLM 분류
    search_results = google_search(prompt) if classification["category"] == "internet_search" else None
    response, model = generate_response(prompt, classification["category"], search_results)
    return {"classification": classification["category"], "response": response, "model": model}
```


---

## 16.5 고급 기술

- **적응 도구 선택**: 비용·지연 고려.
- **컨텍스트 요약**: 토큰 절약.
- **자원 예측**: 사전 할당.
- **비용 탐색**: 통신 최적.
- **에너지 효율**: 에지 최적화.
- **학습 정책**: 피드백 기반 적응.
- **우아한 저하**: 제약 시 폴백.

---

## 16.6 OpenRouter 통합

- **자동 선택**: `model="openrouter/auto"`
- **폴백**: `model=["claude-3.5-sonnet", "mythomax"]`
- **리더보드**: 토큰 생산량 순위.

---

## 16.7 한눈에 보기

- **무엇**: **자원 동적 관리**(모델·도구·할당).
- **왜**: 비용·지연 ↓, 품질 유지.
- **어떻게**: 라우터 + Critique + 폴백, ADK·OpenAI 구현.
- **언제**: 예산·실시간·에지 환경.

---

## 16.8 핵심 정리

- 자원 인식 최적화는 **지속 가능 AI** 필수.
- 라우터·Critique로 적응성 ↑.
- 다중 에이전트 + 이전 패턴 결합 완성.
