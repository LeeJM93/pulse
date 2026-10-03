# Phase 8 — 장중 운영 검증

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: 전체 명세, 특히 AC-24~AC-25, AC-36.
- 선행 조건: 실제 거래일, 설정된 현물·선물·AI·게시 환경과 앞 단계의 관련 검증 근거.
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 전체 기능명세, `docs/data_mapping.md`, `docs/implementation_status.md`와 현재 운영 로그를 먼저 확인하라.

이번 단계의 목적은 새로운 기능을 크게 추가하는 것이 아니라 **실제 한 거래 세션에서 시스템의 운영 가정을 검증하는 것**이다.

가능한 실제 환경에서 다음을 관찰하고 기록한다.

- 현물 API 안정성
- 실제 데이터 갱신 주기
- rate-limit usage
- 누락 및 지연 데이터
- source time / observed time 차이
- 투자자 수급 갱신 주기
- 선물 가격 갱신
- 장중 OI 갱신
- 투자자 선물 수급 갱신
- 현물/선물 timestamp alignment
- 최근월/차근월 계약 처리
- Codex 실행 성공률과 fallback
- publication success/failure
- restart/recovery
- resource usage

관찰 결과와 기능명세의 초기 default가 다르면 코드를 임의로 숨겨서 맞추지 말고 실제 결과를 기록하고 configurable default 조정안을 제시한다.

검증하지 못한 외부 동작은 그대로 미검증 상태로 남긴다.

AC-24, AC-25, AC-36을 포함하여 실제 장중 환경이 필요한 acceptance criteria를 점검한다.

필요한 버그 수정은 수행하되 unrelated refactoring이나 P1 기능 추가는 하지 않는다.

마지막에 `docs/implementation_status.md`를 실제 검증 결과로 갱신한다.

`장중 운영 확인`은 실제 장중 검증이 완료된 경우에만 완료 처리한다.

완료 후 전체 quality gates를 실행하고 다음을 구분하여 보고하라.

- automated verification
- actual API verification
- actual Codex verification
- actual deployment verification
- operational verification
- remaining gaps
