# 점검 계약 검증

2026-09-09에 수행한 변경 검증이다.

- `claude plugin validate .`: PASS, 기존 author 미기재 및 plugin 루트 CLAUDE.md 로딩 안내 경고.
- `claude plugin validate skills`: PASS.
- `claude plugin validate agents`: PASS.
- `git diff --check`: PASS.
- 체크리스트 18개 ID의 유일성, eval JSON 10개 사례와 참조 파일 존재 확인.
- 실제 CLI의 새 컨텍스트에서 도구 없이 합성 사례 10개를 일괄 평가했다.
  LSP 미기동/비활성, 깨진 Hook, malformed 설정, 최소 프로젝트, stale 결과를 구분했다.
- wrapper 존재에서 TEST1 PASS를 추정한 과잉 판정을 발견해 실제 task/config/source 확인 조건을 보강했다.
  별도의 새 컨텍스트에서 wrapper-only 및 fixture-only 두 경계를 재평가했다.
  최종 판정은 TEST1 UNKNOWN, HOOK1 UNKNOWN, HOOK2 fixture 범위 PASS였다.
  응답 초반 FAIL을 UNKNOWN으로 자체 정정한 문장이 있어 최종 출력의 단일 판정 재검토 지시도 추가했다.
  이 마지막 출력 형식 지시는 별도 실행 재검증하지 않았다.

이 검증은 합성 입력에 대한 지시 준수 smoke다.
설치형 스킬의 자동 호출, 실제 프로젝트 전체 감사, LSP 서버 기동, Hook 이벤트 실행은 이번 변경에서 수행하지 않았다.
LLM 판정이므로 JSON/schema 검증기처럼 결과를 강제하지 않는다.
보고서의 근거와 미확인 범위를 검토해야 한다.

재현하려면 SKILL.md와 두 references를 새 컨텍스트에 제공하고 `evals.json`의 prompt를 입력한다.
기대 결과는 답변을 생성할 컨텍스트에 제공하지 않고, 생성 후 사람이 비교한다.
실제 도구 검증은 별도로 안전한 프로젝트에서 `/check-harness --verify`를 수행한다.

## 컨텍스트 구조 리뷰 보완

- 긴 공통 지침 유지, 짧은 지침 충돌, import 분할, 과도한 분할의 합성 사례 4개를 추가했다.
- 새 CLI 컨텍스트에서 네 사례를 평가해 길이만으로 실패 처리하지 않고, 충돌 확인·import 동시 로딩·기존 가이드 통합을 권장하는 결과를 확인했다.
- 응답에 참조 단계 추정과 '유일한 방법' 단정이 있어 측정값/예상값 구분 및 대안 배제 금지 지시를 추가했다.
  이 마지막 문구 보완은 별도 실행 재검증하지 않았다.
- skills/agents 형식 검증과 diff 검사는 통과했다.
- 실제 프로젝트 문서의 수집·길이 측정·전체 보고서 생성은 이번 합성 평가 범위가 아니다.
