# Phase 4 — 선물 공급자 연동

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-23~FR-28, AC-25.
- 선행 조건: Phase 2의 선물 모델·계산·품질 처리.
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 기능명세 FR-23~FR-28, 현재 futures sample implementation, data mapping과 implementation status를 먼저 확인하라.

이번 단계은 실제 `FuturesDataProvider`를 연결하고 검증하는 단계다.

공급자는 기능명세에 기록된 후보와 현재 공식 문서를 출발점으로 검토하되 실제 제공 여부를 다시 확인한다.

다음을 각각 독립적으로 검증한다.

- 표준 코스피200 계약 master
- 최근월물/차근월물 선택에 필요한 정보
- 계약별 가격
- 거래량
- 장중 OI
- OI 기준 시각과 갱신 방식
- 투자자별 수급
- 투자자 수급의 월물 집계 범위
- session 범위
- timestamp semantics
- rate limit
- 권한 및 인증 조건

특히 다음 상태를 별도로 기록한다.

- 가격 연결
- 장중 OI 연결
- 투자자 수급 연결
- 월물 정합성 확인
- 시각 정합성 확인

현재가만 조회됐다고 전체 선물 기능을 완료 처리하지 않는다.

전일 OI만 확인된 경우 장중 OI가 연결됐다고 표시하지 않는다.

API 호출 빈도와 데이터 갱신 빈도를 혼동하지 않는다.

공급자 미설정, 권한 부족, 실제 제공 불가를 서로 구분한다.

실제 API가 확인되지 않는 부분은 fixture path를 유지하고 정확한 상태를 문서화한다.

완료 후 formatter, lint, type check, tests 및 가능한 integration verification을 실행하고 결과를 보고하라.

AC-25의 실제 공급자 응답·단위·범위 확인 결과를 기록한다. 장중 OI·수급의 실제 갱신 검증이 남으면 Phase 8까지 이어서 확인하고 AC-25 전체 통과로 표시하지 마라.
