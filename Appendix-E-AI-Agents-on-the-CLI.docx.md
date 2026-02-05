# 부록 E. AI Agents on the CLI(CLI의 AI 에이전트)

## E.1 소개

- **변화**: 터미널 = **자연어·코드베이스 컨텍스트·자동화**.
- **주요 4개**: Claude CLI·Gemini CLI·Aider·GitHub Copilot CLI.
- **벤치마크**: Terminal-Bench(80 작업, Terminus 에이전트).

---

## E.2 Claude CLI (Claude Code)

- **강점**: **대규모 리팩토링·전체 아키텍처 이해** (멘탈 모델).
- **사례**:
  - JWT 리팩토링(전체 코드).
  - API 통합(서비스·컴포넌트).
  - 문서 생성(TSDoc).
- **특징**: Git 통합·MCP 확장.

---

## E.3 Gemini CLI (Gemini 2.5 Pro)

- **강점**: **오픈소스·멀티모달·Google Cloud** (Reason & Act).
- **사례**:
  - 이미지 → React 컴포넌트.
  - GKE 클러스터 업그레이드.
  - HR API 도구(MCP).
  - Java 로깅 리팩토링.
- **특징**: 파일·셸·웹 도구, 샌드박스.

---

## E.4 Aider

- **강점**: **직접 편집·Git 자동 커밋** (모델 무관).
- **사례**:
  - TDD(테스트 작성·통과).
  - 버그 수정·테스트 확인.
  - 종속성 업데이트(요구사항.txt).
- **특징**: 페어 프로그래머, 효율·추적.

---

## E.5 GitHub Copilot CLI

- **강점**: **GitHub 통합** (이슈→PR).
- **사례**:
  - 자동 버그 PR.
  - 저장소 Q&A.
  - 셸 명령 생성(gh).

---

## E.6 선택 가이드

| 필요 | 추천 |
|------|------|
| **대규모 리팩토링** | Claude |
| **멀티모달·클라우드** | Gemini |
| **직접 Git** | Aider |
| **GitHub 워크플로** | Copilot CLI |

---

## E.7 핵심 정리

- **트렌드**: CLI = **지능 워크스페이스**.
- **미래**: 개발 패러다임 변화.
- **측정**: Terminal-Bench 사용.
