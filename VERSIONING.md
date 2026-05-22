# 버전·동기화 정책

## 버전 규칙 (SemVer)

agent-crew는 **git tag로 버전을 끊는다**. 별도 버전 관리 시스템을 두지 않는다.

`vMAJOR.MINOR.PATCH`

| 증가 | 언제 |
|---|---|
| **MAJOR** | 소비 프로젝트가 `.claude/project.yaml`을 고쳐야 하는 변경 (스키마 변경, 자산 제거·개명) |
| **MINOR** | 자산 추가, 하위호환되는 동작 개선 |
| **PATCH** | 문구 교정, 버그 수정 — 동작·스키마 영향 없음 |

태그를 끊을 때 `releases/vX.Y.Z.md`를 작성하고 `CHANGELOG.md` 인덱스에 한 줄 추가한다.
→ 아래 [릴리스 기록](#릴리스-기록).

## 릴리스 기록

에이전트·스킬은 소중한 자산이다. 버전마다 "무엇이 바뀌었나"를 넘어 **무엇이·왜
좋아졌고, 어떻게 검증됐나**를 남긴다.

- 버전마다 `releases/vX.Y.Z.md` 파일을 둔다. 양식은 `releases/TEMPLATE.md`.
- `CHANGELOG.md`는 버전당 한 줄 인덱스다 — 전체 기록은 `releases/`에 있다.
- 릴리스 파일이 담는 것:
  - **자산별 변경·개선** — 무엇이 바뀌고, 이전 대비 *왜* 더 나은가
  - **검증 (ops-pilot)** — 어떤 시나리오로, 모델별로 어떤 결과였나, 회귀는 없나
  - **호환성** — project.yaml 영향·마이그레이션

> agent-crew는 평가를 *실행*하지 않는다 (ops-pilot 몫). 릴리스 파일의 "검증" 섹션은
> ops-pilot 검증 *결과의 기록*이다 — "평가 기능"이 아니라 버전 메타데이터 문서다.

### 검증 후 태그

태그된 버전은 사실상 불변이다. 검증되지 않은 자산에 태그가 붙지 않도록:

1. 릴리스 커밋을 main에 올린다 (`releases/vX.Y.Z.md` 포함, "검증" 섹션은 "미검증").
2. ops-pilot으로 그 시점 자산을 검증한다.
3. 검증 결과를 `releases/vX.Y.Z.md`의 "검증" 섹션에 채워 커밋한다.
4. 그 커밋에 `git tag -a vX.Y.Z`.

즉 **태그는 검증 기록이 채워진 커밋을 가리킨다.** 빠른 수정(PATCH)이라 검증을 건너뛰면
"검증 상태: 미검증"을 명시한 채 태그한다 — 생략은 가능하되 흔적은 남긴다.

## 동기화 — 왜 submodule이 아닌가

git submodule은 **디렉토리를 통째로** mount한다. 소비 프로젝트의 `.claude/agents`에는
그 프로젝트 고유 에이전트도 함께 있으므로, agent-crew의 일부만 `.claude/agents`에
submodule로 얹을 수 없다 (전부거나 전무).

→ v0.1.0 단계의 공유 방식은 **버전이 고정된 동기화(복사)**다:

1. 소비 프로젝트는 agent-crew의 특정 **태그**를 정해 그 시점 자산을 `.claude/`로 복사한다.
2. 복사한 버전을 `.claude/agent-crew.lock`에 기록한다 (예: `version: v0.1.0`).
3. 업그레이드는 *의도적으로* — 새 태그를 골라 다시 동기화하고 lock을 갱신한다.

symlink와 다르다. 실제 파일이 프로젝트 안에 있고(세션이 항상 로드 가능), lock으로
"어느 버전을 쓰는 중인지"가 추적된다. 로컬 경로에 의존하지 않는다.

> 소비 프로젝트의 `.claude/agents` 전체를 agent-crew로 대체할 수 있게 되면
> (= 프로젝트가 공유 표준 조직을 100% 채택하면) 그때 submodule 전환이 가능하다.
> 그 결정은 각 프로젝트 몫이다. marketplace는 `HTCA-73`에서 다룬다.

## lock 파일 형식

소비 프로젝트 `.claude/agent-crew.lock`:

```yaml
version: v0.1.0
syncedAt: 2026-05-22
assets:
  agents: [jira-manager, journal-recorder, wiki-curator, wiki-lookup]
  skills: [journal]
```
