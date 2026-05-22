# agent-crew — Claude Code 작업 지침

이 레포는 **여러 프로젝트가 공유하는 Claude Code 에이전트·스킬 자산의 단일 원본**이다.
여기서 자산을 저작·수정하고 git tag로 버전을 끊는다. 평가·적용은 `ops-pilot`이 맡는다.

배경·구조는 [README.md](./README.md), 버전 정책은 [VERSIONING.md](./VERSIONING.md) 참조.

---

## 🚨 절대 규칙 — 프로젝트 비종속

이 레포의 모든 자산(`agents/*.md`, `skills/*/SKILL.md`)은 **특정 프로젝트에 종속되면 안 된다.**

자산 본문에 **하드코딩 금지**:
- 절대 경로 (`/Users/...`) — vault 경로 등은 `.claude/project.yaml`에서 읽는다
- 특정 프로젝트 이름 (`marketplace`, `FileFlow` 등)
- 특정 Jira 키, 도메인·모듈 개수, 조직도 같은 프로젝트 고유 수치

→ 프로젝트 고유 값은 **소비 프로젝트의 `.claude/project.yaml`을 런타임 Read**해서 얻는다.
   (Claude Code 에이전트 `.md`는 정적 텍스트 — `{{var}}` 치환이 없으므로 이 패턴이 유일한 방법.)

프로젝트 고유 에이전트(특정 코드베이스 전용)는 **이 레포에 두지 않는다.** 그 프로젝트의 `.claude/`에 둔다.
판단 기준: *"프로젝트를 안 가리고 쓸 수 있나?"* → 예일 때만 여기에.

### 스택 특화는 프로젝트 종속과 다르다

`java-spring`처럼 *스택*에 특화된 자산은 그 스택을 쓰는 **모든 프로젝트가 공유**하므로
허용된다 (프로젝트 종속이 아님). 단:

- 자산 본문(에이전트·스킬)은 스택 무관한 **제네릭 코어**로 쓴다.
- 스택 specifics(프레임워크·도구·관용구)는 `references/<stack>/` 팩에 분리한다.
- 자산은 소비 프로젝트 `project.yaml`의 `project.stack`을 읽어 해당 references 팩을 로드한다.
- `project.stack`이 없거나 팩이 없으면 멈추지 말고 스택 무관 동작으로 폴백한다.

---

## 작업 원칙 — 4줄

자산 저작·코드·문서 등 **모든 작업**에 적용한다. `work-evaluator`가 작업을 채점하는 축이기도 하다.

1. **가정하지 마라.** 모르면 묻는다. 혼란을 숨기지 말고 트레이드오프를 드러낸다.
2. **최소만 만들어라.** 문제를 푸는 최소한의 코드·자산만. 추측성 산출 금지.
3. **범위를 지켜라.** 꼭 필요한 것만 건드린다. 요청보다 부푼 diff 금지.
4. **성공 기준을 정하고 검증하라.** 완료 조건을 명시하고, 검증될 때까지 돈다.

> "규칙 100개보다 원칙 4줄" — Karpathy의 AI 코딩 에이전트 실패 패턴 분석에서.

---

## 자산 작성 규칙

### 에이전트 `.md`
- frontmatter: `name`, `description`, `allowed-tools`
- `description`은 프로젝트명 없이 역할만 (소비 프로젝트가 어디든 맞게)
- 본문 첫 섹션은 **"작업 전 — 프로젝트 설정 로드 (필수)"** — `.claude/project.yaml`에서 무엇을 읽는지 명시
- 프로젝트 값은 본문에서 `{vault.path}`, `{rawPrefix}` 같은 플레이스홀더로 표기

### 스킬 `skills/<name>/SKILL.md`
- frontmatter `description`에 트리거 키워드를 충분히 (자동 트리거 정확도)
- 에이전트와 같은 "프로젝트 설정 로드" 규칙 적용

### 기존 자산 일관성
새 자산은 기존 `agents/`·`skills/` 자산의 구조·어투를 따른다. 새 패턴을 만들기 전에 기존 것을 본다.

---

## 작업 절차

### 자산 추가·수정
1. `agents/` 또는 `skills/`에 작성·수정
2. **검증** — 커밋 전 하드코딩 점검:
   ```bash
   grep -rnE "/Users/|marketplace|FileFlow" agents skills references
   ```
   결과가 나오면 (project.yaml.example의 예시 경로 제외) 일반화가 덜 된 것
3. 변경 항목을 다음 릴리스의 `releases/` 파일에 적어둔다 (없으면 `TEMPLATE.md` 복사해 생성)
4. 커밋 (메시지 한국어)

### 버전 릴리스 (git tag)
1. 변경 성격에 따라 SemVer 결정 (VERSIONING.md):
   - MAJOR: 소비 프로젝트가 `project.yaml`을 고쳐야 하는 변경
   - MINOR: 자산 추가, 하위호환 개선
   - PATCH: 문구 교정·버그 수정
2. `releases/TEMPLATE.md`를 복사해 `releases/vX.Y.Z.md` 작성 — 자산별 변경·개선·호환성
3. `CHANGELOG.md` 인덱스에 한 줄 추가
4. 릴리스 커밋 push → ops-pilot으로 검증 → 결과를 `releases/vX.Y.Z.md` "검증" 섹션에 채워 커밋
5. 검증 기록이 채워진 커밋에 `git tag -a vX.Y.Z -m "..."` 후 `git push origin vX.Y.Z`

→ 자세한 정책은 VERSIONING.md "릴리스 기록" (검증 후 태그).

---

## 디렉토리 구조

```
agent-crew/
├── CLAUDE.md            # 이 파일
├── README.md            # 레포 소개·생태계 위치
├── VERSIONING.md        # 버전·릴리스 정책
├── CHANGELOG.md         # 버전별 한 줄 인덱스
├── project.yaml.example # 소비 프로젝트 설정 스키마
├── agents/              # 공유 에이전트 (.md)
├── skills/              # 공유 스킬 (<name>/SKILL.md)
├── references/          # 스택 특화 지식 팩 (references/<stack>/)
└── releases/            # 버전별 릴리스 기록 (변경·개선·검증)
```

`project.yaml.example`의 **기존 키를 바꾸거나 제거**하면 = 소비 프로젝트가 `project.yaml`을
고쳐야 함 → MAJOR. **선택적 블록을 추가**(기본값으로 끄고 기존 소비 프로젝트가 손댈 필요 없음)하는
것은 MINOR. (판정 기준은 VERSIONING.md)

---

## 언어

모든 응답·자산 본문·커밋 메시지는 **한국어**. 식별자(에이전트 name, frontmatter 키)는 영어 유지.

## 커밋 메시지

```
<타입>: <한국어 요약>

<본문 — 필요 시>

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
```
