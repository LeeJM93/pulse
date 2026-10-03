# Phase 0+1 — 프로젝트 구성과 현물 샘플

[개발 진행 지침](../development.md) · [상시 규칙](../../AGENTS.md) · [구현·검증 상태](../implementation_status.md)

- 작업 디렉터리: 현재 Pulse checkout 루트(기본 `stock/programs/pulse/`). 본문의 일반 파일 경로와 명령은 이 프로젝트 루트 기준이다.
- 요구사항 원문: [기능명세](../../장중_시황_브리핑_프로그램_기능명세.md).
- 관련 요구사항: 명세 전체, 특히 FR-05~FR-10, FR-12, FR-14~FR-15, FR-18~FR-20, AC-01, AC-03~AC-10.
- 선행 조건: 없음. 기존 파일과 도구를 먼저 확인한다..
- 적용 시점: 현재 사용자 요청과 상태표를 바탕으로 이 단계가 선택됐을 때.

이 저장소의 다음 파일을 먼저 읽어라.

- `AGENTS.md`
- `장중_시황_브리핑_프로그램_기능명세.md`

기능명세를 이 프로젝트의 요구사항에 대한 primary source of truth로 사용하고 `AGENTS.md`의 engineering rules를 준수하라.

이 프로젝트는 greenfield 프로젝트로 시작했다. 재개 작업에서는 이미 작성된 코드와 검증 상태를 먼저 확인하고 필요한 부분을 이어서 구현하라.

이번 단계에서는 기능명세의 전체 프로그램을 한 번에 구현하지 않는다.

우선 **Phase 0 — Repository bootstrap**과 **Phase 1 — Sample vertical slice**를 실제로 구현하고 검증하라.

계획만 작성하고 종료하지 마라.

외부 credential 없이 구현 가능한 부분은 실제 코드로 완성하라.

---

## Phase 0 — Repository bootstrap

현재 작업 공간을 먼저 확인하라.

[Git 운영 지침](../git_workflow.md)에 따라 저장소·기존 변경·최초 기준 커밋을 확인하고 작업 브랜치를 준비하라. 이미 문서 기준 커밋이 있다면 다시 초기화하지 마라. 검증되는 논리적 단위별로 로컬 커밋하라.

프로젝트 루트는 현재 Pulse checkout이다(기본 경로 `stock/programs/pulse/`). 그 안에 코드·설정·테스트·실행 데이터를 구성하라. 기존 리서치·명세 파일을 불필요하게 변경하지 마라.

Python을 기본 언어로 사용한다.

SQLite와 정적 HTML 생성을 기본 구성으로 사용한다.

필요한 경우 기능명세의 권장 폴더 구조를 출발점으로 사용하되, 구조를 맞추기 위해 불필요한 파일이나 abstraction을 만들지 마라.

다음을 구성하라.

- `pyproject.toml`
- Python package (`src/pulse/`)
- CLI entry point (`pulse`)
- test environment
- formatter
- linter
- type checker
- README skeleton
- `docs/data_mapping.md`
- `docs/implementation_status.md`
- fixture directory
- runtime data directory
- private run directory
- public `dist/` directory

runtime/private/public 데이터의 경계를 명확히 하라.

실행 데이터와 secret이 Git에 들어가지 않도록 적절한 ignore 설정을 추가하라.

dependency는 현재 단계에 실제 필요한 것만 추가한다.

---

## Phase 1 — Sample vertical slice

실제 키움, 선물 공급자, Codex, Cloudflare credential을 사용하지 않는다.

fixture 기반으로 다음 end-to-end 경로를 구현하라.

```text
sample fixtures
→ normalization
→ SQLite storage
→ metric calculation
→ evidence package
→ TemplateGenerator
→ briefing
→ static HTML site
```

한 명령으로 전체 demo pipeline을 실행할 수 있어야 한다.

기능명세의 제안 CLI를 참고하여 다음과 같거나 동등한 인터페이스를 제공하라.

```bash
pulse demo
```

실행 결과로 최소한 다음이 생성되어야 한다.

- sample observations 저장
- deterministic metric 계산
- 기본 브리핑
- 최신 브리핑 페이지
- 날짜별 목록
- 개별 브리핑 페이지
- 공개 가능한 정적 산출물

첫 실행으로 브리핑 기록이 없는 상태도 정상적으로 처리한다.

---

## Sample data

fixture는 실제 시장 응답이라고 가장하지 않는다.

샘플임을 명확하게 표시한다.

정상 흐름뿐 아니라 테스트에 필요한 다음 fixture를 만들 수 있는 구조로 설계하라.

- missing observation
- stale observation
- future observation
- cumulative amount decrease/reset
- delayed investor flow
- 신규 추적 종목
- 한 시장 일부 데이터 누락
- 양 시장 핵심 지수 누락

fixture 자체가 domain logic을 숨기지 않도록 단순하고 읽기 쉽게 유지하라.

---

## Data model

기능명세의 저장 요구사항을 기준으로 최소 데이터 모델을 설계한다.

현재 Phase에 필요하지 않은 모든 미래 테이블을 미리 구현할 필요는 없다.

그러나 다음 개념을 잃지 않도록 한다.

- trading date
- session
- market / venue / universe
- entity type / entity id
- provider / source API
- source time
- observed time
- time basis
- value
- unit
- quality
- missing reason
- raw/schema reference

`0`, `null`, missing, stale을 서로 구분한다.

시간은 timezone-aware로 저장·처리한다.

---

## Metric implementation

기능명세의 deterministic 계산을 코드로 구현한다.

이번 단계에서는 최소 다음을 fixture로 검증하라.

- 전일 대비 등락률
- 최근 15분 수익률
- 시장 대비 업종 강도
- 최근 15분 거래대금
- 거래대금 증가율
- 업종 거래대금 비중
- 거래대금 비중 변화
- 상승 종목 비율
- 투자자 수급 변화
- 최근 15분과 직전 게시 이후 기간의 구분

FR-09의 업종 순위·동률 처리·대표 종목 선정과 FR-10의 비교 기간 구분도 샘플 근거 자료와 기본 브리핑에 반영한다. 추적 종목 기준과 전체 시장 기준을 구별하고 데이터 누락을 순위 이탈로 해석하지 않는다.

계산은 가능한 한 pure function으로 작성한다.

DB나 네트워크 접근을 계산 함수 안에 숨기지 마라.

---

## Data-quality behavior

다음을 반드시 구현하고 테스트하라.

- denominator가 0이면 비율을 만들지 않는다.
- 누적 거래대금 감소를 정상 음수 거래대금으로 출력하지 않는다.
- 누적 투자자 순매수 감소는 거래대금 감소와 같은 오류로 취급하지 않는다.
- 기준 시각 이후 observation을 과거 계산에 사용하지 않는다.
- 신규 추적 종목의 기준값이 없으면 변화량을 0으로 만들지 않는다.
- 미갱신 수급을 새로운 흐름으로 해석하지 않는다.
- 일부 지표가 누락되면 유효한 부분만 제공할 수 있다.
- 양 시장 핵심 지수 모두 사용할 수 없으면 새 브리핑을 게시하지 않는다.

---

## TemplateGenerator

이번 단계에서는 AI를 연결하지 않는다.

확정된 metric/evidence를 이용하는 deterministic `TemplateGenerator`를 구현하라.

TemplateGenerator가 없어도 안 되는 fallback 경로라는 점을 고려하여 독립적으로 테스트한다.

브리핑과 표의 숫자는 Python이 계산한 값만 사용한다.

---

## Static site

JavaScript 없이도 주요 내용을 읽을 수 있는 정적 사이트를 생성한다.

최소 경로는 기능명세를 따른다.

```text
/
days/YYYY-MM-DD/
briefings/<briefing-id>/
archive/
```

모바일에서도 읽을 수 있는 기본 responsive layout을 제공한다.

상승·하락을 색만으로 표현하지 않는다.

sample mode에서는 페이지에 명확한 `샘플 데이터` 표시를 한다.

HTML에 삽입되는 외부 텍스트는 escape한다.

`dist/`에 DB, raw responses, private runs, config secret 또는 로그가 포함되지 않도록 검사한다.

---

## Acceptance criteria

이번 단계에서 우선 다음 acceptance criteria를 자동화 가능한 테스트로 구현하고 통과시켜라.

- AC-01
- AC-03
- AC-04
- AC-05
- AC-06
- AC-07
- AC-08
- AC-09
- AC-10

그 밖에 현재 구현 범위에서 자연스럽게 검증 가능한 AC가 있으면 추가할 수 있다.

실제 외부 서비스가 필요한 AC를 통과했다고 표시하지 마라.

`docs/implementation_status.md`에 각 상태를 정확하게 기록한다.

---

## Quality gates

완료 전에 다음을 실행하라.

- formatter
- linter
- type checker
- tests
- demo command

실패가 있으면 원인을 수정한 뒤 다시 검증하라.

자신의 전체 diff를 검토하여 다음을 확인하라.

- unnecessary abstraction
- duplicated logic
- unclear naming
- accidental behavior changes
- unnecessary dependency
- hidden side effects
- security issue
- secret/public boundary violation
- missing edge case

---

## Completion

이번 단계에서는 실제 API integration, Codex integration, scheduler, Cloudflare deployment를 완료하려 하지 마라.

다만 후속 단계가 연결될 수 있도록 필요한 최소한의 명확한 boundary는 유지한다.

완료 후 `AGENTS.md`에 정의된 형식으로 상태를 보고하라.

특히 다음을 구분하라.

- sample implementation completed
- automated tests passed
- external integration not yet verified

계획만 제시하지 말고 실제 구현·테스트·demo 실행까지 수행하라.

## 로컬 상태와 게시 상태

이 단계의 정적 파일 생성은 원격 게시 확인이 아니다. `published` 상태를 가짜로 만들지 마라. 직전 게시 이후 비교는 명시적인 샘플 게시 이력 또는 테스트 대역으로 검증하고 실제 게시 기록과 구분한다.
