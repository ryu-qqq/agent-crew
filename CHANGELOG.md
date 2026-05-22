# CHANGELOG

## Unreleased

Notion 작업 추적·문서 저작 에이전트 추가.

### 추가
- `agents/notion-manager.md` — Notion 작업·일정 데이터베이스 운영. 레코드 status 전이·일정(date) 속성·코멘트·라벨·상위 항목. `jira-manager`의 Notion 대응 형제 에이전트.
- `agents/notion-doc-writer.md` — 스펙·PRD·설계 문서 등 능동 산출 문서를 Notion 페이지로 저작. 문서 장르는 템플릿으로 분기.
- `project.yaml.example` — `tracking.notion`(작업 추적 DB), `docs.notion`(문서 저작 루트) 블록 추가. 둘 다 선택적 — 기존 Jira 전용 소비 프로젝트는 수정 불필요.

### 설계 결정
- Notion 자산을 **연산/책임 축으로 2개 분리** — 데이터베이스 운영(`notion-manager`)과 페이지 저작(`notion-doc-writer`). 문서 장르(spec/PRD 등)별 분리는 에이전트 증식을 부르므로 템플릿으로 처리.
- `jira-manager`는 **유지** — `notion-manager`와 형제. 소비 프로젝트가 `project.yaml`의 `tracking.jira` / `tracking.notion`으로 택일. 자산 제거 없음.
- `notion-doc-writer`(능동 산출 문서) ↔ `wiki-curator`(raw 시드 사후 합성)의 경계를 에이전트 본문에 명시. Notion 문서가 vault wiki를 대체하지 않는다.

### 버전 — 릴리스 시 결정
- 자산 추가는 MINOR. `project.yaml.example`에 추가한 블록은 *선택적·additive* — 기존 소비 프로젝트가 강제로 고칠 게 없다 (VERSIONING.md의 MAJOR 기준 "project.yaml을 고쳐야 하는 변경"에 해당하지 않음).
- 단, CLAUDE.md는 "project.yaml.example 수정 = MAJOR"로 명시 — 두 문서가 충돌한다. 태그를 끊을 때 v0.2.0(MINOR) / v1.0.0(MAJOR) 중 택하고, 정한 기준으로 CLAUDE.md·VERSIONING.md 문구를 일치시킬 것.

## v0.1.0 — 2026-05-22

첫 버전. 기록·지식 공통 코어.

### 추가
- `agents/jira-manager.md` — 작업 추적. Jira 이슈/태스크 status·코멘트·라벨. marketplace 버전을 베이스로 파이프라인 Phase 의존을 걷어내고 일반화.
- `agents/journal-recorder.md` — 작업 산출물에서 의사결정·진행상황을 추출해 지식 vault `raw/`에 시드 append.
- `agents/wiki-curator.md` — raw 시드를 wiki 페이지로 합성·승격. 백링크 정합. 지식 복리의 핵심.
- `agents/wiki-lookup.md` — 작업 전 wiki 조회. 축적된 지식의 회수 단.
- `skills/journal/SKILL.md` — 사용자 직접 기록·최종 문서 생성 진입점.
- `project.yaml.example` — 소비 프로젝트 설정 스키마 (project / tracking / knowledge).

### 설계 결정
- 지식 모델은 **vault wiki 모델** 채택 (raw 시드 → wiki 합성 승격 → lookup 회수). 단순 `docs/seeds` 적재는 "복리"가 안 되므로 제외.
- 프로젝트 고유 값은 자산 본문에 하드코딩하지 않고 소비 프로젝트의 `.claude/project.yaml`을 런타임 Read.
