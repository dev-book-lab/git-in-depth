# 1.4 커밋 해부하기 (Commit Anatomy)

**난이도: ⭐⭐**

> *"좋은 커밋은 코드의 역사를 이야기합니다. 완벽한 커밋은 미래의 당신을 구합니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 커밋 구조를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 6개월 된 프로젝트에서 버그 발견

$ git log --oneline
abc123 fix
def456 update
ghi789 change
jkl012 fix bug
mno345 update code
...

팀원: "이 버그가 언제 생긴 거죠? 어느 커밋인가요?"
당신: (커밋 메시지 보며) "... 모르겠습니다" (절망)

리더: "커밋 메시지가 왜 이렇게 대충 써있죠?"
당신: "급해서요..." (변명)

리더: "이제 버그 찾는데 100개 커밋 다 봐야겠네요"
```

**커밋을 제대로 이해하면:**
```bash
# 명확한 커밋 히스토리
$ git log --oneline
abc123 feat(auth): Add JWT token validation
def456 fix(api): Resolve null pointer in user service
ghi789 refactor(db): Migrate to connection pooling
jkl012 docs: Update API documentation
mno345 test: Add integration tests for payment

# 버그 찾기 쉬움!
git log --grep="user service"
# def456 fix(api): Resolve null pointer in user service ← 찾았다!
```

> 💡 **핵심:** 커밋은 단순한 스냅샷이 아닙니다.
> 코드 변경의 "왜", "무엇을", "어떻게"를 담는 타임캡슐입니다!

---

### 📌 커밋 객체의 구조

#### **커밋 객체 = 5가지 정보**

```bash
# 커밋 객체 보기
git cat-file -p HEAD

# 출력:
tree 4b825dc642cb6eb9a060e54bf8d69288fbee4904
parent abc123def456789...
author John Doe <john@example.com> 1640995200 +0900
committer Jane Smith <jane@example.com> 1640995200 +0900

feat(auth): Add JWT token validation

Implement JWT token validation for API endpoints
- Add token verification middleware
- Add token expiration check
- Add refresh token logic

Closes #123
```

**구조 분석:**
```
┌─────────────────────────────────┐
│      Commit Object              │
├─────────────────────────────────┤
│ tree        4b825dc...          │ ← 1️⃣ 프로젝트 스냅샷
│ parent      abc123...           │ ← 2️⃣ 부모 커밋
│ author      John <...> 시간      │ ← 3️⃣ 작성자
│ committer   Jane <...> 시간      │ ← 4️⃣ 커밋터
│                                 │
│ 커밋 메시지                        │ ← 5️⃣ 설명
└─────────────────────────────────┘
```

---

#### **1️⃣ tree (프로젝트 스냅샷)**

**역할:** 이 커밋 시점의 전체 프로젝트 상태

```bash
# Tree 객체 확인
git cat-file -p 4b825dc

# 출력:
100644 blob 8d0e41... README.md
100644 blob 9f4d96... package.json
040000 tree a1b2c3... src
040000 tree d4e5f6... tests
```

**의미:**
```
tree = 프로젝트 전체의 "사진"
     = 모든 파일과 디렉토리의 상태
     = "이 커밋 시점의 코드베이스"
```

---

#### **2️⃣ parent (부모 커밋)**

**역할:** 이 커밋의 직전 커밋(들)

**일반 커밋 (부모 1개):**
```bash
parent abc123def456...

# 시각화:
current_commit ← 지금
      ↓
  abc123      ← parent
      ↓
  def456
```

**Merge 커밋 (부모 2개):**
```bash
parent abc123...  ← 첫 번째 부모 (main)
parent def456...  ← 두 번째 부모 (feature)

# 시각화:
    merge_commit
      ↙    ↘
   main   feature
```

**최초 커밋 (부모 없음):**
```bash
# parent 줄이 없음!
tree 4b825dc...
author ...
```

---

#### **3️⃣ author (작성자)**

**역할:** 실제로 코드를 작성한 사람

```bash
author John Doe <john@example.com> 1640995200 +0900
       ↑        ↑                   ↑          ↑
       이름     이메일               타임스탬프  시간대
```

**타임스탬프 해석:**
```bash
# Unix timestamp
1640995200 = 2022-01-01 00:00:00 (UTC)

# 사람이 읽을 수 있게
git log --format="%ai"
# 2022-01-01 09:00:00 +0900
```

---

#### **4️⃣ committer (커밋터)**

**역할:** 실제로 커밋을 저장소에 넣은 사람

**author vs committer:**
```bash
# 일반적인 경우: 같음
author    John <john@email> 1640995200 +0900
committer John <john@email> 1640995200 +0900

# 다른 경우:
author    John <john@email> 1640995200 +0900  ← 코드 작성자
committer Jane <jane@email> 1640995300 +0900  ← 적용한 사람
```

**언제 다를까?**
```
1. Pull Request 머지
   - author: PR 작성자
   - committer: 머지한 사람

2. git am (패치 적용)
   - author: 패치 작성자
   - committer: 적용한 사람

3. git rebase
   - author: 원래 작성자
   - committer: rebase 실행자

4. git cherry-pick
   - author: 원래 커밋 작성자
   - committer: cherry-pick 실행자
```

---

#### **5️⃣ 커밋 메시지**

**구조:**
```
제목 (Subject)
<빈 줄>
본문 (Body)
<빈 줄>
꼬리말 (Footer)
```

**예시:**
```
feat(auth): Add JWT token validation

Implement JWT token validation for API endpoints
to improve security and prevent unauthorized access.

Changes:
- Add token verification middleware
- Add token expiration check  
- Add refresh token logic

BREAKING CHANGE: Auth header format changed
Closes #123
Reviewed-by: Jane Smith
```

---

### 🔍 커밋 메시지 작성법

#### **Conventional Commits 규칙**

**형식:**
```
<type>(<scope>): <subject>

<body>

<footer>
```

**Type (필수):**
```
feat:     새로운 기능
fix:      버그 수정
docs:     문서만 수정
style:    코드 의미 영향 없는 변경 (포맷, 세미콜론 등)
refactor: 리팩토링 (기능 변화 없음)
test:     테스트 추가/수정
chore:    빌드, 설정 등
perf:     성능 개선
ci:       CI 설정 변경
revert:   커밋 되돌리기
```

**Scope (선택):**
```
feat(auth): ...    ← auth 모듈
fix(api): ...      ← api 관련
docs(readme): ...  ← readme 파일
```

**Subject (필수):**
```
✅ DO:
- 명령문 사용 ("Add" not "Added")
- 첫 글자 대문자 (선택적)
- 끝에 마침표 없음
- 50자 이내

❌ DON'T:
- 과거형 사용 ("Added")
- 너무 길게 (80자 넘기지 말기)
- 애매한 표현 ("fix bug", "update")
```

**Body (선택):**
```
- 무엇을 왜 변경했는지
- 72자마다 줄바꿈
- 어떻게보다 왜를 설명
```

**Footer (선택):**
```
BREAKING CHANGE: 호환성 깨지는 변경
Closes #123: 이슈 종료
Reviewed-by: 리뷰어
Refs #456: 참조 이슈
```

---

#### **좋은 커밋 메시지 예시**

**예시 1: 기능 추가**
```
feat(payment): Add Stripe payment integration

Integrate Stripe payment gateway for credit card processing.
This replaces the previous PayPal-only implementation.

Changes:
- Add Stripe SDK dependency
- Implement payment service wrapper
- Add payment webhook handler
- Add error handling for failed payments

Closes #456
```

**예시 2: 버그 수정**
```
fix(auth): Resolve session timeout issue

Fix race condition where sessions were expiring prematurely
when multiple requests were made simultaneously.

Root cause: Session timestamp was updated asynchronously
Solution: Use atomic operations for timestamp updates

Fixes #789
```

**예시 3: Breaking Change**
```
refactor(api)!: Change API response format to camelCase

BREAKING CHANGE: All API responses now use camelCase
instead of snake_case for field names.

Migration guide:
- Update client code to use camelCase
- See docs/migration-v2.md for details

Closes #234
```

---

### 📊 커밋 메시지 통계로 보는 프로젝트 건강도

**좋은 프로젝트:**
```bash
git log --oneline -20
feat(ui): Add dark mode support
fix(db): Resolve connection pool leak  
docs: Update deployment guide
test: Add unit tests for user service
refactor(api): Extract common validation logic
...

# 특징:
✅ 명확한 type
✅ 구체적인 scope
✅ 설명적인 subject
✅ 일관된 형식
```

**나쁜 프로젝트:**
```bash
git log --oneline -20
update
fix
change
update code
fix bug
asdf
WIP
...

# 문제점:
❌ type 없음
❌ 애매한 설명
❌ 형식 불일치
❌ 정보 부족
```

---

### 🔧 커밋 수정 기법

#### **1️⃣ --amend (마지막 커밋 수정)**

**사용 시점:**
```
- 커밋 메시지 오타 수정
- 빠뜨린 파일 추가
- 작은 수정사항 추가
```

**기본 사용:**
```bash
# 마지막 커밋 메시지만 수정
git commit --amend

# 메시지 직접 수정
git commit --amend -m "New message"

# 파일 추가하고 커밋 수정
git add forgotten_file.txt
git commit --amend --no-edit
```

**내부 동작:**
```
Before:
    commit_B (HEAD -> main)
         ↓
    commit_A

After amend:
    commit_B' (HEAD -> main) ← 새 커밋 (다른 Hash!)
         ↓
    commit_A
    
    commit_B ← 고아됨 (reflog에만 남음)
```

**주의사항:**
```bash
# ⚠️ Push 후에는 위험!
git push              # 처음 push
git commit --amend    # 수정
git push              # ❌ Rejected! (히스토리 불일치)

# 강제 push 필요 (위험!)
git push --force-with-lease
```

---

#### **2️⃣ --fixup (나중에 합칠 수정)**

**사용 시점:**
```
- 이전 커밋의 버그 발견
- 리뷰 피드백 반영
- 나중에 squash할 예정
```

**기본 사용:**
```bash
# 1. 원래 커밋
git commit -m "feat(api): Add user endpoint"

# 2. 버그 발견!
git add fix.js
git commit --fixup HEAD
# 생성: "fixup! feat(api): Add user endpoint"

# 3. 또 버그!
git add another_fix.js
git commit --fixup HEAD~1
# 생성: "fixup! feat(api): Add user endpoint"

# 4. Interactive rebase로 합치기
git rebase -i --autosquash HEAD~3
```

**히스토리:**
```
Before fixup:
commit_C feat(api): Add user endpoint
commit_B ...
commit_A ...

After fixup:
commit_D fixup! feat(api): Add user endpoint
commit_C feat(api): Add user endpoint
commit_B ...
commit_A ...

After rebase --autosquash:
commit_C' feat(api): Add user endpoint (C+D 합쳐짐)
commit_B ...
commit_A ...
```

---

#### **3️⃣ --squash (여러 커밋 합치기)**

**사용 시점:**
```
- WIP 커밋들을 하나로
- 실험적 커밋들을 정리
- PR 전 커밋 정리
```

**기본 사용:**
```bash
# 1. 여러 WIP 커밋
git commit -m "WIP: start feature"
git commit -m "WIP: continue"
git commit -m "WIP: almost done"

# 2. Squash 커밋 생성
git commit --squash HEAD~2
# 생성: "squash! WIP: start feature"

# 3. Interactive rebase로 합치기
git rebase -i --autosquash HEAD~4
```

**fixup vs squash:**
```
fixup:  커밋 메시지 버림
squash: 커밋 메시지 합침

# Rebase 시:
pick abc123 Original commit
fixup def456 fixup! Original  ← 메시지 무시
squash ghi789 squash! Other   ← 메시지 합침
```

---

### 🎯 커밋 분할 전략

#### **원자적 커밋 (Atomic Commits)**

**원칙:**
```
1개 커밋 = 1개 논리적 변경

✅ DO:
- 한 가지 목적
- 독립적으로 동작
- 테스트 통과
- 롤백 가능

❌ DON'T:
- 여러 기능 섞기
- 관련 없는 변경
- 빌드 깨지는 커밋
```

**예시:**
```bash
# ❌ 나쁜 예 (여러 기능 섞임)
git commit -m "Add login, fix bug, update docs"

# ✅ 좋은 예 (분리)
git commit -m "feat(auth): Add login functionality"
git commit -m "fix(api): Resolve null pointer in user service"
git commit -m "docs: Update API documentation"
```

---

#### **커밋 크기 가이드**

**너무 작음:**
```bash
git commit -m "Add semicolon"
git commit -m "Fix typo"
git commit -m "Add newline"
# ❌ 너무 세밀함
```

**너무 큼:**
```bash
git commit -m "Implement entire user management system"
# 50 files changed, 5000 insertions(+), 3000 deletions(-)
# ❌ 너무 큼
```

**적절함:**
```bash
git commit -m "feat(auth): Add JWT token validation"
# 5 files changed, 150 insertions(+), 20 deletions(-)
# ✅ 한 기능, 적절한 크기
```

---

### 📝 .gitmessage 템플릿

**커밋 메시지 템플릿 설정:**

```bash
# 1. 템플릿 파일 생성
cat > ~/.gitmessage << 'EOF'
# <type>(<scope>): <subject>
#
# <body>
#
# <footer>

# Type: feat, fix, docs, style, refactor, test, chore
# Scope: 변경된 모듈/파일
# Subject: 50자 이내, 명령문
#
# Body: 무엇을, 왜 변경했는지
# 
# Footer: Closes #123, BREAKING CHANGE
EOF

# 2. Git 설정
git config --global commit.template ~/.gitmessage

# 3. 사용
git commit
# 에디터에 템플릿 자동 로드!
```

**프로젝트별 템플릿:**
```bash
# 프로젝트 루트에 .gitmessage
git config commit.template .gitmessage

# 팀원 모두 같은 템플릿 사용
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 커밋 관련 명령어

#### **1️⃣ 기본 커밋**

```bash
# 일반 커밋
git commit -m "message"

# 여러 줄 메시지
git commit -m "Title" -m "Body line 1" -m "Body line 2"

# 에디터로 상세 메시지
git commit

# Staging 건너뛰고 커밋 (Modified만)
git commit -a -m "message"

# 빈 커밋 (테스트용)
git commit --allow-empty -m "Trigger CI"
```

---

#### **2️⃣ 커밋 수정**

```bash
# 마지막 커밋 메시지 수정
git commit --amend

# 메시지만 수정
git commit --amend -m "New message"

# 파일 추가하고 수정
git add file.txt
git commit --amend --no-edit

# Author 변경
git commit --amend --author="Name <email>"

# Date 변경
git commit --amend --date="2024-01-01 10:00:00"
```

---

#### **3️⃣ Fixup & Squash**

```bash
# Fixup 커밋 생성
git commit --fixup <commit>
git commit --fixup HEAD~3

# Squash 커밋 생성
git commit --squash <commit>

# Autosquash rebase
git rebase -i --autosquash <base>

# Fixup + Rebase 한 번에
git commit --fixup HEAD~2
git rebase -i --autosquash HEAD~5
```

---

#### **4️⃣ 커밋 정보 조회**

```bash
# 커밋 상세 정보
git show <commit>
git show HEAD
git show HEAD~3

# 커밋 메시지만
git log --format=%B -n 1 <commit>

# Author 정보
git log --format="%an <%ae>" -n 1

# Committer 정보  
git log --format="%cn <%ce>" -n 1

# 커밋 날짜
git log --format="%ai" -n 1  # Author date
git log --format="%ci" -n 1  # Commit date

# 통계
git show --stat <commit>
git show --shortstat <commit>
```

---

### 📊 명령어 비교표

#### **커밋 수정 명령어**

| 명령어 | 대상 | 히스토리 변경 | 위험도 |
|:------|:-----|:-------------|:------|
| `commit --amend` | 마지막 커밋 | 있음 | ⚠️ |
| `commit --fixup` | 특정 커밋 | 없음* | ✅ |
| `rebase -i` | 여러 커밋 | 있음 | ⚠️⚠️ |
| `revert` | 특정 커밋 | 없음 | ✅ |

*Rebase 전까지는 변경 없음

---

#### **커밋 메시지 형식 비교**

| 형식 | 예시 | 용도 | 적합도 |
|:-----|:-----|:-----|:------|
| **Conventional** | `feat(api): Add endpoint` | 대규모 프로젝트 | ⭐⭐⭐ |
| **Simple** | `Add user authentication` | 소규모 프로젝트 | ⭐⭐ |
| **Semantic** | `[FEATURE] User auth` | 레거시 프로젝트 | ⭐ |
| **Custom** | 팀 규칙 | 팀 정책 | ⭐⭐ |

---

### ⚡ 실행 결과 시각화

#### **Scenario: Amend의 내부 동작**

**초기 상태:**
```
    commit_B (HEAD -> main)
    Hash: abc123
    Message: "feat: Add feature"
         ↓
    commit_A
```

**파일 추가 후 amend:**
```bash
echo "new" > file.txt
git add file.txt
git commit --amend --no-edit
```

**결과:**
```
    commit_B' (HEAD -> main)
    Hash: xyz789 ← 다른 Hash!
    Message: "feat: Add feature" (같은 메시지)
    Tree: 다른 tree (파일 추가됨)
         ↓
    commit_A
    
    commit_B (abc123) ← 고아됨
```

**Reflog:**
```bash
git reflog
xyz789 HEAD@{0}: commit (amend): feat: Add feature
abc123 HEAD@{1}: commit: feat: Add feature
```

---

#### **Scenario: Fixup + Autosquash**

**초기 커밋들:**
```
commit_D feat(ui): Add button
commit_C feat(api): Add endpoint
commit_B fix: Bug fix
commit_A Initial
```

**Fixup 생성:**
```bash
# endpoint에 버그 발견!
git add fix.js
git commit --fixup HEAD~1  # commit_C

# 상태:
commit_E fixup! feat(api): Add endpoint
commit_D feat(ui): Add button
commit_C feat(api): Add endpoint
commit_B fix: Bug fix
commit_A Initial
```

**Autosquash rebase:**
```bash
git rebase -i --autosquash HEAD~4

# 에디터 자동 생성:
pick commit_C feat(api): Add endpoint
fixup commit_E fixup! feat(api): Add endpoint ← 자동 배치!
pick commit_D feat(ui): Add button
```

**최종 결과:**
```
commit_D' feat(ui): Add button
commit_C' feat(api): Add endpoint (C+E 합쳐짐)
commit_B fix: Bug fix
commit_A Initial
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 완벽한 커밋 메시지 작성

**목표:** Conventional Commits으로 프로페셔널한 커밋

#### **Problem: 커밋 메시지를 어떻게 써야 할지 모르겠다!**

```bash
# 현재 상태
git commit -m "fix"
git commit -m "update"
git commit -m "change code"

# 6개월 후...
git log --oneline
# abc123 fix
# def456 update
# ghi789 change code
# "뭐가 뭔지 모르겠다..." (절망)
```

#### **Solution: 체계적인 커밋 메시지**

```bash
# 1. 기능 추가
git commit -m "feat(auth): Implement JWT authentication

Add JWT-based authentication system for API security.

Changes:
- Add JWT token generation
- Add token verification middleware
- Add refresh token logic
- Add token expiration handling

Security considerations:
- Use RS256 algorithm
- 15min access token expiry
- 7day refresh token expiry

Closes #234"

# 2. 버그 수정
git commit -m "fix(payment): Resolve duplicate charge issue

Fix race condition causing duplicate payment charges
when users click submit button multiple times.

Root cause: Payment API called without debouncing
Solution: Add client-side debounce + server-side idempotency key

Impact: Affects ~0.1% of transactions
Tested: Manual testing + integration tests added

Fixes #456
References: docs/payment-flow.md"

# 3. Breaking Change
git commit -m "refactor(api)!: Change response format to REST standard

BREAKING CHANGE: API response structure changed from:
  { data: {...}, error: null }
to:
  { ...data }

Migration:
- Update client to access response directly
- Remove .data wrapper
- See migration guide: docs/api-v2-migration.md

Closes #789
Reviewed-by: Senior Dev Team"

# 6개월 후...
git log --grep="payment"
# fix(payment): Resolve duplicate charge issue ← 찾았다!

git log --grep="BREAKING"
# refactor(api)!: Change response format ← Breaking 변경 확인!
```

**베스트 프랙티스:**
```
1. Type 명확히
   - feat, fix, docs 등

2. Scope 구체적으로
   - (auth), (payment), (api)

3. Subject 설명적으로
   - "Add" > "Add feature"
   - "Implement JWT authentication" ✅

4. Body에 맥락 설명
   - 왜 변경했는지
   - 어떤 문제를 해결하는지

5. Footer에 참조
   - Closes #123
   - Fixes #456
   - BREAKING CHANGE
```

---

### 💼 Scenario 2: 커밋 실수 수정하기

**목표:** Amend로 안전하게 커밋 수정

#### **Problem: 커밋했는데 파일을 빼먹었다!**

```bash
# 1. 커밋 생성
git add file1.js file2.js
git commit -m "feat(api): Add user endpoint"

# 2. 실수 발견!
# file3.js를 빼먹었다...
# 새 커밋? 아니면 수정?
```

#### **Solution: Amend로 깔끔하게**

```bash
# 1. 빠진 파일 추가
git add file3.js

# 2. 마지막 커밋에 합치기
git commit --amend --no-edit

# 결과: 하나의 깔끔한 커밋!
git show HEAD --stat
# feat(api): Add user endpoint
#  file1.js | 10 ++++
#  file2.js | 15 +++++
#  file3.js | 8 +++  ← 추가됨!
```

**주의사항:**
```bash
# ⚠️ Push 전에만 사용!
git log --oneline
# abc123 (HEAD -> main) feat(api): Add endpoint
# def456 (origin/main) Previous commit

# Amend 전: main과 origin/main이 다름
git commit --amend

# Amend 후:
# xyz789 (HEAD -> main) feat(api): Add endpoint ← 새 Hash!
# def456 (origin/main) Previous commit

# Push하면?
git push
# ❌ Error: Updates were rejected

# 강제 push (팀원 확인 후!)
git push --force-with-lease
```

---

### 💼 Scenario 3: WIP 커밋 정리하기

**목표:** Fixup과 Autosquash로 커밋 히스토리 정리

#### **Problem: 개발 중 여러 WIP 커밋이 쌓였다!**

```bash
# 개발 과정
git commit -m "WIP: start login feature"
git commit -m "WIP: add validation"
git commit -m "WIP: fix bug"
git commit -m "WIP: add tests"

# 히스토리:
# abc123 WIP: add tests
# def456 WIP: fix bug
# ghi789 WIP: add validation
# jkl012 WIP: start login feature
# mno345 Previous commit

# PR 올리기 전에 정리하고 싶다!
```

#### **Solution: Fixup + Autosquash**

```bash
# 1. 각 수정사항을 fixup으로
# "fix bug"는 "start login"의 수정
git reset --soft HEAD~3  # 마지막 3개 커밋 취소 (파일은 유지)

# 2. 제대로 된 커밋으로 재작성
git add login.js validation.js
git commit -m "feat(auth): Implement login with validation

Add user login functionality with input validation
- Email/password validation
- Token generation
- Session management
- Unit tests included

Closes #123"

# 최종 히스토리:
# xyz789 feat(auth): Implement login with validation
# mno345 Previous commit

# ✅ 깔끔!
```

**대안: Interactive Rebase**
```bash
# 또는 interactive rebase로
git rebase -i HEAD~4

# 에디터:
pick jkl012 WIP: start login feature
squash ghi789 WIP: add validation
squash def456 WIP: fix bug  
squash abc123 WIP: add tests

# 저장 → 커밋 메시지 수정
# 결과: 4개 커밋이 1개로!
```

---

### 💼 Scenario 4: 커밋 메시지 템플릿 활용

**목표:** 팀 전체가 일관된 커밋 메시지 작성

#### **Problem: 팀원마다 커밋 스타일이 달라서 혼란스럽다!**

```bash
# 팀원 A
git commit -m "add feature"

# 팀원 B  
git commit -m "[FEAT] Add user management"

# 팀원 C
git commit -m "feat: implement authentication (#123)"

# 팀원 D
git commit -m "update"
```

#### **Solution: 프로젝트 공통 템플릿**

```bash
# 1. 프로젝트 루트에 .gitmessage 생성
cat > .gitmessage << 'EOF'
# <type>(<scope>): <subject> (Max 50 chars)
#
# <body> (Wrap at 72 chars)
#
# <footer>
#
# ──────────────────────────────────────────────────────
# Type:
#   feat:     새로운 기능
#   fix:      버그 수정
#   docs:     문서 변경
#   style:    코드 포맷팅
#   refactor: 리팩토링
#   test:     테스트 추가/수정
#   chore:    빌드/설정 변경
#
# Scope: auth, api, ui, db, etc.
#
# Subject:
#   - 명령문 사용 (Add, Fix, Update)
#   - 첫 글자 대문자
#   - 끝에 마침표 없음
#
# Body:
#   - 무엇을 왜 변경했는지
#   - 어떻게는 코드로 설명
#
# Footer:
#   Closes #123
#   Fixes #456
#   BREAKING CHANGE: 설명
# ──────────────────────────────────────────────────────
EOF

# 2. Git 설정
git config commit.template .gitmessage

# 3. 팀원들도 설정
echo "git config commit.template .gitmessage" >> README.md

# 4. 사용
git commit
# 에디터가 템플릿과 함께 열림!
```

**Pre-commit Hook으로 강제:**
```bash
# .git/hooks/commit-msg 생성
cat > .git/hooks/commit-msg << 'EOF'
#!/bin/bash

commit_msg=$(cat "$1")

# Conventional Commits 형식 검증
pattern="^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,50}"

if ! echo "$commit_msg" | grep -qE "$pattern"; then
    echo "❌ Error: Commit message doesn't follow convention!"
    echo ""
    echo "Format: <type>(<scope>): <subject>"
    echo "Example: feat(auth): Add login functionality"
    echo ""
    echo "Types: feat, fix, docs, style, refactor, test, chore"
    exit 1
fi

echo "✅ Commit message format valid"
EOF

chmod +x .git/hooks/commit-msg

# 잘못된 커밋 시도
git commit -m "update"
# ❌ Error: Commit message doesn't follow convention!

# 올바른 커밋
git commit -m "feat(api): Add user endpoint"
# ✅ Commit message format valid
```

---

### 💼 Scenario 5: 커밋 분할 전략

**목표:** 대규모 변경을 논리적 단위로 분할

#### **Problem: 50개 파일을 수정했는데 하나의 커밋으로?**

```bash
# 상황
git status
# Modified: 50 files
# 
# 변경사항:
# - User 모듈 리팩토링
# - API 버그 수정
# - 문서 업데이트
# - 테스트 추가

# 이걸 어떻게 커밋하지?
```

#### **Solution: 논리적 단위로 분할**

```bash
# 1. User 모듈 리팩토링
git add src/user/*.js
git commit -m "refactor(user): Extract validation logic

Separate user validation logic into dedicated validators
for better reusability and testing.

Changes:
- Extract email validator
- Extract password validator  
- Extract profile validator
- Update user service to use validators

No functional changes, pure refactoring."

# 2. API 버그 수정
git add src/api/userController.js src/api/authController.js
git commit -m "fix(api): Resolve null pointer in user lookup

Fix crash when looking up non-existent users.

Root cause: Missing null check after database query
Solution: Add explicit null check with proper error response

Fixes #789"

# 3. 문서 업데이트
git add docs/*.md
git commit -m "docs: Update API documentation

Update docs to reflect recent API changes:
- Add new endpoints
- Update response formats
- Add error code reference

Related to #789"

# 4. 테스트 추가
git add tests/**/*.test.js
git commit -m "test: Add integration tests for user API

Add comprehensive integration tests for user endpoints:
- Create user
- Update user
- Delete user
- Error scenarios

Coverage increased from 65% to 85%."

# 최종: 4개의 깔끔한 커밋!
git log --oneline
# abc123 test: Add integration tests
# def456 docs: Update API documentation
# ghi789 fix(api): Resolve null pointer
# jkl012 refactor(user): Extract validation
```

**분할 원칙:**
```
1. 기능별 분리
   - 리팩토링 vs 버그 수정 vs 새 기능

2. 파일 그룹별
   - 같은 모듈 파일끼리

3. 테스트 분리
   - 기능 커밋 + 테스트 커밋 (선택적)

4. 문서 분리
   - 코드 변경 + 문서 업데이트 (선택적)
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: 커밋 메시지가 너무 짧음**

```bash
# 나쁜 예
git commit -m "fix"
git commit -m "update"
git commit -m "change"

# 문제점:
# 6개월 후 "뭘 고쳤더라?"
```

**올바른 방법:**
```bash
# 최소한 이 정도는
git commit -m "fix(auth): Resolve login timeout issue"

# 더 좋은 것은
git commit -m "fix(auth): Resolve login timeout issue

Fix race condition causing premature session timeouts
when users have multiple active tabs.

Fixes #123"
```

---

#### ❌ **실수 2: Push 후 amend**

```bash
# 위험한 패턴
git push
git commit --amend  # ⚠️ 히스토리 변경!
git push            # ❌ Rejected!

# 강제 push?
git push --force    # ⚠️⚠️ 팀원 코드 날릴 수 있음!
```

**올바른 방법:**
```bash
# Push 전에만 amend
git commit --amend

# 확인
git log --oneline --all
# abc123 (HEAD -> main) My commit
# def456 (origin/main) Previous

# main이 origin/main보다 앞서있나?
# YES → 안전하게 push
# NO → amend하지 말 것!

# Push 후 수정이 필요하면
git revert <commit>  # 새 커밋으로 되돌리기
```

---

#### ❌ **실수 3: Fixup 없이 수동 squash**

```bash
# 비효율적인 방법
git rebase -i HEAD~10

# 에디터에서 수동으로:
pick abc123 feat: Add feature
pick def456 fix bug in feature  ← 수동으로 squash로 변경
pick ghi789 fix another bug     ← 수동으로 squash로 변경
...
```

**올바른 방법:**
```bash
# Fixup 활용
git commit --fixup abc123
git commit --fixup abc123

# Autosquash
git rebase -i --autosquash HEAD~10

# 자동으로 정리됨!
pick abc123 feat: Add feature
fixup def456 fixup! feat: Add feature  ← 자동 배치!
fixup ghi789 fixup! feat: Add feature  ← 자동 배치!
```

---

#### ❌ **실수 4: Breaking Change 표시 안 함**

```bash
# 위험한 커밋
git commit -m "refactor(api): Change response format"

# 6개월 후...
# "왜 갑자기 API가 안 되지?"
# "아, 이 커밋에서 format 바뀌었네..."
# "하지만 Breaking Change 표시가 없어서 못 찾았네..."
```

**올바른 방법:**
```bash
# Breaking Change 명시!
git commit -m "refactor(api)!: Change response format

BREAKING CHANGE: Response format changed from:
  { data: {...}, error: null }
to:
  { ...data }

Migration: Update client to remove .data wrapper
See: docs/migration-v2.md"

# 나중에 찾기 쉬움
git log --grep="BREAKING"
# refactor(api)!: Change response format ← 찾았다!
```

---

### 🎯 실습 미션

#### **미션 1: 완벽한 커밋 메시지** 📝

**목표:** Conventional Commits으로 커밋 작성

```bash
# 1. 저장소 생성
git init perfect-commits && cd perfect-commits

# 2. 파일 생성
cat > login.js << 'EOF'
function login(email, password) {
  // TODO: implement
}
EOF

# 3. 미션:
# 다음 시나리오대로 커밋 3개 생성
# - feat(auth): Implement login functionality
# - fix(auth): Resolve validation error
# - docs: Add login API documentation

# 각 커밋 메시지는:
# - Type, Scope 포함
# - Subject 명확
# - Body에 상세 설명
# - Footer에 이슈 참조 (가상)
```

<details>
<summary>정답 보기</summary>

```bash
# 커밋 1: 기능 추가
git add login.js
git commit -m "feat(auth): Implement login functionality

Add user login with email/password authentication.

Features:
- Email validation
- Password hashing with bcrypt
- JWT token generation
- Session management

Closes #101"

# 커밋 2: 버그 수정
echo "// Fixed validation" >> login.js
git add login.js
git commit -m "fix(auth): Resolve email validation error

Fix regex pattern not accepting plus signs in email addresses.

Root cause: Regex was too restrictive
Solution: Update to RFC 5322 compliant pattern

Fixes #102
Reported-by: user@example.com"

# 커밋 3: 문서
cat > API.md << 'EOF'
# Login API
POST /api/login
EOF

git add API.md
git commit -m "docs: Add login API documentation

Document login endpoint with request/response examples.

Includes:
- Endpoint URL
- Request body format
- Response codes
- Error handling

Related: #101"

# 확인
git log --oneline
# abc123 docs: Add login API documentation
# def456 fix(auth): Resolve email validation error
# ghi789 feat(auth): Implement login functionality

# ✅ 완벽한 커밋 히스토리!
```

</details>

---

#### **미션 2: Amend 마스터** 🔧

**목표:** Amend로 커밋 완벽하게 수정

```bash
# 1. 초기 커밋
echo "V1" > file.txt
git add file.txt
git commit -m "feat: Add feature"

# 2. 미션:
# - file.txt에 "V2" 추가
# - file2.txt 생성
# - 마지막 커밋에 합치기 (amend)
# - 커밋 메시지도 더 자세하게 수정

# 힌트:
# git add file.txt file2.txt
# git commit --amend
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 파일 수정
echo "V2" >> file.txt
echo "New file" > file2.txt

# 2. Staging
git add file.txt file2.txt

# 3. Amend
git commit --amend

# 에디터에서:
# feat(core): Add feature with configuration
#
# Add new feature with external config file support
# - Add feature implementation in file.txt
# - Add config file file2.txt
# - Support runtime config updates

# 4. 확인
git show HEAD --stat
# feat(core): Add feature with configuration
#  file.txt  | 2 ++
#  file2.txt | 1 +

# ✅ 완벽하게 합쳐짐!
```

</details>

---

#### **미션 3: Fixup 워크플로우** 🎯

**목표:** Fixup으로 커밋 정리

```bash
# 1. 초기 커밋들
for i in {1..3}; do
  echo "Feature $i" > file$i.txt
  git add file$i.txt
  git commit -m "feat: Add feature $i"
done

# 2. 미션:
# - Feature 1에 버그 발견! 수정 후 fixup 커밋
# - Feature 2에 개선사항! 수정 후 fixup 커밋
# - Interactive rebase로 정리

# 힌트:
# git commit --fixup HEAD~2  (Feature 1)
# git rebase -i --autosquash HEAD~5
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Feature 1 수정
echo "Bug fix" >> file1.txt
git add file1.txt
git commit --fixup HEAD~2

# 2. Feature 2 개선
echo "Improvement" >> file2.txt
git add file2.txt
git commit --fixup HEAD~2

# 3. 상태 확인
git log --oneline
# xyz789 fixup! feat: Add feature 2
# abc123 fixup! feat: Add feature 1
# def456 feat: Add feature 3
# ghi789 feat: Add feature 2
# jkl012 feat: Add feature 1

# 4. Autosquash rebase
git rebase -i --autosquash HEAD~5

# 에디터 (자동 생성):
# pick jkl012 feat: Add feature 1
# fixup abc123 fixup! feat: Add feature 1
# pick ghi789 feat: Add feature 2
# fixup xyz789 fixup! feat: Add feature 2
# pick def456 feat: Add feature 3

# 5. 저장 → 완료!

# 6. 최종 확인
git log --oneline
# mno345 feat: Add feature 3
# pqr678 feat: Add feature 2 (개선 포함!)
# stu901 feat: Add feature 1 (버그 수정 포함!)

# ✅ 깔끔하게 정리됨!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 기본 커밋 ===
git commit -m "message"              # 간단한 커밋
git commit                           # 에디터로 상세 작성
git commit -a -m "message"           # add + commit

# === 커밋 수정 ===
git commit --amend                   # 마지막 커밋 수정
git commit --amend --no-edit         # 메시지 유지하고 수정
git commit --amend -m "new msg"      # 메시지만 변경

# === Fixup & Squash ===
git commit --fixup <commit>          # Fixup 커밋
git commit --squash <commit>         # Squash 커밋
git rebase -i --autosquash <base>    # 자동 정리

# === 커밋 정보 ===
git show <commit>                    # 상세 정보
git show --stat <commit>             # 통계
git log --format=%B -n 1             # 메시지만

# === 커밋 검색 ===
git log --grep="pattern"             # 메시지 검색
git log --author="name"              # 작성자 검색
git log --since="2 weeks ago"        # 기간 검색
```

---

### 🎨 Conventional Commits 빠른 참조

```
형식:
<type>(<scope>): <subject>

<body>

<footer>

Type:
feat:     새 기능
fix:      버그 수정
docs:     문서
style:    포맷
refactor: 리팩토링
test:     테스트
chore:    기타

Examples:
feat(api): Add user endpoint
fix(db): Resolve connection leak
docs: Update README
style(lint): Fix ESLint warnings
refactor(auth): Extract validation
test: Add unit tests
chore(deps): Update dependencies
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [1.1 Git 객체 모델](./01-object-model.md)
- [1.2 3가지 영역](./02-three-areas.md)
- [1.3 HEAD와 참조](./03-head-and-refs.md)

**다음 학습:**
- [2.1 브랜치 기본](../02-branching/01-branch-basics.md)
- [4.2 Interactive Rebase](../04-rebase/02-interactive-rebase.md)

**실전 응용:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)
- [5.1 Reset 모드](../05-recovery/01-reset-modes.md)

---

### 💡 추가 팁

#### **유용한 Alias**

```bash
# .gitconfig에 추가
[alias]
  # 커밋 템플릿
  cm = commit
  
  # Fixup 간편화
  fixup = commit --fixup
  squash = commit --squash
  
  # Autosquash rebase
  rb = rebase -i --autosquash
  
  # 커밋 정보
  show-msg = log --format=%B -n 1
  who = log --format="%an <%ae>" -n 1
  
  # 마지막 커밋 수정
  amend = commit --amend --no-edit
  reamend = commit --amend
```

---

#### **Commit Hook 예제**

```bash
# .git/hooks/commit-msg
#!/bin/bash

# Conventional Commits 검증
msg=$(cat "$1")
pattern="^(feat|fix|docs|style|refactor|test|chore)(\(.+\))?: .{1,}"

if ! echo "$msg" | grep -qE "$pattern"; then
    echo "❌ Invalid commit message format!"
    echo "Use: <type>(<scope>): <subject>"
    exit 1
fi

# 제목 길이 검증
subject=$(echo "$msg" | head -n 1)
if [ ${#subject} -gt 72 ]; then
    echo "❌ Subject too long (${#subject} > 72)"
    exit 1
fi

echo "✅ Commit message valid"
```

---

<div align="center">

**🎉 축하합니다! Git 기초 4부작 완성!**

이제 커밋의 모든 것을 이해했습니다!

[⬅️ 이전: HEAD와 참조](./03-head-and-refs.md) | [README로](../README.md) | [다음: 브랜치 기본 ➡️](../02-branching/01-branch-basics.md)

---

## 🏆 Git 기초 완전 정복!

**완성한 4부작:**
1. ✅ 객체 모델 - Git이 "무엇"을 저장하는가
2. ✅ 3가지 영역 - Git이 "어디"에 저장하는가
3. ✅ HEAD와 참조 - Git이 "어떻게" 찾는가
4. ✅ 커밋 해부 - Git이 "왜" 그렇게 동작하는가

**이제 브랜치 전략으로!** 🚀

</div>
