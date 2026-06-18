---
name: kb-note
description: 특정 프로젝트에 속하지 않는 단발 지식/노트 문서(조사 결과, 전략·설계 메모, 학습 메모, 회의/결정 노트)를 Obsidian 보관함의 적절한 주제 폴더에 저장한다. "정리해줘", "조사 결과 메모로 남겨", "노트로 만들어" 같은 비프로젝트 노트에 사용. 프로젝트 단위 지식베이스(번호 영역 폴더 구조)는 kb-init/kb-update를 쓴다. 코드 레포에 속하는 README/개발가이드 등은 대상이 아니다.
user-invocable: true
argument-hint: "[노트 주제/내용]"
tools: Read, Write, Edit, Bash, Glob, AskUserQuestion
---

# kb-note — 비프로젝트 단발 노트 (보관함)

기존 `knowledge-base` 스킬을 흡수한 것. 공통 규칙은 kb-conventions를 공유한다.

## 0. 먼저 규칙을 읽는다 (필수)

`${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/conventions.md`를 Read한다(tags·wikilink·명명·vault root).

## 1. 적용 범위 (중요)

- **대상**: 특정 코드 레포/프로젝트에 묶이지 않는 지식·노트(조사·설계 메모·학습 노트·결정 노트).
- **비대상**:
  - **프로젝트 지식베이스** → `kb-init`(생성)/`kb-update`(갱신) 사용. 노트를 프로젝트 폴더(`<vault>/<프로젝트>/`)에 떨구지 않는다.
  - 코드 레포에 속하는 문서(README/CONTRIBUTING/개발가이드/ADR) → 해당 레포에 둔다.
- 어디에 속하는지 모호하면 사용자에게 묻는다.

## 2. 먼저 둘러본다 (Reference first)

- `<vault>` 최상위의 **주제 폴더**(프로젝트 폴더가 아닌)를 살펴 관련 노트가 있는지 확인하고, 있으면 재사용·링크한다.
- 프로젝트 폴더(번호 영역 폴더와 📑 인덱스를 가진 폴더)와 `_archive/`는 노트 저장 대상에서 제외한다.

## 3. 폴더 결정

- 기존 주제 폴더와 매칭. 없으면 **새 폴더명을 제안하고 확인** 후 생성(자동 생성 금지).

## 4. 작성

- `<vault>/<주제 폴더>/<제목>.md`로 저장.
- frontmatter `tags`(차원/용도) + `date`. 같은 주제의 관련 노트에 `[[wikilink]]`.

## 5. 마무리

- 저장 위치·태그·링크를 요약 보고. 보관함 단일 커밋 권고.
