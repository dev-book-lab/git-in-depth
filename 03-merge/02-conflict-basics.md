# 3.2 충돌의 원리 (Conflict Basics)

**난이도: ⭐⭐**

> *"충돌은 무서운 게 아닙니다. 충돌은 Git이 '도와주세요!'라고 하는 것입니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 충돌을 이해해야 하나?

**실제 개발 시나리오:**
```
상황: 팀 프로젝트 Merge

git merge feature
# CONFLICT (content): Merge conflict in app.js
# Automatic merge failed; fix conflicts and commit

개발자: "충돌이다! 😱"
주니어: "뭘 어떻게 해야 하죠?"
시니어: "충돌 마커가 뭐예요?"
팀장: "작업 날아가는 거 아니죠...?"

공통 문제:
→ 충돌이 무엇인지 몰라서 두려움
→ 충돌 마커를 읽을 줄 몰라서 혼란
→ 잘못 해결해서 코드 손실
```

**충돌을 이해하면:**
```bash
충돌 발견:
<<<<<<< HEAD
const name = "main version";
=======
const name = "feature version";
>>>>>>> feature

이해:
- HEAD: 현재 브랜치 (main)
- feature: merge할 브랜치
- ======= : 경계선
→ 둘 다 같은 줄 수정!

해결:
const name = "final version";

✅ 자신감
✅ 빠른 해결
✅ 코드 손실 없음
```

> 💡 **핵심:** 충돌은 Git이 자동으로 결정할 수 없을 때 
> 개발자에게 도움을 요청하는 것입니다!

---

### 📌 충돌이란?

#### **정의**

```
충돌 (Conflict):
두 브랜치가 같은 부분을 다르게 수정했을 때
Git이 자동으로 merge할 수 없는 상황

발생 조건:
1. 같은 파일
2. 같은 줄 (또는 가까운 줄)
3. 다른 내용으로 수정

Git의 반응:
"어떤 버전이 맞는지 모르겠어요! 
 개발자님이 결정해주세요!"
```

---

#### **충돌 vs 자동 Merge**

```
자동 Merge 가능:
main:   줄1 수정
feature: 줄10 수정
→ 겹치지 않음 → 자동 merge ✅

충돌 발생:
main:   줄3 = "A"
feature: 줄3 = "B"
→ 같은 줄 수정 → 충돌! ❌

인접 줄도 충돌 가능:
main:   줄3 수정
feature: 줄4 수정
→ 너무 가까움 → 충돌 가능 ⚠️
```

---

### 🔍 충돌 마커 해부

#### **충돌 마커 구조**

```
<<<<<<< HEAD
현재 브랜치의 내용
=======
merge할 브랜치의 내용
>>>>>>> branch-name

3개 부분:
1. <<<<<<< HEAD : 시작 (현재)
2. ======= : 구분선
3. >>>>>>> name : 끝 (다른 브랜치)
```

---

#### **실제 예시**

```javascript
// 충돌 발생한 파일 (app.js)

function greet() {
<<<<<<< HEAD
  return "Hello from main!";
=======
  return "Hello from feature!";
>>>>>>> feature/greeting
}

해석:
- HEAD (main): "Hello from main!"
- feature: "Hello from feature!"
- 개발자 선택 필요!
```

---

#### **복잡한 충돌**

```javascript
<<<<<<< HEAD
function calculate(a, b) {
  const result = a + b;
  console.log("Sum:", result);
  return result;
}
=======
function calculate(x, y) {
  return x + y;
}
>>>>>>> feature/refactor

해석:
- HEAD: 로그 추가, 상세한 버전
- feature: 간결한 버전
- 선택 또는 결합!

해결 예시:
function calculate(a, b) {
  const result = a + b;
  return result;
}
```

---

### 🎨 3-way Merge와 충돌

#### **3-way Merge 원리**

```
비교 대상 3개:
1. Base (공통 조상)
2. Ours (현재 브랜치)
3. Theirs (merge할 브랜치)

        Base
         ↓
    "original"
       ↙    ↘
  Ours      Theirs
   ↓          ↓
"changed1"  "changed2"

비교:
Base → Ours: "original" → "changed1" (변경)
Base → Theirs: "original" → "changed2" (변경)

결론:
둘 다 변경! → 충돌!
```

---

#### **자동 Merge 가능한 경우**

```
Case 1: 한쪽만 변경
Base:   "original"
Ours:   "changed"
Theirs: "original"

결과: "changed" ✅ (Ours 채택)

Case 2: 같은 내용으로 변경
Base:   "original"
Ours:   "same"
Theirs: "same"

결과: "same" ✅ (동일)

Case 3: 다른 줄 변경
Base:   줄1="A", 줄2="B"
Ours:   줄1="X", 줄2="B"
Theirs: 줄1="A", 줄2="Y"

결과: 줄1="X", 줄2="Y" ✅
```

---

#### **충돌 발생 경우**

```
Case 1: 같은 줄, 다른 내용
Base:   줄1="A"
Ours:   줄1="X"
Theirs: 줄1="Y"

결과: 충돌! ❌

Case 2: 인접 줄 (컨텍스트 부족)
Base:   줄1="A", 줄2="B", 줄3="C"
Ours:   줄1="A", 줄2="X", 줄3="C"
Theirs: 줄1="A", 줄2="B", 줄3="Y"

결과: 충돌 가능 ⚠️

Case 3: 삭제 vs 수정
Base:   줄1="A"
Ours:   (줄1 삭제)
Theirs: 줄1="X"

결과: 충돌! ❌
```

---

### 📊 충돌 발생 시 Git 상태

#### **Working Directory**

```bash
git status

# On branch main
# You have unmerged paths.
#   (fix conflicts and run "git commit")
#   (use "git merge --abort" to abort the merge)
# 
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#         both modified:   app.js
#         both modified:   config.js
# 
# no changes added to commit

상태:
- Merge 진행 중
- 2개 파일 충돌
- Commit 불가 (해결 필요)
```

---

#### **Index (Staging Area)**

```bash
git ls-files -s

# 100644 abc123... 1    app.js  (Base)
# 100644 def456... 2    app.js  (Ours)
# 100644 ghi789... 3    app.js  (Theirs)

Stage 번호:
0: 정상 (충돌 없음)
1: Base (공통 조상)
2: Ours (현재 브랜치)
3: Theirs (merge할 브랜치)

충돌 파일:
3개 버전 모두 저장!
```

---

### 🔬 Git Diff와 충돌

#### **git diff 출력**

```bash
git diff

# diff --cc app.js
# index abc123,def456..0000000
# --- a/app.js
# +++ b/app.js
# @@@ -1,3 -1,3 +1,7 @@@
#   function greet() {
# ++<<<<<<< HEAD
# +   return "Hello from main!";
# ++=======
# +   return "Hello from feature!";
# ++>>>>>>> feature
#   }

기호:
++: 충돌 마커 (추가됨)
-: Base
+: 현재 변경
```

---

#### **git diff --ours / --theirs**

```bash
# Ours (현재 브랜치) 기준
git diff --ours app.js

# Theirs (merge할 브랜치) 기준
git diff --theirs app.js

# Base 기준
git diff --base app.js

용도:
- 각 버전 비교
- 변경 내용 파악
- 결정 도움
```

---

### 🎯 충돌 종류

#### **1️⃣ Content Conflict (내용 충돌)**

```
가장 흔한 충돌
같은 파일의 같은 부분을 다르게 수정

예시:
<<<<<<< HEAD
const API_URL = "http://localhost:3000";
=======
const API_URL = "http://api.example.com";
>>>>>>> feature

해결: 하나 선택 또는 새로운 값
```

---

#### **2️⃣ Tree Conflict (트리 충돌)**

```
파일/디렉토리 구조 충돌

예시 1: 파일 vs 디렉토리
Ours:   utils.js (파일)
Theirs: utils/ (디렉토리)

예시 2: 삭제 vs 수정
Ours:   old.js (삭제)
Theirs: old.js (수정)

예시 3: 이름 변경
Ours:   old.js → new1.js
Theirs: old.js → new2.js
```

---

#### **3️⃣ Rename Conflict (이름 변경 충돌)**

```
같은 파일을 다른 이름으로 변경

Base:   config.js
Ours:   config.prod.js
Theirs: config.production.js

Git 감지:
# CONFLICT (rename/rename):
# Rename config.js -> config.prod.js in HEAD
# Rename config.js -> config.production.js in feature
```

---

#### **4️⃣ Add/Add Conflict**

```
없던 파일을 양쪽에서 추가

Ours:   새로운 test.js 생성
Theirs: 새로운 test.js 생성 (다른 내용)

# CONFLICT (add/add): Merge conflict in test.js

해결: 내용 비교 후 통합
```

---

### 🛠️ 충돌 해결 방법론

#### **3단계 해결 프로세스**

```
1. 충돌 파악
   - git status
   - 충돌 파일 확인
   - 충돌 이유 이해

2. 충돌 해결
   - 파일 편집
   - 마커 제거
   - 올바른 코드 작성

3. 해결 완료
   - git add
   - git commit
   - 확인
```

---

#### **해결 전략**

```
전략 1: Ours 선택
- 현재 브랜치 버전 유지
- 다른 브랜치 버전 무시

전략 2: Theirs 선택
- 다른 브랜치 버전 채택
- 현재 브랜치 버전 무시

전략 3: Manual (수동)
- 두 버전 검토
- 최선의 결합
- 새로운 코드 작성

전략 4: Both (둘 다)
- 두 변경 모두 포함
- 순서 결정
- 통합
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 충돌 확인 명령어

#### **1️⃣ 충돌 파일 확인**

```bash
# 전체 상태
git status

# 충돌 파일만
git diff --name-only --diff-filter=U

# 상세 정보
git ls-files -u
```

---

#### **2️⃣ 충돌 내용 확인**

```bash
# 기본 diff
git diff

# 충돌 파일만
git diff --diff-filter=U

# 통계
git diff --stat

# Ours 기준
git diff --ours

# Theirs 기준
git diff --theirs

# Base 기준
git diff --base
```

---

#### **3️⃣ 각 버전 확인**

```bash
# Ours (현재 브랜치) 보기
git show :2:app.js

# Theirs (merge할 브랜치) 보기
git show :3:app.js

# Base (공통 조상) 보기
git show :1:app.js

# 파일로 저장
git show :2:app.js > app.js.ours
git show :3:app.js > app.js.theirs
git show :1:app.js > app.js.base
```

---

### ⚡ 충돌 해결 명령어

#### **1️⃣ 수동 해결**

```bash
# 1. 파일 편집
vim app.js
# ... 충돌 마커 제거 및 수정 ...

# 2. 해결 표시
git add app.js

# 3. 상태 확인
git status
# On branch main
# All conflicts fixed but you are still merging.
#   (use "git commit" to conclude merge)

# 4. Commit
git commit
# 자동으로 merge 커밋 메시지 생성

# 또는 커스텀 메시지
git commit -m "Merge: Resolved conflicts in app.js"
```

---

#### **2️⃣ Ours 선택 (전체 파일)**

```bash
# Ours 버전으로 해결
git checkout --ours app.js
git add app.js

# 모든 충돌 파일에 Ours
git checkout --ours .
git add .
```

---

#### **3️⃣ Theirs 선택 (전체 파일)**

```bash
# Theirs 버전으로 해결
git checkout --theirs app.js
git add app.js

# 모든 충돌 파일에 Theirs
git checkout --theirs .
git add .
```

---

#### **4️⃣ 부분 선택 (-X 옵션)**

```bash
# Merge 재시도 (Ours 우선)
git merge --abort
git merge -X ours feature

# Merge 재시도 (Theirs 우선)
git merge --abort
git merge -X theirs feature

# 차이:
# --ours/--theirs: 파일 단위
# -X ours/theirs: 충돌 내 선택
```

---

### 🔍 충돌 분석 도구

#### **1️⃣ git diff 옵션**

```bash
# 충돌 마커 포함한 diff
git diff

# 3-way diff
git diff --diff-filter=U --cc

# 간단한 diff
git diff --diff-filter=U --simplified

# 단어 단위 diff
git diff --word-diff

# 색상 강조
git diff --color-words
```

---

#### **2️⃣ git log로 추적**

```bash
# Merge base 확인
git merge-base HEAD feature

# Base 이후 변경사항
git log --oneline $(git merge-base HEAD feature)..HEAD
git log --oneline $(git merge-base HEAD feature)..feature

# 파일별 히스토리
git log --follow -- app.js
```

---

#### **3️⃣ git blame으로 원인 파악**

```bash
# 누가 언제 수정했나
git blame app.js

# 특정 줄만
git blame -L 10,20 app.js

# 시간 범위
git blame --since=2.weeks app.js
```

---

### 📋 충돌 해결 워크플로우

#### **완전한 프로세스**

```bash
# 1. Merge 시도
git merge feature
# CONFLICT!

# 2. 충돌 확인
git status
# both modified: app.js
# both modified: config.js

# 3. 각 파일 분석
git diff app.js

# 4. 버전 비교
git show :2:app.js > app.ours
git show :3:app.js > app.theirs
git show :1:app.js > app.base

# 5. 수동 해결
vim app.js
# ... 수정 ...

# 6. 해결 표시
git add app.js

# 7. 다음 파일
vim config.js
# ... 수정 ...
git add config.js

# 8. 모두 해결 확인
git status
# All conflicts fixed

# 9. Commit
git commit

# 10. 확인
git log --oneline --graph -5
```

---

### ⚠️ 충돌 취소

#### **1️⃣ Merge 중단**

```bash
# Merge 완전 취소
git merge --abort

# 효과:
# - Merge 이전 상태로 복귀
# - 충돌 파일 원상복구
# - HEAD 이동
```

---

#### **2️⃣ 특정 파일만 취소**

```bash
# 파일 원상복구
git checkout HEAD app.js

# 또는
git restore app.js

# 다시 해결
vim app.js
git add app.js
```

---

#### **3️⃣ 이미 Commit한 경우**

```bash
# Merge 커밋 되돌리기
git reset --hard HEAD~1

# 또는 Revert
git revert -m 1 HEAD
```

---

### 📊 명령어 비교표

#### **충돌 해결 방법**

| 방법 | 명령어 | 범위 | 자동화 |
|:-----|:------|:-----|:------|
| **수동** | 편집 + add | 부분 선택 | 불가 |
| **Ours** | `--ours` | 파일 전체 | 가능 |
| **Theirs** | `--theirs` | 파일 전체 | 가능 |
| **-X ours** | `-X ours` | 충돌 부분 | 가능 |
| **-X theirs** | `-X theirs` | 충돌 부분 | 가능 |

---

#### **버전 확인**

| 명령어 | 대상 | 용도 |
|:------|:-----|:-----|
| `git show :1:file` | Base | 공통 조상 |
| `git show :2:file` | Ours | 현재 브랜치 |
| `git show :3:file` | Theirs | Merge 브랜치 |
| `git diff --ours` | Ours diff | Ours 변경사항 |
| `git diff --theirs` | Theirs diff | Theirs 변경사항 |

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 첫 충돌 해결

**목표:** 기본 충돌 완벽 해결

#### **Problem: 처음으로 충돌이 발생했다!**

```bash
git merge feature
# Auto-merging app.js
# CONFLICT (content): Merge conflict in app.js
# Automatic merge failed; fix conflicts

개발자: "😱 뭐지?"
```

#### **Solution: 단계별 해결**

```bash
# ===== Step 1: 상황 파악 =====

git status
# On branch main
# You have unmerged paths.
#   (fix conflicts and run "git commit")
# 
# Unmerged paths:
#   (use "git add <file>..." to mark resolution)
#         both modified:   app.js

# 이해:
# - Merge 진행 중
# - app.js 충돌
# - 해결 후 commit 필요

# ===== Step 2: 충돌 내용 확인 =====

cat app.js
# function greet(name) {
# <<<<<<< HEAD
#   return `Hello, ${name}! Welcome to main.`;
# =======
#   return `Hi, ${name}! Welcome to feature.`;
# >>>>>>> feature
# }

# 이해:
# HEAD (main): "Hello... main"
# feature: "Hi... feature"
# 인사말이 다름!

# ===== Step 3: 컨텍스트 파악 =====

# Base 버전 확인
git show :1:app.js
# function greet(name) {
#   return `Hello, ${name}!`;
# }

# 변경 이력:
# Base → main: "Welcome to main" 추가
# Base → feature: "Hi" + "Welcome to feature"

# ===== Step 4: 결정 =====

# 옵션 1: Main 선택
git checkout --ours app.js
git add app.js

# 옵션 2: Feature 선택
git checkout --theirs app.js
git add app.js

# 옵션 3: 수동 결합 (최선!)
vim app.js

# 수정:
function greet(name) {
  return `Hi, ${name}! Welcome.`;
}
# 둘 다 반영!

# 마커 제거 확인
grep -n "<<<\|===\|>>>" app.js
# (출력 없음 = 마커 제거됨)

# ===== Step 5: 해결 완료 =====

git add app.js

git status
# On branch main
# All conflicts fixed but you are still merging.
#   (use "git commit" to conclude merge)
# 
# Changes to be committed:
#         modified:   app.js

# ===== Step 6: Commit =====

git commit
# 에디터 열림:
# Merge branch 'feature'
# 
# # Conflicts:
# #       app.js

# 메시지 수정 (선택):
# Merge branch 'feature'
# 
# Resolved conflicts:
# - Merged greeting messages
# - Used "Hi" with generic "Welcome"

# 저장 후 종료

# ===== Step 7: 확인 =====

git log --oneline --graph -3
# *   abc123 Merge branch 'feature'
# |\  
# | * def456 Update greeting
# * | ghi789 Update greeting
# |/  

cat app.js
# function greet(name) {
#   return `Hi, ${name}! Welcome.`;
# }

# ✅ 완벽하게 해결!

# ===== 배운 점 =====
# 1. 충돌은 무섭지 않음
# 2. 마커를 읽을 줄 알면 쉬움
# 3. 항상 Base 확인
# 4. 수동 결합이 최선일 때가 많음
```

---

### 💼 Scenario 2: 다중 파일 충돌

**목표:** 여러 파일 효율적 해결

#### **Problem: 10개 파일에 충돌!**

```bash
git merge feature
# CONFLICT in file1.js
# CONFLICT in file2.js
# ... (10개 파일)
```

#### **Solution: 전략적 해결**

```bash
# ===== Step 1: 충돌 분류 =====

git status
# both modified:   config.js
# both modified:   api.js
# both modified:   utils.js
# both modified:   styles.css
# both modified:   test1.js
# both modified:   test2.js
# ... (10개)

# 분류:
# 1. 간단한 것 (Ours/Theirs)
# 2. 복잡한 것 (수동)

# ===== Step 2: 간단한 것 먼저 =====

# config.js - 설정 파일, Theirs 선택
git diff config.js
# Theirs가 최신 설정
git checkout --theirs config.js
git add config.js

# styles.css - 스타일, Theirs 선택
git checkout --theirs styles.css
git add styles.css

# test1.js, test2.js - 테스트, Theirs 선택
git checkout --theirs test1.js test2.js
git add test1.js test2.js

# 진행상황
git status
# both modified:   api.js
# both modified:   utils.js
# ... (6개 남음)

# ===== Step 3: 복잡한 것 하나씩 =====

# api.js - 중요한 로직
git diff api.js

# 3버전 비교
git show :1:api.js > api.base
git show :2:api.js > api.ours
git show :3:api.js > api.theirs

# 비교 도구 사용
code --diff api.ours api.theirs

# 수동 해결
vim api.js
# ... 신중하게 수정 ...
git add api.js

# utils.js - 유틸 함수
vim utils.js
# ... 수정 ...
git add utils.js

# 나머지도 동일...

# ===== Step 4: 중간 저장 (선택) =====

# 일부만 해결하고 쉬고 싶다면
# (이미 add된 것은 해결 완료 상태 유지)

# 현재 상태 확인
git status
# Changes to be committed: (5개 해결)
# Unmerged paths: (5개 남음)

# 작업 중단 가능
# 나중에 이어서 해결

# ===== Step 5: 모두 해결 =====

git status
# All conflicts fixed

# ===== Step 6: Commit =====

git commit -m "Merge feature: Resolved 10 conflicts

Resolved files:
- config.js: Used feature settings
- api.js: Merged both implementations
- utils.js: Combined utilities
- styles.css: Applied new styles
- tests: Updated test suites
- ... (나머지)"

# ✅ 10개 충돌 해결!

# ===== 팁: 배치 작업 =====

# 같은 전략으로 여러 파일
for file in *.css; do
  git checkout --theirs $file
  git add $file
done

# 또는
git checkout --theirs '*.css'
git add '*.css'
```

**전략적 접근:**
```
1. 충돌 분류:
   - 간단 (자동)
   - 복잡 (수동)

2. 우선순위:
   - 쉬운 것 먼저
   - 중요한 것 나중

3. 도구 활용:
   - Batch 명령
   - Diff 도구
   - 비교 스크립트

4. 중간 저장:
   - 일부 해결 후 휴식
   - 다음날 이어서
```

---

### 💼 Scenario 3: 삭제 vs 수정 충돌

**목표:** 트리 충돌 해결

#### **Problem: 한쪽은 삭제, 한쪽은 수정!**

```bash
git merge feature
# CONFLICT (modify/delete): old-api.js deleted in main
# and modified in feature.
```

#### **Solution: 컨텍스트 기반 결정**

```bash
# ===== 상황 파악 =====

git status
# deleted by us:   old-api.js

# 이해:
# main: old-api.js 삭제
# feature: old-api.js 수정

# ===== 이유 조사 =====

# Main의 삭제 커밋
git log --oneline main -- old-api.js
# abc123 refactor: Remove old API, use new-api.js

# Feature의 수정 커밋
git log --oneline feature -- old-api.js
# def456 feat: Add error handling to old API

# 컨텍스트:
# - Main: 새 API로 마이그레이션
# - Feature: 기존 API 개선

# ===== 옵션 1: 삭제 유지 (Ours) =====

# 새 API를 사용하기로 결정
git rm old-api.js
git add old-api.js

# Feature의 변경사항을 새 API에 반영
vim new-api.js
# (error handling 로직 추가)
git add new-api.js

# ===== 옵션 2: 수정 유지 (Theirs) =====

# 파일 복구
git checkout feature -- old-api.js
git add old-api.js

# 나중에 마이그레이션 계획

# ===== 올바른 결정 =====

# 컨텍스트 고려:
# 1. 왜 삭제했나? → 마이그레이션
# 2. 왜 수정했나? → 기능 개선
# 3. 어느 것이 미래? → 새 API

# 결정: 삭제 + 기능 이식
git rm old-api.js

vim new-api.js
# Feature의 개선사항 추가
git add new-api.js

git commit -m "Merge: Removed old API, migrated improvements

- Removed old-api.js (deprecated)
- Migrated error handling to new-api.js
- Maintained feature improvements

Related: #123"

# ✅ 최선의 결정!
```

---

### 💼 Scenario 4: 이름 변경 충돌

**목표:** Rename 충돌 해결

#### **Problem: 같은 파일을 다른 이름으로!**

```bash
git merge feature
# CONFLICT (rename/rename):
# Rename api.js -> api-v1.js in HEAD
# Rename api.js -> api.prod.js in feature
```

#### **Solution: 의도 파악 후 통합**

```bash
# ===== 상황 파악 =====

git status
# renamed:    api.js -> api-v1.js
# renamed:    api.js -> api.prod.js

# ===== 의도 조사 =====

# Main: api.js → api-v1.js
git log --oneline -1 main
# abc123 refactor: Version API files

# Feature: api.js → api.prod.js
git log --oneline -1 feature
# def456 chore: Separate prod config

# 의도:
# - Main: 버전별 구분
# - Feature: 환경별 구분

# ===== 옵션 1: 하나 선택 =====

# Main 선택
git add api-v1.js
git rm api.prod.js

# Feature 선택
git add api.prod.js
git rm api-v1.js

# ===== 옵션 2: 둘 다 유지 =====

# 두 파일 모두 추가
git add api-v1.js api.prod.js

# 문제: 중복!
# 해결: 내용 분리

# api-v1.js: 버전별 로직
# api.prod.js: 프로덕션 설정

vim api-v1.js
# (버전별 API 로직만)

vim api.prod.js
# (프로덕션 설정만)

git add api-v1.js api.prod.js

# ===== 옵션 3: 새 이름 =====

# 둘 다 버리고 새 이름
git rm api-v1.js api.prod.js
mv api.js api.config.js
git add api.config.js

# ===== 최선의 결정 =====

# 컨텍스트 고려:
# - 팀 네이밍 규칙
# - 프로젝트 구조
# - 미래 확장성

# 결정: 통합된 네이밍
git rm api-v1.js api.prod.js

# 새로운 구조
mkdir -p config/api
git mv api.js config/api/production.js

git add .
git commit -m "Merge: Unified API naming structure

- Resolved rename conflict
- Adopted consistent naming: config/api/production.js
- Prepared for multi-environment support

Closes #456"

# ✅ 깔끔한 해결!
```

---

### 💼 Scenario 5: 충돌 예방 전략

**목표:** 충돌 최소화

#### **Problem: 충돌이 너무 자주 발생!**

```bash
# 매번 merge할 때마다 충돌!
git merge feature
# CONFLICT! (10개 파일)
```

#### **Solution: 예방 전략**

```bash
# ===== 전략 1: 자주 통합 =====

# 나쁜 습관:
git switch feature
# ... 2주 작업 ...
git switch main
git merge feature
# 충돌 폭탄!

# 좋은 습관:
git switch feature
# ... 매일 ...
git fetch origin
git rebase origin/main
# 충돌 조기 발견 및 해결!

git switch main
git merge feature
# 충돌 최소!

# ===== 전략 2: 작은 단위 =====

# 나쁜 습관:
git switch -c big-feature
# ... 50개 파일 수정 ...
git merge
# 대규모 충돌!

# 좋은 습관:
git switch -c feature-part1
# ... 5개 파일 ...
git merge
# 작은 충돌

git switch -c feature-part2
# ... 5개 파일 ...
git merge
# 작은 충돌

# ===== 전략 3: 커뮤니케이션 =====

# 팀 규칙:
# 1. 작업 전 선언
#    "utils.js 리팩토링 시작"
# 
# 2. 중복 작업 방지
#    "누가 utils.js 작업 중?"
# 
# 3. 코드 리뷰
#    충돌 가능성 조기 발견

# ===== 전략 4: 모듈화 =====

# 나쁜 구조:
# app.js (1000줄, 모든 로직)
# → 모두가 수정 → 충돌!

# 좋은 구조:
# /modules
#   /auth
#     login.js
#     register.js
#   /payment
#     checkout.js
#     refund.js
# → 각자 다른 파일 → 충돌 없음!

# ===== 전략 5: CODEOWNERS =====

cat > .github/CODEOWNERS << 'EOF'
# 파일별 담당자 지정
/auth/** @auth-team
/payment/** @payment-team
/api/** @backend-team

# 중요 파일은 2명 승인
/config/** @backend-team @devops-team
EOF

# 효과:
# - 책임 명확
# - 중복 작업 방지
# - 리뷰 자동화

# ===== 전략 6: Pre-merge 테스트 =====

# Merge 전 확인
git fetch origin
git checkout -b test-merge
git merge origin/feature --no-commit

# 충돌 확인
if git diff --name-only --diff-filter=U | grep -q .; then
  echo "⚠️  Conflicts detected!"
  git merge --abort
  # 조기 해결
else
  echo "✅ No conflicts"
  git merge --abort
  # 실제 merge
  git checkout main
  git merge origin/feature
fi

# ===== 결과 =====

# Before:
# - 충돌 빈번 (주 3-5회)
# - 해결 시간: 2-3시간
# - 스트레스 높음

# After:
# - 충돌 드묾 (월 1-2회)
# - 해결 시간: 10-20분
# - 스트레스 낮음

# ✅ 예방이 최선!
```

**예방 체크리스트:**
```
□ 자주 통합 (매일 rebase)
□ 작은 PR (<200줄)
□ 팀 커뮤니케이션
□ 모듈화 (파일 분리)
□ CODEOWNERS 설정
□ Pre-merge 테스트
□ 브랜치 수명 단축 (< 3일)
□ 페어 프로그래밍 (같은 파일)
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: 충돌 마커 남김**

```bash
# 위험한 패턴
vim app.js
# ... 수정 ...
# <<<<<<< 마커 일부 남김
git add app.js
git commit

# 결과: 코드에 마커가!
console.log("<<<<<<< HEAD");  # 버그!
```

**올바른 방법:**
```bash
# 마커 검사
grep -rn "<<<\|===\|>>>" .

# 또는 Git hook
cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash
if grep -rn "<<<\|===\|>>>" --include="*.js" .; then
  echo "❌ Conflict markers found!"
  exit 1
fi
EOF
chmod +x .git/hooks/pre-commit
```

---

#### ❌ **실수 2: Base 무시**

```bash
# 위험한 패턴
# Base 확인 안 하고
# Ours와 Theirs만 비교

# 잘못된 결정:
# Ours: "A" → "B"
# Theirs: "A" → "C"
# 선택: "B"
# 결과: Theirs 기능 손실!
```

**올바른 방법:**
```bash
# Base 확인
git show :1:app.js

# 변경 이유 파악
git log --oneline $(git merge-base HEAD feature)..HEAD -- app.js
git log --oneline $(git merge-base HEAD feature)..feature -- app.js

# 의도 파악 후 결정
```

---

#### ❌ **실수 3: 테스트 없이 Commit**

```bash
# 위험한 패턴
vim app.js
# ... 충돌 해결 ...
git add app.js
git commit  # 테스트 안 함!

# 결과: 런타임 에러!
```

**올바른 방법:**
```bash
# 해결 후 테스트
vim app.js
git add app.js

# 테스트 실행
npm test
npm run lint

# 통과 후 commit
git commit
```

---

#### ❌ **실수 4: 충돌 회피**

```bash
# 나쁜 습관
git merge feature
# CONFLICT!
git merge --abort  # 도망!

# 계속 도망치면...
# 충돌은 계속 쌓임!
```

**올바른 방법:**
```bash
# 직면하고 해결
git merge feature
# CONFLICT!

# 해결
# (위에서 배운 방법들)

# 자신감 증가!
```

---

### 🎯 실습 미션

#### **미션 1: 기본 충돌 해결** 🎯

**목표:** 충돌 발생 → 해결 전체 프로세스

```bash
# 준비
mkdir conflict-practice && cd conflict-practice
git init

# 미션:
# 1. 충돌 상황 만들기
# 2. 충돌 해결
# 3. Commit 확인
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 초기 상태
echo "Hello" > file.txt
git add file.txt
git commit -m "Initial"

# 2. Feature 브랜치
git switch -c feature
echo "Hello from feature!" > file.txt
git add file.txt
git commit -m "Feature change"

# 3. Main 브랜치
git switch main
echo "Hello from main!" > file.txt
git add file.txt
git commit -m "Main change"

# 4. Merge (충돌!)
git merge feature
# CONFLICT!

# 5. 확인
git status
cat file.txt
# <<<<<<< HEAD
# Hello from main!
# =======
# Hello from feature!
# >>>>>>> feature

# 6. 해결
echo "Hello from both!" > file.txt

# 7. Commit
git add file.txt
git commit -m "Resolved conflict"

# 8. 확인
git log --oneline --graph
# *   Merge branch 'feature'
# |\  
# | * Feature change
# * | Main change
# |/  
# * Initial

# ✅ 완료!
```

</details>

---

#### **미션 2: 다중 전략 해결** 🔧

**목표:** 파일별 다른 전략 사용

```bash
# 시나리오:
# file1.txt → Ours
# file2.txt → Theirs
# file3.txt → 수동
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "A" > file1.txt
echo "B" > file2.txt
echo "C" > file3.txt
git add .
git commit -m "Initial"

git switch -c feature
echo "Feature1" > file1.txt
echo "Feature2" > file2.txt
echo "Feature3" > file3.txt
git add .
git commit -m "Feature"

git switch main
echo "Main1" > file1.txt
echo "Main2" > file2.txt
echo "Main3" > file3.txt
git add .
git commit -m "Main"

# Merge (충돌!)
git merge feature
# CONFLICT in 3 files

# file1 → Ours
git checkout --ours file1.txt
git add file1.txt

# file2 → Theirs
git checkout --theirs file2.txt
git add file2.txt

# file3 → 수동
echo "Manual" > file3.txt
git add file3.txt

# Commit
git commit -m "Resolved with mixed strategies"

# 확인
cat file1.txt  # Main1
cat file2.txt  # Feature2
cat file3.txt  # Manual

# ✅ 완료!
```

</details>

---

#### **미션 3: 충돌 예방** 🛡️

**목표:** 자주 통합으로 충돌 최소화

```bash
# 시나리오:
# 1. Feature 개발
# 2. 매일 main 동기화
# 3. 최종 merge (충돌 없음!)
```

<details>
<summary>정답 보기</summary>

```bash
# Day 1
git init
echo "Day 1" > app.txt
git add app.txt
git commit -m "Day 1"

git switch -c feature

# Day 2
echo "Feature Day 2" >> app.txt
git add app.txt
git commit -m "Feature work"

# Main 업데이트
git switch main
echo "Main Day 2" >> app.txt
git add app.txt
git commit -m "Main work"

# 동기화!
git switch feature
git rebase main
# 충돌 해결 (작음)

# Day 3
echo "Feature Day 3" >> app.txt
git add app.txt
git commit -m "More feature"

git switch main
echo "Main Day 3" >> app.txt
git add app.txt
git commit -m "More main"

# 동기화!
git switch feature
git rebase main
# 충돌 해결 (작음)

# 최종 Merge
git switch main
git merge feature
# Fast-forward! (충돌 없음!)

# ✅ 예방 성공!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 충돌 확인 ===
git status                         # 충돌 파일 목록
git diff --name-only --diff-filter=U  # 충돌 파일만
git diff                           # 충돌 내용

# === 버전 확인 ===
git show :1:file                   # Base
git show :2:file                   # Ours
git show :3:file                   # Theirs

# === 해결 ===
git checkout --ours file           # Ours 선택
git checkout --theirs file         # Theirs 선택
vim file                           # 수동 해결
git add file                       # 해결 표시

# === 취소 ===
git merge --abort                  # Merge 취소
git checkout HEAD file             # 파일 복구

# === Commit ===
git commit                         # Merge 완료
```

---

### 🎨 충돌 마커 읽는 법

```
<<<<<<< HEAD
현재 브랜치 (Ours)
=======
Merge할 브랜치 (Theirs)
>>>>>>> branch-name

해석:
1. HEAD = 현재
2. branch-name = 대상
3. 둘 중 선택 또는 결합
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [3.1 Merge 타입](./01-merge-types.md)

**다음 학습:**
- [3.3 복잡한 충돌](./03-complex-conflicts.md)

**관련 주제:**
- [2.1 브랜치 기본](../02-branching/01-branch-basics.md)
- [4.1 Rebase 기초](../04-rebase/01-rebase-basics.md)

---

<div align="center">

**🎉 축하합니다! 충돌의 원리를 완전히 마스터했습니다!**

이제 충돌이 두렵지 않습니다!

[⬅️ 이전: Merge 타입](./01-merge-types.md) | [README로](../README.md) | [다음: 복잡한 충돌 ➡️](./03-complex-conflicts.md)

</div>
