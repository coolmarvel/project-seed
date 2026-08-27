# INITIALIZE.md — 킥오프 절차 (SSOT)

에이전트가 백지 아이디어에서 작업 가능한 프로젝트까지 만드는 절차다. 순서대로 수행하되,
각 단계의 **산출물**이 나와야 다음으로 넘어간다. 사용자 확인이 필요한 지점은 ✋ 로 표시.

> 원칙: **대화가 코드보다 먼저다.** 1단계 위저드의 ✋ 최종 확정 전에는 파일을 만들지 않는다.
>
> 질문 방식: 선택지가 있는 질문은 텍스트로 나열하지 말고 **선택형 질문 UI**(Claude Code의
> `AskUserQuestion` 도구 등)로 제시한다 — 한 번에 최대 4개 질문, 질문당 선택지 2~4개, **직전 답을
> 근거로 만든 추천안을 첫 번째 선택지로 놓고 "(권장)" 표기**. 도구가 자동으로 붙이는 "Other" 가
> "chat about this" 역할이다 — 사용자가 Other 로 자유 서술하면 그 내용을 반영해 다시 추천안으로 묻는다.
> 자유 서술이 꼭 필요한 것(아이디어 한두 줄 설명, 성공 기준)만 일반 텍스트로 묻는다.
>
> 모드: plan mode 를 요구하지 않는다. auto/기본 모드에서 그대로 진행하며, 파일 생성의 승인은
> 1단계 마지막 ✋ 확정 질문이 대신한다.

## 0. 프로젝트 이름·위치

1단계 위저드의 5번 질문에서 정한다 (영어 kebab-case, 예: `sh-shop`). 생성 위치 기본값은 이 저장소의
형제 경로 `../<이름>/`.

## 1. 인터뷰 — 메뉴 위저드

한 질문씩 `AskUserQuestion` 으로 묻고, **각 답을 근거로 다음 질문의 추천안을 만든다.** 웹/앱만이
아니다 — 바이너리 실행기(데스크톱·CLI)도 1급 선택지다.

| # | 질문 | 선택지 예 (첫 항목 = 직전 답 기반 권장안) |
|---|---|---|
| 1 | 어떤 프로젝트를 시작하고 싶으세요? | 웹 서비스(쇼핑몰·커뮤니티·SaaS) / 데스크톱·CLI 실행기 / 모바일 앱 / 서버·API·자동화 |
| 1' | (자유 서술) 누가 쓰고 무엇이 되면 좋은지 한두 줄 | — |
| 2 | 어떤 언어·프레임워크·라이브러리로 만들까요? | 쇼핑몰이면 React+NestJS / Spring Boot+Thymeleaf / Next.js 풀스택 ; 실행기면 .NET WPF / Electron / Rust+Tauri |
| 3 | DB·저장 방식은? | 2번 스택에 맞춰: PostgreSQL / SQLite / 파일·로컬 / 없음 |
| 4 | 디자인·UI 방향은? | **oh-my-design 레퍼런스 기반(권장)** / UI 킷만(shadcn·MUI·Fluent…) / 최소 기능 UI |
| 4' | 어느 브랜드를 디자인 레퍼런스로 쓸까요? (4번이 oh-my-design 일 때) | 유형에 맞춰 추천: 커머스·핀테크 → Toss / 당근 / 쿠팡 ; 관리자·B2B → AWS / Atlassian / Linear ; 콘텐츠·커뮤니티 → Naver / Kakao ; 데스크톱 → Fluent(Microsoft) / Apple HIG. 카탈로그: oh-my-design.kr/design-systems |
| 4'' | 다른 브랜드에서 차용할 컴포넌트가 있나요? (multiSelect, 선택) | 예: 버튼은 Toss, 카드·리스트는 Naver, 모달·다이얼로그는 AWS — 베이스 1개 + 차용 2~3개까지만 |
| 5 | 프로젝트 이름은? | kebab-case 제안 2~3개 (+ 위치 `../<이름>/` 확인) |
| 6 | ✋ 이 구성으로 설계를 시작할까요? | 요약(유형·스택·DB·디자인 레퍼런스·이름) 확인 / 특정 항목 다시 고르기 |

브리프에 필요한데 아직 안 나온 것만 골라 묻는다 (이미 답한 건 확인만): 핵심 시나리오("누가 무엇을
해서 무엇을 얻는다" 3~7개), 제약(기간·예산·인증/결제), 성공 기준("이것만 되면 v1.0" — 자유 서술),
범위 밖.

6번이 확정되면 "설계를 시작합니다" 하고 2단계로 넘어간다.

## 2. 프로젝트 생성 + 템플릿 인스턴스화

```bash
mkdir ../<이름> && cd ../<이름> && git init -b main
cp -r <seed>/templates/. .          # 숨김 파일 포함 전체 복사
chmod +x scripts/hooks/*.sh
```

그 다음 플레이스홀더 치환: 복사된 모든 파일에서 `{{PROJECT_NAME}}` → 프로젝트 이름,
`{{DATE}}` → 오늘 날짜(YYYY-MM-DD), `{{YEAR}}` → 현재 연도(YYYY). 이후 작업은 전부
**새 프로젝트 디렉토리에서** 한다.

> `templates/LICENSE` 도 함께 복사된다 — 제작자(이성현/SeongHyun Lee) 라이센스 전문. 위 치환으로
> 프로젝트명·연도가 채워진다. 저작자 표기를 스택별로 어디에 넣는지는 `guides/licensing-and-branding.md`.

## 3. 브리프 작성 — `docs/brief.md`

1단계 인터뷰 내용으로 채우고 ✋ 사용자 확인 후 frontmatter 를 `status: confirmed` 로 바꾼다.
브리프는 프로젝트의 **왜/무엇 SSOT** — 이후 설계 논쟁은 브리프로 돌아와 판정한다.

## 4. 스택/DB ADR 기록 — `docs/adr/0002-stack.md`

1단계에서 사용자가 고른 언어·프레임워크·라이브러리·DB·디자인 방향과 그 근거(왜 이것, 왜 위저드에서
제시했던 다른 후보가 아니라)를 ADR-0002 로 기록한다. 에이전트가 임의로 바꾸지 않는다.
(ADR-0001 은 하네스 도입 — 템플릿에 포함됨)

## 5. MVP 계획 — `docs/plans/0001-mvp.md`

핵심 시나리오를 마일스톤으로 자른다. 첫 마일스톤은 **"며칠 안에 사용자가 만져볼 수 있는 것"** 으로.
상태 표기는 `docs/plans/README.md` 규약(`[ ]/[~]/[x]/[?]`)을 따른다.

## 6. 스캐폴드 + 검증 루프 + 하네스 완성

1. 선택된 스택의 표준 도구로 뼈대를 생성한다 (예: `npm create vite`, `laravel new`, `cargo new` …).
2. **검증 명령 확정**: typecheck / lint / test / build 에 해당하는 이 스택의 명령을 정하고
   전부 한 번 실행해 통과를 확인한다.
3. `.gitignore` 를 스택에 맞게 보강한다.
4. **포맷 훅 추가**: `scripts/hooks/format.sh` 를 스택의 포매터(prettier/pint/ruff/gofmt …)로 작성하고
   `.claude/settings.json` 의 PostToolUse 에 연결한다 (env-guard/git-add-guard 는 이미 연결돼 있음).
5. 필요하면 `.mcp.json` 에 스택 전용 MCP 서버를 추가한다 (기본: context7 + playwright).
6. **데스크톱 앱이면**: 발사대의 `guides/desktop-packaging.md` 를 새 프로젝트
   `docs/guides/packaging.md` 로 복사하고 제품명·appId·빌드 스크립트를 실제 값으로 구체화한다.
7. **디자인 시스템(oh-my-design)** — 위저드 4번이 oh-my-design 이면 (UI 가 있는 프로젝트 전부):
   ```bash
   npx oh-my-design-cli@latest          # project-local 설치 → .claude/ 에 스킬·서브에이전트·440개 DESIGN.md
   npx oh-my-design-cli@latest doctor   # 재시작 후 설치 확인
   ```
   설치 후 `/omd:init` 을 4' 의 레퍼런스로 실행해 루트 `DESIGN.md` 를 만들고, 4'' 의 차용 컴포넌트가
   있으면 "카드 계약은 Naver 레퍼런스를 참고해 다시 써줘" 식으로 해당 계약만 갱신한다. `DESIGN.md` 는
   프로젝트 소유 파일이므로 커밋한다. 결정(베이스 브랜드·차용 목록·이유)은 ADR-0002 의 디자인 절에 기록.
   이후 UI 작업 규칙(`/omd:apply`·`/omd:harness`·`/omd:slop-audit`·`/omd:feel`)은 새 프로젝트
   `CLAUDE.md` 의 "디자인 시스템" 절에 이미 들어 있다 — 레퍼런스 이름만 채운다.
8. **라이센스·저작자 표기 적용**: `guides/licensing-and-branding.md` 를 따라 스택별 위치
   (package.json / csproj / Inno `.iss` / pyproject 등)에 제작자(이성현)·저작권·라이센스를 채운다.
   설치형 앱이면 인스톨러에 라이센스 동의 페이지(`LICENSE`)와 저작권 VersionInfo 를 반드시 넣는다.

## 7. 프로젝트 CLAUDE.md 자기완성

새 프로젝트의 `CLAUDE.md` 에 있는 `<!-- TODO(kickoff) -->` 슬롯을 전부 실제 값으로 채운다:

- **이 프로젝트가 뭔가** — 브리프 요약 한 문단
- **자주 쓰는 명령** — dev / 검증 / 빌드·배포
- **코드 지도** — 스캐폴드의 디렉토리별 역할 + "새 기능 추가 = ①…②…③…" 체크리스트
- **피드백 채널** — 사용자가 피드백을 주는 방식 확인 ✋ (예: 스크린샷을 루트에 두면 미처리 피드백으로 간주)
- **검증·산출물 규칙** — 6단계에서 확정한 검증 명령과 전달 방식

`AGENTS.md` 의 스택 요약도 채운다. 남은 TODO 슬롯이 없어야 킥오프 완료다.

## 8. 기록 + 첫 커밋

1. `docs/session-log.md` 첫 블록: "킥오프 완료" — 결정 요약(이름/스택/MVP 범위)과 다음 할 일.
2. `docs/todo.md`: MVP 첫 마일스톤을 P1 으로 등록.
3. 첫 커밋 메시지를 작성해 사용자에게 전달한다 (`init: 프로젝트 킥오프 — 브리프·스택 ADR·스캐폴드`).
   **커밋/푸시는 사용자가 직접.**

---

## 킥오프 완료 기준 (체크리스트)

- [ ] `docs/brief.md` — `status: confirmed`
- [ ] `docs/adr/0002-stack.md` — 위저드에서 사용자가 고른 스택·DB·디자인과 근거
- [ ] `docs/plans/0001-mvp.md` — 마일스톤
- [ ] 스캐폴드 존재 + 검증 명령 전체 1회 통과
- [ ] `CLAUDE.md`/`AGENTS.md` 에 `TODO(kickoff)` 잔여 0개
- [ ] 훅 3종(env-guard/git-add-guard/format) 연결
- [ ] (UI 있으면) oh-my-design 설치 + `DESIGN.md` 생성 + CLAUDE.md 디자인 절 레퍼런스 채움
- [ ] session-log 첫 블록 + todo P1 + 첫 커밋 메시지 전달

이후의 모든 세션은 새 프로젝트 `CLAUDE.md` 의 **부팅 프로토콜**을 따른다. 이 파일(INITIALIZE.md)은
새 프로젝트로 복사되지 않는다 — 킥오프는 한 번뿐이다.
