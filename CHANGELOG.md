# CHANGELOG

버전별 한 줄 인덱스. **변경·개선·검증·모델별 전체 기록**은 `releases/vX.Y.Z.md` 참조.
릴리스 기록 정책은 [VERSIONING.md](./VERSIONING.md)의 "릴리스 기록".

| 버전 | 날짜 | 요약 | 전체 기록 |
|---|---|---|---|
| v0.13.0 | 2026-06-05 | tf-import 스킬 — 기존 AWS 리소스 0-diff terraform 입양 (인벤토리→미러링→import→plan 게이트(태그 외 변경 STOP)→태그 apply). agent-crew 최초 인프라 실행 절차 자산 (MINOR, 미검증) | [releases/v0.13.0.md](./releases/v0.13.0.md) |
| v0.12.0 | 2026-06-03 | frontmatter 정합 — agents 16개 `allowed-tools`→`tools`(서브에이전트 정식 키) + journal SKILL description YAML 파싱 버그 해소 (PATCH 성격·동작 무변, 태그는 MINOR 자리로 발행·기록 사후 보충) | [releases/v0.12.0.md](./releases/v0.12.0.md) |
| v0.11.0 | 2026-06-02 | 평가 설계 자산화 — scenario-designer 신규(시나리오 input+성공조건 통합 생성) + trigger-designer description 개선 모드 추가 (ops-pilot ADR 0002, MINOR, scenario-designer 1B/4B 실연동 ✅·나머지 미검증) | [releases/v0.11.0.md](./releases/v0.11.0.md) |
| v0.10.1 | 2026-06-02 | journal-recorder git 미관여 명문화 — 공유 vault 타 프로젝트 브랜치 오염 방지 (PATCH, 동작 축소, 검증 보류) | [releases/v0.10.1.md](./releases/v0.10.1.md) |
| v0.10.0 | 2026-06-01 | 하네스 creator — skill·agent 저작 오케스트레이터 + 보조 에이전트 (skills/harness-creator · agents/harness-author · agents/harness-trigger-designer, 평가는 ops-pilot 위임) | [releases/v0.10.0.md](./releases/v0.10.0.md) |
| v0.9.1 | 2026-05-27 | agent-evaluator 호출 가이드 구체화 + rubric §9 한계 명시 (PATCH, 점수 변화 0, 정직성 향상) | [releases/v0.9.1.md](./releases/v0.9.1.md) |
| v0.9.0 | 2026-05-27 | context-preprocessor skill + java-spring 추출 가이드 — 우아한 하네스 글 96.5% 토큰 절감 패턴 흡수 (skills/context-preprocessor · references/java-spring/preprocessor) | [releases/v0.9.0.md](./releases/v0.9.0.md) |
| v0.8.0 | 2026-05-27 | agent-evaluator + 8차원 asset quality rubric — agents/agent-evaluator · references/conventions/asset-quality-rubric (work-evaluator와 직교, 자산 구조 품질 채점) | [releases/v0.8.0.md](./releases/v0.8.0.md) |
| v0.7.0 | 2026-05-26 | must-reference 강제 참조 스니펫 (Claude Code · Cursor) — references/conventions/must-reference · project.yaml.ide · mustReference | [releases/v0.7.0.md](./releases/v0.7.0.md) |
| v0.6.0 | 2026-05-26 | Git 커밋·PR convention — references/conventions · engineering-os · project.yaml git.commit | [releases/v0.6.0.md](./releases/v0.6.0.md) |
| v0.5.0 | 2026-05-25 | OpsPilot feedback loop — proposal-reviewer · proposal-applier · feedback-loop skill | [releases/v0.5.0.md](./releases/v0.5.0.md) |
| v0.4.0 | 2026-05-25 | Engineering OS Notion 작업 루프 (engineering-os skill · references/notion · notion-manager convention) | [releases/v0.4.0.md](./releases/v0.4.0.md) |
| v0.3.0 | 2026-05-23 | 테스트 전략·ADR 의사결정·작업 평가 파이프라인 (test-strategist · decision-researcher · adr-author · work-evaluator + references/java-spring) | [releases/v0.3.0.md](./releases/v0.3.0.md) |
| v0.2.0 | 2026-05-22 | Notion 작업 추적·문서 저작 에이전트 추가 (notion-manager, notion-doc-writer) | [releases/v0.2.0.md](./releases/v0.2.0.md) |
| v0.1.0 | 2026-05-22 | 첫 버전 — 기록·지식 공통 코어 (jira-manager, journal·wiki 4종) | [releases/v0.1.0.md](./releases/v0.1.0.md) |
