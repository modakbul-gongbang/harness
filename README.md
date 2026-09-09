# harness-session

**Harness Engineering** 세션을 위한 Claude Code 스킬과 실습 자료.
플러그인 없이 필요한 스킬만 바로 설치할 수 있으며, 기존 플러그인 방식도 지원한다.

AI 에이전트가 잘 일하는 환경을 설계하는 기술 - Harness Engineering의 핵심 개념을 실습하고, 바로 써볼 수 있는 스킬과 자료를 제공한다.

## Skills

| Skill | 설명 | 사용법 |
|-------|------|--------|
| **check-harness** | 개발 환경·컨텍스트·Hook·검증 체계 종합 점검 + 근거 기반 개선 권장 | `/check-harness` |
| **scaffold** | 기술·의존성 선택부터 동작하는 기준 예제와 하네스 구성까지 계획·구현 | `/scaffold` |
| **specify** | 목표를 구조화된 구현 계획(spec.md)으로 변환 | `/specify "목표"` |
| **deep-interview** | Socratic 방식의 요구사항 인터뷰 (Ambiguity Score 기반) | `/deep-interview "주제"` |

## Scaffold 사용 범위

`/scaffold`는 언어·프레임워크·DB·라이브러리를 프로젝트 요구와 기존 팀 환경에 맞춰 선택하고, 구조와 하네스를 계획한 뒤 요청에 따라 실제 구현까지 진행한다.
설치된 도구만으로 기술을 결정하지 않으며, 새 의존성의 공식 호환성 근거와 실제 빌드 결과를 구분한다.
기존 프로젝트에서는 현재 구조와 작업 중인 파일을 보존하고 합의한 범위만 보강한다.
작은 프로젝트는 필요한 선택만 묻고, 계획만 요청했다면 구현하지 않는다.

구현 결과는 선택한 DB·API 계약을 사용하는 기준 예제 하나와 테스트, 실제 실행·검증 명령, 필요한 지침·스킬·Hook이다.
검증 보고서는 실제 실행 결과와 미확인 항목을 구분하며, Hook fixture 통과를 runtime 활성화 성공으로 쓰지 않는다.
필요한 UI 검증이 막혔다면 완료로 보고하지 않는다.
절차는 [scaffold SKILL.md](skills/scaffold/SKILL.md), 세부 검증 기준은 [verification.md](skills/scaffold/references/verification.md), 지시 준수 점검 사례는 [evals.json](skills/scaffold/evals/evals.json)에 있다.

## Materials

### Harness Engineering 체크리스트

`materials/harness-checklist.md`

강의에서 하네스 설계 개념을 설명하기 위한 자가진단 참고 자료다.
현재 `/check-harness`의 실행 기준이나 결과 점수표가 아니다.
스킬의 점검 절차는 [SKILL.md](skills/check-harness/SKILL.md), 항목별 판정 기준은 [checklist.md](skills/check-harness/references/checklist.md)를 따른다.

- **준비 (Scaffolding)** - AI가 프로젝트를 스스로 파악할 수 있는가
- **맥락 (Context)** - CLAUDE.md, 규칙, 점진적 노출
- **실행 설계 (Execution)** - 계획, 위임, 오케스트레이션
- **검증 (Verification)** - 테스트, 리뷰, 품질 관리
- **개선 (Improvement)** - 학습, 피드백 루프

### 발표 슬라이드

`materials/slides/`

"Harness Engineering - AI가 잘 일하는 환경을 설계하는 기술" 세션 발표 자료.
HTML 슬라이드 50장 + `viewer.html`로 로컬에서 바로 열어볼 수 있다.

```bash
# 슬라이드 뷰어 열기
open materials/slides/viewer.html
```

## 설치하고 바로 사용하기

[grab-skills](https://github.com/yansfil/grab-skills)의 설치 방식처럼 [skills CLI](https://github.com/vercel-labs/skills)를 사용한다.
Node.js와 `npx`, Claude Code가 필요하다.

### 현재 프로젝트에 설치 (권장)

사용할 프로젝트 폴더에서 실행한다.
기존에 같은 이름의 스킬이 있다면 설치 전에 내용을 비교하고 백업한다.

```bash
npx --yes skills add modakbul-gongbang/harness --skill check-harness --agent claude-code --copy
claude
```

새 Claude Code 세션에서 호출한다.

```text
/check-harness
/check-harness --verify
```

`.claude/skills/check-harness/`에 SKILL.md와 참조 문서가 함께 복사된다.
`--agent claude-code`는 설치 대상을 Claude Code로 한정한다.
설치기는 버전 추적용 `skills-lock.json`도 생성할 수 있다.
프로젝트 설치 파일은 팀 공유가 필요하면 lock 파일과 함께 Git에 포함한다.

### 다른 스킬 또는 모든 프로젝트에서 사용

```bash
# 설치 가능한 스킬 목록만 확인
npx --yes skills add modakbul-gongbang/harness --list

# 원하는 스킬 선택
npx --yes skills add modakbul-gongbang/harness --skill specify --agent claude-code --copy

# 모든 스킬을 Claude Code에만 설치
npx --yes skills add modakbul-gongbang/harness --skill '*' --agent claude-code --copy

# check-harness를 사용자 범위에 설치해 모든 프로젝트에서 사용
npx --yes skills add modakbul-gongbang/harness --skill check-harness --agent claude-code --copy -g
```

사용자 설치 위치는 `~/.claude/skills/check-harness/`다.
업데이트는 같은 설치 명령을 다시 실행하고 새 세션을 시작한다.
복사 설치이므로 이 저장소를 `git pull`하는 것만으로 설치된 스킬이 갱신되지는 않는다.
제거할 때는 설치한 범위의 해당 스킬 폴더만 삭제한다.

### npx 없이 수동 설치

저장소를 clone한 뒤, 사용할 프로젝트에서 스킬 폴더 전체를 복사해도 된다.
아래 `harness` 경로는 실제 clone 위치로 바꾼다.

```bash
git clone https://github.com/modakbul-gongbang/harness.git /path/to/harness
mkdir -p .claude/skills
# 기존 설치가 있으면 중단하여 덮어쓰기를 방지한다.
test ! -e .claude/skills/check-harness && test ! -L .claude/skills/check-harness && cp -R /path/to/harness/skills/check-harness .claude/skills/check-harness
```

### 설치 범위와 플러그인 방식

직접 설치는 **스킬과 그 폴더의 참조 파일**을 설치한다.
저장소의 `agents/`, `hooks/hooks.json`, 권한 설정, MCP, LSP 서버는 설치하지 않는다.
`check-harness`는 직접 조사하는 것이 기본이며 서브에이전트 호출은 필수가 아니다.
저장소의 agent 파일 수는 점검 시 호출하는 에이전트 수를 뜻하지 않는다.
다른 스킬이 요구하는 브라우저 도구 등의 실행 의존성은 각 SKILL.md를 확인한다.
현재 `hooks/hooks.json`은 빈 템플릿이며 자동으로 활성화할 Hook은 없다.

기존 플러그인 방식은 `claude --plugin-dir /path/to/harness`로 사용할 수 있다.
직접 설치 호출은 `/check-harness`, 플러그인 호출은 `/harness-session:check-harness`다.
중복 노출을 피하려면 한 방식을 선택한다.
이 저장소 자체에서 실습할 때는 clone한 폴더에서 `claude`를 실행하면 된다.

명령이 보이지 않으면 새 세션에서 `/` 목록과 설치 경로의 `SKILL.md`를 확인한다.
참조 파일을 못 찾으면 SKILL.md 한 파일만 복사하지 않았는지 확인한다.
개인 스킬과 프로젝트 스킬에 같은 이름이 있다면 적용 범위를 확인한다.
직접 스킬 경로와 지원 파일 구성은 [Claude Code 공식 문서](https://code.claude.com/docs/en/skills)를 따른다.

## Project Structure

```
.claude-plugin/plugin.json    # Plugin manifest
skills/
  check-harness/SKILL.md      # 개발 준비 상태 종합 점검
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

### 직접 설치 검증

2026-09-09에 빈 임시 프로젝트에서 위 GitHub 설치 명령을 `-y`로 실행해 성공했다.
SKILL.md와 세 참조 문서가 원격 원본과 일치하는 실제 파일로 복사됨을 확인했다.
Codex 설치 폴더와 Claude settings.json은 생성되지 않았다.
플러그인·스킬 형식 검사는 통과했으며, 이 설치 smoke에서 실제 종합 점검이나 LSP·Hook 기동까지 실행하지는 않았다.
