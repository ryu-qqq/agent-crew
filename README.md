# agent-crew

여러 프로젝트가 공유하는 **Claude Code 에이전트·스킬 자산의 단일 원본(source of truth)**.

한 곳에서 고도화 → 모든 프로젝트에 반영. 복사본이 갈라지는 drift를 막는다.

---

## 왜 있나 — 팀 생산성의 저점을 높인다

같은 모델·같은 IDE를 써도 결과물 차이는 극심하다. 그 차이는 코딩 실력이 아니라
**'LLM을 얼마나 정교하게 제어하는가(LLM Literacy)'의 노하우 격차**다. 이 격차를
개인의 센스에 맡기면 조직 차원의 손실이다 — 가장 잘하는 사람의 노하우는 팀이
설계·배포하는 *시스템*이 되어야 한다.

agent-crew는 그 시스템의 한 조각이다. 에이전트·스킬을 **즉석에서 막 만들어 쓰는
개인 도구**가 아니라, **버전으로 끊어 관리하는 공유 자산**으로 다룬다.

- **저점을 높인다 (Raising the Floor)** — 가장 잘 만든 에이전트를 단일 원본으로 두고
  모든 프로젝트가 같은 버전을 가져다 쓴다. 잘하는 사람의 노하우가 곧 팀의 표준이 된다.
- **버전·관리** — 자산은 git tag로 끊고 `CHANGELOG`로 이력을 남긴다. 코드 리뷰하듯
  자산도 검증·고도화된다 (평가·관측은 `ops-pilot`).
- **파편화 제거** — 프로젝트마다 흩어진 복사본, *존재 이유를 아무도 모르는* 에이전트를
  없앤다. 공통 자산은 한 원본에 응집시킨다.

복사본이 갈라지는 drift(같은 역할의 에이전트가 프로젝트마다 분기 — 예: `jira-manager`가
3갈래로)는 이 관리의 부재가 낳는 증상이다. agent-crew는 그 원본을 단일화해 증상을 없앤다.

프로젝트 고유 값(경로·키·이름)은 자산 본문에 하드코딩하지 않고 각 프로젝트의
`.claude/project.yaml`에서 런타임에 읽는다 — 그래야 하나의 원본이 모든 프로젝트에 맞는다.

> **모토** — "개인의 '슈퍼'를 넘어 팀의 '표준'으로." LLM을 개인의 도구가 아니라
> 팀의 시스템으로 편입시킨다.
> 참고: [Software 3.0 시대, Harness를 통한 조직 생산성 저점 높이기](https://toss.tech/article/harness-for-team-productivity)

## 생태계에서의 위치

| | 역할 |
|---|---|
| **agent-crew** (이 레포) | 에이전트·스킬 `.md` **원본 저장소**. git tag로 버전을 끊는다. |
| **ops-pilot** | agent-crew 자산을 **평가·관측·A/B diff**하는 도구. |
| 소비 프로젝트 (marketplace 등) | agent-crew의 특정 버전을 `.claude/`로 가져와 사용. |

agent-crew는 *순수 자산 저장소*다. 평가·대시보드·런타임 기능은 두지 않는다 (그건 ops-pilot 몫).

## 구조

```
agent-crew/
├── agents/                  # 공유 에이전트 (.md)
│   ├── jira-manager.md       # 작업 추적 — Jira 이슈/태스크 연동
│   ├── notion-manager.md     # 작업 추적 — Notion 작업·일정 데이터베이스
│   ├── notion-doc-writer.md  # 문서 저작 — 스펙·PRD·설계 문서 → Notion 페이지
│   ├── journal-recorder.md   # 지식 축적 — 작업 산출물 → raw 시드
│   ├── wiki-curator.md       # 지식 축적 — raw 시드 → wiki 합성·승격
│   └── wiki-lookup.md        # 지식 회수 — 작업 전 wiki 조회
├── skills/
│   └── journal/SKILL.md      # 사용자 직접 기록·문서 생성 진입점
├── project.yaml.example      # 소비 프로젝트가 채워야 할 설정 스키마
├── releases/                 # 버전별 릴리스 기록 (변경·개선·검증·모델별)
│   ├── TEMPLATE.md
│   └── vX.Y.Z.md
├── VERSIONING.md             # 버전·동기화·릴리스 기록 정책
└── CHANGELOG.md              # 버전별 한 줄 인덱스
```

### v0.1.0 범위 — 기록·지식 공통 코어

첫 버전은 **모든 프로젝트가 공통으로 필요한 "메타 운영" 레이어**만 담는다:
작업 추적(`jira-manager`)과 지식 복리 축적(`journal-recorder` → `wiki-curator` → `wiki-lookup`).

헥사고날 코드 생성 파이프라인(builder·reviewer 등)은 프로젝트별 조직 설계가 갈려 있어
별도 수렴 결정 이후에 다룬다. → marketplace `HTCA-73` 참조.

## 소비 프로젝트에서 쓰는 법

1. 이 레포의 특정 버전 태그를 가져온다 (동기화 방식은 [VERSIONING.md](./VERSIONING.md)).
2. `agents/*.md`·`skills/*` 를 프로젝트의 `.claude/agents`·`.claude/skills`에 둔다.
3. `project.yaml.example`을 프로젝트의 `.claude/project.yaml`로 복사해 값을 채운다.
4. Claude Code 세션을 재시작하면 에이전트가 잡힌다.

> ⚠️ Claude Code 에이전트 `.md`는 정적 텍스트다 — `{{var}}` 런타임 치환이 없다.
> 그래서 각 에이전트는 *작업 첫 단계로 `.claude/project.yaml`을 Read*하여 프로젝트 값을 얻는다.

## 버전

[VERSIONING.md](./VERSIONING.md) · [CHANGELOG.md](./CHANGELOG.md)
