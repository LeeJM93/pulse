# Pulse — Codex 상시 규칙

## 진입점과 경로

- 이 파일이 있는 현재 Pulse checkout을 프로젝트 루트로 사용한다(기본 로컬 경로 `stock/programs/pulse/`). 코드·설정·테스트·실행 데이터·웹 산출물을 그 아래에 둔다.
- Python 패키지는 `pulse`, 코드 위치는 `src/pulse/`다. CLI 이름은 `pulse`를 기본으로 구성한다.
- 작업 시작 시 [개발 진행 지침](docs/development.md)과 [구현·검증 상태](docs/implementation_status.md)를 읽고, 현재 요청에 해당하는 단계 문서를 선택한다.
- 첫 구현 전에는 [기능명세 원문](장중_시황_브리핑_프로그램_기능명세.md) 전체를 읽는다. 이후에는 관련 FR·AC와 수정된 부분을 다시 확인한다.
- 사용자가 “구현 시작”, “계속 진행”, “다음 단계”라고 요청하면 문서를 직접 읽고 실행한다. 긴 프롬프트를 다시 요청하지 않는다.
- 명세를 복제하지 않는다. 아래의 일반 파일 경로와 실행 명령은 프로젝트 루트 기준이다. Markdown 링크는 각 문서 위치 기준이다.
- 사용자의 현재 요청 범위를 우선한다. 문서 작성이나 검토 요청을 구현 시작으로 취급하지 않는다.

## Git workflow

파일을 변경하는 작업은 시작 전에 [브랜치·커밋 운영 지침](docs/git_workflow.md)을 읽는다.

- Pulse는 독립 Git 저장소다. 현재 checkout의 루트·브랜치·시작 커밋과 기존 staged·unstaged·untracked 변경을 먼저 확인한다.
- `main`을 기준 브랜치로 두고 작업별 `codex/pulse-<작업명>` 브랜치를 사용한다. 같은 작업의 기존 브랜치나 사용자가 지정한 브랜치를 우선한다.
- 요청한 구현·수정·문서 변경은 검증한 논리적 단위로 로컬 커밋한다. 브랜치 생성·커밋마다 승인을 다시 요청하지 않는다. 조사·설명·읽기 전용 검토에는 커밋하지 않는다.
- 커밋이 없는 저장소는 명세·지침·제외 설정의 최초 기준 커밋을 `main`에 만들 수 있다. 프로그램 구현은 이후 작업 브랜치에서 시작한다.
- 이번 작업의 변경만 명시적으로 선택하고 index 전체를 확인한다. 기존 사용자 변경과 staging 상태를 보존하며, 전체 변경을 일괄 추가하지 않는다.
- 커밋 전 실제 포함할 내용을 검토하고 변경에 맞는 검증을 실행한다. API 등 외부 검증이 남으면 샘플 검증과 구분해서 기록한다.
- 메시지는 `<type>(<scope>): <요약>` 형식을 사용한다. 코드·관련 테스트·필요한 문서는 같은 논리적 변경에 포함한다.
- Push·PR·`main` 병합은 현재 요청 또는 사전 허용 범위에서 수행한다. 허용된 범위에 반복 확인을 요구하지 않는다.
- 사용자 변경 폐기, 공유 커밋 재작성, 강제 push를 일상 절차로 실행하지 않는다. 일반적인 수정은 새 커밋으로 남긴다.
- 완료 시 브랜치·커밋 ID·검증 결과·남은 변경·push/병합 여부를 보고한다. Git 실행이나 커밋이 실패하면 이유를 명시한다.

## Project

이 프로젝트는 **pulse — 장중 시황 브리핑 프로그램**을 구현한다.

프로젝트의 기능 요구사항과 완료 기준은 다음 문서를 primary source of truth로 사용한다.

[장중_시황_브리핑_프로그램_기능명세.md](장중_시황_브리핑_프로그램_기능명세.md)

코드를 수정하기 전에 관련 명세와 현재 구현을 먼저 읽는다.

명세의 `FR-*`, `AC-*` 식별자를 유지하고 구현·테스트·검증 상태를 추적 가능하게 한다.

---

## Engineering priorities

우선순위는 다음과 같다.

1. Correctness
2. Data integrity
3. Simplicity
4. Readability
5. Maintainability
6. Testability
7. Operational reliability

기능 수를 늘리는 것보다 잘못된 데이터를 정상 데이터처럼 처리하지 않는 것이 중요하다.

---

## Scope discipline

현재 P0 범위를 우선 구현한다.

P1 기능은 P0 구현에 필요한 확장 지점만 남기고 선행 구현하지 않는다.

다음 기능은 현재 범위 밖이다.

- 자동 주문
- 계좌 잔고 조회
- 개인 계좌 미체결 조회
- 매수·매도 추천
- 목표가 생성
- 미래 수익률 또는 방향 확률 예측
- 사용자 회원가입
- 웹 채팅
- 방문자별 AI 실행
- 초 단위 호가 분석
- 휩쏘 탐지 전체 기능

현재 작업과 관계없는 리팩터링도 하지 않는다.

---

## Greenfield design

이 프로젝트는 greenfield 프로젝트다.

현재 요구사항을 만족하는 가장 작은 coherent architecture를 만든다.

미래의 가상 요구사항을 위해 abstraction을 미리 만들지 않는다.

다음을 피한다.

- speculative abstraction
- unnecessary factory
- unnecessary manager/service/wrapper
- generic base class
- 사용처가 하나뿐인 과도한 interface
- framework를 위한 framework
- 불필요한 dependency

구체적인 architectural boundary가 존재하거나 구현 교체가 필요한 경우에는 interface/adapter를 사용할 수 있다.

특히 다음 경계는 독립적으로 유지한다.

- 현물 데이터 공급자
- 선물 데이터 공급자
- 저장소
- 지표 계산
- 브리핑 생성
- 브리핑 검증
- 정적 웹 생성
- 게시

---

## Code style

명확하고 직접적인 코드를 선호한다.

clever code보다 readable code를 선택한다.

이름으로 의도를 드러낸다.

함수와 모듈은 명확한 책임을 가진다.

control flow를 불필요하게 중첩하지 않는다.

외부 I/O와 순수 계산을 가능한 한 분리한다.

지표 계산은 가능한 한 pure function으로 작성한다.

주석은 주로 다음을 설명할 때 사용한다.

- why
- domain constraint
- 외부 API의 비직관적인 동작
- 시간 의미
- 데이터 품질 규칙
- 의도적인 trade-off

자명한 코드를 문장으로 반복하는 주석은 작성하지 않는다.

---

## Data integrity

숫자 값만 저장하지 말고 가능하면 그 의미와 provenance를 함께 보존한다.

중요한 데이터에는 필요에 따라 다음 정보를 유지한다.

- provider
- source_api
- source_time
- observed_at
- time_basis
- unit
- quality
- missing_reason
- aggregation_scope
- schema_version

다음을 서로 다른 상태로 취급한다.

- `0`
- `null`
- 아직 관측되지 않음
- 제공 불가
- 지연
- stale
- 범위 불일치

누락값을 임의로 `0`으로 변환하지 않는다.

가격, 거래량, 거래대금, 투자자 수급, OI의 의미와 단위를 혼동하지 않는다.

`%`와 `%p`를 구별한다.

화면 반올림 전 원래 값으로 계산한다.

---

## Time semantics

모든 내부 datetime은 timezone-aware로 처리한다.

화면은 `Asia/Seoul` 기준으로 표시한다.

다음을 구별한다.

- scheduled_as_of
- source_time
- observed_at
- generated_at
- published_at

`as_of`보다 뒤에 관측된 값을 해당 과거 브리핑 계산에 사용하지 않는다.

실제로 계산에 사용한 observation과 비교 시작·종료 시각을 추적 가능하게 유지한다.

서로 다른 기준 시각의 데이터를 동시 관측값처럼 사용하지 않는다.

시간 의존 로직은 테스트에서 고정된 시각을 주입할 수 있게 한다.

---

## Futures and open interest

실제 선물 계약 코드와 `최근월물`이라는 역할을 구별한다.

서로 다른 계약을 이어 붙여 가격 또는 OI 변화를 계산하지 않는다.

최근월물과 차근월물을 별도로 저장한다.

표준 선물과 미니선물 계약 수를 직접 합산하지 않는다.

OI 증가 또는 감소만으로 다음을 추론하지 않는다.

- 신규 롱
- 신규 숏
- 롱 청산
- 숏 커버
- 특정 투자자의 포지션 생성 또는 청산

투자자 순매수 계약 수는 현재 보유 포지션이 아니다.

전 월물 투자자 수급을 특정 월물 수급처럼 표현하지 않는다.

가격·OI·수급의 집계 범위와 기준 시각이 맞지 않으면 조합 해석을 보류한다.

명세에서 정의한 deterministic observation state는 Python이 계산한다.

AI가 이 상태를 대신 추론하게 하지 않는다.

---

## External APIs

외부 API의 endpoint, field, unit, timestamp, aggregation scope 또는 갱신 주기를 추측하지 않는다.

실제 integration에서는 공식 문서와 실제 응답을 확인한다.

다음을 구별한다.

- verified
- implemented but not verified
- not configured
- unavailable
- blocked

샘플 fixture로 테스트에 성공했다는 이유로 실제 API 연결을 `verified`로 표시하지 않는다.

API를 1분마다 호출할 수 있다는 사실을 데이터가 1분마다 갱신된다는 의미로 해석하지 않는다.

공급자별 authentication, rate limiting, timeout, retry와 failure를 독립적으로 처리한다.

한 공급자의 장애 때문에 다른 정상 데이터 수집을 중단하지 않는다.

---

## SQLite and external I/O

SQLite transaction은 짧게 유지한다.

다음 작업을 수행하면서 DB write lock을 유지하지 않는다.

- HTTP request
- Codex 실행
- static site build
- deployment
- remote deployment verification

가능하면 다음 흐름을 사용한다.

external I/O → short DB transaction → external I/O → short DB transaction

AI 생성 또는 배포 중에도 데이터 수집이 계속될 수 있어야 한다.

---

## AI boundary

시장 수치와 deterministic state는 Python이 계산한다.

AI의 역할은 확정된 evidence를 자연어로 설명하는 것이다.

AI 입력에 다음을 포함하지 않는다.

- API key
- access token
- account information
- deployment credentials
- 기타 secret

AI 해설은 evidence의 `fact_id`를 참조해야 한다.

가능하면 자유 문장 속 임의 숫자 생성을 허용하지 않고 검증 가능한 fact placeholder를 사용한다.

AI가 입력에 없는 뉴스, 정책, 기업 재료, 원인 또는 매매 주체를 새로 만들어내게 하지 않는다.

AI 출력 검증 실패 또는 AI 이용 불가 시 deterministic `TemplateGenerator`로 fallback한다.

AI 실패 때문에 데이터 수집이나 기존 웹 열람이 중단되어서는 안 된다.

---

## Security and public output

`dist/`에는 공개 가능한 파일만 생성한다.

다음을 `dist/`에 포함하지 않는다.

- SQLite DB
- raw API responses
- credentials
- private configuration
- logs
- Codex authentication data
- private run evidence

외부 텍스트와 AI 출력은 HTML 출력 전에 escape한다.

웹 방문으로 시장 API, Codex 또는 데이터베이스 mutation이 실행되지 않게 한다.

---

## Testing

외부 서비스 없이 검증 가능한 로직은 fixture와 deterministic test로 검증한다.

특히 다음 유형을 테스트한다.

- 정상 데이터
- missing data
- stale data
- future observation
- timestamp mismatch
- cumulative value reset
- denominator zero
- provider failure
- timeout
- duplicate run
- restart
- invalid AI output
- futures contract rollover
- OI unchanged
- OI stale
- aggregation scope mismatch

기능명세의 `AC-*`를 acceptance-test 기준으로 사용한다.

외부 서비스가 필요한 검증과 자동 테스트를 구분한다.

---

## Quality gates

코드 변경 후 가능한 경우 다음을 모두 실행한다.

- formatter
- linter
- type checker
- relevant tests

phase 완료 시 전체 테스트를 실행한다.

문서만 바꾼 작업은 링크·경로·명세와의 일관성을 확인한다. 아직 존재하지 않는 코드 품질 도구나 테스트를 실행했다고 기록하지 않는다.

검증하지 못한 항목을 성공으로 보고하지 않는다.

---

## Documentation

다음을 유지한다.

`docs/data_mapping.md`

각 외부 지표에 대해 가능하면 다음을 기록한다.

- metric
- provider
- API
- response field
- raw unit
- normalized unit
- timestamp semantics
- market scope
- aggregation scope
- conversion rule
- verification status

verification status는 최소 다음을 구분한다.

- 제공 확인
- 직접 계산
- 제공 불가
- 검증 전

또한 다음 파일을 유지한다.

`docs/implementation_status.md`

최소한 다음 상태를 구분한다.

- 샘플 구현 완료
- 실제 API 연결 확인
- AI 생성 확인
- 웹 게시 확인
- 장중 운영 확인

선물 기능은 추가로 다음을 각각 구분한다.

- 가격 연결
- 장중 OI 연결
- 투자자 수급 연결
- 월물 정합성 확인
- 시각 정합성 확인

---

## Working method

새 작업을 시작할 때:

1. 관련 명세를 읽는다.
2. Git 운영 지침과 작업 시작 상태를 확인하고 관련 기존 코드를 읽는다.
3. 기존 utility와 pattern을 검색한다.
4. 가장 작은 coherent change를 구현한다.
5. 검증한다.
6. 자신의 diff를 리뷰한다.
7. 검증한 작업 변경을 로컬 커밋하고 남은 변경을 확인한다.

diff review에서는 다음을 확인한다.

- unnecessary complexity
- duplicated logic
- unclear naming
- accidental behavior changes
- unnecessary abstraction
- unnecessary dependency
- type-safety regression
- missing edge cases
- hidden side effects
- stale comments
- secret leakage
- unrelated changes

문제를 발견하면 완료 보고 전에 수정한다.

---

## Completion reporting

작업 종료 보고는 다음 상태를 혼동하지 않는다.

- implemented
- automated test passed
- integration verified
- operationally verified
- blocked

보고에는 최소한 다음을 포함한다.

### Completed
실제로 구현한 내용.

### Verified
직접 실행하거나 테스트해서 확인한 내용.

### Not yet verified
구현됐지만 실제 외부 환경에서 확인하지 못한 내용.

### Blocked
외부 조건 때문에 진행하지 못한 내용.

### Tests
실행한 formatter, linter, type checker, tests와 결과.

### Important decisions
새롭게 결정한 중요한 architecture/data decision.

### Next
기능명세상 다음 단계.

Git을 사용한 변경 작업은 브랜치, 생성한 커밋 ID·요약 또는 커밋 불가 사유, 남은 변경, push·PR·병합 여부도 함께 보고한다.

완료 상태를 과장하지 않는다.
