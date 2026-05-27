# agent-evaluator.md — 자가검증 (Self Dogfooding)

- **대상**: `agents/agent-evaluator.md` (v0.8.0 신규)
- **채점일**: 2026-05-27
- **채점자**: 사람 (수동, agent-evaluator 자동 실행 인프라 부재로 수동)
- **rubric**: `references/conventions/asset-quality-rubric.md` v1.0
- **목적**: v0.8.0 release 조건 충족 확인 (≥32 + 차원 7≥3 + 차원 4≥3)

> *"평가 자산이 자기 평가를 못 통과하면 다른 자산을 평가할 자격 없음"* — 우아한 A1 verifier 자기검증 패턴

---

## 종합 점수

**32 / 40** — `release-pass` 영역 (32~40)

### Release 조건 체크

| 조건 | 결과 |
|---|---|
| 종합 ≥ 32 | ✅ (32 == 32, **빠듯한 통과**) |
| 차원 7 ≥ 3 | ✅ (3) |
| 차원 4 ≥ 3 | ✅ (3) |

→ **release 통과**. 단 32는 마지노선 — 정직한 한계 노출 (아래 §메타 발견).

---

## 차원별 채점

### [1] Context Efficiency — **5 / 5**

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) LLM 기본지식 중복 X | ✓ | Markdown·agent 기본 안 적힘 |
| (b) 중요 정보 시작·끝 | ✓ | "역할" 앞쪽, "핵심 원칙" 끝 |
| (c) 1024+ 토큰 안정 prefix | ✓ | 154줄, 캐시 친화 |
| (d) sub-agent 위임 권장 | ✓ | Step 3에서 "자산을 sub-agent로 호출 가능" 명시 |

**모범적**.

---

### [2] Activation Scope — **5 / 5**

자산 유형: Claude Code agent → **피호출자**. 적용 가능 항목 = description 트리거 정확도.

| 항목 | 결과 | 근거 |
|---|---|---|
| description 트리거 키워드 | ✓ | "채점", "rubric", "8차원", "자산 평가", "직교" 등 풍부 |
| 호출 조건 명시 | ✓ | "ops-pilot이 sync·PR 검증 시 호출" |
| `globs`/`alwaysApply` | — | 해당 자산 유형 미적용 (감점 X) |

**모범적**.

---

### [3] Invocation Guarantee — **3 / 5**

자산 유형: 피호출자.

| 항목 | 결과 | 근거 |
|---|---|---|
| 호출자 (ops-pilot) Level 보장 | △ | ops-pilot의 자동 호출 메커니즘 미확인 (다른 레포) |
| description 트리거 정확도 | ✓ | 매우 정확 |

피호출자 채점: 호출자 Level 미확인 + description 정확 → **3점** (보수적).

ops-pilot이 Level 2+ 보장이 확인되면 5점으로 상향 가능. **rubric 피드백**: 피호출자 채점 시 호출자 메커니즘이 다른 레포에 있는 경우 어떻게 다룰지 모호.

---

### [4] Determinism — **3 / 5**

**자산 분류**: LLM 호출 + temp 명시 없음(기본>0) → 비결정 자산. Pass^k 의미 있음. 단 실측 환경 없어 Step 2A 정성만.

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 비결정 단계 명시 | ✓ | "결정적 자산은 Step 3 skip" 명시 — 자기가 LLM임 인식 |
| (b) 결정성 강화 설계 | △ | temp=0 명시 없음, 캐싱 언급 없음. JSON schema는 결정성 보강 |
| (c) LLM-as-Judge 대체 | ✗ | 본질적으로 LLM-as-Judge — verdict 판정도 LLM |
| (d) 결정성 패턴 | △ | "rubric의 한계 인정"이 메타-결정성 자각 |

**점수**: 3 (정성 절반 충족, 본질적 한계 인정).

---

### [5] Portability — **4 / 5**

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 하드코딩 X | ✓ | `{vault.path}`, `{rawPrefix}` 플레이스홀더 |
| (b) 다중 IDE | △ | Claude Code agent frontmatter 종속 |
| (c) project.yaml 의존만 명시 | ✓ | "작업 전 — 프로젝트 설정 로드" 섹션 |
| (d) 스택 분리 | — | 본 자산은 스택 무관 |

**감점 1점**: (b) Claude Code 종속 불가피.

---

### [6] Composability — **4 / 5**

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 역할 명시 | ✓ | "자산 구조 8차원", "자산을 수정하지 않는다" |
| (b) 종료조건 | ✓ | JSON 출력 + vault append 후 종료 |
| (c) 격리 컨텍스트 (피호출자) | △ | description에 "isolated 권장" 명시적 힌트는 약함 |
| (d) Decision avoidance 회피 | ✓ | verdict 명시 강제, "rubric 한계 인정" |

**감점 1점**: (c) description에 isolation 힌트 명시 미흡.

---

### [7] Verifiability — **3 / 5**

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 결정적 verifier | △ | JSON schema는 명시, 점수 "옳음" 검증은 LLM-as-Judge |
| (b) 자동화 가능 완료기준 | ✓ | JSON schema (asset, scores, total, verdict 등) |
| (c) ops-pilot 자동 측정 | ✓ | verdict 필드가 정책 입력 |
| (d) in-context | ✓ | RAG 의존 없음 |

verifier 부재 강도: **3점** — executable schema validation은 가능하나 정답 매칭 모호. ("점수가 옳은가" verifier는 본질적으로 LLM evaluator의 자기 한계)

---

### [8] Asset Type Fitness — **5 / 5**

| 항목 | 결과 | 근거 |
|---|---|---|
| (a) 자산 유형 적합 | ✓ | 메타 평가 = agent 역할에 적합 |
| (b) 다른 자산 중복 X | ✓ | work-evaluator와 직교 명시. proposal-reviewer와 책임 분리 |
| (c) description 트리거 키워드 | ✓ | 풍부 (직교·8차원·rubric 등) |
| (d) 표준 컨벤션 | ✓ | frontmatter·allowed-tools·구조 표준 |

**모범적**.

---

## 메타 발견 (자가검증 결과)

### 발견 A — 자가검증의 본질적 한계 (거짓 양성 위험)

자기 자신을 채점하면 **후하게 보는 경향**이 있을 수 있음. 32점은 빠듯한 통과 — 이 점수가 *진짜 32*인지, *자기 호의로 +2~3*인지 확신 어려움.

→ **권장 보강**: agent-evaluator를 **다른 사람** 또는 **다른 LLM**이 동일 rubric으로 재채점. 두 점수 일치하면 32점 신뢰. 큰 차이면 자가 호의 의심.

### 발견 B — 차원 4·7이 마지노선 3점 — 본질적 한계 인정

차원 4 (결정성): LLM evaluator는 본질적으로 LLM-as-Judge. *"deterministic 모델로 대체"*를 자기 자신에게 적용하면 *"자기 부정"*. 한계 명시는 정직하나 점수는 낮음.

차원 7 (검증 가능성): *"점수가 옳은가"*의 verifier는 evaluator 자신이 만들 수 없음. 외부 사람·다른 LLM 비교만이 진짜 검증.

→ **rubric 피드백 §7 (신규)**: 평가 자산 자체에 대한 차원 4·7 채점은 본질적 한계가 있음. *"메타 평가는 항상 외부 검증을 동반"*이 권장 운영 원칙.

### 발견 C — 차원 3 피호출자 채점에서 호출자 레포 의존성

ops-pilot의 sync 호출 보장 메커니즘은 다른 레포에 있음. 본 자산 채점만으로 차원 3을 5점 줄 수 없음 (정보 부재).

→ **rubric 피드백 §8 (신규)**: 피호출자 채점 시 호출자가 *다른 레포*에 있으면 채점 한계 명시. 호출자 정보 없이는 기본 3점 (description 정확도만 평가).

### 발견 D — Release 통과는 했으나 정직한 risk

32 == 32 마지노선 통과. 자가검증 거짓 양성 가능성 + 차원 4·7 본질적 한계 인정.

→ **권장**: v0.8.0 git tag는 **추가 검증 후** — proposal-reviewer 같은 다른 자산을 agent-evaluator로 채점해 일관성 확인 + ops-pilot에서 실제 실행 확인.

---

## v0.8.0 Release 결정 (제안)

| 옵션 | 설명 | 추천 |
|---|---|---|
| A | release 통과 → main 머지 + git tag v0.8.0 즉시 | 빠르지만 자가 호의 risk |
| B | release 통과 → main 머지만, tag는 외부 검증 후 | ⭐ 균형 |
| C | release 보류 → 외부 검증·다른 자산 채점 후 tag | 가장 보수적 |

→ **추천 B** — 32점은 release-pass 영역이지만 마지노선이고 자가검증 한계 인정. main 머지로 변경은 통합하되, **git tag는 다음 자산 채점 결과(또는 외부 검증) 후**로 미룸. 이게 *"검증 후 태그"* 정책의 정신과 일치.

---

## 다음 행동 제안

1. **rubric 피드백 §7·§8을 99번에 반영** (자가검증의 메타 한계 명시) — 작음
2. **main 머지** (v0.7.0 패턴) — feat/agent-evaluator-v0.8.0 → main
3. **v0.8.0.md 검증 섹션 업데이트** — 자가검증 결과 32/40 + Option B 결정 기록
4. **tag는 보류** — 후속: proposal-reviewer 자산을 agent-evaluator로 채점 → 일관성 확인 후 tag
