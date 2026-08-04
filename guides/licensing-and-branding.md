---
title: 라이센스·저작자 브랜딩 가이드 (모든 프로젝트 공통)
created: 2026-08-04
updated: 2026-08-04
domain: licensing
---

# 라이센스·저작자 브랜딩 가이드

**모든 새 프로젝트는 제작자의 라이센스·저작권 표기를 일관되게 적용한다.** 언어·프레임워크가
무엇이든 아래 정체(identity)를 그대로 쓰고, 스택별 "적용 위치"에 값을 채운다.
(sh-ip-scanner(C#/Avalonia/Inno), pdf-editor·file-converter(Electron) 에서 검증해 승격.)

## 제작자 정체 (SSOT — 이 값을 그대로 쓴다)

| 항목 | 값 |
|---|---|
| 제작자(한글) | **이성현** |
| 제작자(영문) | **SeongHyun Lee** |
| 병기 표기 | `이성현 (SeongHyun Lee)` |
| 저작권 문구 | `Copyright © <연도> 이성현 (SeongHyun Lee). All rights reserved.` |
| 라이센스 종류 | **UNLICENSED (개인 저작물 · 독점)** — 오픈소스 아님. 제작자 동의 없이 재배포·판매 금지 |
| 이메일 | chungmu.xyz@gmail.com |
| appId 관례 | `seonghyunlee.<앱이름소문자>` (예: `seonghyunlee.pdfeditor`, `seonghyunlee.shipscanner`) |

> 라이센스 전문 템플릿은 `templates/LICENSE` 에 있다 — 킥오프 시 새 프로젝트 루트로 자동 복사되고
> `{{PROJECT_NAME}}`·`{{YEAR}}` 가 치환된다. 그 파일이 "사람이 읽는 라이센스"의 원본이다.

## 스택별 적용 위치

새 프로젝트 스택이 정해지면 아래 해당 칸을 실제 값으로 채운다. 값은 위 표에서 가져온다.

### 공통 (모든 스택)
- 루트에 `LICENSE`(또는 `LICENSE.txt`) — `templates/LICENSE` 에서 인스턴스화됨.
- README 하단에 한 줄: `© <연도> 이성현 (SeongHyun Lee). All rights reserved.`
- 앱 UI 안에 저작권 표기가 들어갈 자리가 있으면(정보/설정/헤더) 병기 표기를 넣는다.

### Node / Electron (package.json + electron-builder)
```jsonc
{
  "author": "이성현",
  "license": "UNLICENSED",
  "build": {
    "appId": "seonghyunlee.<앱이름>",
    "copyright": "Copyright © <연도> 이성현",
    "license": "build/license.txt"   // 설치 마법사에 표시할 라이센스 파일
  }
}
```
- `build/license.txt` 는 `templates/LICENSE` 내용으로 채운다(제작자·저작권 포함).

### .NET / C# (csproj + Inno Setup)
csproj `<PropertyGroup>`:
```xml
<Authors>SeongHyun Lee</Authors>
<Company>SeongHyun Lee</Company>
<Copyright>© <연도> 이성현 (SeongHyun Lee). All rights reserved.</Copyright>
```
Inno Setup `.iss` `[Setup]`:
```ini
AppPublisher=SeongHyun Lee
AppCopyright=Copyright (C) <연도> SeongHyun Lee
VersionInfoCompany=SeongHyun Lee
VersionInfoCopyright=Copyright (C) <연도> SeongHyun Lee
LicenseFile=LICENSE.txt          ; 설치 시 라이센스 동의 페이지에 표시
```
- **한글 주의**: `.iss` 본문 필드는 ASCII 로 두고(컴파일 안정), 한글 전문은 `LicenseFile` 로 뺀다.
  그 `LICENSE.txt` 는 **UTF-8 BOM** 으로 저장해야 Inno 유니코드가 한글을 깨짐 없이 표시한다.

### Python / 기타
- `pyproject.toml`/`setup.cfg` 의 `authors`, `license` 에 위 값. 배포물엔 루트 `LICENSE` 동봉.

## 코드 서명(Authenticode) — 별개 사안

이름·저작권 표기와 **디지털 코드 서명은 다르다.** 서명은 유료 코드서명 인증서가 있어야 하고,
없으면 Windows SmartScreen 이 "알 수 없는 게시자" 경고를 띄운다(설치는 됨). 인증서를 확보하면
Inno `.iss` 의 `SignTool` 훅(주석으로 준비해 둠)이나 electron-builder `sign` 설정으로 서명한다.
개인 배포 단계에서는 표기까지만 하고 서명은 선택으로 둔다.

## 관련 문서
- `templates/LICENSE` — 라이센스 전문 원본(자동 인스턴스화)
- `guides/desktop-packaging.md` — 인스톨러 굽기(서명 위치 포함)
- `INITIALIZE.md` 6단계(패키징) — 킥오프에서 이 가이드를 적용하는 지점
