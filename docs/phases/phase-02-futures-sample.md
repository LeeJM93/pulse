# Phase 2 — 선물 샘플

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-23~FR-28, AC-26~AC-36.
- 선행 조건: Phase 0+1의 샘플 실행·저장·기본 브리핑·웹 경로.
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`와 `장중_시황_브리핑_프로그램_기능명세.md`를 다시 확인하고 현재 구현 상태를 먼저 점검하라.

Phase 1의 sample vertical slice가 정상 동작하는 것을 확인한 뒤 이번 단계에서는 **fixture 기반 코스피200 선물 경로**를 구현한다.

실제 선물 API는 아직 연결하지 않는다.

구현 대상:

- futures contract model
- 실제 contract ID와 최근월물/차근월물 role 분리
- futures price observations
- volume
- open interest
- investor flow
- spot KOSPI200 index observation
- basis
- 5분/15분 비교
- timestamp alignment
- price/OI observation state
- spot/futures combined observation state
- front/next month OI comparison
- rollover-related safeguards

명세의 FR-23~FR-28을 기준으로 구현한다.

특히 서로 다른 계약의 가격/OI를 연결해서 변화량을 계산하지 않는다.

OI와 투자자 수급만으로 신규 롱·신규 숏·숏커버·롱청산을 추론하지 않는다.

fixture를 사용하여 우선 다음 acceptance criteria를 자동 테스트로 구현한다.

- AC-26
- AC-27
- AC-28
- AC-29
- AC-30
- AC-31
- AC-32
- AC-33
- AC-34
- AC-35

AC-36의 샘플·기본 브리핑·정적 추이 부분도 구현한다. 가격·OI·수급의 추이는 단위별 패널과 수치표를 제공하고 결측 구간을 연결하거나 0으로 채우지 않는다. 실제 공급자 장애 검증은 별도 상태로 남긴다.

FR-26~FR-27의 중립·정확한 경계값·자료 부족 상태를 검증한다. 전일 기준과 당일 첫 유효 관측값 비교, 최근월물·차근월물 OI 합계와 월물 이전 관찰을 보존한다. 방향과 데이터 품질을 서로 다른 상태로 관리한다.

완료 후 formatter, lint, type check, 전체 tests와 demo를 실행하고 상태를 보고하라.

실제 선물 API가 연결되지 않은 상태를 `선물 기능 연결 완료`라고 표현하지 마라.
