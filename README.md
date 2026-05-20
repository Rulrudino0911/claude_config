# Claude Code 개인 설정

React + Vite + TypeScript + Tailwind 개발 워크플로우에 맞춘 Claude Code 커스텀 커맨드 모음.

---

## 설치 (프로젝트에 적용하기)

`.claude/` 폴더를 사용할 프로젝트 루트에 복사한다.

```bash
cp -r /path/to/this/repo/.claude /your/project/.claude
```

또는 글로벌로 적용하려면 `~/.claude/commands/` 에 복사한다.
(글로벌 커맨드는 모든 프로젝트에서 `/` 메뉴에 나타난다)

```bash
cp -r .claude/commands/* ~/.claude/commands/
```

---

## 기본 설정 (settings.json)

`.claude/settings.json` 을 프로젝트에 추가하면 팀 공통 설정을 적용할 수 있다.

```json
{
  "model": "sonnet",
  "permissions": {
    "allow": [
      "Edit(src/**)",
      "Write(src/**)",
      "Bash(npm run *)",
      "Bash(npx tsc *)",
      "Bash(git log *)",
      "Bash(git diff *)",
      "Bash(git status)"
    ],
    "ask": [
      "Bash(git push *)",
      "Bash(git commit *)"
    ]
  }
}
```

개인 설정(팀에 공유하지 않을 것)은 `.claude/settings.local.json` 에 작성하고 `.gitignore` 에 추가한다.

---

## 커맨드 목록

### `/component` — 컴포넌트 생성기

새 React 컴포넌트를 대화형으로 만든다.
종류를 선택하면 TypeScript props + Tailwind + 재내보내기까지 한 번에 생성한다.

**사용법**

```
/component
/component UserCard
```

**동작 순서**

1. 컴포넌트 종류 선택 (Button / Card / Modal / Form Field / 직접 입력)
2. 추가 옵션 선택 (Storybook 스텁 / index.ts 재내보내기 / 테스트 파일)
3. `src/components/<이름>/` 아래에 파일 생성

**생성 파일 예시**

```
src/components/UserCard/
├── UserCard.tsx          # 본체
├── index.ts              # 재내보내기
├── UserCard.stories.tsx  # Storybook (선택)
└── UserCard.test.tsx     # 테스트 (선택)
```

---

### `/feature` — 기능 개발 워크플로우

새 기능을 Research → Plan → Implement 3단계로 개발한다.
각 단계 사이에 확인을 거쳐 방향을 수정할 수 있다.

**사용법**

```
/feature
/feature 소셜 로그인 기능 추가
```

**동작 순서**

| 단계 | 내용 | 결과 |
|------|------|------|
| 1. Research | 관련 기존 코드·의존성 파악 | 재사용 가능한 코드 목록, 영향 파일 |
| 2. Plan | 태스크 리스트 + 컴포넌트 트리 설계 | 번호 순 구현 태스크 |
| 3. Implement | 태스크 순서대로 구현 + 빌드 확인 | 완성된 기능 |

> 각 단계 완료 후 "계속할까요?" 확인이 뜬다. 이 시점에 방향을 수정할 수 있다.

---

### `/pr-check` — PR 제출 전 체크리스트

PR을 올리기 전 코드 품질을 자동으로 점검한다.
현재 브랜치에서 변경된 파일을 기준으로 동작한다.

**사용법**

```
/pr-check
```

**점검 항목**

| 항목 | 기준 |
|------|------|
| TypeScript 오류 | `tsc --noEmit` 통과 |
| 미사용 import | import 선언 vs 실제 사용 여부 |
| console.log 잔재 | git diff 에서 `console.*` 검색 |
| 하드코딩 색상 | `#fff`, `rgb(...)` 등 — Tailwind 토큰 권장 |
| 접근성 기본 항목 | `<img alt>`, `<button>` 레이블, `<input>` 연결 |
| 번들 임포트 | `import * as` 또는 라이브러리 전체 import |

**결과 예시**

```
PR 체크리스트 결과
══════════════════════
✅ TypeScript 오류     PASS
⚠️  미사용 import      WARN — src/Foo.tsx:3
✅ console.log         PASS
⚠️  하드코딩 색상      WARN — src/Bar.tsx:12
✅ 접근성              PASS
✅ 번들 임포트         PASS
```

FAIL/WARN 항목은 수정 제안과 함께 자동 수정 여부를 묻는다.

---

### `/ui-loop` — UI 수정 반복 루프

UI 버그를 한 번에 고치려 하지 않고, 수정 → 빌드 확인 → 요약 → 반복으로 접근한다.
브라우저 없이도 체계적으로 iterate할 수 있다.

**사용법**

```
/ui-loop
/ui-loop 모바일에서 헤더가 겹쳐 보여
```

**동작 순서**

1. 문제 설명 입력 (인자 또는 질문)
2. 관련 파일 탐색 (Grep으로 키워드 검색)
3. Tailwind 클래스 수정
4. `npm run build` + `tsc --noEmit` 확인
5. 수정 내용 요약 출력
6. 추가 문제가 있으면 2번으로 돌아가고, 없으면 종료

**팁** — Chrome 확장(Claude in Chrome)이나 Playwright MCP가 있으면 실제 브라우저에서 바로 검증할 수 있다.

---

### `/standup` — 일일 개발 현황 요약

매일 아침 하나의 커맨드로 어제 작업 컨텍스트를 빠르게 복구한다.
git log와 소스 코드에서 직접 현황을 뽑아낸다.

**사용법**

```
/standup
```

**출력 예시**

```
📋 스탠드업 — 2026-05-20
══════════════════════════════

어제 한 것
----------
• feat: 로그인 폼 유효성 검사 추가
• fix: 모바일 네비게이션 overflow 수정

현재 브랜치 상태
-----------------
브랜치: feature/auth
미커밋 변경: 2개 파일

미완성 TODO
-----------
• src/auth/LoginForm.tsx:42  — TODO: 소셜 로그인 버튼 연결
• src/hooks/useAuth.ts:87    — FIXME: 토큰 만료 처리 누락

오늘 추천 작업
--------------
1. useAuth.ts 토큰 만료 처리 (FIXME)
2. LoginForm 소셜 로그인 버튼 연결 (TODO)
3. 변경 파일 /pr-check 로 점검 후 PR 준비
```

**자동 실행** — 세션이 유지 중일 때 매일 자동 실행하려면:

```
/loop 24h /standup
```

---

## 폴더 구조

```
.claude/
├── commands/
│   ├── component.md    # /component
│   ├── feature.md      # /feature
│   ├── pr-check.md     # /pr-check
│   ├── ui-loop.md      # /ui-loop
│   └── standup.md      # /standup
└── settings.json       # 권한·모델 설정 (선택)
```

---

## 참고

커맨드 파일은 `referendce/` 폴더의 설정을 참고해 React + Vite + TS + Tailwind 스택에 맞게 한국어로 각색했다.
각 파일 상단 주석에 원본 영감 출처가 표시되어 있다.
