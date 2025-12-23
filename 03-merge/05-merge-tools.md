# 3.5 Merge Tools (충돌 해결 도구)

**난이도: ⭐⭐**

> *"올바른 도구는 복잡한 충돌도 쉽게 만듭니다. 시각화가 이해를 돕습니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Merge Tools를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 복잡한 충돌 발생

터미널에서:
<<<<<<< HEAD
function calculate(a, b, c, d, e) {
  const result = a + b + c;
  console.log("Debug:", result);
  return result * d / e;
}
=======
function calculate(x, y, z) {
  return (x + y) * z;
}
>>>>>>> feature

개발자: "어디가 뭐지? 😵"
주니어: "너무 복잡해요..."
시니어: "Base가 뭐였더라?"

GUI 도구에서:
┌─────────┬─────────┬─────────┐
│  Base   │  Ours   │ Theirs  │
├─────────┼─────────┼─────────┤
│ a,b,c   │a,b,c,d,e│  x,y,z  │
│ a+b+c   │ a+b+c   │ (x+y)*z │
│         │ +debug  │         │
└─────────┴─────────┴─────────┘
         ↓
      Result
      
개발자: "이제 명확해! 😊"
```

**Merge Tools를 이해하면:**
```bash
터미널 (Before):
- 마커 읽기 어려움
- Base 확인 번거로움
- 실수 위험 높음
- 시간 오래 걸림

GUI 도구 (After):
✅ 3-way diff 시각화
✅ Base 자동 표시
✅ 클릭으로 선택
✅ 빠른 해결
```

> 💡 **핵심:** 좋은 도구는 충돌 해결을 10배 빠르게 만듭니다!

---

### 📌 Merge Tool의 종류

```
┌─────────────────────────────────────────────────┐
│           Merge Tool 분류                        │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. IDE 내장                                     │
│     - VS Code                                   │
│     - IntelliJ IDEA                             │
│     - Eclipse                                   │
│                                                 │
│  2. 전문 도구                                     │
│     - KDiff3                                    │
│     - Meld                                      │
│     - P4Merge                                   │
│                                                 │
│  3. 상용 도구                                     │
│     - Beyond Compare                            │
│     - Araxis Merge                              │
│     - DiffMerge                                 │
│                                                 │
│  4. 터미널 기반                                    │
│     - vimdiff                                   │
│     - ediff (Emacs)                             │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

### 🎨 3-way Diff 이해하기

#### **정의**

```
3-way Diff:
3개 버전을 동시에 비교

구성:
1. Base (공통 조상)
2. Ours (현재 브랜치)
3. Theirs (merge할 브랜치)
4. Result (결과)
```

---

#### **시각적 레이아웃**

```
전통적 레이아웃:

┌──────────────────────────────────────┐
│              Base                    │
│         (공통 조상)                    │
└──────────────────────────────────────┘
┌──────────────┬───────────────────────┐
│    Ours      │      Theirs           │
│  (현재)       │    (merge할)           │
└──────────────┴───────────────────────┘
┌──────────────────────────────────────┐
│             Result                   │
│            (결과)                     │
└──────────────────────────────────────┘

수평 레이아웃:

┌──────┬──────┬──────┬──────┐
│ Base │ Ours │Theirs│Result│
└──────┴──────┴──────┴──────┘
```

---

#### **색상 코딩**

```
일반적인 색상:

🟢 Green: 추가됨
🔴 Red: 삭제됨
🟡 Yellow: 수정됨
⚪ White: 변경 없음
🔵 Blue: 충돌 (도구마다 다름)

예시:
Base:   🟡 const x = 1;
Ours:   🟢 const x = 2;
Theirs: 🟡 const x = 3;
Result: 🔵 ??? (선택 필요)
```

---

### 💻 VS Code Merge Editor

#### **특징**

```
장점:
✅ 무료
✅ 가볍고 빠름
✅ 익숙한 인터페이스
✅ 확장 기능 풍부
✅ Git 통합

단점:
⚠️ 복잡한 충돌은 어려움
⚠️ 3-way diff 제한적
```

---

#### **인터페이스**

```
VS Code 충돌 표시:

function calculate() {
<<<<<<< Current Change (main)
  return a + b;
||||||| Base
  return a;
=======
  return x + y;
>>>>>>> Incoming Change (feature)
}

버튼:
[Accept Current Change]
[Accept Incoming Change]
[Accept Both Changes]
[Compare Changes]
```

---

#### **새로운 Merge Editor**

```
VS Code 1.70+:

3-way 레이아웃:
┌─────────────────────────────┐
│     Incoming (Theirs)       │
├─────────────────────────────┤
│          Base               │
├─────────────────────────────┤
│      Current (Ours)         │
├─────────────────────────────┤
│          Result             │
└─────────────────────────────┘

체크박스로 선택:
☑ Incoming
☑ Current
→ Result에 자동 반영
```

---

### 🎯 IntelliJ IDEA Merge Tool

#### **특징**

```
장점:
✅ 강력한 3-way diff
✅ 자동 merge 제안
✅ 문법 하이라이팅
✅ 인텔리전스 지원
✅ 리팩토링 감지

단점:
⚠️ 무거움
⚠️ 유료 (Ultimate)
```

---

#### **인터페이스**

```
IntelliJ 레이아웃:

┌──────────┬──────────┬──────────┐
│  Yours   │  Base    │  Theirs  │
│  (Ours)  │          │          │
├──────────┴──────────┴──────────┤
│          Result                │
│   (편집 가능한 병합 결과)           │
└────────────────────────────────┘

화살표 버튼:
→ Accept Yours
← Accept Theirs
⇄ Accept Both
```

---

#### **스마트 기능**

```
자동 감지:
- Rename 감지
- Move 감지
- 구조 변경 감지
- 의미있는 변경만 표시

제안:
"Merge automatically" 버튼
→ 충돌 없는 부분 자동 해결
```

---

### 🔧 KDiff3

#### **특징**

```
장점:
✅ 무료, 오픈소스
✅ 강력한 3-way diff
✅ 크로스 플랫폼
✅ 자동 merge 우수
✅ 디렉토리 비교

단점:
⚠️ UI가 복잡
⚠️ 학습 곡선
```

---

#### **인터페이스**

```
KDiff3 레이아웃:

┌────────┬────────┬────────┐
│   A    │   B    │   C    │
│ (Base) │ (Ours) │(Theirs)│
├────────┴────────┴────────┤
│        Output            │
│   (A, B, C 버튼으로)       │
└──────────────────────────┘

상태:
🟢 Green: 자동 해결됨
🔴 Red: 충돌 (수동 필요)
🟡 Yellow: 차이 있지만 충돌 없음
```

---

### 🔀 기타 도구들

#### **Meld**

```
특징:
- Linux에서 인기
- 직관적 UI
- 디렉토리 비교

레이아웃:
┌─────┬─────┬──────┐
│Mine │Base │Theirs│
└─────┴─────┴──────┘
```

---

#### **P4Merge (Perforce)**

```
특징:
- 무료
- 깔끔한 UI
- 4-pane 레이아웃

레이아웃:
      ┌─────┐
      │Base │
      └─────┘
  ┌─────┬──────┐
  │Local│Remote│
  └─────┴──────┘
      ┌─────┐
      │Merge│
      └─────┘
```

---

#### **Beyond Compare**

```
특징:
- 상용 (유료)
- 매우 강력
- 프로 레벨

기능:
- 파일/폴더 동기화
- FTP 비교
- 압축 파일 비교
- 이진 비교
```

---

### 📊 도구 비교표

| 도구 | 가격 | 난이도 | 3-way | 추천 |
|:-----|:-----|:------|:------|:-----|
| **VS Code** | 무료 | ⭐ | 제한적 | 일상용 |
| **IntelliJ** | 유료 | ⭐⭐ | 우수 | 전문가 |
| **KDiff3** | 무료 | ⭐⭐ | 최고 | 고급 |
| **Meld** | 무료 | ⭐ | 좋음 | Linux |
| **P4Merge** | 무료 | ⭐⭐ | 우수 | 일반 |
| **Beyond Compare** | 유료 | ⭐⭐⭐ | 최고 | 프로 |

---

### 🎯 도구 선택 가이드

```
일상적 충돌:
→ VS Code (익숙함)

복잡한 충돌:
→ IntelliJ 또는 KDiff3

Linux 사용자:
→ Meld

프로젝트 표준:
→ 팀과 통일

비용 고려:
→ 무료: VS Code, KDiff3, Meld
→ 유료: IntelliJ Ultimate, Beyond Compare
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 Merge Tool 설정

#### **1️⃣ 기본 도구 설정**

```bash
# VS Code 설정
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait $MERGED'

# IntelliJ 설정
git config --global merge.tool intellij
git config --global mergetool.intellij.cmd \
  'idea merge $LOCAL $REMOTE $BASE $MERGED'

# KDiff3 설정
git config --global merge.tool kdiff3
git config --global mergetool.kdiff3.path \
  '/usr/local/bin/kdiff3'

# Meld 설정
git config --global merge.tool meld
git config --global mergetool.meld.cmd \
  'meld $LOCAL $BASE $REMOTE --output $MERGED'

# P4Merge 설정
git config --global merge.tool p4merge
git config --global mergetool.p4merge.path \
  '/Applications/p4merge.app/Contents/MacOS/p4merge'
```

---

#### **2️⃣ 도구별 상세 설정**

```bash
# === VS Code ===
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'
git config --global mergetool.vscode.trustExitCode false

# === IntelliJ IDEA (Mac) ===
git config --global merge.tool idea
git config --global mergetool.idea.cmd \
  '/Applications/IntelliJ\ IDEA.app/Contents/MacOS/idea merge $LOCAL $REMOTE $BASE $MERGED'
git config --global mergetool.idea.trustExitCode true

# === IntelliJ IDEA (Windows) ===
git config --global mergetool.idea.cmd \
  '"C:/Program Files/JetBrains/IntelliJ IDEA/bin/idea.bat" merge $LOCAL $REMOTE $BASE $MERGED'

# === IntelliJ IDEA (Linux) ===
git config --global mergetool.idea.cmd \
  'idea merge $LOCAL $REMOTE $BASE $MERGED'

# === KDiff3 (Mac) ===
git config --global merge.tool kdiff3
git config --global mergetool.kdiff3.path \
  '/Applications/kdiff3.app/Contents/MacOS/kdiff3'
git config --global mergetool.kdiff3.trustExitCode false

# === KDiff3 (Windows) ===
git config --global mergetool.kdiff3.path \
  '"C:/Program Files/KDiff3/kdiff3.exe"'

# === Meld ===
git config --global merge.tool meld
git config --global mergetool.meld.cmd \
  'meld --auto-merge $LOCAL $BASE $REMOTE --output $MERGED'
git config --global mergetool.meld.trustExitCode true
```

---

#### **3️⃣ 공통 설정**

```bash
# 백업 파일 생성 안 함
git config --global mergetool.keepBackup false

# 종료 코드 신뢰
git config --global mergetool.trustExitCode true

# 확인 메시지 생략
git config --global mergetool.prompt false

# 임시 파일 자동 삭제
git config --global mergetool.keepTemporaries false
```

---

### ⚡ Merge Tool 사용

#### **1️⃣ 기본 사용**

```bash
# 충돌 발생
git merge feature
# CONFLICT!

# Merge tool 실행
git mergetool

# 출력:
# Merging:
# file1.js
# 
# Normal merge conflict for 'file1.js':
#   {local}: modified file
#   {remote}: modified file
# Hit return to start merge resolution tool (vscode):

# Enter 누르면 도구 실행
```

---

#### **2️⃣ 특정 파일만**

```bash
# 특정 파일만 도구로
git mergetool file1.js

# 여러 파일
git mergetool file1.js file2.js

# 패턴
git mergetool src/*.js
```

---

#### **3️⃣ 도구 지정**

```bash
# 임시로 다른 도구 사용
git mergetool --tool=kdiff3

# 모든 파일에 적용
git mergetool --tool=intellij file1.js file2.js
```

---

#### **4️⃣ 해결 후 작업**

```bash
# 도구에서 저장 후 종료
# → Git이 자동으로 add

# 상태 확인
git status
# On branch main
# All conflicts fixed but you are still merging.

# 백업 파일 확인
ls *.orig
# file1.js.orig (keepBackup이 true면)

# 백업 삭제
rm *.orig

# Commit
git commit
```

---

### 📋 변수 이해하기

```bash
Git이 도구에 전달하는 변수:

$LOCAL:
현재 브랜치의 파일
= Ours

$REMOTE:
merge할 브랜치의 파일
= Theirs

$BASE:
공통 조상의 파일
= Base

$MERGED:
결과 파일 (편집할 파일)
= Working directory의 파일

예시:
/tmp/file_LOCAL_12345.js
/tmp/file_REMOTE_12345.js
/tmp/file_BASE_12345.js
/path/to/project/file.js (MERGED)
```

---

### 🔍 도구 확인

```bash
# 설정된 도구 확인
git config --global --get merge.tool
# vscode

# 도구 경로 확인
git config --global --get mergetool.vscode.cmd
# code --wait $MERGED

# 모든 merge 관련 설정
git config --global --get-regexp merge
# merge.tool vscode
# mergetool.vscode.cmd code --wait $MERGED
# mergetool.keepBackup false

# 사용 가능한 도구 목록
git mergetool --tool-help
# 'git mergetool --tool=<tool>' may be set to one of:
# emerge
# gvimdiff
# kdiff3
# meld
# vimdiff
# ...
```

---

### 📊 명령어 비교표

| 작업 | 명령어 | 효과 |
|:-----|:------|:-----|
| **도구 실행** | `git mergetool` | 모든 충돌 파일 |
| **특정 파일** | `git mergetool file.js` | 해당 파일만 |
| **도구 지정** | `git mergetool --tool=kdiff3` | 임시 변경 |
| **도구 확인** | `git config merge.tool` | 현재 도구 |
| **설정 초기화** | `git config --unset merge.tool` | 기본값 복원 |

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: VS Code로 첫 충돌 해결

**목표:** VS Code 완전 정복

#### **Problem: IDE에서 충돌을 쉽게 해결하고 싶다!**

```bash
# 상황
git merge feature
# CONFLICT in app.js
```

#### **Solution: VS Code Merge Editor**

```bash
# ===== Step 1: 설정 =====

# VS Code 설정 (한번만)
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'
git config --global mergetool.keepBackup false
git config --global mergetool.prompt false

# ===== Step 2: 충돌 발생 =====

# Merge
git merge feature
# Auto-merging app.js
# CONFLICT (content): Merge conflict in app.js
# Automatic merge failed; fix conflicts

# 파일 확인
cat app.js
# function calculate(a, b) {
# <<<<<<< HEAD
#   const result = a + b;
#   console.log("Result:", result);
#   return result;
# =======
#   return a + b;
# >>>>>>> feature
# }

# ===== Step 3: VS Code 실행 =====

# Merge tool 실행
git mergetool

# VS Code가 열림!

# ===== Step 4: VS Code에서 해결 =====

# 화면 구성:
# ┌─────────────────────────────────┐
# │  Incoming Change (feature)      │
# │  [Accept Incoming]              │
# │  return a + b;                  │
# ├─────────────────────────────────┤
# │  Current Change (HEAD)          │
# │  [Accept Current]               │
# │  const result = a + b;          │
# │  console.log("Result:", result);│
# │  return result;                 │
# ├─────────────────────────────────┤
# │  [Accept Both]                  │
# │  [Compare Changes]              │
# └─────────────────────────────────┘

# 선택 1: Current 선택
# [Accept Current] 클릭

# 선택 2: Incoming 선택
# [Accept Incoming] 클릭

# 선택 3: Both 선택
# [Accept Both] 클릭
# (순서: Current → Incoming)

# 선택 4: 수동 편집
# Result 영역 직접 수정
function calculate(a, b) {
  const result = a + b;
  // 로그는 디버그 모드에서만
  if (DEBUG) console.log("Result:", result);
  return result;
}

# 저장 (Cmd/Ctrl + S)
# VS Code 종료

# ===== Step 5: 새 Merge Editor (VS Code 1.70+) =====

# Settings > Git: Merge Editor 활성화
# 또는 settings.json:
{
  "git.mergeEditor": true
}

# 화면 구성 (3-way):
# ┌─────────────────────────────────┐
# │    Incoming (feature)           │
# │    ☐ return a + b;              │
# ├─────────────────────────────────┤
# │    Base                         │
# │    return a;                    │
# ├─────────────────────────────────┤
# │    Current (HEAD)               │
# │    ☑ const result = a + b;      │
# │    ☑ console.log(...);          │
# │    ☑ return result;             │
# ├─────────────────────────────────┤
# │    Result                       │
# │    const result = a + b;        │
# │    console.log("Result:", res); │
# │    return result;               │
# └─────────────────────────────────┘

# 체크박스로 선택:
# ☑ Current의 console.log
# ☑ Incoming의 간결함
# → Result에 자동 반영

# [Complete Merge] 버튼 클릭

# ===== Step 6: Git에서 확인 =====

git status
# On branch main
# All conflicts fixed but you are still merging.
# Changes to be committed:
#   modified: app.js

# Diff 확인
git diff --cached

# ===== Step 7: Commit =====

git commit -m "Merge feature: Resolved app.js conflict

Combined both approaches:
- Kept detailed logging (from current)
- Added debug mode check (new)
- Maintained clean return (from incoming)"

# ===== Step 8: 정리 =====

# 백업 파일 확인
ls *.orig
# (없음 - keepBackup false)

# 로그 확인
git log --oneline --graph -3
# *   abc123 Merge feature
# |\  
# | * def456 Simplify calculate
# * | ghi789 Add logging

# ✅ VS Code로 완벽 해결!

# ===== 추가 팁: VS Code 확장 =====

# GitLens 설치
# - Blame 정보
# - 파일 히스토리
# - 충돌 원인 파악

# Git Graph 설치
# - 시각적 브랜치
# - 커밋 추적

# 사용:
# 1. Conflict 발생
# 2. GitLens로 누가 왜 수정했는지 확인
# 3. Merge Editor로 해결
# 4. Git Graph로 결과 확인
```

**VS Code 장점:**
```
✅ 익숙한 인터페이스
✅ 빠른 실행
✅ 버튼으로 간편 선택
✅ 확장 기능 풍부
✅ 무료

최적:
- 간단한 충돌
- 일상적 개발
- 빠른 해결
```

---

### 💼 Scenario 2: IntelliJ로 복잡한 충돌

**목표:** IntelliJ의 강력한 기능 활용

#### **Problem: 리팩토링으로 복잡한 충돌!**

```bash
# 상황
# - 클래스 이름 변경
# - 메서드 이동
# - 패키지 재구성
# - 100개 파일 충돌
```

#### **Solution: IntelliJ Smart Merge**

```bash
# ===== Step 1: 설정 =====

# IntelliJ 설정
git config --global merge.tool idea

# Mac
git config --global mergetool.idea.cmd \
  '/Applications/IntelliJ\ IDEA.app/Contents/MacOS/idea merge $LOCAL $REMOTE $BASE $MERGED'

# Windows
git config --global mergetool.idea.cmd \
  '"C:/Program Files/JetBrains/IntelliJ IDEA/bin/idea.bat" merge $LOCAL $REMOTE $BASE $MERGED'

git config --global mergetool.idea.trustExitCode true

# ===== Step 2: 충돌 발생 =====

git merge refactor
# CONFLICT in 100 files!

# ===== Step 3: IntelliJ 실행 =====

git mergetool

# IntelliJ가 열림!

# ===== Step 4: IntelliJ 인터페이스 =====

# 화면 구성:
# ┌──────────┬──────────┬──────────┐
# │  Yours   │  Base    │  Theirs  │
# │ (Local)  │          │ (Remote) │
# │          │          │          │
# │ UserMgr  │  User    │  UserSvc │
# │ class    │  class   │  class   │
# │          │          │          │
# │ [→]      │          │    [←]   │
# ├──────────┴──────────┴──────────┤
# │          Result                │
# │   (편집 가능한 병합 결과)           │
# │                                │
# │  class UserManager {           │
# │    // 자동 merge 시도            │
# │  }                             │
# └────────────────────────────────┘

# ===== Step 5: 자동 Merge =====

# "Accept Non-Conflicting Changes" 버튼
# → 충돌 없는 부분 자동 해결
# → 100개 → 20개로 감소!

# ===== Step 6: 스마트 감지 =====

# Rename 감지:
# Base:   class User
# Yours:  class UserManager
# Theirs: class UserService
# 
# IntelliJ 제안:
# "Both renamed, manual decision needed"

# Move 감지:
# Base:   method in UserModel
# Yours:  moved to UserManager
# Theirs: moved to UserService
# 
# IntelliJ 제안:
# "Method moved to different classes"

# ===== Step 7: 화살표 버튼 =====

# → 버튼: Yours 선택
# 해당 변경사항이 Result로

# ← 버튼: Theirs 선택
# 해당 변경사항이 Result로

# ⇄ 버튼: Both
# 양쪽 모두 Result로

# X 버튼: Neither
# 양쪽 모두 무시

# ===== Step 8: 구조 비교 =====

# Structure View (Cmd+7)
# - 클래스 구조 비교
# - 메서드 이름 비교
# - 패키지 차이 확인

# 예시:
# Yours:
#   UserManager
#     - createUser()
#     - deleteUser()
# 
# Theirs:
#   UserService
#     - addUser()
#     - removeUser()
# 
# 결정:
# 클래스명: UserManager (표준)
# 메서드: addUser, removeUser (명확)

# ===== Step 9: 검색과 교체 =====

# 충돌 많은 패턴:
# Yours: logger.debug(...)
# Theirs: log.info(...)

# IntelliJ에서:
# Cmd+R (Replace)
# 패턴 찾아서 일괄 변경

# ===== Step 10: 결과 확인 =====

# Result 패널에서:
# - 문법 하이라이팅
# - 에러 표시
# - 자동 완성

# 완료 후 저장
# [Apply] 버튼

# ===== Step 11: Git 상태 =====

git status
# All conflicts fixed

# 테스트 (IntelliJ에서)
# Shift+F10: Run tests
# → 모든 테스트 통과!

git commit -m "Merge refactor: Resolved 100 conflicts

Used IntelliJ smart merge:
- Auto-resolved 80 files
- Manual decision on 20 files
- Maintained refactoring structure
- All tests passing"

# ✅ IntelliJ로 복잡한 충돌 해결!

# ===== 추가 기능 =====

# Local History:
# - IntelliJ가 자동 저장
# - Merge 전 상태 복구 가능
# - Right-click → Local History

# Compare with Branch:
# - Merge 전 미리 비교
# - Tools → Git → Compare with Branch

# Resolve Conflicts (GUI):
# - VCS → Git → Resolve Conflicts
# - 모든 충돌 파일 목록
# - 하나씩 또는 일괄 처리
```

**IntelliJ 장점:**
```
✅ 스마트 감지
✅ 자동 merge 제안
✅ 구조 비교
✅ 문법 인식
✅ 테스트 통합

최적:
- 복잡한 충돌
- 리팩토링
- 대규모 변경
- 전문 개발
```

---

### 💼 Scenario 3: KDiff3로 정밀 비교

**목표:** KDiff3의 강력한 3-way diff

#### **Problem: 3개 버전을 정확히 비교하고 싶다!**

```bash
# 상황
# - Base부터 정확히 추적
# - 각 변경의 이유 파악
# - 정밀한 merge 필요
```

#### **Solution: KDiff3 3-way Diff**

```bash
# ===== Step 1: 설치 =====

# Mac
brew install --cask kdiff3

# Windows
# https://kdiff3.sourceforge.net/

# Linux
sudo apt install kdiff3

# ===== Step 2: 설정 =====

git config --global merge.tool kdiff3
git config --global mergetool.kdiff3.path \
  '/Applications/kdiff3.app/Contents/MacOS/kdiff3'
git config --global mergetool.kdiff3.trustExitCode false

# ===== Step 3: 충돌 발생 =====

git merge feature
# CONFLICT!

# ===== Step 4: KDiff3 실행 =====

git mergetool

# KDiff3가 열림!

# ===== Step 5: KDiff3 인터페이스 =====

# 화면 구성:
# ┌──────────┬──────────┬──────────┐
# │    A     │    B     │    C     │
# │  (Base)  │ (Local)  │ (Remote) │
# │          │          │          │
# │ Line 1   │ Line 1   │ Line 1   │
# │ Line 2   │ Line 2+  │ Line 2*  │
# │ Line 3   │ -        │ Line 3   │
# │          │          │          │
# ├──────────┴──────────┴──────────┤
# │          Output                │
# │   (A,B,C 버튼으로 선택)           │
# │                                │
# │  Line 1                        │
# │  <Merge Conflict>              │
# │    B: Line 2+                  │
# │    C: Line 2*                  │
# │  Line 3                        │
# └────────────────────────────────┘

# ===== Step 6: 색상 이해 =====

# 🟢 Green: 자동 merge 성공
# 🔴 Red: 충돌 (수동 필요)
# 🟡 Yellow: 차이 있음 (충돌 아님)
# ⚪ White: 동일

# 예시:
# Line 1: ⚪ (모두 같음)
# Line 2: 🔴 (B와 C 다름)
# Line 3: 🟡 (B 삭제, C 유지)

# ===== Step 7: 자동 Merge =====

# Settings → Auto Advance
# → 자동 해결된 것은 스킵

# Settings → Auto Merge
# → 충돌 없는 것 자동 merge

# 결과:
# 100줄 중 90줄 자동
# 10줄만 수동!

# ===== Step 8: 수동 해결 =====

# 충돌 위치로 이동:
# Ctrl+PgDn: 다음 충돌
# Ctrl+PgUp: 이전 충돌

# 선택 버튼:
# A: Base 버전 선택
# B: Local 버전 선택
# C: Remote 버전 선택
# A+B: Base + Local
# A+B+C: 모두

# 또는 직접 편집:
# Output 창에서 타이핑

# 예시:
# <Conflict>
#   B: console.log("debug");
#   C: logger.debug();
# 
# 선택: C 버튼
# 또는 직접: logger.debug("info");

# ===== Step 9: 고급 기능 =====

# 공백 무시:
# Settings → Ignore white space
# → 공백 차이는 무시

# 대소문자 무시:
# Settings → Ignore case
# → 대소문자 차이 무시

# 숫자 무시:
# Settings → Ignore numbers
# → 버전 번호 등 무시

# ===== Step 10: 완료 =====

# 모든 충돌 해결 확인
# Merge → Go to Next Unsolved Conflict
# "No more unsolved conflicts"

# 저장 및 종료
# File → Save
# File → Quit

# ===== Step 11: Git 확인 =====

git status
# All conflicts fixed

# Diff 확인
git diff --cached

git commit -m "Merge feature: Resolved with KDiff3

Detailed 3-way comparison:
- Base changes tracked precisely
- Local improvements kept
- Remote bug fixes applied
- All conflicts resolved manually"

# ✅ 정밀한 해결!

# ===== 추가: 디렉토리 비교 =====

# 전체 디렉토리 비교
kdiff3 branch1/ branch2/ branch3/

# 파일 목록:
# 🟢 identical.js (동일)
# 🟡 modified.js (차이)
# 🔴 conflict.js (충돌)

# 각 파일 클릭하여 개별 해결
```

**KDiff3 장점:**
```
✅ 정밀한 3-way diff
✅ 자동 merge 우수
✅ 디렉토리 비교
✅ 무료, 오픈소스
✅ 크로스 플랫폼

최적:
- 정밀한 비교
- Base 추적
- 대량 충돌
- 고급 사용자
```

---

### 💼 Scenario 4: 팀 표준 도구 설정

**목표:** 팀 전체 동일한 도구 사용

#### **Problem: 팀원마다 다른 도구 사용!**

```bash
# 문제
# - 개발자 A: VS Code
# - 개발자 B: IntelliJ
# - 개발자 C: vim
# - 개발자 D: 터미널 직접 편집
# 
# → 충돌 해결 품질 불균일
# → 리뷰 어려움
```

#### **Solution: 팀 표준화**

```bash
# ===== Step 1: 도구 선택 =====

# 팀 회의:
# - 무료 vs 유료
# - 학습 곡선
# - 플랫폼 (Mac/Windows/Linux)
# - 기존 도구 (IDE)

# 결정: VS Code (최대 공약수)
# 이유:
# - 무료
# - 모든 플랫폼
# - 학습 쉬움
# - 이미 대부분 사용

# ===== Step 2: 설정 파일 =====

# 프로젝트에 설정 파일 추가
cat > .gitattributes << 'EOF'
# Merge tool 설정
* merge=vscode
EOF

# Git 설정 스크립트
cat > scripts/setup-merge-tool.sh << 'EOF'
#!/bin/bash

echo "=== Setting up merge tool ==="

# VS Code 설치 확인
if ! command -v code &> /dev/null; then
  echo "❌ VS Code not found"
  echo "Install: https://code.visualstudio.com/"
  exit 1
fi

# Git 설정
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'
git config --global mergetool.keepBackup false
git config --global mergetool.prompt false

echo "✅ VS Code merge tool configured"

# 확인
echo ""
echo "Current merge tool:"
git config --global merge.tool

echo ""
echo "Test with: git mergetool"
EOF

chmod +x scripts/setup-merge-tool.sh

# ===== Step 3: 문서화 =====

cat > docs/MERGE_TOOL.md << 'EOF'
# Merge Tool Guide

## Team Standard
We use **VS Code** as the standard merge tool.

## Setup

### 1. Install VS Code
Download: https://code.visualstudio.com/

### 2. Run Setup Script
```bash
./scripts/setup-merge-tool.sh
```

### 3. Verify
```bash
git config --global merge.tool
# Should output: vscode
```

## Usage

### When conflicts occur:
```bash
git merge feature
# CONFLICT!

git mergetool
# VS Code opens automatically
```

### In VS Code:
1. Review changes in diff view
2. Click action buttons:
   - [Accept Current Change]
   - [Accept Incoming Change]
   - [Accept Both Changes]
3. Or edit manually in Result pane
4. Save and close (Cmd/Ctrl + S)

### Complete merge:
```bash
git status
# All conflicts fixed

git commit
```

## Tips

### Enable new Merge Editor:
Settings → Git: Merge Editor → Enable

### Extensions:
- GitLens: Better conflict context
- Git Graph: Visualize history

### Keyboard Shortcuts:
- `Cmd/Ctrl + Shift + P`: Command palette
- Search: "Merge Conflict"
- Quick actions available

## Troubleshooting

### Tool doesn't open:
```bash
# Check configuration
git config --global --get merge.tool

# Re-run setup
./scripts/setup-merge-tool.sh
```

### Want to use different tool temporarily:
```bash
git mergetool --tool=kdiff3
```

### Backup files (*.orig):
```bash
# Disable backup
git config --global mergetool.keepBackup false

# Clean existing
rm *.orig
```

## Support
Contact: @tech-lead for help
EOF

# ===== Step 4: Onboarding =====

# 신입 개발자 체크리스트
cat > docs/ONBOARDING.md << 'EOF'
# Developer Onboarding

## Day 1: Setup

### ✅ Install Tools
- [ ] VS Code
- [ ] Git
- [ ] Extensions: GitLens, Git Graph

### ✅ Configure Git
```bash
./scripts/setup-merge-tool.sh
```

### ✅ Test Merge Tool
```bash
# Create test conflict
git checkout -b test-merge
echo "test" > file.txt
git add file.txt
git commit -m "test"

git checkout main
echo "main" > file.txt
git add file.txt
git commit -m "main"

git merge test-merge
# Conflict!

git mergetool
# VS Code should open

# Resolve and commit
git merge --abort
git branch -D test-merge
```

## Resources
- Merge Tool Guide: docs/MERGE_TOOL.md
- Team Lead: @tech-lead
EOF

# ===== Step 5: CI 검증 =====

# 잘못된 도구 사용 감지
cat > .github/workflows/check-merge-tool.yml << 'EOF'
name: Check Merge Tool

on: [pull_request]

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Check for backup files
        run: |
          if find . -name "*.orig" | grep -q .; then
            echo "❌ Found .orig files"
            echo "Please configure: mergetool.keepBackup false"
            exit 1
          fi
      
      - name: Check merge conflicts
        run: |
          if git diff --check | grep -q "conflict"; then
            echo "❌ Unresolved conflicts found"
            exit 1
          fi
EOF

# ===== Step 6: 모니터링 =====

# 사용 통계
cat > scripts/merge-tool-stats.sh << 'EOF'
#!/bin/bash

echo "=== Merge Tool Usage Stats ==="

# 팀원별 설정 확인
echo ""
echo "Team members:"
for user in alice bob charlie; do
  tool=$(git config --global merge.tool 2>/dev/null || echo "not set")
  echo "  $user: $tool"
done

# 최근 충돌 해결 시간
echo ""
echo "Recent merge conflict resolutions:"
git log --grep="Merge.*conflict" --oneline -5
EOF

# ===== 결과 =====

# Before:
# - 도구 각자 다름
# - 해결 품질 불균일
# - 신입 어려움

# After:
# - 통일된 도구 (VS Code)
# - 일관된 품질
# - 빠른 온보딩
# - 문서화

# ✅ 팀 표준화 완료!
```

---

### 💼 Scenario 5: 여러 도구 조합

**목표:** 상황에 맞는 도구 선택

#### **Problem: 모든 충돌에 하나의 도구?**

```bash
# 현실:
# - 간단한 충돌: VS Code (빠름)
# - 복잡한 충돌: IntelliJ (강력)
# - 대량 충돌: KDiff3 (자동화)
# - 특수 파일: 전문 도구
```

#### **Solution: 상황별 도구**

```bash
# ===== Step 1: 다중 도구 설정 =====

# VS Code (기본)
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait --merge $REMOTE $LOCAL $BASE $MERGED'

# IntelliJ (복잡한 것)
git config --global mergetool.idea.cmd \
  '/Applications/IntelliJ\ IDEA.app/Contents/MacOS/idea merge $LOCAL $REMOTE $BASE $MERGED'

# KDiff3 (대량)
git config --global mergetool.kdiff3.cmd \
  '/Applications/kdiff3.app/Contents/MacOS/kdiff3 $BASE $LOCAL $REMOTE -o $MERGED'

# ===== Step 2: 스마트 스크립트 =====

cat > scripts/smart-merge.sh << 'EOF'
#!/bin/bash

echo "=== Smart Merge Tool Selector ==="

# 충돌 파일 수 확인
conflict_count=$(git diff --name-only --diff-filter=U | wc -l)

echo "Conflicts: $conflict_count files"

# 파일 타입 확인
has_java=$(git diff --name-only --diff-filter=U | grep -q "\.java$" && echo "yes" || echo "no")
has_complex=$(git diff --diff-filter=U | wc -l)

# 도구 선택
if [ $conflict_count -le 3 ] && [ $has_complex -lt 50 ]; then
  # 간단한 충돌 → VS Code
  echo "Using: VS Code (simple conflicts)"
  tool="vscode"
  
elif [ "$has_java" = "yes" ]; then
  # Java 파일 → IntelliJ
  echo "Using: IntelliJ IDEA (Java files)"
  tool="idea"
  
elif [ $conflict_count -gt 10 ]; then
  # 대량 충돌 → KDiff3
  echo "Using: KDiff3 (many conflicts)"
  tool="kdiff3"
  
else
  # 기본 → VS Code
  echo "Using: VS Code (default)"
  tool="vscode"
fi

# 실행
git mergetool --tool=$tool "$@"
EOF

chmod +x scripts/smart-merge.sh

# 사용:
# git merge feature
# ./scripts/smart-merge.sh

# ===== Step 3: 파일별 도구 =====

# 특정 파일 타입별 설정
cat > .gitconfig.local << 'EOF'
[mergetool "image-merge"]
  cmd = /usr/local/bin/image-merge.sh $LOCAL $REMOTE $MERGED

[mergetool "json-merge"]
  cmd = jq -s '.[0] * .[1]' $LOCAL $REMOTE > $MERGED
  trustExitCode = true
EOF

# 이미지 merge
cat > scripts/image-merge.sh << 'EOF'
#!/bin/bash

LOCAL=$1
REMOTE=$2
MERGED=$3

echo "Image conflict: $MERGED"
echo "1. Keep local ($LOCAL)"
echo "2. Keep remote ($REMOTE)"
echo "3. Open in editor"

read -p "Choice [1/2/3]: " choice

case $choice in
  1) cp "$LOCAL" "$MERGED" ;;
  2) cp "$REMOTE" "$MERGED" ;;
  3) open "$LOCAL" "$REMOTE" ;;
esac
EOF

chmod +x scripts/image-merge.sh

# ===== Step 4: 자동 선택 Wrapper =====

cat > git-smart-merge << 'EOF'
#!/bin/bash

# Git merge wrapper

# 일반 merge 실행
git merge "$@"

if [ $? -ne 0 ]; then
  # 충돌 발생
  echo ""
  echo "Conflicts detected!"
  echo "Starting smart merge tool..."
  
  ./scripts/smart-merge.sh
fi
EOF

chmod +x git-smart-merge

# 사용:
# ./git-smart-merge feature
# 자동으로 적절한 도구 실행!

# ===== Step 5: 팀 가이드 =====

cat > docs/TOOL_SELECTION.md << 'EOF'
# Merge Tool Selection Guide

## When to use which tool?

### VS Code (기본)
**Use for:**
- 1-3 files conflict
- Simple changes
- Quick fixes
- Daily development

**How:**
```bash
git mergetool
# (default tool)
```

### IntelliJ IDEA (복잡)
**Use for:**
- Java/Kotlin files
- Refactoring conflicts
- Complex changes
- Need smart features

**How:**
```bash
git mergetool --tool=idea
```

### KDiff3 (대량)
**Use for:**
- 10+ files conflict
- Need precise comparison
- Base tracking important
- Batch operations

**How:**
```bash
git mergetool --tool=kdiff3
```

### Automatic Selection
Use smart script:
```bash
./scripts/smart-merge.sh
```

Automatically selects best tool based on:
- Number of conflicts
- File types
- Complexity

## Tips

### Override for specific file:
```bash
git mergetool --tool=idea src/Main.java
```

### Check current tool:
```bash
git config merge.tool
```

### Temporary change:
```bash
GIT_MERGE_TOOL=kdiff3 git mergetool
```
EOF

# ✅ 유연한 도구 선택 체계!
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: keepBackup=true 방치**

```bash
# 문제
git mergetool
# 해결 후...

ls
# file.js
# file.js.orig  ← 백업 파일!
# file.js.BASE.12345
# file.js.LOCAL.12345
# file.js.REMOTE.12345

# 지저분!
```

**올바른 방법:**
```bash
# 백업 비활성화
git config --global mergetool.keepBackup false

# 기존 백업 제거
git clean -f
```

---

#### ❌ **실수 2: 도구 종료 코드 무시**

```bash
# 문제
git mergetool
# 도구에서 저장 안 하고 종료
# Git은 "해결됨"으로 간주!
```

**올바른 방법:**
```bash
# 신뢰하지 않는 도구
git config mergetool.vscode.trustExitCode false

# 수동 확인
git diff --check
```

---

#### ❌ **실수 3: 임시 파일 혼동**

```bash
# 실수
vim file.js.LOCAL.12345
# 임시 파일 편집!
# $MERGED가 아님!
```

**올바른 방법:**
```bash
# 도구가 자동으로 $MERGED 편집
# 수동 편집 시:
vim file.js  # 원본 파일!
```

---

#### ❌ **실수 4: 도구 없이 설정**

```bash
# 실수
git config --global merge.tool kdiff3
# KDiff3 설치 안 함!

git mergetool
# error: cannot run kdiff3
```

**올바른 방법:**
```bash
# 설치 확인
which kdiff3

# 없으면 설치
brew install --cask kdiff3

# 그 다음 설정
git config --global merge.tool kdiff3
```

---

### 🎯 실습 미션

#### **미션 1: VS Code 설정** 🎯

**목표:** VS Code 완전 설정

```bash
# 준비
# VS Code 설치 필요

# 미션:
# 1. Git 설정
# 2. 충돌 생성
# 3. Merge tool 실행
# 4. 해결
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 설정
git config --global merge.tool vscode
git config --global mergetool.vscode.cmd \
  'code --wait $MERGED'
git config --global mergetool.keepBackup false

# 2. 테스트 저장소
git init test-vscode && cd test-vscode
echo "Base" > file.txt
git add file.txt
git commit -m "Base"

# 3. 충돌 생성
git switch -c feature
echo "Feature" > file.txt
git add file.txt
git commit -m "Feature"

git switch main
echo "Main" > file.txt
git add file.txt
git commit -m "Main"

# 4. Merge (충돌!)
git merge feature

# 5. Merge tool
git mergetool
# VS Code 열림!

# 6. 해결 (VS Code에서)
# [Accept Current] 또는 [Accept Incoming]

# 7. 완료
git commit

# ✅ 완료!
```

</details>

---

#### **미션 2: 여러 도구 비교** 🔧

**목표:** 도구 차이 경험

```bash
# 시나리오:
# 같은 충돌을
# 다른 도구로 해결
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init tool-comparison && cd tool-comparison
echo "Base" > file.txt
git add . && git commit -m "Base"

git switch -c feat1
echo "Feature 1" > file.txt
git add . && git commit -m "Feat1"

git switch main
echo "Main" > file.txt
git add . && git commit -m "Main"

# === Test 1: VS Code ===
git merge feat1
# Conflict!

git mergetool --tool=vscode
# VS Code로 해결

git merge --abort
git checkout file.txt

# === Test 2: KDiff3 ===
# (KDiff3 설치 필요)
git merge feat1
# Conflict!

git mergetool --tool=kdiff3
# KDiff3로 해결

# 비교:
# VS Code: 버튼 클릭, 간단
# KDiff3: 3-way 뷰, 정밀

# ✅ 차이 이해!
```

</details>

---

#### **미션 3: 스마트 스크립트** 🤖

**목표:** 자동 도구 선택

```bash
# 시나리오:
# 충돌 개수에 따라
# 자동으로 도구 선택
```

<details>
<summary>정답 보기</summary>

```bash
# 스크립트 생성
cat > smart-merge.sh << 'EOF'
#!/bin/bash

count=$(git diff --name-only --diff-filter=U | wc -l)

if [ $count -le 3 ]; then
  echo "Using VS Code"
  tool="vscode"
else
  echo "Using KDiff3"
  tool="kdiff3"
fi

git mergetool --tool=$tool
EOF

chmod +x smart-merge.sh

# 테스트 1: 적은 충돌
git init test && cd test
echo "A" > f1.txt
git add . && git commit -m "A"

git switch -c feat
echo "B" > f1.txt
git add . && git commit -m "B"

git switch main
echo "C" > f1.txt
git add . && git commit -m "C"

git merge feat
# 1개 충돌

../smart-merge.sh
# → VS Code!

# ✅ 완료!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 도구 설정 ===
git config --global merge.tool vscode
git config --global mergetool.keepBackup false

# === 실행 ===
git mergetool                      # 기본 도구
git mergetool --tool=kdiff3        # 특정 도구
git mergetool file.js              # 특정 파일

# === 확인 ===
git config merge.tool              # 현재 도구
git mergetool --tool-help          # 사용 가능 도구
```

---

### 🎨 도구 선택 가이드

```
간단한 충돌 (1-3개):
→ VS Code

복잡한 충돌 (리팩토링):
→ IntelliJ

대량 충돌 (10+):
→ KDiff3

정밀 비교 필요:
→ KDiff3 또는 Beyond Compare
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [3.4 Merge 전략](./04-merge-strategies.md)

**다음 학습:**
- [3.6 장기 브랜치 통합](./06-long-running-branch.md)

**관련 주제:**
- [3.2 충돌의 원리](./02-conflict-basics.md)
- [3.3 복잡한 충돌](./03-complex-conflicts.md)

---

<div align="center">

**🎉 축하합니다! Merge Tools를 완전히 마스터했습니다!**

이제 어떤 도구로도 충돌을 쉽게 해결할 수 있습니다!

[⬅️ 이전: Merge 전략](./04-merge-strategies.md) | [README로](../README.md) | [다음: 장기 브랜치 통합 ➡️](./06-long-running-branch.md)

</div>
