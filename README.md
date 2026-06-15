# project-kb

프로젝트 단위 지식베이스를 **일관된 구조로 생성·갱신·정합성 유지**하는 Claude Code 플러그인.
지식 문서는 Obsidian 보관함에 `<vault>/me/<프로젝트>/` 형태로 저장되며, 4개 영역 + 시각 자료(`images/`) + MOC(📑 인덱스) + 전 프로젝트 카탈로그로 구성된다.

## 무엇을 주는가

### 스킬 (8종)
| 스킬 | 호출 | 역할 |
|---|---|---|
| `kb-conventions` | (참조 전용) | taxonomy·tags·wikilink·callout·정본·다이어그램·vault root 규칙의 **단일 출처**. 다른 스킬/에이전트가 작업 전 읽는다. |
| `kb-set-vault` | 슬래시 | 보관함 루트 절대경로를 영속 config(`~/.claude/project-kb/config.json`)에 설정·변경 |
| `kb-init` | 슬래시 | 신규 프로젝트 폴더 구조 + MOC + 문서 스텁 + `images/` 생성, 루트 카탈로그 등록 |
| `kb-update` | 슬래시 | 새 정보를 올바른 문서/섹션으로 라우팅, 정본 갱신, 흐름도 SVG 임베드 |
| `kb-status` | 슬래시 | 코드↔문서 드리프트 감사 → **승인 후** 구현현황/흐름도/Phase 갱신 |
| `kb-curate` | 슬래시/내부 | MOC·wikilink·정본·이미지·카탈로그 무결성 점검 |
| `kb-archive` | 슬래시 | 종료/중단 프로젝트를 `_archive/`로 이동, 카탈로그 상태 갱신 |
| `kb-note` | 슬래시 | 프로젝트에 속하지 않는 단발 노트 저장 |

### 에이전트 (3종 · 전부 읽기/제안 전용)
| 에이전트 | 역할 |
|---|---|
| `kb-planner` | 기획 PM — 의도 탐색 → 구현 계획·아이디어 백로그 초안 |
| `kb-status-pm` | 상황관리 PM — 코드↔문서 드리프트 근거 기반 감사 |
| `kb-risk-reviewer` | 리스크 검토 — 계획·아이디어의 기술/운영/금융 리스크 평가 |

> 에이전트는 보관함에 **직접 쓰지 않는다.** 초안/감사 결과만 산출하고, 보관함 기록은 항상 스킬이 수행한다(단일 쓰기 경로).

## 설치

```
/plugin marketplace add dnrgus1127/project-kb
/plugin install project-kb@project-kb
```

설치 후 활성화하면 7개 스킬과 3개 에이전트가 자동 발견된다.

## 구성 — 보관함 루트 지정

이 플러그인은 보관함 경로를 하드코딩하지 않는다. 보관함 루트 절대경로는 **설치 환경별로 한 번 입력받아 config 파일에 영속 저장**한다.

config 파일 (버전과 무관하게 유지되는 경로 — 플러그인 업데이트에 안전):
```
~/.claude/project-kb/config.json
```
```json
{ "vaultRoot": "/absolute/path/to/knowledge-base" }
```
(Windows: `%USERPROFILE%\.claude\project-kb\config.json`, 예 `{ "vaultRoot": "C:\\path\\to\\vault" }`)

해석 순서(상세는 `conventions.md` §0):
1. config 파일의 `vaultRoot`가 있으면 사용.
2. 없으면 환경변수 `KB_VAULT_ROOT` (하위 호환).
3. 둘 다 없으면 **첫 사용 시 스킬이 절대경로를 물어** config 파일에 저장한 뒤 사용한다.

경로를 나중에 바꾸려면 `/kb-set-vault` 스킬을 쓴다(현재 값 표시 → 새 절대경로 검증 → config 기록).
보관함 폴더는 자동 생성하지 않으며, 없으면 확인 후 진행한다.

## 사용 흐름

```
새 프로젝트          /kb-init  →  4영역 + images/ + 📑 인덱스 + 카탈로그 등록
기능 기획            kb-planner (초안) → /kb-update (기록) → kb-risk-reviewer (검토 노트)
정보/문서 추가       /kb-update  →  라우팅·정본 갱신 → kb-curate
코드 진행 동기화     /kb-status  →  드리프트 리포트 → 승인분만 반영
정합성 점검          /kb-curate  →  링크·이미지·정본·카탈로그
프로젝트 종료        /kb-archive
비프로젝트 메모      /kb-note
```

## 보관함 구조 (kb-init이 만드는 것)

```
<vault>/me/
├── 📚 프로젝트 카탈로그.md        # 전 프로젝트 횡단 인덱스
└── <프로젝트>/
    ├── 📑 인덱스.md               # MOC: 진입점·문서지도·Phase·정본 테이블
    ├── 1. 개요·전략/
    ├── 2. 개발·구현/              # 계획·아이디어·구현현황(정본)·흐름도
    ├── 3. 운영·셋업/
    ├── 4. 사용설명서/
    └── images/                    # 흐름도·구성도 SVG 등 시각 자료
```

## 설계 원칙

- **쓰기 단일 경로**: 보관함 기록은 스킬만. 에이전트는 읽기/제안만.
- **컨벤션 단일 출처**: 모든 컴포넌트가 `${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/conventions.md`를 먼저 읽는다.
- **자동 덮어쓰기 금지**: 진행도·정본은 드리프트 리포트 → 사람 승인 후 반영.
- **개인 데이터 0**: 이 레포에는 어떤 실제 보관함 내용/개인 경로도 없다. 템플릿은 순수 스텁이다.

## 라이선스

MIT
