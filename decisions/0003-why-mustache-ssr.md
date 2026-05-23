# ADR-0003 · Why Mustache SSR over SPA?

- **Status**: Accepted
- **Date**: 2026-03-02
- **Last updated**: 2026-05-23
- **Decision driver**: 책임개발자 (김민구)

## Context

HMS는 내부 직원용 시스템으로 다음 특성을 가진다:

- SEO 불필요 (사내 인증 후 접근)
- 화면 40개+ · Mustache 템플릿 74개를 4주 안에 찍어내야 함
- 4인 팀 중 백엔드 비중이 큼 (Spring 경험 풍부, 모던 프론트 경험 차이 있음)
- 클라이언트 인터랙션은 폼 입력 + 기본 차트 정도. 실시간 알림·복잡한 상태관리 요구 없음

대안 검토:
1. **Mustache SSR (Spring Boot 표준)**: 백엔드와 동일 빌드 파이프라인. 학습 곡선 낮음.
2. **Thymeleaf**: 더 풍부한 표현식. 다만 팀 친숙도는 비슷.
3. **React/Vue SPA + REST API**: 클라이언트 UX 최상. 그러나 빌드·배포·CORS·인증 동기화 등 추가 복잡도.
4. **HTMX**: SSR과 SPA의 중간 지점. 모던하지만 팀 학습 비용.

## Decision

**Spring Boot + Mustache SSR**을 채택하고, 부분 비동기 영역에 한해 Vanilla JS로 보완한다.

- Mustache 템플릿 + Tailwind CSS 4 + Lucide Icons + Chart.js + Flatpickr
- 예약 폼·자동완성 같은 동적 영역만 Vanilla JS로 fetch + DOM 갱신
- 별도 프론트엔드 빌드 파이프라인 없음 (npm은 Tailwind 컴파일용)

## Consequences

**받아들이는 이점**
- 백엔드와 템플릿이 같은 리포·같은 빌드에 묶여 일관성 ↑. PR 하나로 백엔드 + 화면 동시 변경 가능.
- 디버깅이 단순 (요청-응답-렌더가 한 프로세스 안에서 완결).
- 4주 일정 안에 화면 40+개 찍어내기 가능 (실측: 평균 화면당 0.5일).
- 팀 내 프론트엔드 학습 곡선 비용 0.

**받아들이는 비용**
- 클라이언트 인터랙션이 풍부한 영역(예: 실시간 차트, 드래그앤드롭)은 자연스럽지 않아 추후 SPA로 마이그레이션 비용 발생 가능.
- 페이지 전환이 풀 페이지 리로드라 모바일 환경에서 체감 속도가 SPA만큼 빠르지 않음 (내부 직원 PC 사용이라 영향 적음).
- API-first 설계가 강제되지 않으므로, 외부 통합(예: 모바일 앱) 시점에 별도 REST API 레이어를 분리해야 함.

## Links

- 코드: `hms/src/main/resources/templates/**`
- 관련 ADR: ADR-0001 (Spring Security와 동일한 세션 모델 위에서 동작)
