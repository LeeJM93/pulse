# Phase 7 — 정적 사이트 게시

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-18~FR-22, AC-18~AC-23.
- 선행 조건: 정적 사이트 생성과 검증된 브리핑·게시 상태 모델.
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 기능명세 FR-18~FR-22, 현재 static site builder와 publication model을 먼저 확인하라.

이번 단계의 목적은 완성된 정적 사이트를 안전하게 게시하는 것이다.

먼저 local production build 검증을 완성한다.

다음 경계를 사용하거나 동등한 구조를 유지한다.

```text
publish(directory) -> deployment_result
```

구현 대상:

- complete site build
- latest page
- day pages
- briefing permalink pages
- archive
- publication candidate selection
- public-directory inspection
- deployment adapter
- deployment result persistence
- remote verification
- stale publication protection
- failure/retry behavior

새 브리핑만 들어 있는 폴더를 게시하여 과거 기록을 없애지 않는다.

오래된 pending publication을 재시도하더라도 latest page가 과거로 되돌아가지 않게 한다.

배포 성공 응답과 운영 URL의 briefing ID·본문을 확인한 경우에만 `published`로 기록한다. 원격 확인을 실행하지 못했거나 실패했으면 미검증 상태로 둔다.

배포 실패 시 마지막 성공 사이트를 유지한다.

`dist/`의 secret/private 파일 검사를 자동화한다.

실제 Cloudflare Pages 계정이나 credential이 없다면 local publisher/build 검증까지 완료하고 external deployment를 blocked로 기록한다.

가능하면 다음 AC를 검증한다.

- AC-18
- AC-19
- AC-20
- AC-21
- AC-22
- AC-23

실제 웹 게시가 검증된 경우에만 `웹 게시 확인`을 완료 처리한다.

완료 후 formatter, lint, type check, tests와 가능한 deployment verification 결과를 보고하라.

실제 게시 전 선택한 계정의 게시 빈도·파일 수·보존 기간·요금제 제한을 공식 자료와 계정 설정으로 확인한다. 지원 한계를 숨기거나 브리핑 주기를 임의로 바꾸지 않는다. 업로드 성공 후 본문 검증이 실패하면 마지막 검증된 배포로 복구하는 경로도 확인한다. 아직 모르는 실제 게시 완료 시각을 산출물에 미리 넣지 않는다.
