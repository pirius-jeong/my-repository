# 10장. Model Context Protocol (MCP)

## 10.1 MCP 패턴 개요

- **정의**: LLM이 **외부 리소스·도구·데이터와 표준화된 인터페이스로 상호작용**하는 개방형 프로토콜이다.
- **목적**: LLM 기능 확장(텍스트 생성 → 외부 작업 실행), **상호 운용성** 확보(Gemini·GPT·Claude 등 호환).
- **아키텍처**: **클라이언트-서버** 모델.  
  - **클라이언트**: LLM 호스트(에이전트·앱).  
  - **서버**: 도구·리소스·프롬프트 노출.

### 핵심 구성 요소

- **리소스**: 정적 데이터(PDF·DB 레코드).
- **도구**: 실행 기능(API 쿼리·이메일 전송).
- **프롬프트**: LLM 상호작용 템플릿.

---

## 10.2 MCP vs 도구 함수 호출

| 특징 | 도구 함수 호출 | MCP |
|------|----------------|-----|
| **표준화** | 독점적(공급자별 다름) | 개방 표준(모든 LLM 호환) |
| **범위** | 특정 기능 직접 호출 | 동적 발견·통신 프레임워크 |
| **아키텍처** | LLM ↔ 앱 1:1 | 클라이언트 ↔ 다중 서버 |
| **발견** | 미리 설명 | 동적 쿼리(매니페스트) |
| **재사용성** | 앱·LLM 결합 | 독립 MCP 서버 재사용 |

- **비유**: 함수 호출 = 특정 도구 세트 / MCP = 표준 콘센트(호환 도구 무한).

---

## 10.3 상호작용 흐름

1. **발견**: 클라이언트 → 서버 쿼리 → 매니페스트(도구·리소스 목록).
2. **요청**: LLM → 도구·인수 지정.
3. **실행**: 서버 → 외부 시스템 연동(인증·유효성).
4. **응답**: 서버 → 클라이언트 → LLM 컨텍스트 업데이트.

---

## 10.4 응용 사례

- **DB 통합**: BigQuery 쿼리·보고서 생성(MCP Toolbox).
- **생성 미디어**: Imagen(이미지)·Veo(비디오)·Chirp(음성)·Lyria(음악).
- **외부 API**: 날씨·주가·이메일·CRM.
- **정보 추출**: LLM 추론으로 정확 절 반환.
- **맞춤 도구**: 내부 시스템 MCP 서버화.
- **워크플로**: DB 검색 → 이미지 생성 → 이메일 발송.
- **IoT 제어**: 스마트 기기 명령.
- **금융 자동화**: 시장 분석·거래·보고서.

---

## 10.5 구현: Google ADK + MCP

### 10.5.1 MCPToolset 설정

- **파일 시스템 예**:
```python
from google.adk.tools.mcp_tool.mcp_toolset import MCPToolset, StdioServerParameters

target_folder = "/path/to/folder"  # 절대 경로

agent = LlmAgent(
    tools=[MCPToolset(
        connection_params=StdioServerParameters(
            command="npx", args=["@modelcontextprotocol/server-filesystem", target_folder]
        )
    )]
)
```

- **실행**: `adk web` → 에이전트 선택 → "폴더 내용 보여줘" 등 테스트.


### 10.5.2 다른 연결(HTTP·Python·UVX)

- **HTTP (FastMCP 서버)**: `HttpServerParameters(url="http://localhost:8000")`
- **Python**: `command="python3", args=["mcp_server.py"]`
- **UVX**: `command="uvx", args=["mcp-google-sheets@latest"]`

---

## 10.6 구현: FastMCP (Python 프레임워크)

### 10.6.1 서버 생성

```python
from fastmcp import FastMCP

mcp = FastMCP()

@mcp.tool
def greet(name: str) -> str:
    """개인화된 인사말 생성"""
    return f"안녕하세요, {name}님!"

if __name__ == "__main__":
    mcp.run(transport="http", host="127.0.0.1", port=8000)
```

- **자동**: 타입·docstring → 스키마 생성.


### 10.6.2 클라이언트(ADK 에이전트)

```python
agent = LlmAgent(
    tools=[MCPToolset(
        connection_params=HttpServerParameters(url="http://localhost:8000"),
        tool_filter=["greet"]
    )]
)
```

- **테스트**: 서버 실행 → `adk web` → "John Doe 인사해".

---

## 10.7 고려사항

- **API 설계**: 에이전트 친화적(필터·정렬·텍스트 형식).
- **보안**: 인증·권한, 에이전트 이해 가능 데이터(PDF X, Markdown O).
- **오류 처리**: 실패 시 LLM 전달·재시도.
- **로컬 vs 원격**: 속도·공유 고려.
- **전송**: STDIO(로컬 JSON-RPC), HTTP/SSE(원격).

---

## 10.8 한눈에 보기

- **무엇**: **LLM-외부 표준 인터페이스**로 도구·데이터 동적 접근.
- **왜**: 독점 통합 복잡성 → MCP로 상호 운용·재사용 생태계.
- **어떻게**: 클라이언트-서버(발견→요청→실행→응답), ADK·FastMCP 구현.
- **언제**: 다중 도구·LLM·시스템 복잡 워크플로 시.

---

## 10.9 핵심 정리

- MCP는 **LLM을 진정한 에이전트로 만드는 표준 게이트웨이**다.
- 함수 호출 보완: 동적·호환·확장성 ↑.
- ADK·FastMCP로 즉시 구현 가능, 엔터프라이즈 워크플로 혁신.
