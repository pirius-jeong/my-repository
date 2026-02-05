# 15장. Inter-Agent Communication (A2A, 에이전트 간 통신)

## 15.1 A2A 패턴 개요

- **정의**: **다른 프레임워크 에이전트 간 표준화된 통신** 프로토콜(Google 개방 표준).
- **왜 필요?** 개별 에이전트 한계 → 협업(위임·조정·정보 공유).
- **지원**: LangGraph·CrewAI·ADK·Atlassian·Salesforce 등.

### 핵심 개념

| 개념 | 설명 |
|------|------|
| **배우** | 사용자·클라이언트 에이전트·서버 에이전트(불투명) |
| **Agent Card** | JSON ID(기능·URL·기술·인증·모드) |
| **발견** | URI·레지스트리·직접 구성 |
| **통신** | JSON-RPC 2.0 over HTTP(S) |
| **작업** | 비동기(제출·진행·완료) |

---

## 15.2 상호작용 메커니즘

| 방식 | 특징 | 용도 |
|------|------|------|
| **요청/응답** | 동기 단일 교환 | 빠른 작업 |
| **폴링** | 작업 ID 주기 확인 | 장기 작업 |
| **SSE** | 실시간 푸시 | 증분 결과 |
| **웹훅** | 비동기 알림 | 리소스 절약 |

- **메시지**: 속성(메타) + 부분(텍스트·파일·JSON).
- **아티팩트**: 출력(스트리밍 가능).

---

## 15.3 A2A vs MCP

| A2A | MCP |
|----|----|----|
| 에이전트 ↔ 에이전트(고수준 위임) | LLM ↔ 도구/데이터(저수준) |

---

## 15.4 응용 사례

- **다중 프레임워크**: ADK + LangChain 협업.
- **워크플로**: 데이터 수집 → 분석 → 보고 위임.
- **동적 검색**: 실시간 정보 교환.

---

## 15.5 구현: Google ADK 예제 (캘린더 에이전트)

```python
# AgentCard 정의
agent_card = AgentCard(
    name="캘린더 에이전트",
    skills=[AgentSkill(id="check_availability", ...)],
    url="http://host:port/"
)

# ADK 에이전트
adk_agent = create_agent(client_id, client_secret)  # CalendarToolset

# A2A 앱
a2a_app = A2AStarletteApplication(agent_card, request_handler)
uvicorn.run(app, host=host, port=port)
```

- **흐름**: AgentCard 게시 → 클라이언트 발견 → sendTask (e.g. "내일 10시 가용?").

---

## 15.6 보안

- **mTLS·TLS**: 암호화·인증.
- **감사 로그**: 추적·디버깅.
- **인증**: OAuth·API 키(헤더).

---

## 15.7 한눈에 보기

- **무엇**: **A2A 프로토콜**로 에이전트 상호 운용.
- **왜**: 협업 없인 복잡 문제 한계 → 모듈식 시스템.
- **어떻게**: AgentCard·JSON-RPC·SSE/폴링, ADK 구현.
- **언제**: 다중 에이전트 워크플로.

---

## 15.8 핵심 정리

- A2A는 **다중 에이전트 생태계 표준**이다.
- AgentCard + 비동기 작업으로 확장성 ↑.
- MCP 보완: 에이전트 협업 완성.
