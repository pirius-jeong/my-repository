# 19장. Evaluation & Monitoring(평가 및 모니터링)

## 19.1 패턴 개요

- **정의**: 에이전트 **성과·효율·준수 지속 측정** + 피드백 루프 시스템.
- **왜 필요?** 확률적 에이전트: 드리프트·이상 발생 → **신뢰·개선 보장**.
- **범위**: 목표 달성(11장) + 외부 측정(정확도·지연).

### 핵심 지표

| 지표 | 설명 |
|------|------|
| **정확도** | 응답 vs 기준(의미 유사도) |
| **지연** | 처리 시간 모니터링 |
| **토큰** | 비용 추적(입력·출력) |
| **궤적** | 단계 순서·효율 평가 |

---

## 19.2 응용 사례

| 사례 | 목적 |
|------|------|
| **라이브 추적** | 챗봇 해결률·응답 시간 |
| **A/B 테스트** | 전략 비교(계획 알고리즘) |
| **감사** | 윤리·규제 준수 |
| **드리프트** | 성능 저하 감지 |
| **이상** | 오류·공격 식별 |

---

## 19.3 구현: ADK 평가

- **방식**: 웹 UI(adk web)·pytest·CLI(adk eval).
```python
# pytest 통합
def test_agent():
    evaluator = AgentEvaluator()
    results = evaluator.evaluate(agent_module="my_agent", test_files=["tests/*.json"])

# 테스트 파일: JSON (턴: 쿼리·궤적·기대 응답)
# Evalset: 다중 세션 통합 테스트
```

- **궤적 비교**: 정확·순서·임의 일치·정밀도·재현율.

---

## 19.4 구현: 응답 정확도 예제

```python
def evaluate_response_accuracy(agent_output: str, expected: str) -> float:
    # 기본 문자열 비교 (개선: 의미 유사도·LLM Judge)
    return 1.0 if agent_output.strip().lower() == expected.strip().lower() else 0.0
```


---

## 19.5 구현: 토큰 모니터링

```python
class LLMInteractionMonitor:
    def record_interaction(self, prompt: str, response: str):
        input_tokens = len(prompt.split())  # 실제: 토크나이저
        output_tokens = len(response.split())
        self.total_input_tokens += input_tokens
        self.total_output_tokens += output_tokens
```


---

## 19.6 LLM-as-a-Judge

- **법률 설문 평가**: 명확성·중립·관련성 등 1-5점 JSON.

```python
judge = LLMJudgeForLegalSurvey(model="gemini-1.5-flash")
result = judge.judge_survey_question("질문?")
# {"overall_score": 4, "detailed_feedback": {...}}
```


---

## 19.7 계약자 모델

- **진화**: 프롬프트 → 계약(명세·협상·검증·하도급).
- **혜택**: 모호성 ↓, 신뢰 ↑, 미션 크리티컬 적합.

---

## 19.8 한눈에 보기

- **무엇**: **지표·궤적·LLM Judge + 계약**.
- **왜**: 프로덕션 신뢰성.
- **어떻게**: ADK evalset·pytest·모니터링 클래스.
- **언제**: 배포·A/B·감사.

---

## 19.9 핵심 정리

- 평가/모니터링은 **에이전트 지속 운영** 기반.
- 궤적 + LLM Judge로 정교화.
- 계약자 → 자율성 + 책임 완성.

