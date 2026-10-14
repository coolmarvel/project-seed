# CLAUDE.md - {{PROJECT_NAME}} 작업 가이드

이 파일은 **세션이 바뀌어도 맥락을 즉시 복구**하기 위한 진입점이다. Claude Code는 세션 시작 시
이 파일을 자동으로 읽는다. (도구 중립 절대 규칙은 `AGENTS.md`.)

> [project-seed](https://github.com/coolmarvel/project-seed) 에서 {{DATE}} 에 생성됨.
> `<!-- TODO(kickoff) -->` 가 남아 있으면 킥오프가 끝나지 않은 것이다. 채우기 전에 기능 작업을 시작하지 않는다.

## 세션 시작 부팅 프로토콜 (매 세션 첫 작업 전에 반드시 수행)

새 세션에서 작업을 시작하면, **코드를 건드리기 전에** 순서대로:

1. `docs/session-log.md` 읽기 - 마지막으로 무엇을 했고 지금 어디쯤인지 (**진행 이력 SSOT**)
2. `docs/todo.md` 읽기 - 남은 일 (P1~P4)
3. 최근 `docs/plans/*.md` 1개 읽기 - 진행 중 기능의 설계 의도
4. 사용자 피드백 확인: <!-- TODO(kickoff): 피드백 채널 규약. 예: 루트/전용 폴더의 스크린샷 = 미처리 피드백으로 읽고, 반영 후 docs/feedback-archive/YYYY-MM-DD-*/ 로 이동 -->
5. `git status` 에 모르는 변경이 있으면 session-log 와 대조 - 다른 세션의 흔적일 수 있다. 출처 불명이면 사용자에게 확인.

## 변경 후 자동 규칙 (사용자가 매번 요청하지 않아도 수행)

1. 코드를 바꾸면 **같은 턴에** `docs/session-log.md`(최상단 블록 추가)·`docs/todo.md`
   (+릴리스급이면 `docs/changelog.md`)를 갱신한다. 문서 규칙은 `docs/writing-guide.md`.
2. 검증을 통과하기 전에는 커밋 메시지 작성/산출물 전달을 하지 않는다:
   <!-- TODO(kickoff): 검증 명령. 예: npm run typecheck && npm test && npm run build -->
3. 버전을 판단해 올린다 (아래 "버전 정책").
4. 산출물 전달: <!-- TODO(kickoff): 전달 방식. 예: 인스톨러를 바탕화면에 복사, 스테이징 배포, 로컬 실행 안내 -->

## 버전 정책 (semver `MAJOR.MINOR.PATCH`)

**MINOR 승격은 사용자만 선언한다.** 에이전트가 판단해서 올리지 않는다.

- **PATCH**: 기본값. 다듬는 중인 기능의 수정 하나 반영할 때마다 +1.
- **MINOR**: 사용자가 "이 기능은 더 수정할 게 없다, 넘어가자"라고 선언한 그 시점에만.
- **MAJOR**: 대규모 재설계/호환 깨짐. 사용자와 상의.

## 커밋 컨벤션

Conventional Commits - `<type>: <한국어 제목>` + 리스트형 본문. **커밋/푸시는 사용자가 직접**
(에이전트는 요청 시 커밋 메시지만 작성). 메시지 끝에 `Co-Authored-By` 트레일러.

type: `feat` `fix` `refactor` `chore` `docs` `style` `test` `perf` `ci` `build` `revert` `init` `remove` `rename` `hotfix`

## 협업 규칙

- **진행 이력의 SSOT 는 `docs/session-log.md`**: git history 가 아니다 (커밋이 성긴 단위라서).
- 세션이 끊겨도 이어서 작업할 수 있게 **모든 진행 사항을 `docs/` 에 파일로 기록**한다.
- 설계 논쟁이 생기면 `docs/brief.md`(왜/무엇 SSOT)로 돌아와 판정한다. 브리프 밖 기능은 스코프 확인 먼저.
- `.env` 는 직접 수정하지 않는다. `.env.example` 수정 또는 사용자에게 요청 (훅이 차단함).
- 규칙이 미확정이면 이 파일에 `<!-- TODO -->` 로 남기고, 확정되는 순간 채운다.

## 이 프로젝트가 뭔가

<!-- TODO(kickoff): 한 문단 - 누구를 위해, 어떤 문제를, 어떤 형태로. docs/brief.md 의 요약. 벤치마킹 대상이 있으면 명시. -->

## 문서 인덱스 (docs/)

| 파일 | 용도 |
|---|---|
| `docs/writing-guide.md` | **문서 지배 규칙** (SSOT·frontmatter·코드 1:1 대조). 문서 쓰기 전 필독 |
| `docs/brief.md` | 프로젝트 **왜/무엇 SSOT**: 킥오프 산출물 |
| `docs/session-log.md` | 세션별 진행 이력. **"언제 무슨 일" SSOT** (최신이 위) |
| `docs/todo.md` | 미해결·향후 작업만 (P1~P4). 완료분은 session-log 로 |
| `docs/changelog.md` | 릴리스 단위 사람용 요약 |
| `docs/adr/*.md` | 구조적 결정 기록 - 왜 이렇게 했는가 (`NNNN-kebab.md`) |
| `docs/plans/*.md` | 앞으로 만들 것 - 기능 단위 구현 계획 (역할 구분은 `plans/README.md`) |
| `docs/guides/*.md` | 현재 구현된 동작·코드 위치 (기능별) |
| `docs/feedback-archive/` | 처리 완료한 사용자 피드백 보관소 |

## 자주 쓰는 명령

<!-- TODO(kickoff): 스택 확정 후 채움
```bash
# dev 서버 / 검증(typecheck·lint·test·build) / 패키징·배포
```
-->

## 코드 지도 (수정 시 어디를 보나)

<!-- TODO(kickoff): 스캐폴드 후 채움. 디렉토리별 역할 +
"새 기능 추가 = 1) 모델 2) 로직 3) 화면 4) 테스트 ..." 체크리스트 형태 권장.
"어기면 재발하는 함정"(과거 사고)이 생기면 여기에 1줄씩 박제. -->

## 디자인 시스템 (oh-my-design)

<!-- TODO(kickoff): UI 가 없는 프로젝트(CLI·서버·라이브러리)면 이 절을 통째로 지운다. -->

디자인 레퍼런스: <!-- TODO(kickoff): 베이스 브랜드 (예: Toss) + 차용 (예: 카드=Naver, 모달=AWS) -->
디자인 계약은 루트 **`DESIGN.md`** (프로젝트 소유, 커밋 대상). 색·간격·컴포넌트 구조는 여기서만 정하고
코드에 임의 값을 넣지 않는다. 설치 확인: `npx oh-my-design-cli@latest doctor`.

| 상황 | 반드시 쓰는 명령 |
|---|---|
| 새 화면·기능 UI 만들기 | `/omd:harness` (조사, 설계, 구현, 검증 순, 체크포인트 3개) - 작은 컴포넌트 하나면 `/omd:apply` |
| 기존 UI 손보기 | `/omd:apply` 로 `DESIGN.md` 기준 수정 후 `/omd:slop-audit` |
| UI 작업 마무리 전 | `/omd:slop-audit`(AI 티·일관성 감사) + `/omd:feel`(사용감 점검) 통과 후에만 산출물 전달 |
| 디자인 원칙·토큰 바꾸기 | `/omd:init` 재실행 또는 `DESIGN.md` 직접 수정, 변경 이유를 `docs/adr/` 에 기록 |
| 디자인 문서 열람 | `npx oh-my-design-cli@latest book` (localhost:6060) |
| 번들 갱신 | `npx oh-my-design-cli@latest update` 후 `doctor` |

## 하네스 (Harness Engineering)

지시문이 아니라 시스템으로 제약한다. 도입 근거: `docs/adr/0001-harness-engineering.md`.

| 축 | 내용 |
|---|---|
| Hooks (`scripts/hooks/`) | `env-guard.sh`(.env 편집 차단) · `git-add-guard.sh`(`git add -A/.`·.env staging 차단) · <!-- TODO(kickoff): format.sh - 스택 포매터로 작성해 PostToolUse 연결 --> |
| Settings (`.claude/settings.json`) | `disableRemoteControl: true` + `remoteControlAtStartup: false` + `autoUploadSessions: false` - Claude Code **Remote Control**(로컬 세션을 claude.ai/code 에 동기화) 차단. 대화 내용이 웹에 남지 않게 한다. 세션 중 `/rc` 가 켜져 있으면 끊는다 |
| Commands (`.claude/commands/`) | `/review-security` · `/deploy-check` <!-- TODO(kickoff): 스택별 /review-* 추가 검토 --> |
| MCP (`.mcp.json`) | `context7`(라이브러리 문서) · `playwright`(브라우저 QA) <!-- TODO(kickoff): 스택 전용 서버 추가 검토 --> |
| Skills | oh-my-design 번들(`/omd:*` 22개 스킬 + 19개 서브에이전트, UI 있을 때) · <!-- TODO(kickoff): 스택별 스킬 도입 시 skills-lock.json + scripts/install-skills.sh 방식으로 락 --> |
