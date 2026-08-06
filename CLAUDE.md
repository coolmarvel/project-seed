# CLAUDE.md — project-seed (프로젝트 공장)

**이 저장소는 프로젝트가 아니라 발사대다.** md 파일만 있고, 여기서 에이전트를 실행하면
새 프로젝트를 **이 저장소 바깥에** 만들어 주는 것이 유일한 목적이다.
(도구 중립 요약은 `AGENTS.md` — Codex/Cursor 등은 그 파일을 읽는다.)

## 세션 시작 시 행동

사용자가 아무 말 없이 세션을 열었거나 새 프로젝트 이야기를 꺼내면, 첫 마디로 묻는다:

> **"어떤 프로젝트를 개발하고 싶으신가요?"** 하고 싶은 것을 자유롭게 설명해 주세요 —
> 누가 쓰는지, 무엇이 되면 좋은지, 떠오르는 대로면 충분합니다.

첫 질문(자유 서술)에 답을 받은 뒤부터는, 선택지가 있는 인터뷰 질문·✋ 확정 지점을
**`AskUserQuestion` 도구**로 제시한다 (텍스트로 질문 나열 금지 — 상세는 `INITIALIZE.md` 상단 참고).

그리고 `INITIALIZE.md` 의 킥오프 절차를 처음부터 끝까지 수행한다. 요약:

1. **인터뷰** — 문제/사용자/핵심 시나리오/제약/성공 기준을 대화로 수집
2. **프로젝트 생성** — 이 저장소 **바깥**(기본 `../<kebab-이름>/`)에 디렉토리 생성 + `git init`
3. **템플릿 인스턴스화** — `templates/` 전체를 새 프로젝트로 복사하고 플레이스홀더 치환
4. **브리프 확정** — `docs/brief.md` 작성 → 사용자 확인 → `status: confirmed`
5. **스택/DB 결정** — 후보 비교 제시, **사용자가 선택** → `docs/adr/0002-stack.md`
6. **스캐폴드 + 검증 루프** — 뼈대 생성, typecheck/lint/test/build 상당의 검증 명령 확정, 포맷 훅 추가
7. **CLAUDE.md 자기완성** — 새 프로젝트 CLAUDE.md 의 `TODO(kickoff)` 슬롯을 실제 값으로 채움
8. **첫 커밋 메시지 작성** — 커밋/푸시는 사용자가 직접

## 이 저장소 안에서 하지 말 것

- **제품 코드를 여기 만들지 않는다.** 코드는 항상 새로 생성한 프로젝트 디렉토리에.
- `templates/` 를 특정 스택 전용으로 오염시키지 않는다 — 템플릿은 스택 중립을 유지한다.

## 보일러플레이트 자체를 수정하는 경우 (유지보수 모드)

사용자가 "씨앗/보일러플레이트를 고치자"고 하면 그때만 이 저장소를 편집한다.
여러 프로젝트를 거치며 검증된 규칙만 승격해서 넣는다 — 한 프로젝트의 특수 사정은 그 프로젝트에 남긴다.

## 구조

```
CLAUDE.md / AGENTS.md   # 이 발사대의 에이전트 지침 (지금 읽는 파일)
INITIALIZE.md           # 킥오프 절차의 상세 SSOT
README.md               # 사람용 소개·사용법
assets/branding/        # 제작자 브랜딩 자산 — 사진(공개 OK)·서명(로컬 전용, gitignore됨). 규칙: guides/licensing-and-branding.md
guides/                 # 승격 지식 (킥오프 때 참고/복사)
├── desktop-packaging.md    #   exe/dmg(arm64·x64)/pkg 인스톨러 굽기 + 소스 보호 — pdf-editor 검증본
└── licensing-and-branding.md  # 제작자(이성현) 라이센스·저작권을 스택별로 적용하는 법 (모든 프로젝트 공통)
templates/              # 새 프로젝트로 복사되는 모든 것 (아래는 복사 후 역할)
├── CLAUDE.md           #   프로젝트 에이전트 진입점 (부팅 프로토콜 + TODO(kickoff) 슬롯)
├── AGENTS.md           #   도구 중립 절대 규칙
├── LICENSE             #   제작자 라이센스 전문 ({{PROJECT_NAME}}·{{YEAR}} 치환) — 모든 새 프로젝트 루트로
├── .gitignore / .mcp.json / .claude/ / scripts/hooks/   # 하네스 (훅·리뷰 커맨드·MCP)
└── docs/               #   writing-guide, brief, session-log, todo, changelog, adr/, plans/, guides/
```
