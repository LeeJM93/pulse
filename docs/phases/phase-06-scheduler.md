# Phase 6 — 일정·중복 방지·복구

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-04, FR-06, FR-10, FR-16~FR-17, AC-14~AC-17, AC-35.
- 선행 조건: 수집·생성 실행 경계와 상태 저장. 실제 계정 없이 가상 시각과 대역으로 검증할 수 있다..
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 기능명세 FR-16~FR-17과 현재 collection/generation 상태 모델을 먼저 확인하라.

이번 단계은 장중 실행 orchestration을 구현하는 것이다.

구현 대상:

- trading calendar abstraction
- date-specific override
- session model
- scheduled `as_of`
- collection scheduling
- briefing scheduling
- closing summary
- grace period
- run unique key
- run lock
- duplicate prevention
- AI generation state
- restart behavior
- missed-run handling
- daily AI-call budget

현재 wall clock에 강하게 결합하지 말고 fixed/fake clock으로 테스트할 수 있게 한다.

단순 월~금만으로 거래일을 판정하지 않는다.

같은 회차를 두 번 실행해도 중복 브리핑이나 중복 AI 호출을 만들지 않는다.

이미 생성된 결과의 deployment retry 때문에 AI를 다시 호출하지 않는다.

외부 AI 호출 중 프로세스가 종료되어 결과를 알 수 없는 경우 명세의 `generation_unknown` 의미를 보존하고 자동 재호출하지 않는다.

재시작 후 지나간 모든 브리핑을 대량 생성하지 않는다.

다음을 우선 검증한다.

- AC-14
- AC-15
- AC-16
- AC-17

fixture/fake clock을 사용해서 deterministic하게 테스트한다.

완료 후 formatter, lint, type check와 전체 tests를 실행하고 보고하라.

확인되지 않은 거래일은 실시간 자동 게시를 중단한다. 수집은 AI 생성·배포와 독립적으로 계속되고 외부 I/O 중 DB 쓰기 잠금을 유지하지 않아야 한다. AC-35의 현물·선물 세션 차이와 종료 이후 입력 제외도 일정 경로에서 검증한다.
