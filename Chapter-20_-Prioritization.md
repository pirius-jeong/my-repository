# 20장. Prioritization(우선순위 지정)

## 20.1 패턴 개요

- **정의**: 에이전트가 **중요·긴급·종속 기준으로 작업 순위화**하는 패턴.
- **왜 필요?** 다중 목표·제한 자원 → 효율·목표 달성.
- **수준**: 고수준(목표)·중간(하위 작업)·저수준(다음 행동).

### 기준 요소

| 기준 | 설명 |
|------|------|
| **긴급성** | 시간 민감도 |
| **중요성** | 목표 영향 |
| **종속성** | 전제 조건 |
| **리소스** | 가용성·비용 |
| **사용자 선호** | 개인화 |

---

## 20.2 응용 사례

| 도메인 | 예시 |
|--------|------|
| **고객 지원** | 중단 > 재설정, VIP 우선 |
| **클라우드** | 수요 시 중요 앱 자원 우선 |
| **자율주행** | 제동 > 차선 유지 |
| **금융** | 위험·이익 거래 우선 |
| **프로젝트** | 마감·종속 작업 |
| **보안** | 위협 심각도 경고 |
| **비서** | 마감·중요 일정 |

---

## 20.3 구현: LangChain 예제 (프로젝트 매니저)

- **TaskManager + PM 에이전트**: 생성·우선순위(P0-P2)·할당·목록.
```python
class Task(BaseModel):
    id: str
    description: str
    priority: Optional[str] = None  # P0, P1, P2
    assigned_to: Optional[str] = None

class SuperSimpleTaskManager:
    def create_task(self, description: str) -> Task:
        task_id = f"TASK-{self.next_task_id:03d}"
        new_task = Task(id=task_id, description=description)
        self.tasks[task_id] = new_task
        self.next_task_id += 1
        return new_task

pm_tools = [
    Tool(name="create_new_task", func=create_new_task_tool, args_schema=CreateTaskArgs),
    Tool(name="assign_priority_to_task", func=assign_priority_to_task_tool, args_schema=PriorityArgs),
    Tool(name="assign_task_to_worker", func=assign_task_to_worker_tool, args_schema=AssignWorkerArgs),
    Tool(name="list_all_tasks", func=task_manager.list_all_tasks)
]

pm_agent = create_react_agent(llm, pm_tools, pm_prompt_template)
pm_agent_executor = AgentExecutor(agent=pm_agent, tools=pm_tools, memory=ConversationBufferMemory())
```

- **프롬프트 지시**: 생성 → 우선·할당 → 목록 확인.

---

## 20.4 동적 재우선순위

- **트리거**: 신 이벤트·마감·상황 변화.
- **혜택**: 적응력 ↑.

---

## 20.5 한눈에 보기

- **무엇**: **기준 기반 순위화**(긴급·중요).
- **왜**: 자원 최적·지연 ↓.
- **어떻게**: LLM 평가·도구(생성·할당), LangChain 구현.
- **언제**: 다중 작업·제한 환경.

---

## 20.6 핵심 정리

- 우선순위는 **에이전트 효율 초석**.
- 동적 + 사용자 중심으로 실전.
- 전체 책 완성: 자율 에이전트 빌드 가이드.

