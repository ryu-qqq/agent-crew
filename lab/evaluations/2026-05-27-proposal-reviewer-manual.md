# proposal-reviewer.md — 외부 검증 채점 (Option B)

- **대상**: `agents/proposal-reviewer.md` (v0.5.0)
- **채점일**: 2026-05-27
- **채점자**: 사람 (수동, agent-evaluator 자동 실행 인프라 부재)
- **rubric**: `references/conventions/asset-quality-rubric.md` v1.0
- **목적**: agent-evaluator의 일관성 외부 검증 — 자가검증(32) + work-evaluator(26)에 *제3 자산* 점수를 추가해 패턴 확인

---

## 종합 점수

**30 / 40** — `improve-recommended-release` 영역 (24~31)

### 자가검증 + 다른 자산 비교

| # | 차원 | proposal-reviewer | work-evaluator | agent-evaluator |
|---|---|---|---|---|
| 1 | Context Efficiency | **3** | 4 | 5 |
| 2 | Activation Scope | 5 | 3 | 5 |
| 3 | Invocation Guarantee | 3 | 2 | 3 |
| 4 | Determinism | 3 | 2 | 3 |
| 5 | Portability | **3** | 4 | 4 |
| 6 | Composability | 4 | 4 | 4 |
| 7 | Verifiability | **4** | 2 | 3 |
| 8 | Asset Type Fitness | 5 | 5 | 5 |
| **종합** | | **30** | **26** | **32** |

---

## 차원별 채점 (요약)

### [1] Context Efficiency — 3 / 5

(c) ✓ 토큰 충분 · (a) ✓ 기본지식 X · (b) △ 정책 표가 중간 위치 · (d) ✗ sub-agent 권장 없음.

### [2] Activation Scope — 5 / 5

description 트리거 매우 풍부 ("proposal", "검토", "approve/reject/revise"). 호출 조건 명확 ("work-evaluator가 만든 ... 검토").

### [3] Invocation Guarantee — 3 / 5

피호출자. ops-pilot 호출자 Level 미확인 (다른 레포) + description 정확 → 3점 (자가검증과 같은 한계).

### [4] Determinism — 3 / 5

LLM 호출 비결정 자산. **정책 표** (`cursor_rule` 신규/중복 별 risk·autoApply)가 결정 규칙 일부 흡수 → work-evaluator(2)보다 높음.

### [5] Portability — 3 / 5

**🔴 발견 — `작업 전 — 프로젝트 설정 로드 (필수)` 섹션 누락**. work-evaluator·agent-evaluator는 있는데 이 자산만 없음. 일관성 결여.

→ proposal-reviewer 개선 포인트로 별도 기록 (아래 §발견).

### [6] Composability — 4 / 5

(a) 역할 명시 · (b) JSON 종료 · (d) decision 강제. (c) isolation 힌트 없음 → 1점 감점.

### [7] Verifiability — 4 / 5

**최고 점수.** 이유:
- JSON schema 명확 (reviews[].decision, confidence, risk, autoApply, rationale, conflicts)
- **정책 표가 부분 정답 매칭** (예: `cursor_rule 중복 없음 → low risk/autoApply true`) — schema validation 이상

→ 차원 7 4점은 *결정적 규칙 일부 명시* 인정. work-evaluator(2)·agent-evaluator 자가(3)보다 우수.

### [8] Asset Type Fitness — 5 / 5

표준 컨벤션·역할 분리·description 모범적.

---

## 🔍 일관성 분석 — rubric은 작동하는가?

### 일관된 패턴 (rubric이 자산 특성 반영)

| 차원 | 세 자산 점수 패턴 | 해석 |
|---|---|---|
| 8 (Asset Type Fitness) | 모두 5 | agent-crew agents 표준 컨벤션 잘 따름 |
| 3 (Invocation) | 2~3 | 피호출자 한계 (호출자가 다른 레포) |
| 4 (Determinism) | 2~3 | 모두 LLM 호출 비결정 자산 |
| 6 (Composability) | 4 | 역할 분리·종료조건 모범적 |

### 유의미한 차이 (자산 특성 반영)

| 차원 | 차이 | 근거 |
|---|---|---|
| 5 (Portability) | reviewer 3 vs 다른 둘 4 | reviewer는 project.yaml 로드 절차 누락 |
| 7 (Verifiability) | reviewer 4 vs 자가 3 vs work-eval 2 | 정책 표·JSON schema 명시 정도 차이 |
| 1 (Context Efficiency) | reviewer 3 vs work-eval 4 vs agent-eval 5 | 자산 크기·sub-agent 명시 정도 |

→ **무작위 점수가 아니라 *근거 있는* 차이**. rubric이 일관되게 작동.

### 종합 — Option B 완성 여부

세 자산 점수 분포:
- 32 (자가) — release-pass 마지노선
- 30 (외부) — improve-recommended
- 26 (외부) — improve-recommended

**reevaluate(<24)나 redesign(<16)이 없음** = 자산들이 기본은 갖춤 (rubric이 너무 가혹하지 않음).
**모두 5점 만점 차원이 8(Asset Type Fitness) 하나** = rubric이 과도 점수도 아님.

→ **agent-evaluator 일관성 검증 통과**. v0.8.0 git tag 정당화 가능.

---

## 🔥 부수 발견 — proposal-reviewer 개선 포인트

### 발견 §A — `작업 전 — 프로젝트 설정 로드` 섹션 누락

다른 agents(work-evaluator, agent-evaluator, journal-recorder 등)는 모두 본문 첫 섹션이 *"작업 전 — 프로젝트 설정 로드 (필수)"*. proposal-reviewer만 누락.

**의도된 누락인가**? proposal-reviewer는:
- ops-pilot이 clone 환경에서 호출 (다른 자산과 호출 맥락 다름)
- project.yaml을 직접 안 봐도 work-evaluator가 전달한 draft만으로 동작 가능
- 따라서 *의도된 단순화*일 가능성

→ **이게 의도되었다면 본문에 명시 권장**: *"본 자산은 ops-pilot이 전달하는 draft·clone path만으로 동작하며 project.yaml을 직접 읽지 않는다"*. 명시되면 차원 5는 4점 (의도된 단순화 인정).

### 발견 §B — `Step 2`의 외부 사람 점수 비교가 가능해짐

자가(32)와 사람 외부(26·30) 점수 차이 = -6 ~ -2. 자가검증이 *과대* 평가 경향 시사 (절대값은 작음, 통계적 의미 X).

→ **rubric 피드백 §9 (신규)**: 자가검증 점수와 외부 평균의 차이를 *"자가 호의 보정 계수"*로 기록. 후속 self-evaluation에서 이 계수만큼 차감 권장.

### 발견 §C — 차원 1 평균이 자산 크기에 의존

agent-evaluator(154줄) 5 / work-evaluator(147줄) 4 / proposal-reviewer(67줄) 3.

작은 자산이 차원 1 (context efficiency) 낮은 패턴 — 이게 *진짜*인지(작아서 sub-agent 위임 명시 누락 등) vs *측정 한계*인지 (작은 자산이 본질적으로 좋은데도 점수 낮게 나옴) 추가 조사 필요.

→ **rubric 피드백 §10 (신규)**: 차원 1의 측정 항목이 자산 크기에 편향되지 않는지 검증. 작은 자산은 다른 가중치?

---

## v0.8.0 git tag 결정 (제안)

| 옵션 | 설명 | 추천 |
|---|---|---|
| A | 외부 검증 1개 완료 → 지금 즉시 `git tag v0.8.0` | 빠름, 정책 일부 충족 |
| B | ops-pilot 실제 실행까지 완료 후 tag | ⭐ 완전 검증, 정책 완벽 |
| C | proposal-reviewer 발견(§A) 반영 후 tag | 자산 자체 개선 우선 |

→ **추천 A** — 외부 검증 1개 일관성 확인됐고 rubric 작동 검증. ops-pilot 실행은 별도 인프라 작업으로 분리. 발견 §A는 별도 minor patch (v0.8.1)로.

---

## 다음 행동 제안

1. **v0.8.0.md 검증 섹션에 외부 검증 결과 추가** + git tag 결정 기록
2. **git tag v0.8.0** (Option A 채택 시)
3. **proposal-reviewer §A·rubric §9·§10**을 v0.8.1 또는 99번 v1.1에 반영 (별도 Task)
