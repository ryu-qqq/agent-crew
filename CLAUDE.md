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
   grep -rnE "/Users/|marketplace|FileFlow" agents skills
   ```
   결과가 나오면 (project.yaml.example의 예시 경로 제외) 일반화가 덜 된 것
3. `CHANGELOG.md`에 변경 항목 추가
4. 커밋 (메시지 한국어)

### 버전 릴리스 (git tag)
- 변경 성격에 따라 SemVer 결정 (VERSIONING.md):
  - MAJOR: 소비 프로젝트가 `project.yaml`을 고쳐야 하는 변경
  - MINOR: 자산 추가, 하위호환 개선
  - PATCH: 문구 교정·버그 수정
- `CHANGELOG.md`에 해당 버전 섹션 작성
- `git tag -a vX.Y.Z -m "..."` 후 `git push origin vX.Y.Z`

---

## 디렉토리 구조

```
agent-crew/
├── CLAUDE.md            # 이 파일
├── README.md            # 레포 소개·생태계 위치
├── VERSIONING.md        # 버전·동기화 정책
├── CHANGELOG.md         # 버전별 변경 이력
├── project.yaml.example # 소비 프로젝트 설정 스키마
├── agents/              # 공유 에이전트 (.md)
└── skills/              # 공유 스킬 (<name>/SKILL.md)
```

`project.yaml.example`을 고치면 = 스키마 변경 = 소비 프로젝트 영향 → MAJOR 버전.

---

## 언어

모든 응답·자산 본문·커밋 메시지는 **한국어**. 식별자(에이전트 name, frontmatter 키)는 영어 유지.

## 커밋 메시지

```
<타입>: <한국어 요약>

<본문 — 필요 시>

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
```
