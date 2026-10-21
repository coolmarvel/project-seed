# project-seed

**AI 에이전트로 새 프로젝트를 시작하는 발사대.** 이 저장소에는 md 파일만 있다.

여기서 에이전트(Claude Code, Codex 등)를 실행하면 에이전트가 **"어떤 프로젝트를 개발하고
싶으신가요?"** 라고 묻고, 대화(인터뷰, 브리프, 스택/DB 선택, MVP 계획)를 거쳐
**이 저장소 바깥에** 새 프로젝트를 생성한다. 문서 템플릿 복사·갱신, 스캐폴드,
하네스 엔지니어링(훅·리뷰 커맨드·MCP) 적용, 프로젝트 전용 CLAUDE.md 자기완성까지.

여러 프로젝트를 거치며 검증된 "AI 에이전트 협업 표준"의 일반화다. 코드는 프로젝트마다 다르지만
**프로세스는 같았다**: 그 프로세스를 템플릿화했다.

## 사용법

```bash
git clone https://github.com/coolmarvel/project-seed.git
cd project-seed
claude        # Claude Code
# 또는 같은 디렉토리에서 codex 실행
```

새 세션에서 "let's go" 또는 "시작하자"만 입력하면 선택형 인터뷰를 시작한다. 이전 대화를
이어갈 필요는 없다. Claude는 `CLAUDE.md`, Codex는 `AGENTS.md` 를 진입 지침으로 사용하고,
둘 다 `INITIALIZE.md` 의 공통 절차를 따른다. 별도 `CODEX.md` 는 필요하지 않다.

Codex는 `request_user_input` 으로 선택 질문을 제시한다. 이 도구가 Plan 모드에서만 제공되는
환경에서는 안내에 따라 Plan 모드로 전환한다. 인터뷰와 설계 후 실제 파일 생성에는 실행 가능한
모드가 필요하다. 선택 도구 자체가 없는 환경에서는 제한을 안내하고 텍스트 응답으로 진행한다.

만들고 싶은 것을 처음부터 설명해도 된다. 이미 제공한 내용을 반영해 다음 질문을 이어간다. 예:

> "재즈 커뮤니티 앱. 뮤지션·클럽 사장·일반 유저가 있고, 공연 일정을 보고 예약하고
> 후기를 남기고, 사장과 뮤지션은 채팅으로 일정을 잡고 결제하고..."

에이전트가 `INITIALIZE.md` 절차대로 `../jazz-community/` 같은 형제 디렉토리에 프로젝트를
만들어 준다. 한 번 clone 해 두면 여러 프로젝트를 계속 발사할 수 있다.

## 새 프로젝트가 받는 것

| 구성 | 내용 |
|---|---|
| `CLAUDE.md` | 세션 부팅 프로토콜 + 변경 후 자동 규칙 + 버전 정책 + 커밋 컨벤션. 킥오프가 스택 확정 값으로 자기완성 |
| `AGENTS.md` | 도구 중립 절대 규칙 (Claude 외 에이전트용) |
| `docs/` | writing-guide(문서 지배 규칙) · brief(왜/무엇 SSOT) · session-log(이력 SSOT) · todo(P1~P4) · changelog · adr/ · plans/ · guides/ · feedback-archive/ |
| 하네스 | `scripts/hooks/`(env-guard·git-add-guard + 스택별 format), `.claude/commands/`(/review-security, /deploy-check), `.mcp.json`(context7·playwright), `.claude/settings.json`(Remote Control 차단, 대화가 claude.ai 에 동기화되지 않게) |

## 핵심 원칙

1. **대화가 코드보다 먼저**: 브리프 없이는 스택도 코드도 없다. 스택은 사용자가 고른다.
2. **SSOT**: 이력은 session-log, 왜/무엇은 brief, 결정은 ADR, 현재 동작은 guides. 같은 사실을 두 곳에 적지 않는다.
3. **세션은 끊긴다**: 모든 진행 사항은 파일로. 다음 세션(다른 에이전트여도)이 부팅 프로토콜로 이어받는다.
4. **검증 없이 전달하지 않는다**: 검증 루프는 킥오프에서 확정하고 자동 규칙으로 돌린다.
5. **제약은 시스템으로**: 지시문이 아니라 훅이 막는다 (.env 편집, `git add -A` 등).
6. **커밋/푸시는 사용자가 직접**: 에이전트는 커밋 메시지를 작성한다.

## 새 PC 에서 처음 쓸 때 (사용자 설정)

프로젝트 `.claude/settings.json` 이 Remote Control 을 막지만, 프로젝트 밖에서 `claude` 를 띄울 때도 막으려면
PC 마다 한 번 `~/.claude/settings.json` 에 같은 값을 둔다 (또는 `/config` 에서 "Enable Remote Control for all sessions" 를 false):

```json
{ "disableRemoteControl": true, "remoteControlAtStartup": false }
```

## 이 저장소를 발전시키기

여러 프로젝트에서 반복 검증된 규칙이 생기면 이 저장소의 `templates/` 로 승격한다.
한 프로젝트의 특수 사정은 그 프로젝트 CLAUDE.md 에 남긴다.

## 제작자

<img src="assets/branding/이성현_반명함.jpg" alt="이성현 (SeongHyun Lee)" width="96" align="left" style="margin-right:12px">

**이성현 (SeongHyun Lee)**: chungmu.xyz@gmail.com

여기서 발사되는 모든 프로젝트의 저작권은 제작자에게 있다 (UNLICENSED · 개인 저작물).
표기 규칙은 `guides/licensing-and-branding.md`, 라이센스 전문은 `templates/LICENSE`.

<br clear="left">

© 2026 이성현 (SeongHyun Lee). All rights reserved.
