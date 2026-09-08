# harness-session

**Harness Engineering** 세션을 위한 Claude Code 플러그인.

AI 에이전트가 잘 일하는 환경을 설계하는 기술 — Harness Engineering의 핵심 개념을 실습하고, 바로 써볼 수 있는 스킬과 자료를 제공한다.

## Skills

| Skill | 설명 | 사용법 |
|-------|------|--------|
| **check-harness** | 현재 프로젝트의 개발 환경·LSP·Hook·검증을 근거로 종합 점검 | `/check-harness` |
| **scaffold** | Greenfield 프로젝트에 AI-optimized 하네스 구조를 스캐폴딩 | `/scaffold` |
| **specify** | 목표를 구조화된 구현 계획(spec.md)으로 변환 | `/specify "목표"` |
| **deep-interview** | Socratic 방식의 요구사항 인터뷰 (Ambiguity Score 기반) | `/deep-interview "주제"` |

## Materials

### Harness Engineering 체크리스트

`materials/harness-checklist.md`

AI가 잘 일하는 환경을 설계하기 위한 자가진단 체크리스트. 3단계 성숙도(L1 시작하기 → L2 내 것으로 만들기 → L3 자율 운영)로 나뉘며, 5개 축에 걸쳐 35개 항목을 점검한다.

- **준비 (Scaffolding)** — AI가 프로젝트를 스스로 파악할 수 있는가
- **맥락 (Context)** — CLAUDE.md, 규칙, 점진적 노출
- **실행 설계 (Execution)** — 계획, 위임, 오케스트레이션
- **검증 (Verification)** — 테스트, 리뷰, 품질 관리
- **개선 (Improvement)** — 학습, 피드백 루프

### 발표 슬라이드

`materials/slides/`

"Harness Engineering — AI가 잘 일하는 환경을 설계하는 기술" 세션 발표 자료.
HTML 슬라이드 50장 + `viewer.html`로 로컬에서 바로 열어볼 수 있다.

```bash
# 슬라이드 뷰어 열기
open materials/slides/viewer.html
```

## Quick Start

```bash
# 1. 이 플러그인이 있는 디렉토리에서 Claude Code 실행
cd harness-session
claude

# 2. 현재 프로젝트의 하네스 준비 상태 점검
/check-harness

# 3. 새 프로젝트에 하네스 스캐폴딩
/scaffold

# 4. 요구사항이 불명확할 때 인터뷰
/deep-interview 뭘 만들어야 할지 모르겠어

# 5. 목표를 구현 계획으로 변환
/specify "사용자 인증 시스템 구현"
```

## Project Structure

```
.claude-plugin/plugin.json    # Plugin manifest
skills/
  check-harness/SKILL.md      # Harness 성숙도 진단
  scaffold/SKILL.md            # 프로젝트 스캐폴딩
  specify/SKILL.md             # Goal → spec.md
  deep-interview/SKILL.md     # Socratic 인터뷰
hooks/hooks.json               # Hook 등록 (빈 템플릿)
materials/                     # 세션 발표 자료
.claude/settings.json          # Claude Code 프로젝트 설정
```

## License

Internal use only.

## 종합 점검 사용법

다른 프로젝트에서 로컬 플러그인을 시험하려면 이 저장소의 절대경로로 `claude --plugin-dir /path/to/harness`를 실행한다.
설치형 플러그인 호출은 `/harness-session:check-harness`, 이 저장소의 project skill 호출은 `/check-harness`다.

- `/check-harness`: 현재 프로젝트의 런타임, LSP, 컨텍스트, 권한, Hook, 테스트/CI, 도구와 검증 절차를 읽기 중심으로 점검한다.
- `/check-harness --verify`: 안전한 로컬 테스트/Hook fixture/LSP 조회로 동작 확인도 시도한다.
- `/check-harness user`: 사용자 설치 인벤토리만 점검한다.
- `/check-harness all`: 프로젝트와 사용자 인벤토리를 함께 점검한다.

보고서는 각 항목의 필요성, 구성 상태, 실제 동작, 근거와 다음 조치를 보여준다.
예를 들어 LSP 플러그인과 서버가 있어도 정의/참조 조회를 하지 않았다면 LSP 동작은 미확인이다.
Hook도 등록과 fixture 실행, 실제 runtime 이벤트 확인을 구분한다.
총점 대신 우선 조치 최대 3개와 미확인 항목을 제시하며 설치 개수로 평가하지 않는다.
보고서는 매번 새 임시 디렉토리의 `report.md`에 저장하며 설정이나 gitignore를 바꾸지 않는다.

현재 판정 계약은 [checklist](skills/check-harness/references/checklist.md), 설치·검증 방법과 공식 출처는 [probes](skills/check-harness/references/probes.md)에 있다.
`materials/`의 성숙도 체크리스트는 강의 참고 자료이며 현재 스킬의 판정 기준이 아니다.

### 변경 검증

[evals](skills/check-harness/evals/evals.json)는 고정된 입력과 기대 판정 사례다.
각 사례를 새 Claude 컨텍스트에서 SKILL 및 참조 문서와 함께 평가하고 기대 판정과 비교한다.
이 평가는 지시 준수 smoke이며 실제 LSP/Hook 기동이나 전체 프로젝트 감사의 대체 증거가 아니다.

### 컨텍스트 구조 리뷰

CLAUDE.md, 하위 CLAUDE.md, Rules와 연결된 가이드의 길이·역할·적용 범위·로딩 방식을 표로 보여준다.
중복, 충돌, 깨진 참조, 과도한 분할을 확인하고 현재 구조를 유지해도 되는 조건과 함께 개선을 권장한다.
긴 파일이나 Rules 부재 자체를 실패로 판정하지 않는다.
종합 리뷰는 잘 된 점과 권장 개선을 먼저 보여주며 상세 PASS/FAIL 근거는 부록에 남긴다.
