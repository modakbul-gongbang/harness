# 점검 방법

공식 문서 확인일: 2026-09-09.
설치 안내는 제안이며 감사 중 설치하거나 전역 설정을 수정하지 않는다.

## 환경과 LSP

manifest와 소스 확장자를 먼저 보고 필요한 언어만 검사한다.
서버 명령은 활성 플러그인의 실제 설정을 기준으로 PATH/실행 파일을 확인한다.
`command -v`와 안전한 버전 조회는 설치 증거이며 LSP 기동 증거가 아니다.
설정 파일과 활성 플러그인 목록을 함께 확인하고 effective scope를 알 수 없으면 UNKNOWN으로 남긴다.
활성화 상태 확인에는 현재 CLI의 `claude plugin list --help`로 지원 옵션을 확인하거나 `/plugin` 화면을 이용한다.
개인 플러그인 캐시 전체를 재귀적으로 읽지 않는다.

| 언어 | 공식 plugin | 대표 서버 binary |
|---|---|---|
| Java | jdtls-lsp | jdtls |
| TypeScript/JavaScript | typescript-lsp | typescript-language-server |
| Python | pyright-lsp | pyright-langserver |
| Go | gopls-lsp | gopls |
| Rust | rust-analyzer-lsp | rust-analyzer |
| C/C++ | clangd-lsp | clangd |

다른 언어와 사용자 정의 LSP도 실제 설정과 upstream 요구를 확인하며 위 표만 지원한다고 단정하지 않는다.
Java는 `java -version`, wrapper의 toolchain 요구와 jdtls 실행용 JDK를 각각 확인한다.
Eclipse JDT LS upstream은 현재 서버 실행에 Java 21 이상을 요구한다.
앱의 타깃 Java 버전이 더 낮아도 별개이며, 설치한 서버 버전의 요구가 우선이다.

macOS Java 설치가 필요한 경우 제안:

```sh
brew install jdtls
```

Claude Code에서:

```text
/plugin install jdtls-lsp@claude-plugins-official
```

marketplace가 없을 때만:

```text
/plugin marketplace add anthropics/claude-plugins-official
```

설치 후 PATH와 JDK를 확인하고 새 세션 또는 공식 reload 절차 후 실제 정의/참조 찾기를 수행한다.
`--verify`에서는 기존 소스의 정의와 참조를 먼저 읽고, 해당 심볼 위치에서 LSP의 정의/참조 조회를 호출한다.
관찰한 파일·라인을 보고서에 남긴다.
이 런타임에서 LSP 도구가 제공되지 않거나 서버가 기동 불가면 LSP2 BLOCKED이고, grep 결과로 대체 PASS하지 않는다.
문법 오류를 심어 diagnostics를 검증하거나 사용자 코드를 편집하지 않는다.

## 컨텍스트·권한

`@import`는 참조한 CLAUDE.md와 함께 로딩된다.
일반 Markdown 경로와 읽는 조건, 하위 CLAUDE.md, `paths`가 있는 Rules, 호출되는 Skill을 구분한다.
문장에 'X 작업 때만'이 있어도 로더의 조건부 동작을 입증하지 못한다.
`additionalDirectories` 자체도 조건부 로딩 증거가 아니다.

권한 설정과 Hook, sandbox는 다른 층이다.
`.gitignore`는 Git 추적 방지이며 도구의 파일 읽기를 차단하지 않는다.
차단 증명에 실제 비밀 파일이나 위험 명령을 사용하지 않는다.
관리 정책을 볼 수 없으면 로컬 설정만 확인했음을 명시한다.

## Hook

실제 활성 settings 및 plugin `hooks/hooks.json`에서 이벤트와 matcher를 읽는다.
command뿐 아니라 prompt/agent/http 등 사용하는 hook 타입의 공식 계약을 확인한다.
명령이 참조하는 경로, cwd, plugin root 변수, 실행 권한, 의존 도구와 timeout을 확인한다.
프로젝트 경로의 공백 처리도 확인한다.
설정 파일을 전부 출력하거나 인증 헤더 값을 보고서에 기록하지 않는다.

`--verify`에서 구현을 먼저 읽고 네트워크/파일 수정/종료 부작용이 없는 fixture 실행 방법을 찾는다.
최소 사례는 대상 성공, 의도한 실패, 비대상, 잘못된 입력이다.
Stop이면 `stop_hook_active` 재진입에서 무한 차단하지 않는지, 검증 누락/실패/stale 및 BLOCKED 종료 경로도 확인한다.
실제 runtime 이벤트를 통한 확인과 script fixture 직접 실행을 명시적으로 구분한다.
안전하게 분리할 수 없으면 HOOK2 BLOCKED로 두고 필요한 fixture를 제안한다.
매 편집 full test나 무조건 format hook을 필수로 요구하지 않는다.

## 테스트·CI·도구

- Java: `build.gradle`, `build.gradle.kts`, `settings.gradle*`, `gradlew`, wrapper JAR/properties 또는 `pom.xml`, `mvnw`, `.mvn/`.
- JS/TS: package scripts, lockfile, runner config.
- Python/Rust/Go: pyproject/pytest, Cargo, go.mod와 해당 검증 진입점.
- 공통 `scripts/verify.sh`나 Makefile이 있으면 새 명령 대신 먼저 사용.

`--verify`에서는 README와 실제 script를 읽고 로컬 검증 명령을 timeout과 함께 실행한다.
원격 DB나 외부 서비스에 쓰거나 의존성을 설치해야 하면 자동 진행하지 않고 BLOCKED로 남긴다.
로그의 실패, 건너뛴 검사, 0 tests를 종료코드와 함께 읽는다.
중첩 앱의 `.github/workflows`가 부모 리포에서 실행되는 것으로 오인하지 않는다.
원격 CI를 조회하지 않았다면 CI 구성 확인만 PASS이며 원격 실행은 미확인이다.
MCP나 CLI 동작 점검은 `--verify`에서 인증값을 노출하지 않는 읽기 요청으로 한정한다.
브라우저가 필요한 프로젝트는 실제 브라우저로 확인하고 HTTP 200만으로 UI PASS를 주지 않는다.

## 출처

- [플러그인 및 LSP](https://code.claude.com/docs/en/discover-plugins)
- [Java 플러그인](https://github.com/anthropics/claude-plugins-official/blob/main/plugins/jdtls-lsp/README.md)
- [JDT LS 요구사항](https://github.com/eclipse-jdtls/eclipse.jdt.ls#requirements)
- [메모리 로딩](https://code.claude.com/docs/en/memory)
- [Hook 계약](https://code.claude.com/docs/en/hooks)
- [설정 범위](https://code.claude.com/docs/en/settings)
