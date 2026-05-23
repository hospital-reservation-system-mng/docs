# HMS 설계 결정 노트 (ADR)

HMS 프로젝트의 주요 설계 결정 기록입니다. 각 결정은 **맥락(왜 결정이 필요했는가) / 결정(무엇을 선택했는가) / 결과(어떤 트레이드오프를 받아들였는가)** 3부 구조로 작성됩니다.

포맷: [Michael Nygard ADR](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)

## 용어집

조직 프로필 README의 다이어그램과 ADR 본문에서 일관되게 쓰이는 컴포넌트 이름:

| 표기 | 의미 |
|---|---|
| Web Layer | Spring Boot 4 + Spring Security + Mustache SSR 묶음 |
| Service Layer | Reservation / Treatment / Inventory / LlmService |
| Data Layer | Spring Data JPA + MySQL/H2 + Redis |
| LLM Service | Java 측의 LLM 라우터 (Claude vs Python LLM 분기) |
| Python LLM | FastAPI + Qwen 2.5 + ChromaDB RAG |

## 인덱스

| # | 제목 | 상태 | 최종 갱신 |
|---|---|---|---|
| [0001](./0001-why-session-based-5-role.md) | Why Session-based 5-ROLE over OAuth/JWT? | Accepted | 2026-05-23 |
| [0002](./0002-why-hybrid-ai.md) | Why Hybrid AI (Claude + 자체 Qwen)? | Accepted | 2026-05-23 |
| [0003](./0003-why-mustache-ssr.md) | Why Mustache SSR over SPA? | Accepted | 2026-05-23 |

[↑ 조직 프로필로](https://github.com/proejct-team-alpha)
