# Phase 5 — AI 브리핑

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: FR-12~FR-15, FR-17, FR-28, AC-11~AC-13, AC-36.
- 선행 조건: Phase 0+1과 Phase 2의 근거 자료·TemplateGenerator. 외부 시장 API 미검증이어도 샘플로 개발할 수 있다..
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

`AGENTS.md`, 기능명세 FR-12~FR-15, 현재 evidence와 TemplateGenerator 구현을 먼저 확인하라.

이번 단계에서는 Codex 기반 브리핑 생성을 추가한다.

기존 deterministic TemplateGenerator fallback을 제거하거나 약화하지 마라.

구현 흐름은 다음과 같다.

```text
metric snapshot
→ evidence package
→ structured Codex input
→ structured output
→ briefing validation
→ fact substitution
→ publishable briefing
```

구현 대상:

- evidence schema
- briefing output schema
- `CodexCliGenerator`
- `TemplateGenerator` 공통 generator boundary
- timeout
- AI-call state tracking
- validation
- fallback
- prompt/schema version recording

공식 Codex CLI와 공식 인증 방식만 사용한다.

browser cookie 추출이나 비공식 ChatGPT 호출을 구현하지 않는다.

Codex child process에는 필요한 최소 환경만 전달한다.

시장 API credential이나 deployment secret을 전달하지 않는다.

AI 해설은 입력 evidence의 fact ID에 연결되어야 한다.

허용하지 않은 숫자, 존재하지 않는 fact ID, 잘못된 schema는 validation failure로 처리한다.

AI 실패 시 새로운 수치 중심 TemplateGenerator 브리핑을 생성한다.

과거 AI 문장을 새 브리핑 해설로 재사용하지 않는다.

가능한 fixture를 이용하여 다음을 검증한다.

- 정상 structured output
- unknown fact
- unauthorized number
- malformed output
- timeout
- CLI unavailable
- authentication failure
- usage-limit/failure fallback

AC-11, AC-12, AC-13의 현재 범위에서 자동 검증 가능한 부분을 구현한다.

실제 Codex 실행이 가능한 경우에만 `AI 생성 확인`을 verified로 표시한다.

완료 후 formatter, lint, type check, tests 및 가능한 실제 Codex 단일 실행을 검증하고 보고하라.
