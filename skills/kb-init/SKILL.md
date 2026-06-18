---
name: kb-init
description: 신규 프로젝트의 지식베이스 구조를 Obsidian 보관함에 스캐폴딩한다. 영역 폴더(00 백로그·1~5·90 논의사항·91 코드리뷰) + images/ + 📑 인덱스(MOC) + 영역별 문서 스텁을 생성하고 루트 카탈로그에 등록한다. "프로젝트 지식베이스 만들어", "새 프로젝트 문서 구조 생성", "kb 초기화"에 사용.
user-invocable: true
argument-hint: "[프로젝트명] [코드 리포 경로(선택)]"
tools: Read, Write, Edit, Bash, AskUserQuestion
---

# kb-init — 프로젝트 지식베이스 스캐폴딩

## 0. 먼저 규칙을 읽는다 (필수)

`${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/conventions.md`를 Read한다. 이후 모든 명명·구조·frontmatter는 그 규칙을 따른다.

## 1. 입력 수집

- **프로젝트명**: 인자로 받거나 묻는다(보관함 폴더명이자 MOC 제목에 쓰임).
- **코드 리포 경로**: 있으면 받는다(없으면 빈 값, MOC의 "코드가 기준" 주석에 사용).
- **vault root**: `conventions.md` §0의 해석 순서를 따른다(config 파일 `~/.claude/project-kb/config.json`의 `vaultRoot` → 환경변수 `KB_VAULT_ROOT` → 둘 다 없으면 절대경로를 묻고 config에 영속 저장). 경로 변경은 `kb-set-vault` 스킬.
- **도메인 태그·한 줄 정의**: 사용자에게 간단히 확인(template placeholder 치환용). 모르면 비워두고 사용자가 나중에 채우게 한다.

## 2. 가드 (자동 생성 금지)

- `<vault>/<프로젝트명>/`이 **이미 있으면** 사용자에게 알리고, 덮어쓸지/중단할지 확인한다. 기본은 중단.
- `<vault>` 자체가 없으면 만들기 전에 확인한다.

## 3. 구조 생성

`conventions.md`의 taxonomy대로 생성한다:

```
<vault>/<프로젝트명>/
├── 📑 인덱스.md
├── 00. 백로그/          (빈 폴더, .gitkeep으로 유지)
├── 1. 개요·전략/        → 개요.md
├── 2. 개발·구현/        → 구현 계획.md · 아이디어 백로그.md · 구현 현황.md · 데이터 흐름도.md
├── 3. 분석자료/         (빈 폴더, .gitkeep으로 유지)
├── 4. 운영·셋업/        → 운영 가이드.md
├── 5. 사용설명서/       → 00 사용설명서 개요.md
├── 90. 논의사항/        (빈 폴더, .gitkeep으로 유지)
├── 91. 코드리뷰/        (빈 폴더, .gitkeep으로 유지)
└── images/             (빈 폴더, .gitkeep 등으로 유지)
```

- 작업성 폴더(`00. 백로그`·`3. 분석자료`·`90. 논의사항`·`91. 코드리뷰`)와 `images/`는 **스텁 없이 빈 폴더**로 만들고 `.gitkeep`으로 유지한다. 내용은 `kb-update`(또는 사용자 직접)로 채운다.
- MOC는 `templates/moc.md`, 각 문서는 `templates/doc-stubs/`의 대응 스텁을 복사한다.
- 모든 `{{PLACEHOLDER}}`를 실제 값으로 치환:
  - `{{PROJECT_NAME}}` `{{PROJECT_SLUG}}`(공백 제거) `{{DATE}}`(오늘) `{{CODE_REPO_PATH}}` `{{DOMAIN_TAG}}` `{{ONE_LINE_DEFINITION}}` `{{STATUS_SUMMARY}}`(예: "착수 단계 ⬜").
  - 모르는 값은 짧은 placeholder 문구(`(작성 필요)`)로 둬 사용자가 채우게 한다.
- 한글·이모지·공백 경로는 따옴표 처리. 파일 쓰기는 Write 도구로 직접 한다.

## 4. 루트 카탈로그 등록

- `<vault>/📚 프로젝트 카탈로그.md`가 없으면 `templates/catalog.md`로 생성.
- 있으면 표에 **이 프로젝트 한 줄을 추가**(상태 🟢 활성, MOC 링크, 한 줄 요약). 기존 항목은 건드리지 않는다(부분 갱신).

## 5. 마무리

- 생성된 트리를 사용자에게 요약 보고.
- `kb-curate`로 링크/카탈로그 정합성을 한 번 점검할 것을 안내.
- 보관함을 단일 커밋하도록 권고(예: `git -C "<vault>" add -A && git commit`).

> 이 스킬은 **빈 구조와 스텁만** 만든다. 실제 내용 작성은 `kb-update`(또는 사용자 직접)로 채운다.
