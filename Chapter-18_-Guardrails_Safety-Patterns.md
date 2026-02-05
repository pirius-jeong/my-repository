# 18장. Guardrails & Safety Patterns(가드레일/안전 패턴)

## 18.1 패턴 개요

- **정의**: 에이전트의 **해롭거나 편향·탈옥·비윤리 출력 방지** 다층 보호 시스템.
- **왜 필요?** 자율성 ↑ → 위험(탈옥·편향·법적) → **신뢰·준법 보장**.
- **목적**: 역량 제한 NO, 안전 안내 YES.

### 구현 계층

| 계층 | 기법 |
|------|------|
| **입력** | 검증·정화·조정 API |
| **출력** | 필터링·후처리(독성·편향) |
| **행동** | 프롬프트 지시·도구 제한 |
| **감독** | 인간 참여(HITL) |

---

## 18.2 응용 사례

| 도메인 | 가드레일 역할 |
|--------|---------------|
| **챗봇** | 공격 언어·잘못 조언 차단 |
| **콘텐츠 생성** | 증오·허위·노골 필터 |
| **교육** | 편향·부적절 대화 방지 |
| **법률** | 자문 대신 전문가 안내 |
| **HR** | 차별 기준 제거 |
| **소셜 미디어** | 증오·허위 표시 |

---

## 18.3 CrewAI 구현 예제

- **정책 집행 에이전트**: 입력 사전 검토(탈옥·유해·오프토픽).
```python
policy_enforcer_agent = Agent(
    role="AI 콘텐츠 정책 집행자",
    llm=LLM(model="gemini-2.0-flash")  # 저비용
)

evaluate_input_task = Task(
    description=SAFETY_GUARDRAIL_PROMPT + "{{user_input}}",
    agent=policy_enforcer_agent,
    output_pydantic=PolicyEvaluation,  # Pydantic 검증
    guardrail=validate_policy_evaluation
)

crew = Crew(agents=[policy_enforcer_agent], tasks=[evaluate_input_task])
```

- **정책 예**: 탈옥·혐오·위험·욕설·정치·학업 부정·경쟁 논의 금지.

---

## 18.4 Vertex AI 구현 예제

```python
def validate_tool_params(tool, args, tool_context):
    expected_user_id = tool_context.state.get("session_user_id")
    if args.get("user_id_param") != expected_user_id:
        return {"status": "error", "error_message": "User ID mismatch"}
    return None  # 허용

root_agent = Agent(
    before_tool_callback=validate_tool_params,
    tools=[...]
)
```


---

## 18.5 신뢰 엔지니어링

- **체크포인트·롤백**: 상태 트랜잭션.
- **모듈성**: 관심 분리·최소 권한.
- **로깅**: 전체 추적(생각 사슬).
- **테스트**: 위험 평가·적대 훈련.

---

## 18.6 한눈에 보기

- **무엇**: **다층 가드레일**(입력·출력·행동).
- **왜**: 탈옥·편향·법적 위험 ↓.
- **어떻게**: CrewAI 정책 에이전트·Pydantic·콜백, ADK 구현.
- **언제**: 고객 대면·콘텐츠·HR·법률.

---

## 18.7 핵심 정리

- 가드레일은 **책임 AI 초석**.
- 계층적 + 적응형으로 완벽 보호.
- 전체 책 시리즈 마무리(자율성 + 안전).
