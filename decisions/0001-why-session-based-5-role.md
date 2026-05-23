# ADR-0001 · Why Session-based 5-ROLE over OAuth/JWT?

- **Status**: Accepted
- **Date**: 2026-03-01
- **Last updated**: 2026-05-23
- **Decision driver**: 책임개발자 (김민구)

## Context

HMS는 단일 병원 내부 직원 + 비회원 환자가 사용하는 사내 시스템이다. 사용자 경계는 다음 6종으로 명확하다:

- 비회원 환자 (Anonymous)
- ADMIN · STAFF · DOCTOR · NURSE · ITEM_MANAGER (5 ROLE)

요구사항:
- URL 패턴별 권한 매트릭스가 복잡 (예: `/admin/**`는 ADMIN만, `/doctor/treatment/**`는 DOCTOR만, `/llm/qna`는 DOCTOR · NURSE 둘 다)
- 외부 IDP(Google · 카카오 등) 연동 요구사항 없음
- 4주 일정 안에 정상 작동 + 테스트 가능해야 함
- 사용자당 동시 접속 1세션 가정 가능 (병원 직원이 PC 1대씩 사용)

대안 검토:
1. **Session-based + 5 ROLE**: Spring Security 표준. `SecurityFilterChain`에 권한 매트릭스만 적으면 됨.
2. **JWT (Stateless)**: 확장성 좋음. 다만 로그아웃 즉시성·세션 무효화 처리에 별도 블랙리스트 필요.
3. **OAuth2 / OIDC**: 외부 IDP 연동 시 강력. 그러나 요구사항에 없음.

## Decision

**Spring Security 세션 기반 인증 + 5 ROLE 권한**을 채택한다.

- `SecurityConfig`에 URL 패턴별 권한 매트릭스를 선언적으로 표현
- `JSESSIONID` 쿠키 + Redis 세션 저장소 (수평 확장 시 세션 공유)
- 비밀번호는 BCrypt, 로그인 실패 잠금은 5회 / 10분

## Consequences

**받아들이는 이점**
- 4주 일정 내 안정적 구현. Spring Security가 표준 패턴을 제공해 학습 곡선 최소.
- 복잡한 권한 매트릭스를 `.antMatchers().hasRole()` 체이닝으로 한 곳에 모음 → 코드 리뷰가 쉬움.
- 세션 무효화·로그아웃 즉시성 등 운영 요구사항이 무료로 해결됨.

**받아들이는 비용**
- 외부 시스템 연동(예: 보험사 API)이 필요해지면 별도 어댑터를 만들어야 함.
- 수평 확장 시 세션 저장소 분리가 강제됨. Redis로 1차 완화했으나, 멀티 인스턴스 운영 시점에는 별도 검토 필요.
- 모바일 앱이 추가되면 JWT로의 전환 또는 하이브리드 인증을 다시 설계해야 함 (해당 요구사항은 본 프로젝트 범위 밖).

## Links

- 코드: `hms/src/main/java/.../config/SecurityConfig.java`
- 관련 ADR: ADR-0002 (LlmService의 ROLE 분기와 연동)
