---
description: 새 기능을 Research → Plan → Implement 3단계로 체계적으로 개발한다
argument-hint: "[기능 설명]"
allowed-tools:
  - AskUserQuestion
  - Read
  - Glob
  - Grep
  - Write
  - Edit
  - Bash
  - TodoWrite
---

# /feature — 기능 개발 워크플로우 (RPI 패턴)

<!-- 영감: referendce/development-workflows/rpi/rpi-workflow.md (Research → Plan → Implement) -->

각 단계 사이에 사람이 확인(human gate)한다. 단계를 건너뛰지 않는다.

## 사전 준비

인자로 기능 설명이 주어지지 않았으면 AskUserQuestion으로 묻는다.

- 헤더: "기능 설명"
  - "어떤 기능을 만들 건가요? 한 문장으로 설명해 주세요."

---

## Phase 1 — Research (파악)

목표: 이 기능과 관련된 기존 코드·의존성·패턴을 파악해 방향을 잡는다.

1. `src/` 구조를 Glob으로 훑는다
2. 기능과 관련될 컴포넌트·훅·유틸·타입을 Grep으로 찾는다
3. `package.json` 을 읽어 이미 있는 라이브러리를 확인한다
4. 아래 형식으로 파악 결과를 출력한다:

```
[파악 결과]
- 재사용 가능한 기존 코드: ...
- 영향 받는 파일: ...
- 새로 필요한 것: ...
- 주의할 점: ...
```

**→ 여기서 멈추고 사용자 확인을 기다린다.**
"Phase 1 완료. 위 파악 결과로 설계를 시작할까요?"

---

## Phase 2 — Plan (설계)

목표: 무엇을 어떤 순서로 만들지 명확한 태스크 리스트를 만든다.

1. 필요한 파일 목록과 역할을 정리한다
2. 컴포넌트 트리 또는 데이터 흐름을 텍스트로 그린다
3. 구현 태스크를 번호 목록으로 나열한다 (한 태스크 = 한 파일 또는 한 함수)
4. TodoWrite로 태스크 목록을 등록한다

**→ 여기서 멈추고 사용자 확인을 기다린다.**
"Phase 2 완료. 이 계획대로 구현을 시작할까요? 수정할 게 있으면 말씀해 주세요."

---

## Phase 3 — Implement (구현)

목표: Phase 2 계획을 태스크 순서대로 구현한다.

### 규칙
- 태스크를 하나씩 완료한 뒤 TodoWrite로 completed 처리
- TypeScript 타입 먼저, 구현 나중
- Tailwind 클래스만 사용 (인라인 style 금지)
- 기능 단위로 구현이 끝나면 `npm run build` 또는 `npm run typecheck` 로 오류 확인

### 완료 기준
- TypeScript 오류 없음
- 빌드 통과
- 새로 만든 컴포넌트는 `src/components/index.ts` 에 재내보내기

완료 후 요약:
```
[구현 완료]
- 생성/수정한 파일: ...
- 남은 작업 (있다면): ...
```
