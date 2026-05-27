# work-evaluator.md — 수동 채점 (인간 dogfooding)

- **대상**: `agents/work-evaluator.md` (v0.3.0~)
- **채점일**: 2026-05-27
- **채점자**: 사람 (수동)
- **rubric 버전**: v1.0 (정정 후, ryu-qqq-wiki/wiki/research/agent-engineering/99-evaluation-framework.md)
- **목적**: agent-evaluator 자산 구현 *전에* rubric 자체의 작동 가능성을 사람 채점으로 검증 (silver bullet 회피)

---

## 종합 점수

**26 / 40** (관대 해석) · **22 / 40** (차원 7 엄격 해석 시)

이 점수의 의미가 *"좋은지 나쁜지"*는 **rubric에 임계값이 없어 판단 불가** — 이것 자체가 발견 (rubric 피드백 §6 참조).

---

## 차원별 채점

### [1] Context Efficiency — 4 / 5

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) LLM 기본지식 중복 X | ✓ | Markdown 문법·agent 기본 개념 안 적힘 |
| (b) 중요 정보 시작·끝 위치 | ✓ | "역할" 앞쪽, "핵심 원칙" 끝 |
| (c) 1024+ 토큰 안정 prefix | ✓ | 147줄로 캐시 친화 |
| (d) sub-agent 위임 권장 명시 | △ | 본문에 isolation 권장 없음 |

**감점 1점**: (d) 누락.

---

### [2] Activation Scope — 3 / 5

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 항상 vs 조건부 명시 | △ | description에 "파이프라인 마지막 단계 또는 사용자 요청" 정도 |
| (b) globs/applies-when 명시 | ✗ | frontmatter에 없음 |
| (c) 활성화 위치 정보 적절 | ✓ | 호출 시점 명확 |

**감점 2점**: (b) Claude Code agent frontmatter에 globs/applies-when 필드 자체가 없음 — **rubric 피드백 §4 참조** (해당 자산 유형에 적용 가능한 항목인지 분류 필요).

---

### [3] Invocation Guarantee — 2 / 5

본 자산은 **피호출자** (호출자는 engineering-os skill 또는 사용자). 호출 보장은 호출자 책임.

| 항목 | 결과 | 근거 |
|---|---|---|
| Level | 1.5 | description 명시 호출 권고 있음, 강제 없음 |

**감점 3점**: 자기 자산에서 호출 강제 메커니즘 없음. 단 — **rubric 피드백 §1 참조**: 피호출자는 본질적으로 자기 책임이 아닌데 같은 척도로 채점하는 게 옳은가.

---

### [4] Determinism — 2 / 5

**자산 분류**: LLM 호출 핵심 + temp 명시 없음 → 비결정 자산. Pass^k 측정 의미 있음. 하지만 **실행 환경 없이 사람이 측정 불가**.

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) "결정적 vs 비결정" 분류 | ✗ | 본문에 분류 없음 |
| (b) Pass^k 통과율 | — | 측정 불가 (사람 채점 한계) |
| (c) executable verifier | ✗ | "근거 필수"만, 자동 검증 X |
| (d) LLM 학습/검증 분리 | ✗ | 모두 LLM 평가 의존 |

**감점 3점**: 비결정 요소 의식 없음 + verifier 없음. — **rubric 피드백 §2 참조**: Pass^k를 사람이 채점할 수 없는 한계.

---

### [5] Portability — 4 / 5

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 하드코딩 없음 | ✓ | `{vault.path}`, `{rawPrefix}` 플레이스홀더 |
| (b) 다중 IDE | △ | Claude Code agent frontmatter 종속 |
| (c) project.yaml 의존만 명시 | ✓ | "작업 전 — 프로젝트 설정 로드" 섹션 |
| (d) 스택 분리 | — | 본 자산은 스택 무관 |

**감점 1점**: (b) Claude Code 종속은 불가피하나 명시 X.

---

### [6] Composability — 4 / 5

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 역할 명시 | ✓ | "평가 전담 — 작업을 수정하지 않는다" |
| (b) 종료조건 | ✓ | 매니페스트 출력 후 종료 |
| (c) 격리 컨텍스트 권장 | △ | sub-agent isolation 명시 없음 |
| (d) Decision avoidance 회피 | ✓ | "근거 없는 점수 금지" 결정 강제 |

**감점 1점**: (c) — **rubric 피드백 §5 참조**: 자기 자산에서 isolation 권장을 적는 게 자연스러운가, 호출자 결정사항 아닌가.

---

### [7] Verifiability — 2 / 5 (관대) · 0 / 5 (엄격)

**핵심 갈등** — 출력 매니페스트 포맷은 schema 검증 가능하나, *"점수가 옳은지"* 검증 verifier는 없음 (LLM-as-Judge).

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 결정적 verifier 존재 | ✗ | completion 검증 verifier 부재 |
| (b) 자동화 가능 완료기준 | △ | 출력 schema 일부 명시 |
| (c) ops-pilot 자동 측정 | △ | proposals JSON은 schema 검증 가능 |
| (d) in-context | ✓ | RAG 의존 없음 |

**해석**:
- 엄격 (rubric 그대로): "verifier 미명시 = 즉시 0점" → **0점**
- 관대 (부분 verifier 인정): **2점**

→ **rubric 피드백 §3 참조**: 특별 규칙이 *"완전 부재"* vs *"부분 부재"* 구분 필요.

---

### [8] Asset Type Fitness — 5 / 5

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 자산 유형 적합 | ✓ | 평가는 agent 역할에 정확 |
| (b) 다른 자산 중복 X | ✓ | journal-recorder와 명확히 분업 |
| (c) description 트리거 키워드 | ✓ | 풍부 ("채점", "evaluation", "vault raw/") |
| (d) 표준 컨벤션 | ✓ | frontmatter·구조·Markdown 모두 표준 |

**감점 0점**: 모범적.

---

## 🔥 rubric 자체에 대한 피드백 (6건)

### §1. 차원 3 — 호출자 vs 피호출자 분류 필요

work-evaluator처럼 *피호출되는* 자산은 호출 보장을 자기가 책임지지 않는다. 호출자(engineering-os skill)의 alwaysApply가 호출 보장의 본질.

**제안**: 차원 3 채점 전 자산 분류 추가:
- **호출자** (skill·orchestrator·CLAUDE.md): Level 1~3 그대로 적용
- **피호출자** (agent·sub-agent): "이 자산을 호출하는 자산이 강제 호출 보장하는가" 검사 + 본 자산은 "description 트리거 정확도"로 채점

### §2. 차원 4 — 사람 채점 시 Pass^k 측정 불가

수동 채점에서 Pass^k를 돌릴 환경 없음. 그러나 *"비결정 요소가 있는지"*는 사람도 판단 가능.

**제안**: 차원 4를 2단계로:
- **Step A (정성)**: 자산 spec이 비결정 요소를 명시·분류했는가 (사람 채점 가능)
- **Step B (정량)**: Pass^k 실측 (LLM evaluator만 가능, 사람은 skip)

수동 채점 시 Step A만으로 1~5점 매김. Step B는 LLM evaluator가 추가 점수 보강.

### §3. 차원 7 — "0점 특별 규칙" 모호함

*"verifier 미명시 = 0점"*이 *"완전 부재"*인지 *"부분 부재"*인지 불명. work-evaluator처럼 *출력 schema만 있고 정답 검증은 없는* 경우 어디인가?

**제안**:
- **Hard 0점**: verifier·schema·매니페스트 포맷 모두 없음
- **2점**: 부분 — 매니페스트 schema는 있으나 점수·결정의 정합 검증 없음
- **3~5점**: executable verifier 점진 강화

### §4. 차원 2 — 자산 유형별 적용 가능 항목 분리 필요

`globs`/`applies-when`은 Cursor 룰·Claude Code skill에는 있어도, **Claude Code agent frontmatter에는 없는 필드**. 그런데 같은 척도로 채점하면 agent는 항상 감점.

**제안**: 자산 유형별 채점 가능 항목 표 작성:
- Cursor `.mdc`: globs, alwaysApply 적용
- Claude Code `agents/*.md`: description 트리거 정확도, frontmatter 표준성으로 대체
- Claude Code `skills/<n>/SKILL.md`: description progressive disclosure

### §5. 차원 6 — "isolation 권장 명시"의 책임 주체 모호

격리 컨텍스트는 *호출자가* 결정 (sub-agent로 부르거나 main에서 부르거나). 그런데 *피호출자 자산*이 자기 본문에 "isolation 권장"을 적는 게 자연스러운가?

**제안**: 차원 6 (c) 항목을:
- **호출자 자산**: 무거운 sub-task를 sub-agent로 위임하는 가이드 명시했는가
- **피호출자 자산**: 호출자에게 "이 자산은 isolated 권장" 힌트 제공했는가 (description에)

### §6. 임계값 부재 — 26/40이면 좋은가 나쁜가?

rubric에 *"X점 이상 release 통과"*, *"Y점 미만 개선 필수"* 같은 임계값 없음. 점수만 나와도 *"so what"*.

**제안 임계값**:
- **32+ (80%)**: release 통과 (work-evaluator 4축 모두 ✓에 해당)
- **24~31 (60~79%)**: 개선 권장 release 가능
- **<24 (60% 미만)**: 개선 후 재평가

work-evaluator는 26점 → "개선 권장 release 가능" 영역. 합리적 판정.

---

## 메타 발견 (dogfooding 결과)

### 발견 A — Rubric은 작동한다, 단 정정 6건 필요

채점 자체는 가능했고 차원별로 합리적 근거가 나왔다. 그러나 위 6개 피드백을 반영하지 않으면 LLM evaluator 구현 시 같은 모호함에 부딪칠 것.

### 발견 B — work-evaluator의 "4축"과 evaluator의 "8차원"은 직교

- **work-evaluator**: 작업·산출물 *내용 품질*을 4축으로
- **agent-evaluator (8차원)**: 자산 *구조 품질*을 8차원으로

→ 두 평가는 **다른 층위**. work-evaluator가 잘 만들어졌나(8차원) ≠ work-evaluator가 잘 평가하나(4축).

→ rubric에 *"자산이 평가하는 차원과, 자산이 평가받는 차원은 다르다"* 명시 필요할 수도.

### 발견 C — 사람 채점이 LLM 채점보다 *나은 부분*

- 자산의 *맥락적 합리성* — work-evaluator가 *"근거 없는 점수 금지"*라고 적은 것 자체가 차원 6(d) Decision avoidance 회피의 직접 매개 — 이런 *간접* 충족은 LLM이 놓치기 쉬움
- *역할 직관* — work-evaluator가 피호출자임을 직관적으로 알아채는 데 사람이 유리

### 발견 D — LLM 채점이 사람보다 *나은 부분*

- Pass^k 정량 측정 (사람 불가)
- 대규모 일관성 (사람은 자산 5개 넘어가면 기준 흔들림)
- 시간 효율 (사람 1시간 / 자산 vs LLM 1분 / 자산)

→ **결합 모델**: 사람은 정성·맥락, LLM은 정량·일관성. agent-evaluator 구현 시 *"사람 점수와 차이가 큰 차원은 사람 검토 요청"* 흐름이 자연스러움.

---

## 다음 행동 제안

1. **rubric 정정 6건을 99번에 반영** (이번 채점의 직접 산출물)
2. (선택) 1개 자산 더 수동 채점 — 점수 일관성 검증 (예: proposal-reviewer.md 또는 notion-manager.md)
3. **그 다음** agent-evaluator 구현 — 사람 채점 결과(26/40)가 LLM 채점과 얼마나 일치하는지 비교 기준 확보
