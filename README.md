# agent-crew

여러 프로젝트가 공유하는 **Claude Code 에이전트·스킬 자산의 단일 원본(source of truth)**.

한 곳에서 고도화 → 모든 프로젝트에 반영. 복사본이 갈라지는 drift를 막는다.

> **모토** — "개인의 '슈퍼'를 넘어 팀의 '표준'으로." LLM을 개인의 도구가 아니라 팀의
> 시스템으로 편입시켜 팀 생산성의 *저점*을 끌어올린다.
> — [Software 3.0 시대, Harness를 통한 조직 생산성 저점 높이기](https://toss.tech/article/harness-for-team-productivity)

## 생태계 — agent-crew와 ops-pilot은 한 세트

자산을 **만드는 곳**과 **평가·적용하는 곳**이 짝을 이룬다.

| | 역할 |
|---|---|
| **agent-crew** (이 레포) | 에이전트·스킬 `.md` **원본 저장소**. git tag로 버전을 끊는다. |
| **[ops-pilot](https://github.com/ryu-qqq/ops-pilot)** | agent-crew 자산을 **평가·관측하고 소비 프로젝트에 적용**하는 도구. |

agent-crew는 *순수 자산 저장소*다 — 평가·적용·대시보드·런타임 기능은 두지 않는다.
그 역할은 **[ops-pilot](https://github.com/ryu-qqq/ops-pilot)**이 맡는다.
소비 프로젝트는 ops-pilot을 통해 특정 버전을 가져다 쓴다.

## 구조

```
agent-crew/
├── agents/                  # 공유 에이전트 (.md)
│   ├── jira-manager.md         # 작업 추적 — Jira 이슈/태스크 연동
│   ├── notion-manager.md       # 작업 추적 — Notion 작업·일정 데이터베이스
│   ├── notion-doc-writer.md    # 문서 저작 — 스펙·PRD·설계 문서 → Notion 페이지
│   ├── test-strategist.md      # 테스트 전략 — 시나리오 → 테스트 피라미드 매핑
│   ├── decision-researcher.md  # 의사결정 — 조사·옵션·트레이드오프 리서치 브리프
│   ├── adr-author.md           # 의사결정 — ADR 작성·확정 (in-repo docs/adr/)
│   ├── work-evaluator.md       # 작업 평가 — 완료 작업을 4원칙으로 채점
│   ├── journal-recorder.md     # 지식 축적 — 작업 산출물 → raw 시드
│   ├── wiki-curator.md         # 지식 축적 — raw 시드 → wiki 합성·승격
│   └── wiki-lookup.md          # 지식 회수 — 작업 전 wiki 조회
├── skills/
│   ├── journal/SKILL.md      # 사용자 직접 기록·문서 생성 진입점
│   ├── test-plan/SKILL.md    # 테스트 계획 수립 진입점
│   └── adr/SKILL.md          # 조사·결정·기록 ADR 파이프라인 진입점
├── references/               # 스택 특화 지식 팩 (제네릭 자산이 런타임 로드)
│   └── java-spring/          # Java·Spring Boot
├── project.yaml.example      # 소비 프로젝트가 채워야 할 설정 스키마
├── releases/                 # 버전별 릴리스 기록 (변경·개선·검증·모델별)
│   ├── TEMPLATE.md
│   └── vX.Y.Z.md
├── VERSIONING.md             # 버전·릴리스 정책
└── CHANGELOG.md              # 버전별 한 줄 인덱스
```

### 담는 것 / 안 담는 것

agent-crew는 **모든 프로젝트가 공통으로 쓰는 "메타 운영" 레이어**를 담는다 — 작업 추적
(`jira-manager` · `notion-manager`), 문서 저작(`notion-doc-writer`), 테스트 전략
(`test-strategist`), 의사결정(`decision-researcher` → `adr-author`), 작업 평가
(`work-evaluator`), 지식 복리 축적(`journal-recorder` → `wiki-curator` → `wiki-lookup`).

헥사고날 코드 생성 파이프라인(builder·reviewer 등) 같은 **프로젝트 고유 자산**은 담지
않는다 — 조직 설계가 프로젝트마다 갈려 공통화가 안 되기 때문.

## 소비 프로젝트에 적용

소비 프로젝트로의 동기화·적용은 **[ops-pilot](https://github.com/ryu-qqq/ops-pilot)**이
맡는다. 어느 버전을 가져올지·어떻게 핀 고정할지는 ops-pilot 문서를 따른다.

각 소비 프로젝트는 `project.yaml.example`을 `.claude/project.yaml`로 복사해 프로젝트
고유 값을 채운다.

> ⚠️ Claude Code 에이전트 `.md`는 정적 텍스트다 — `{{var}}` 런타임 치환이 없다.
> 그래서 각 에이전트는 *작업 첫 단계로 `.claude/project.yaml`을 Read*하여 프로젝트 값을 얻는다.
> 이 패턴이 하나의 원본을 모든 프로젝트에 맞게 유지하는 핵심이다.

## 버전

[VERSIONING.md](./VERSIONING.md) · [CHANGELOG.md](./CHANGELOG.md)
