---
description: UI 문제를 설명하면 수정 → 빌드 확인 → 요약을 반복하며 개선한다
argument-hint: "[문제 설명]"
allowed-tools:
  - AskUserQuestion
  - Read
  - Edit
  - Bash
  - Glob
  - Grep
---

# /ui-loop — UI 수정 반복 루프

<!-- 영감: referendce/tips/claude-boris-15-tips-30-mar-26.md
     tip#6 (Chrome 확장으로 프론트 결과 검증 — Claude에게 검증 수단을 줘라)
     tip#3 (/loop로 자동화 — iterate until it looks good) -->

UI 문제를 한 번에 완벽하게 고치려 하지 않고, 작은 수정 → 확인 → 반복으로 접근한다.

---

## Step 1: 문제 파악

인자로 문제 설명이 없으면 AskUserQuestion으로 묻는다.

질문 1 — 헤더: "UI 문제"
- "어떤 화면에서 어떤 문제가 있나요? (예: 모바일에서 버튼이 잘림, 다크모드에서 텍스트 안 보임)"

질문 2 — 헤더: "영향 파일" (선택)
- "관련 컴포넌트 파일 이름을 알고 있으면 적어주세요. 모르면 넘어가도 됩니다."

---

## Step 2: 관련 파일 탐색

1. 인자로 파일이 주어졌으면 Read로 읽는다
2. 없으면 Grep으로 문제 설명의 키워드를 `src/` 에서 검색해 후보 파일을 찾는다
3. 후보 파일을 Read해 현재 Tailwind 클래스와 구조를 파악한다

---

## Step 3: 수정

파악한 내용을 바탕으로 수정한다.

### 수정 원칙
- Tailwind 반응형 접두사 우선 (`sm:`, `md:`, `lg:`)
- 색상은 다크모드 변형도 함께 (`text-gray-900 dark:text-gray-100`)
- 레이아웃 문제는 부모 컴포넌트부터 확인
- 한 번에 여러 파일보다 문제 파일 하나씩 수정

---

## Step 4: 빌드 확인

```bash
npm run build 2>&1 | tail -20
```

빌드 오류가 있으면 즉시 수정한다. 빌드가 통과하면 다음으로 넘어간다.

TypeScript 프로젝트라면 추가로:
```bash
npx tsc --noEmit 2>&1 | head -20
```

---

## Step 5: 수정 내용 요약 + 다음 라운드 여부 확인

아래 형식으로 요약 출력:

```
[라운드 N 완료]
수정한 파일:
- src/components/Foo.tsx — 모바일 패딩 추가 (px-4 → px-4 sm:px-6)
- src/components/Bar.tsx — 다크모드 텍스트 색상 추가

확인 필요:
- 실제 브라우저에서 모바일 뷰(375px)로 확인해 주세요
- 다크모드 토글 후 텍스트 가독성 확인 필요

아직 남은 문제가 있으면 설명해 주세요. 없으면 완료입니다.
```

사용자가 추가 문제를 설명하면 Step 2부터 반복한다.
"완료" 또는 "괜찮다"는 신호가 오면 루프를 종료한다.
