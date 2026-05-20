---
description: React + TypeScript + Tailwind 컴포넌트를 대화형으로 생성한다
argument-hint: "[컴포넌트 이름]"
allowed-tools:
  - AskUserQuestion
  - Write
  - Edit
  - Read
  - Glob
---

# /component — 컴포넌트 생성기

<!-- 영감: referendce/.claude/commands/weather-orchestrator.md (AskUserQuestion → 생성 패턴) -->

인자로 이름이 주어지면 그대로 사용하고, 없으면 Step 1부터 진행한다.

## Step 1: 컴포넌트 종류 선택

AskUserQuestion 으로 아래를 묻는다.

질문 1 — 헤더: "컴포넌트 종류"
- Button: 클릭 가능한 버튼, variant(primary/secondary/ghost/destructive) 포함
- Card: 타이틀·설명·액션 슬롯이 있는 카드
- Modal: 오버레이 다이얼로그, 열기/닫기 상태 관리 포함
- Form Field: label + input/textarea + 에러 메시지 세트
- 직접 입력: 원하는 컴포넌트를 자유롭게 설명

질문 2 — 헤더: "추가 옵션"(multiSelect)
- Storybook 스텁 생성 (.stories.tsx)
- index.ts 재내보내기 추가
- 기본 테스트 파일 생성 (.test.tsx)

## Step 2: 기존 코드 파악

`src/components/` 구조를 Glob으로 훑어 네이밍 패턴과 임포트 스타일을 파악한다.
`src/components/index.ts` 또는 `src/components/index.tsx` 가 있으면 읽어 재내보내기 방식을 확인한다.

## Step 3: 파일 생성

생성 위치: `src/components/<ComponentName>/<ComponentName>.tsx`

### 필수 규칙
- Props 인터페이스는 파일 상단에 export
- Tailwind 클래스만 사용 (인라인 style 금지)
- className prop을 항상 받아서 외부에서 확장 가능하게
- forwardRef 사용 (Button, Input 같은 인터랙티브 요소)
- 기본값은 defaultProps 대신 구조 분해 기본값

### 생성할 파일 목록
1. `src/components/<ComponentName>/<ComponentName>.tsx` — 본체 (필수)
2. `src/components/<ComponentName>/index.ts` — 재내보내기 (필수)
3. `src/components/<ComponentName>/<ComponentName>.stories.tsx` — Storybook 스텁 (선택)
4. `src/components/<ComponentName>/<ComponentName>.test.tsx` — 테스트 스켈레톤 (선택)
5. `src/components/index.ts` 업데이트 — 사용자가 index.ts 추가를 선택한 경우

## Step 4: 결과 요약

생성한 파일 목록과 사용 예시 스니펫을 출력한다.

```tsx
// 사용 예시
import { <ComponentName> } from '@/components/<ComponentName>';
```
