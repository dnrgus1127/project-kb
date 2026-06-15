---
name: kb-set-vault
description: project-kb 보관함 루트(vault root) 절대경로를 영속 config 파일에 설정·변경한다. 현재 config의 vaultRoot를 보여주고, 새 절대경로를 받아 존재를 검증한 뒤 ~/.claude/project-kb/config.json에 기록한다. "보관함 경로 바꿔", "vault root 설정", "kb 보관함 위치 변경"에 사용.
user-invocable: true
argument-hint: "[새 보관함 루트 절대경로(선택)]"
tools: Read, Write, Edit, Bash, AskUserQuestion
---

# kb-set-vault — 보관함 루트 절대경로 설정·변경

이 스킬은 보관함 루트(vault root) 절대경로를 **영속 config 파일**에 기록한다.
config 파일과 해석 순서의 단일 출처는 `conventions.md` §0이다.

## 0. 먼저 규칙을 읽는다 (필수)

`${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/conventions.md`의 **§0 보관함 루트(vault root) 해석**을 Read한다. config 파일 경로·스키마·해석 순서는 그 절을 따른다.

## 1. config 파일 경로

```
${HOME}/.claude/project-kb/config.json
```

- Windows에서는 `%USERPROFILE%\.claude\project-kb\config.json`.
- **버전이 박힌 캐시 경로(`.../project-kb/<version>/`) 안에 두지 않는다** — 플러그인 업데이트 시 날아간다.
- 스키마: `{ "vaultRoot": "<보관함 루트 절대경로>" }`

## 2. 현재 값 표시

- config 파일이 있으면 Read해 현재 `vaultRoot`를 사용자에게 보여준다.
- config 파일이 없으면 환경변수 `KB_VAULT_ROOT`가 있는지 확인하고, "현재 영속 설정 없음(필요 시 환경변수 사용 중)"임을 알린다.

## 3. 새 절대경로 입력

- 인자로 새 절대경로를 받았으면 그것을 쓴다. 없으면 사용자에게 **절대경로**를 묻는다(AskUserQuestion).
- 상대경로·빈 값은 거부하고 다시 묻는다.

## 4. 존재 검증 (가드)

- 입력된 경로가 **실제로 존재하는 디렉터리인지** 확인한다(`Bash`로 점검, 한글·이모지·공백 경로는 따옴표 처리).
- 존재하지 않으면 **자동 생성하지 않는다.** 사용자에게 알리고, 만들지 / 다른 경로를 쓸지 / 중단할지 확인한다(보관함 폴더 자동 생성 금지 가드 유지 — `conventions.md` §0).
- 확인 없이 보관함 폴더를 만들지 않는다.

## 5. config 기록

- `~/.claude/project-kb/` 디렉터리가 없으면 만든다(config 디렉터리/파일 자체는 보관함이 아니므로 생성해도 된다 — `conventions.md` §0의 가드 예외).
- config 파일을 Write해 `vaultRoot`를 새 절대경로로 설정한다. 다른 키가 이미 있으면 보존하고 `vaultRoot`만 갱신한다(부분 갱신).
- Windows 경로의 백슬래시는 JSON에서 `\\`로 이스케이프한다(예: `"C:\\WebOfficeEnvironment\\docs"`).

## 6. 마무리

- 기록한 config 파일 경로와 새 `vaultRoot` 값을 요약 보고한다.
- 이후 모든 kb-* 스킬이 이 값을 우선 사용함을 안내한다(`conventions.md` §0 해석 순서 1).
- 환경변수 `KB_VAULT_ROOT`가 함께 설정돼 있으면, config가 우선임을 알린다(하위 호환은 config 부재 시에만 동작).

> 이 스킬은 **config 파일만** 건드린다. 보관함 내용(프로젝트 폴더·문서)은 절대 옮기거나 만들지 않는다.
