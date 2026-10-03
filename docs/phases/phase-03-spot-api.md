# Phase 3 — 키움 현물 연동

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-01~FR-08, AC-02.
- 선행 조건: Phase 0+1의 수집 경계·정규화·저장·품질 처리.
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 기능명세, `docs/data_mapping.md`, `docs/implementation_status.md`와 현재 collector 구현을 먼저 확인하라.

이번 단계의 목적은 **키움 REST API를 이용한 현물 데이터 integration**이다.

fixture/sample path를 깨지 말고 실제 공급자 구현을 별도 경계로 추가한다.

기능명세 FR-01~FR-04를 우선 기준으로 한다.

실제 공식 문서와 실제 응답을 확인하여 다음을 검증한다.

- authentication
- request parameters
- response fields
- units
- timestamps
- market/venue scope
- pagination
- rate limits
- update behavior
- error representation

명세에 적힌 API 이름만 보고 응답 필드를 추측하지 마라.

실제 credential 또는 API 이용 조건이 준비되어 있지 않으면 구현 가능한 adapter와 verification tooling까지만 진행하고 `blocked/not configured`로 기록한다.

fixture 결과를 actual integration verified로 승격하지 않는다.

실제 응답을 확인한 항목만 `docs/data_mapping.md`에서 `제공 확인`으로 변경한다.

현물 공급자 장애가 sample path 또는 향후 선물 수집을 중단시키지 않도록 한다.

가능한 자동 테스트와 필요한 integration verification을 수행한다.

완료 후 formatter, lint, type check, tests를 실행하고 구현/실제연동 확인 상태를 분리해서 보고하라.
