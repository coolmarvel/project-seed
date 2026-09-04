---
title: 데스크톱 인스톨러 패키징 가이드 (exe / dmg / pkg)
created: 2026-07-13
updated: 2026-07-13
domain: packaging
---

# 데스크톱 인스톨러 패키징 가이드 (exe / dmg / pkg)

## 개요

Windows `exe`, macOS `dmg`(Apple Silicon/Intel), macOS `pkg` 인스톨러를 굽는 규칙 모음.
Electron + electron-builder 실제 릴리스에서 검증된 규칙을 발사대로 승격한 것이다. **데스크톱 앱 킥오프 시 이 파일을 새 프로젝트의
`docs/guides/packaging.md` 로 복사한 뒤, 프로젝트 실제 값(제품명·appId·스크립트)으로 구체화한다.**
웹/서버/CLI 프로젝트에는 복사하지 않는다.

## 공통 규칙 (스택 무관)

- 릴리스 전 검증 통과가 먼저다 (typecheck / test / build 상당의 프로젝트 검증 명령).
- 산출물 폴더(`release/` 등)는 `.gitignore` 대상 — **설치 파일을 git 에 커밋하지 않는다.**
  큰 바이너리는 push 제한과 저장소 비대화를 부른다. Git LFS 도 기본 정책으로 쓰지 않는다
  (별도 용량/트래픽 관리 필요).
- 공개 배포는 GitHub Release 자산 업로드로 한다. 자산 파일명은 **ASCII** 로
  (한글 파일명은 URL/도구 호환 문제) — 예: `App-Setup-<v>.exe`, `App-<v>-arm64.dmg`.
- 업로드 스크립트는 버전을 파라미터로 받게 만든다 (하드코딩하면 릴리스마다 스크립트 복사본이 쌓인다). 같은 태그의 릴리스가 이미 있으면 자산만 추가하는
  멱등 동작으로 — 플랫폼별 빌드가 어느 순서로 올라가도 안전하다.
- 새 버전 검증 후 이전 버전 설치 파일과 빌드 중간 산출물(`mac/`, `mac-arm64/`, `*.yml`, `*.blockmap`)은
  로컬에서 정리해 혼동을 막는다.

## 아키텍처 명칭 규약 (macOS)

| 대상 | 파일명 접미사 | 동의어 |
|---|---|---|
| Apple Silicon (M1~M4) | `arm64` | `aarch64` |
| Intel Mac | `x64` | `x86_64`, `amd64` |

`amd64.dmg` 같은 별도 파일을 만들지 않는다 — Intel 대상은 electron-builder 표준 명칭인 `x64` 하나로 통일.
배포 대상은 버전당 3개: `Setup-<v>.exe`(Win) / `<v>-arm64.dmg` / `<v>-x64.dmg`.

## Windows — exe (NSIS)

- 명령: `npm run dist:win` = `electron-builder --win` (검증된 조합: electron-builder `nsis` 타깃)
- 핵심 설정 (`package.json`의 `build`):
  - `appId` 는 처음부터 안정된 값으로 정한다 — **중간에 바꾸면 기존 설치본의 업그레이드/제거가
    깨질 수 있다** (바꿨다면 기존 설치본의 업그레이드 동작을 실물로 검증해야 한다).
  - `nsis`: `oneClick: false` + `allowToChangeInstallationDirectory: true` + 바탕화면/시작메뉴 바로가기,
    `artifactName` 으로 산출물 파일명 고정.
  - 표시 이름·바로가기 이름은 한글이어도 안전하다 (Windows 는 macOS 와 달리 문제없음).

## macOS — dmg

- 명령 패턴 (arch 를 명시적으로 분리):
  - `dist:mac` — 현재 맥 CPU 기준
  - `dist:mac:arm64` / `dist:mac:x64` — 각각 Apple Silicon / Intel 용
- **내부 번들 이름은 반드시 ASCII** — `CFBundleName`/`CFBundleExecutable`/
  Helper app 이름을 한글로 만들면 시작 즉시 `EXC_BREAKPOINT(SIGTRAP)` 크래시.
  한글이 허용되는 곳은 **외부 `.app` 폴더명과 `CFBundleDisplayName`(Finder 표시명)뿐이다.**

  | 항목 | 값 |
  |---|---|
  | `CFBundleName` / `CFBundleExecutable` / Helper 이름 | ASCII (예: `MyApp`) |
  | 외부 `.app` 폴더명 / `CFBundleDisplayName` | 한글 가능 (예: `내 앱.app`) |

- **서명**: Developer ID 인증서가 없으면 dmg 생성 전 ad-hoc 서명(`codesign --sign -`)으로 내부
  프레임워크 서명을 정리한다. 정식 배포에는
  `Developer ID Application` 인증서 + notarization(`notarytool`)이 필요하다 — 없으면 사용자가
  Gatekeeper 경고를 우회해야 한다.

## macOS — pkg

> ⚠️ pkg 는 **아직 실프로젝트 검증 전**이다 (지금까지는 dmg 로만 배포). 아래는 일반 절차 —
> 처음 pkg 를 굽는 프로젝트에서 검증한 뒤 이 문단을 갱신할 것.

- electron-builder 라면 `mac.target` 에 `pkg` 를 추가하면 된다 (`target: ["dmg", "pkg"]`).
  arch 는 dmg 와 동일하게 arm64/x64 분리, 파일명 규약도 동일 적용.
- 서명된 pkg 배포에는 dmg 와 **다른 인증서**가 필요하다: `Developer ID Installer`
  (앱 자체 서명은 여전히 `Developer ID Application`). notarization 은 pkg 에도 필요.
- 비-Electron 스택이나 수동 제작 시: `pkgbuild`(컴포넌트 pkg) → `productbuild`(배포 pkg) 순서.
- dmg(드래그 설치) vs pkg(설치 마법사): 시스템 위치 설치·사전/사후 스크립트·MDM 배포가 필요할 때만
  pkg 를 선택한다. 일반 사용자 배포는 dmg 가 기본.

## 배포 소스 보호 (난독화)

설치본에서 소스 복원을 어렵게 만드는 규칙. Electron 의 `app.asar` 는 단순 아카이브라 풀면 코드가
그대로 나온다(main 은 미압축, renderer 는 minify 뿐). **100% 방지는 불가 — 목표는 "복원 비용 올리기"다.**

- **JS 난독화로 일원화한다** — `javascript-obfuscator`(devDep)를 빌드 파이프라인 마지막에 연결:
  `npm run build` = 번들 빌드 + 난독화 스크립트. main/preload/renderer 전부 대상.
  모든 `dist:*`(win/mac/linux)가 이 `build` 를 경유하므로 **exe·dmg·pkg 어느 인스톨러든 자동 적용**된다
  — 플랫폼별로 따로 난독화하지 않는다.
- **⚠ V8 바이트코드(electron-vite `bytecodePlugin`)는 쓰지 않는다** —
  바이트코드(.jsc)는 **컴파일한 플랫폼에 종속**이라 WSL(Linux)에서 구운 exe 가 사용자 Windows 에서
  시작 즉시 크래시(`cachedDataRejected`). 빌드 머신과 실행 플랫폼이 다른 체제에서는 금지.
  (부수 교훈: 크로스 플랫폼 산출물은 같은 플랫폼 E2E 만으론 못 잡는다 — 실물 대상 OS 검증 필요.)
- **보수 설정 원칙** — 기능·성능에 영향 주는 옵션은 끈다:

  | 옵션 | 값 | 이유 |
  |---|---|---|
  | `controlFlowFlattening` / `deadCodeInjection` / `selfDefending` / `debugProtection` | **OFF** | 성능·안정성 위험 |
  | `identifierNamesGenerator` | `hexadecimal` | 식별자 숨김 |
  | `stringArray` + `stringArrayThreshold: 0.6` + rotate/shuffle | ON | 문자열 숨김 |
  | `renameGlobals` / `splitStrings` / `stringArrayEncoding` | OFF | 호환성 |

- **큰 공개 라이브러리 번들은 제외한다** (예: PDF 렌더 워커 같은 1MB+ 공개 번들) — 공개 코드라 보호
  가치가 없고 성능만 깎는다.
- 검증된 비용: 번들 +6%, 빌드 시간 +6.7s. 적용 후 반드시 전체 E2E 로 기능 무영향 확인.

## 크로스 빌드 한계

- macOS dmg/pkg 는 **맥에서만** 굽는다 (서명·notarization 이 macOS 전용 도구).
- Windows exe 는 WSL/Linux 에서도 electron-builder 로 빌드 가능 (WSL 에서 exe,
  맥에서 dmg 를 굽는 이원 체제로 운용 가능).

