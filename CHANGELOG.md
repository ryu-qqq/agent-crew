# CHANGELOG

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
