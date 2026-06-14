---
name: kb-conventions
description: project-kb 플러그인의 공통 규칙(보관함 taxonomy, 파일/폴더 명명, tags·[[wikilink]]·callout, 정본(canonical), 다이어그램/이미지, vault root 해석) 단일 출처. 다른 kb-* 스킬과 kb 에이전트가 작업 전에 참조한다. 사용자가 직접 호출하기보다 내부 참조용.
user-invocable: false
---

# kb-conventions — 프로젝트 지식베이스 공통 규칙 (단일 출처)

이 스킬은 **규칙의 단일 출처**다. 실제 규칙 본문은 한 파일에만 있다:

```
${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/conventions.md
```

## 사용 방법

- **모든 kb-\* 스킬**: 작업을 시작하기 전에 위 `conventions.md`를 **반드시 Read**한다. 규칙 내용을 다른 파일에 복사하지 않는다(드리프트 방지) — 항상 이 파일을 읽는다.
- **kb 에이전트**: 서브에이전트는 부모 컨텍스트를 상속하지 않으므로, 각 에이전트 프롬프트에도 "작업 전 `conventions.md`를 Read하라"는 지시가 들어 있다.

## 템플릿

스캐폴딩/문서 생성에 쓰는 순수 스텁 템플릿:

```
${CLAUDE_PLUGIN_ROOT}/skills/kb-conventions/references/templates/
├── moc.md            # 📑 인덱스(MOC)
├── catalog.md        # 전 프로젝트 루트 카탈로그
└── doc-stubs/        # 영역별 문서 스텁(개요/계획/아이디어/현황/흐름도/운영/사용설명)
```

템플릿의 `{{PLACEHOLDER}}`는 생성 시 실제 값으로 치환한다. 템플릿에는 어떤 개인 데이터/실제 경로도 넣지 않는다.
