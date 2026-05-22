# agent-crew

여러 프로젝트가 공유하는 **Claude Code 에이전트·스킬 자산의 단일 원본(source of truth)**.

한 곳에서 고도화 → 모든 프로젝트에 반영. 복사본이 갈라지는 drift를 막는다.

---

## 왜 있나

`ryu-qqq` 산하 프로젝트들(marketplace·FileFlow·AuthHub·CrawlingHub·Gateway 등)은 각자
`.claude/agents`·`.claude/skills`를 따로 갖고 있다. 같은 역할의 에이전트가 프로젝트마다
복사본으로 흩어져 이미 drift가 발생했다 (예: `jira-manager`가 3갈래로 분기).

agent-crew는 **프로젝트에 종속되지 않는 공통 자산**만 모아 버전으로 관리한다.
프로젝트 고유 값(경로·키·이름)은 자산 본문에 하드코딩하지 않고,
각 프로젝트의 `.claude/project.yaml`에서 런타임에 읽는다.

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
│   ├── journal-recorder.md   # 지식 축적 — 작업 산출물 → raw 시드
│   ├── wiki-curator.md       # 지식 축적 — raw 시드 → wiki 합성·승격
│   └── wiki-lookup.md        # 지식 회수 — 작업 전 wiki 조회
├── skills/
│   └── journal/SKILL.md      # 사용자 직접 기록·문서 생성 진입점
├── project.yaml.example      # 소비 프로젝트가 채워야 할 설정 스키마
├── VERSIONING.md             # 버전·동기화 정책
└── CHANGELOG.md
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
