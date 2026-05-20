---
description: PR 제출 전 TypeScript·Tailwind·접근성·코드 품질을 순서대로 점검한다
allowed-tools:
  - Bash
  - Glob
  - Grep
  - Read
---

# /pr-check — PR 제출 전 체크리스트

<!-- 영감: referendce/tips/claude-boris-15-tips-30-mar-26.md tip#6 (Claude에게 결과 검증 수단을 줘라)
     + referendce/best-practice/claude-commands.md /security-review 패턴 -->

현재 브랜치의 변경된 파일을 기준으로 점검한다. 항목별로 PASS / WARN / FAIL 로 표시한다.

---

## 점검 순서

### 1. 변경 파일 파악

```bash
git diff --name-only main...HEAD 2>/dev/null || git diff --name-only HEAD~1
```

변경된 `.ts` / `.tsx` 파일 목록을 확인한다.

---

### 2. TypeScript 오류

```bash
npx tsc --noEmit 2>&1 | head -50
```

- PASS: 오류 없음
- FAIL: 오류 목록 출력 후 수정 제안

---

### 3. 미사용 import

변경 파일에서 import 선언을 Grep으로 찾아 실제 사용 여부를 확인한다.
패턴: `^import .+ from` → 각 import 심볼이 파일 본문에 등장하는지 검사한다.

- WARN: 미사용 import가 있으면 목록 출력

---

### 4. console.log 잔재

```bash
git diff main...HEAD -- "*.ts" "*.tsx" | grep "^\+" | grep "console\."
```

- WARN: 남아 있는 console.* 목록과 줄 번호 출력

---

### 5. 하드코딩 색상

변경된 `.tsx` 파일에서 인라인 style 또는 `#`, `rgb(`, `hsl(` 패턴을 Grep.
Tailwind 토큰으로 대체 가능한 경우 제안한다.

- WARN: 발견 시 해당 줄 출력

---

### 6. 접근성 기본 항목

변경된 `.tsx` 파일에서 아래를 Grep으로 확인한다.

- `<img` 태그에 `alt=` 없는 것
- `<button` 태그에 텍스트 내용 또는 `aria-label` 없는 것
- `<input` 태그에 연결된 `<label>` 또는 `aria-label` 없는 것

- WARN: 발견 시 해당 줄 출력

---

### 7. 번들 영향이 큰 임포트

변경 파일에서 `import * as`, `import entire library` 패턴을 확인한다.
(예: `import _ from 'lodash'` → `import debounce from 'lodash/debounce'` 권장)

- WARN: 발견 시 트리쉐이킹 가능한 임포트 방식 제안

---

## 결과 요약 형식

```
PR 체크리스트 결과
══════════════════════
변경 파일: N개

✅ TypeScript 오류     PASS
⚠️  미사용 import      WARN — src/Foo.tsx:3
✅ console.log         PASS
⚠️  하드코딩 색상      WARN — src/Bar.tsx:12 (#fff → text-white)
✅ 접근성              PASS
✅ 번들 임포트         PASS

총평: WARN N개. 위 항목 확인 후 PR 올리세요.
```

FAIL 항목이 있으면 수정을 제안하고, 사용자가 원하면 자동 수정한다.
