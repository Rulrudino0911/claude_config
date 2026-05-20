---
description: 어제 한 것 / 오늘 할 것 / 막히는 것을 git과 코드에서 자동으로 뽑아 정리한다
allowed-tools:
  - Bash
  - Glob
  - Grep
  - Read
---

# /standup — 일일 개발 현황 요약

<!-- 영감: referendce/tips/claude-boris-15-tips-30-mar-26.md
     tip#3 (/loop 30m /slack-feedback 등 — 반복 자동화로 컨텍스트 유지)
     tip#7 (dev server 자동 시작 + 검증 — 현황 파악 자동화) -->

매일 아침 `/standup` 하나로 어제 작업 컨텍스트를 빠르게 복구한다.

---

## Step 1: 어제 커밋 파악

```bash
git log --since="yesterday 00:00" --until="today 00:00" --oneline --author="$(git config user.name)" 2>/dev/null
```

커밋이 없으면 지난 24시간으로 범위를 넓힌다:
```bash
git log --since="24 hours ago" --oneline 2>/dev/null | head -10
```

---

## Step 2: 현재 브랜치 변경 파악

```bash
git status --short 2>/dev/null
git diff --stat HEAD 2>/dev/null | tail -5
```

---

## Step 3: 미완성 TODO 찾기

변경된 파일 또는 `src/` 전체에서 TODO·FIXME·HACK·XXX 를 Grep한다.

```
패턴: (TODO|FIXME|HACK|XXX)
경로: src/
```

결과를 파일명 + 줄번호 + 내용으로 정리한다.

---

## Step 4: 오늘 작업 추천

Step 1~3 결과를 바탕으로 오늘 이어서 할 만한 작업을 3개 이내로 추천한다.

우선순위 기준:
1. 어제 커밋 직전 열려 있던 파일 (git diff 기준)
2. FIXME / 긴급 TODO
3. 미완성 기능 (함수 스텁, `throw new Error('not implemented')`)

---

## 출력 형식

```
📋 스탠드업 — YYYY-MM-DD
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

---

## 참고

매일 아침 자동 실행하려면 `/loop 24h /standup` 을 사용할 수 있다.
(Claude Code 세션이 유지 중인 경우에만 동작)
