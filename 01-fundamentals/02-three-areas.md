# 1.2 Working Directory, Staging Area, Repository

**난이도: ⭐**

> *"Git의 3가지 영역을 이해하면, git add와 git commit이 무엇을 하는지 완벽히 알 수 있습니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 3가지 영역을 알아야 하나?

**실제 개발 시나리오:**
```
상황: 코드를 수정하고 커밋하려는데...

git add .
git commit -m "Fix bug"

팀원: "왜 git add를 먼저 하나요?"
당신: "으... 그냥 그렇게 하는 거 아닌가요?"

팀원: "git add를 안 하면 커밋이 안 되던데, 왜죠?"
당신: "..." (말문이 막힘)
```

**3가지 영역을 이해하면:**
```bash
# 각 명령어가 어떤 영역에 영향을 주는지 정확히 앎
git add      → Working Directory → Staging Area
git commit   → Staging Area → Repository
git restore  → Staging Area → Working Directory

# "왜?"를 설명할 수 있음!
```

> 💡 **핵심:** Git은 3개의 독립적인 영역으로 데이터를 관리합니다.
> 각 영역의 역할과 상호작용을 이해하면 Git 명령어가 보입니다!

---

### 📌 Git의 3가지 영역

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Git Workflow                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌────────────────┐         ┌────────────────┐         ┌─────────────────┐  │
│  │   Working      │         │    Staging     │         │   Repository    │  │
│  │   Directory    │──add─ → │     Area       │─commit→ │   (.git/objects)│  │
│  │                │         │   (Index)      │         │                 │  │
│  │  작업 공간       │←restore─│  준비 영역       │         │   저장소          │  │
│  └────────────────┘         └────────────────┘         └─────────────────┘  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

### 1️⃣ Working Directory (작업 디렉토리)

**"실제로 파일을 수정하는 곳"**

#### **특징:**
- 👀 **눈에 보이는 파일들**
- ✏️ **에디터로 수정 가능**
- 📁 **일반 파일 시스템**
- 🔓 **Git과 무관하게 자유롭게 수정 가능**

#### **위치:**
```bash
/my-project/          ← Working Directory
├── file1.txt
├── file2.txt
└── src/
    └── main.java
```

#### **상태:**
```
Untracked (추적 안 됨)
├─ 새로 생성한 파일
└─ .gitignore에 포함된 파일

Tracked (추적 중)
├─ Unmodified (수정 안 됨)
├─ Modified (수정됨) ⭐
└─ Staged (Staging Area에 있음)
```

#### **실제 예시:**
```bash
# Working Directory 상태
$ ls
file1.txt  file2.txt  src/

# 파일 수정
$ echo "New content" >> file1.txt

# Git 입장:
# file1.txt: Modified (수정됨)
# Working Directory ≠ Repository
```

---

### 2️⃣ Staging Area (준비 영역)

**"다음 커밋에 포함될 변경사항을 준비하는 곳"**

#### **특징:**
- 📋 **커밋 전 대기실**
- 🎯 **선택적 커밋 가능**
- 💾 **.git/index 파일에 저장**
- 🔍 **눈에 안 보임** (git status로만 확인)

#### **위치:**
```bash
.git/index            ← Staging Area (Index 파일)
```

#### **역할:**
```
선택적 커밋 준비
├─ 수정한 파일 중 일부만 선택
├─ 파일의 특정 부분만 선택 (git add -p)
└─ 다음 커밋에 정확히 무엇을 포함할지 결정
```

#### **왜 필요한가?**

**Staging Area가 없다면:**
```bash
# 10개 파일을 수정했는데...
file1.txt  ← 버그 수정
file2.txt  ← 버그 수정
file3.txt  ← 새 기능
file4.txt  ← 새 기능
file5.txt  ← 임시 테스트 코드

# 문제: 커밋을 어떻게 나눌까?
# 버그 수정과 새 기능을 분리해야 하는데...
# Staging Area 없으면 → 10개 전부 한 번에 커밋됨! ❌
```

**Staging Area가 있으면:**
```bash
# 선택적으로 추가!
git add file1.txt file2.txt
git commit -m "Fix bug"

git add file3.txt file4.txt
git commit -m "Add new feature"

# file5.txt는 커밋 안 함 (임시 코드)
# ✅ 논리적으로 분리된 커밋!
```

#### **실제 내용:**
```bash
# .git/index 파일 보기 (바이너리)
git ls-files --stage

# 출력:
# 100644 8d0e41... 0    file1.txt
# 100644 9f4d96... 0    file2.txt
# 100644 a1b2c3... 0    src/main.java

# 형식: [mode] [hash] [stage] [filename]
```

---

### 3️⃣ Repository (저장소)

**"커밋된 스냅샷이 영구 저장되는 곳"**

#### **특징:**
- 💎 **불변 (Immutable)**
- 📜 **전체 히스토리 보관**
- 🗄️ **.git/objects/에 객체로 저장**
- 🔒 **안전하게 보호됨**

#### **위치:**
```bash
.git/objects/         ← Repository
├── 8d/
│   └── 0e41234f24b6da002d962a26c2495ea16a425f
├── 9f/
│   └── 4d96a1b2c3d4e5f6a7b8c9d0e1f2a3b4c5d6
└── pack/
    └── pack-abc123.pack
```

#### **저장 내용:**
```
객체들의 집합
├─ Commit 객체: 스냅샷
├─ Tree 객체: 디렉토리 구조
├─ Blob 객체: 파일 내용
└─ Tag 객체: 특정 커밋 표시
```

#### **영구성:**
```bash
# 커밋 후
git commit -m "Important work"

# 파일 삭제해도
rm -rf *

# Repository에는 여전히 존재!
git checkout HEAD -- .
# 모든 파일 복구됨 ✅
```

---

### 🔄 3가지 영역의 상호작용

#### **전체 흐름:**

```
[1. 파일 생성/수정]
Working Directory
      ↓
   git add
      ↓
[2. 커밋 준비]
Staging Area
      ↓
  git commit
      ↓
[3. 영구 저장]
Repository
```

#### **역방향 흐름:**

```
Repository
      ↓
  git restore --staged (unstage)
      ↓
Staging Area
      ↓
  git restore (discard)
      ↓
Working Directory
```

---

### 📊 파일 상태의 라이프사이클

```
                    Untracked
                        │
                    git add
                        ↓
┌─────────────────────────────────────────┐
│           Tracked Files                 │
├─────────────────────────────────────────┤
│                                         │
│  Unmodified ─edit→ Modified ─add→ Staged│
│      ↑                                 ││
│      └─────────commit──────────────────┘│
│      ↑                                  │
│  git rm ─→ Untracked                    │
└─────────────────────────────────────────┘
```

**각 상태 설명:**

1. **Untracked (추적 안 됨)**
   - 새로 생성된 파일
   - Git이 모르는 파일
   - `git add`로 추적 시작

2. **Unmodified (수정 안 됨)**
   - 커밋 후 변경 없음
   - Working Directory = Repository
   - 파일 수정하면 Modified로

3. **Modified (수정됨)**
   - 파일 수정됨
   - Working Directory ≠ Repository
   - `git add`로 Staged로

4. **Staged (준비됨)**
   - 다음 커밋에 포함될 예정
   - Staging Area에 존재
   - `git commit`으로 Unmodified로

---

### 🔍 각 영역을 파일로 보기

#### **Working Directory 내용:**
```bash
# 실제 파일 시스템
cat file1.txt
# 수정된 내용
```

#### **Staging Area 내용:**
```bash
# Index에 저장된 내용 확인
git ls-files --stage
git diff --cached  # Staging vs Repository 비교
```

#### **Repository 내용:**
```bash
# 최신 커밋의 내용
git show HEAD:file1.txt
# 또는
git cat-file -p HEAD:file1.txt
```

---

### 💡 3영역 실험: 직접 확인하기

```bash
# 1. 새 저장소 생성
mkdir three-areas-test && cd three-areas-test
git init

# 2. 파일 생성 (Working Directory에만 존재)
echo "Version 1" > file.txt
git status
# Untracked files: file.txt

# 3. Staging Area에 추가
git add file.txt
git status
# Changes to be committed: file.txt

# 4. Staging Area 내용 확인
git ls-files --stage
# 100644 abc123... 0    file.txt

# 5. Working Directory 수정
echo "Version 2" > file.txt
git status
# Changes to be committed: file.txt (Version 1)
# Changes not staged: file.txt (Version 2)

# 6. 3영역이 모두 다름!
cat file.txt                    # Working: Version 2
git diff --cached file.txt      # Staging: Version 1
git show HEAD:file.txt 2>/dev/null || echo "없음"  # Repository: 없음

# 7. 커밋
git commit -m "Add file"

# 8. 이제 Repository에도 존재
git show HEAD:file.txt
# Version 1

# 9. 최종 상태
# Working Directory: Version 2 (Modified)
# Staging Area: Version 1 (Committed)
# Repository: Version 1 (Committed)
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 영역 간 이동 명령어

#### **Working Directory → Staging Area**

```bash
# 1. 파일 추가
git add <file>

# 2. 모든 파일 추가
git add .
git add -A   # 삭제된 파일까지 포함

# 3. 대화형 추가 (선택적)
git add -i

# 4. 부분 추가 (hunk 단위)
git add -p <file>

# 5. 강제 추가 (.gitignore 무시)
git add -f <file>
```

---

#### **Staging Area → Repository**

```bash
# 1. 커밋
git commit -m "message"

# 2. 커밋 + 메시지 에디터
git commit

# 3. Staging 건너뛰고 바로 커밋 (Modified 파일만)
git commit -a -m "message"
# = git add (Modified만) + git commit

# 4. 빈 커밋 (파일 변경 없이)
git commit --allow-empty -m "Empty commit"

# 5. 마지막 커밋 수정
git commit --amend
```

---

#### **Staging Area → Working Directory (Unstage)**

```bash
# 1. Unstage (Staging에서 제거)
git restore --staged <file>

# 2. 모든 파일 unstage
git restore --staged .

# 3. 옛날 방식 (Git 2.23 이전)
git reset HEAD <file>
```

---

#### **Repository → Working Directory**

```bash
# 1. Working Directory 변경사항 버리기
git restore <file>

# 2. 특정 커밋에서 가져오기
git restore --source=HEAD~1 <file>

# 3. 모든 변경사항 버리기
git restore .

# 4. 옛날 방식
git checkout -- <file>
```

---

#### **Repository → Staging Area**

```bash
# 1. 특정 커밋을 Staging으로
git restore --staged --source=HEAD~1 <file>

# 2. Reset으로 (옛날 방식)
git reset --mixed HEAD~1
```

---

### 📊 명령어 비교표

#### **영역 간 이동**

| 시작 | 끝 | 명령어 | 설명 |
|:-----|:---|:-------|:-----|
| Working | Staging | `git add` | Stage 추가 |
| Staging | Repository | `git commit` | 커밋 |
| Staging | Working | `git restore --staged` | Unstage |
| Repository | Working | `git restore` | 변경사항 버리기 |
| Repository | Staging | `git restore --staged --source=` | 특정 커밋을 Stage로 |

---

#### **상태 확인**

| 명령어 | 확인 내용 | 출력 |
|:------|:---------|:-----|
| `git status` | 전체 상태 | Untracked, Modified, Staged |
| `git diff` | Working vs Staging | 수정했지만 Stage 안 된 것 |
| `git diff --staged` | Staging vs Repository | Stage됐지만 커밋 안 된 것 |
| `git diff HEAD` | Working vs Repository | 전체 변경사항 |
| `git ls-files --stage` | Staging 내용 | Index 파일 내용 |

---

### ⚡ 실행 결과 시각화

#### **Scenario: 파일 수정 → add → 다시 수정**

**초기 상태:**
```
Working Directory    Staging Area    Repository
┌──────────────┐    ┌──────────┐    ┌──────────┐
│ file.txt     │    │ (empty)  │    │ (empty)  │
│ "Version 1"  │    └──────────┘    └──────────┘
└──────────────┘
```

**`git add file.txt` 후:**
```
Working Directory    Staging Area         Repository
┌──────────────┐    ┌───────────────┐    ┌──────────┐
│ file.txt     │ ─→ │ file.txt      │    │ (empty)  │
│ "Version 1"  │    │ "Version 1"   │    └──────────┘
└──────────────┘    └───────────────┘
```

**파일 다시 수정 후:**
```
Working Directory    Staging Area         Repository
┌──────────────┐    ┌───────────────┐    ┌──────────┐
│ file.txt     │    │ file.txt      │    │ (empty)  │
│ "Version 2"  │ ✗  │ "Version 1"   │    └──────────┘
└──────────────┘    └───────────────┘
      ↑
   Modified!
```

**`git commit -m "msg"` 후:**
```
Working Directory    Staging Area         Repository
┌──────────────┐    ┌───────────────┐    ┌────────────────┐
│ file.txt     │    │ file.txt      │ ─→ │ file.txt       │
│ "Version 2"  │ ✗  │ "Version 1"   │    │ "Version 1"    │
└──────────────┘    └───────────────┘    └────────────────┘
      ↑                     ↑
   Modified!          Committed!
```

**최종 상태:**
```
git status
# Changes not staged for commit:
#   modified: file.txt

# Working Directory: Version 2
# Staging Area: Version 1 (committed)
# Repository: Version 1 (committed)
```

---

### 🎯 diff 명령어 완전 이해

```bash
# 1. Working vs Staging
git diff
# "Stage 안 된 변경사항"

# 2. Staging vs Repository
git diff --staged
git diff --cached  # 같은 명령어
# "커밋 안 된 변경사항"

# 3. Working vs Repository
git diff HEAD
# "전체 변경사항"

# 4. 특정 커밋 간 비교
git diff HEAD~1 HEAD
# "최근 1개 커밋의 변경사항"

# 5. 특정 파일만
git diff <file>
git diff --staged <file>
```

**시각화:**
```
         git diff          git diff --staged
      ◄───────────►     ◄──────────────►
Working ────→ Staging ────→ Repository
      ◄──────────────────────────►
              git diff HEAD
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 선택적 커밋

**목표:** 여러 파일 중 일부만 선택해서 커밋

#### **Problem: 10개 파일을 수정했는데, 2개씩 나눠 커밋하고 싶다!**

```bash
# 상황: 10개 파일 수정
for i in {1..10}; do
  echo "Modified content $i" > file$i.txt
done

git status
# 10개 파일 Modified

# 목표: 2개씩 5개의 커밋으로 나누기
```

#### **Solution: Staging Area 활용**

```bash
# 1. 첫 번째 커밋 (file1, file2)
git add file1.txt file2.txt
git commit -m "Update files 1-2"

# 2. 두 번째 커밋 (file3, file4)
git add file3.txt file4.txt
git commit -m "Update files 3-4"

# 3. 세 번째 커밋 (file5, file6)
git add file5.txt file6.txt
git commit -m "Update files 5-6"

# 4. 네 번째 커밋 (file7, file8)
git add file7.txt file8.txt
git commit -m "Update files 7-8"

# 5. 다섯 번째 커밋 (file9, file10)
git add file9.txt file10.txt
git commit -m "Update files 9-10"

# 결과: 5개의 논리적으로 분리된 커밋! ✅
git log --oneline
```

**배운 점:**
- ✅ Staging Area로 선택적 커밋 가능
- ✅ 논리적으로 관련된 변경사항을 묶을 수 있음
- ✅ 커밋 히스토리가 깔끔해짐

---

### 💼 Scenario 2: 파일의 일부만 커밋

**목표:** 한 파일의 특정 부분만 선택해서 커밋

#### **Problem: 한 파일에 버그 수정 + 새 기능을 함께 수정했다!**

```bash
# 1. 파일 생성
cat > calculator.py << 'EOF'
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b  # 버그: 잘못된 구현

def multiply(a, b):
    return a * b

def divide(a, b):
    return a / b  # 새 기능
EOF

git add calculator.py
git commit -m "Initial version"

# 2. 파일 수정 (버그 수정 + 새 기능)
cat > calculator.py << 'EOF'
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b  # 버그 수정: 올바른 구현

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        return "Error"  # 새 기능: 0 나누기 처리
    return a / b
EOF

# 3. 문제: 버그 수정과 새 기능을 별도 커밋으로 나누고 싶다!
```

#### **Solution: git add -p (patch mode)**

```bash
# 대화형 모드로 부분 선택
git add -p calculator.py

# Git이 각 변경사항(hunk)을 보여줌:
# 
# diff --git a/calculator.py b/calculator.py
# @@ -3,3 +3,3 @@
#  def subtract(a, b):
# -    return a - b  # 버그: 잘못된 구현
# +    return a - b  # 버그 수정: 올바른 구현
# 
# (1/2) Stage this hunk [y,n,q,a,d,s,e,?]?

# y 입력 (버그 수정 부분만 stage)

# 다음 hunk:
# @@ -8,2 +8,4 @@
#  def divide(a, b):
# +    if b == 0:
# +        return "Error"  # 새 기능
#      return a / b
#
# (2/2) Stage this hunk [y,n,q,a,d,s,e,?]?

# n 입력 (새 기능은 stage 안 함)

# 커밋
git commit -m "Fix: subtract 버그 수정"

# 나머지 변경사항 커밋
git add -p  # 이번엔 새 기능 선택
git commit -m "Feat: divide 0 나누기 처리"
```

**add -p 옵션:**
- `y` - yes, 이 hunk stage
- `n` - no, 이 hunk 건너뜀
- `s` - split, hunk를 더 작게 나눔
- `e` - edit, 수동으로 편집
- `q` - quit, 종료
- `?` - help, 도움말

---

### 💼 Scenario 3: 3영역이 모두 다른 상태

**목표:** Working, Staging, Repository가 모두 다른 내용 만들기

#### **Problem: 각 영역의 독립성 이해하기**

```bash
# 1. 초기 커밋
echo "Version 1" > multi.txt
git add multi.txt
git commit -m "Version 1"

# 현재 상태:
# Working: Version 1
# Staging: Version 1
# Repository: Version 1

# 2. 파일 수정 + Stage
echo "Version 2" > multi.txt
git add multi.txt

# 현재 상태:
# Working: Version 2
# Staging: Version 2
# Repository: Version 1

# 3. 파일 다시 수정 (Stage 안 함!)
echo "Version 3" > multi.txt

# 최종 상태:
# Working: Version 3 ⭐
# Staging: Version 2 ⭐
# Repository: Version 1 ⭐
```

#### **Solution: 각 영역 확인하기**

```bash
# 1. Working Directory 확인
cat multi.txt
# Version 3

# 2. Staging Area 확인
git diff --cached multi.txt
# -Version 1
# +Version 2

# 또는
git show :multi.txt
# Version 2

# 3. Repository 확인
git show HEAD:multi.txt
# Version 1

# 4. git status로 전체 확인
git status
# Changes to be committed:
#   modified: multi.txt (Version 2)
# 
# Changes not staged for commit:
#   modified: multi.txt (Version 3)

# 5. 이제 커밋하면?
git commit -m "Version 2"

# Working: Version 3 (여전히 Modified)
# Staging: Version 2 (커밋됨)
# Repository: Version 2 (방금 커밋)

# 6. 다시 add + commit
git add multi.txt
git commit -m "Version 3"

# Working: Version 3
# Staging: Version 3
# Repository: Version 3
# 모두 동기화됨! ✅
```

---

### 💼 Scenario 4: Unstage와 Discard 차이

**목표:** 변경사항을 되돌리는 2가지 방법 이해

#### **Problem: Stage한 파일을 되돌리고 싶다!**

```bash
# 1. 파일 수정
echo "New content" > important.txt
git add important.txt

# 2. 실수를 깨달음! 되돌리고 싶다!
```

#### **Solution 1: Unstage (Staging → Working)**

```bash
# Staging Area에서만 제거
git restore --staged important.txt

# 결과:
# - Working Directory: "New content" (그대로!)
# - Staging Area: 비어있음
# - 수정사항은 유지됨

# 다시 수정 가능!
```

#### **Solution 2: Discard (완전히 버리기)**

```bash
# 파일 수정
echo "New content" > important.txt
git add important.txt

# Unstage
git restore --staged important.txt

# Working Directory 변경사항도 버리기
git restore important.txt

# 결과:
# - Working Directory: 최신 커밋 내용으로 복원
# - 모든 변경사항 사라짐! ⚠️
```

**차이점:**
```bash
# Unstage만
git restore --staged <file>
→ Staging Area만 비움
→ Working Directory는 그대로

# Discard (위험!)
git restore <file>
→ Working Directory 변경사항 버림
→ 복구 불가능! ⚠️

# 둘 다
git restore --staged <file>
git restore <file>
→ 완전히 초기화
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: git add . 남발**

```bash
# 잘못된 습관
git add .
git commit -m "Update"

# 문제:
# - 임시 파일까지 커밋됨
# - .env 같은 민감한 파일 포함 위험
# - 논리적으로 무관한 변경사항이 섞임
```

**올바른 방법:**
```bash
# 1. 상태 확인
git status

# 2. 파일 선택적으로 add
git add file1.txt file2.txt

# 3. 다시 확인
git status

# 4. 커밋
git commit -m "Specific changes"

# 또는 대화형 모드
git add -i
git add -p
```

---

#### ❌ **실수 2: Staging Area를 무시하고 바로 커밋**

```bash
# 위험한 명령어
git commit -a -m "Quick fix"

# -a 옵션:
# = git add (Modified 파일만) + git commit
# 
# 문제:
# - Untracked 파일은 포함 안 됨 (착각 위험)
# - 선택적 커밋 불가능
# - 급하게 커밋하다가 실수하기 쉬움
```

**올바른 방법:**
```bash
# 명시적으로 add
git add <files>

# 확인
git diff --staged

# 커밋
git commit -m "message"

# 신중하게!
```

---

#### ❌ **실수 3: restore와 reset 혼동**

```bash
# Git 2.23 이전: reset 사용
git reset HEAD <file>    # Unstage
git checkout -- <file>   # Discard

# Git 2.23 이후: restore 사용 (더 명확!)
git restore --staged <file>  # Unstage
git restore <file>           # Discard

# 혼동하지 말 것!
# reset: 커밋 히스토리 조작 (위험!)
# restore: 파일 복원 (안전)
```

**암기법:**
```
restore --staged  → Staging에서 복원
restore           → Working에서 복원

reset --soft   → Commit 취소
reset --mixed  → Commit + Staging 취소
reset --hard   → 전부 취소 (위험!)
```

---

#### ❌ **실수 4: diff --staged를 까먹음**

```bash
# 잘못된 확인
git diff
# (아무것도 안 나옴...)
# "변경사항이 없나?"

# 실제론 이미 Stage됨!
git diff --staged
# (변경사항이 여기 있음!)
```

**올바른 확인 순서:**
```bash
# 1. 전체 상태
git status

# 2. Unstaged 변경사항
git diff

# 3. Staged 변경사항
git diff --staged

# 4. 전체 변경사항
git diff HEAD
```

---

### 🎯 실습 미션

#### **미션 1: 3영역 마스터** 🎯

**목표:** 3영역을 완전히 다른 상태로 만들기

```bash
# 1. 저장소 생성
mkdir three-areas-master && cd three-areas-master
git init

# 2. 미션:
# - Repository에 "V1" 커밋
# - Staging Area에 "V2" 준비
# - Working Directory에 "V3" 작성
# - 각 영역 내용 확인하기

# 힌트:
# git show HEAD:file.txt        → Repository
# git show :file.txt             → Staging
# cat file.txt                   → Working
```

<details>
<summary>정답 보기</summary>

```bash
# 1. V1 커밋
echo "V1" > file.txt
git add file.txt
git commit -m "Version 1"

# 2. V2 Stage
echo "V2" > file.txt
git add file.txt

# 3. V3 Working
echo "V3" > file.txt

# 4. 확인
git show HEAD:file.txt  # V1
git show :file.txt      # V2
cat file.txt            # V3

# 성공! ✅
```

</details>

---

#### **미션 2: 선택적 커밋 달인** 🎨

**목표:** 10개 파일을 2-3-5 패턴으로 커밋

```bash
# 1. 10개 파일 생성
for i in {1..10}; do
  echo "Content $i" > file$i.txt
done

# 2. 미션:
# - 1st commit: file1, file2 (2개)
# - 2nd commit: file3, file4, file5 (3개)
# - 3rd commit: file6-10 (5개)

# 3. 검증:
# git log --oneline
# 3개의 커밋이 있어야 함
```

<details>
<summary>정답 보기</summary>

```bash
# 1st commit
git add file1.txt file2.txt
git commit -m "Add files 1-2"

# 2nd commit
git add file3.txt file4.txt file5.txt
git commit -m "Add files 3-5"

# 3rd commit
git add file6.txt file7.txt file8.txt file9.txt file10.txt
git commit -m "Add files 6-10"

# 또는
git add file{6..10}.txt
git commit -m "Add files 6-10"

# 검증
git log --oneline
# abc123 Add files 6-10
# def456 Add files 3-5
# ghi789 Add files 1-2
```

</details>

---

#### **미션 3: Patch Mode 마스터** 🔧

**목표:** 한 파일의 여러 부분을 나눠 커밋

```bash
# 1. 파일 생성
cat > code.py << 'EOF'
def function1():
    pass

def function2():
    pass

def function3():
    pass
EOF

git add code.py
git commit -m "Initial functions"

# 2. 파일 수정 (3곳 수정)
cat > code.py << 'EOF'
def function1():
    return "Updated 1"  # 변경 1

def function2():
    return "Updated 2"  # 변경 2

def function3():
    return "Updated 3"  # 변경 3
EOF

# 3. 미션:
# - function1만 커밋
# - function2만 커밋
# - function3만 커밋
# - 총 3개의 커밋으로 나누기

# 힌트: git add -p
```

<details>
<summary>정답 보기</summary>

```bash
# 1st commit (function1)
git add -p code.py
# 첫 번째 hunk: y
# 두 번째 hunk: n
# 세 번째 hunk: n
git commit -m "Update function1"

# 2nd commit (function2)
git add -p code.py
# 첫 번째 hunk: y
# 두 번째 hunk: n
git commit -m "Update function2"

# 3rd commit (function3)
git add code.py
git commit -m "Update function3"

# 검증
git log --oneline
# 3개의 커밋 확인! ✅
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === Working → Staging ===
git add <file>           # 파일 추가
git add .                # 현재 디렉토리 전체
git add -A               # 삭제 포함 전체
git add -p               # 부분 선택

# === Staging → Repository ===
git commit -m "msg"      # 커밋
git commit               # 에디터로 메시지
git commit -a -m "msg"   # add + commit (Modified만)
git commit --amend       # 마지막 커밋 수정

# === Staging → Working (Unstage) ===
git restore --staged <file>   # Unstage
git restore --staged .        # 전체 unstage

# === Repository → Working (Discard) ===
git restore <file>       # 변경사항 버리기 (위험!)
git restore .            # 전체 버리기

# === 상태 확인 ===
git status               # 전체 상태
git diff                 # Working vs Staging
git diff --staged        # Staging vs Repository
git diff HEAD            # Working vs Repository
git ls-files --stage     # Staging 내용
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [1.1 Git 객체 모델](./01-object-model.md)

**다음 학습:**
- [1.3 HEAD와 참조](./03-head-and-refs.md)
- [1.4 커밋 해부하기](./04-commit-anatomy.md)

**실전 응용:**
- [5.1 Reset 모드](../05-recovery/01-reset-modes.md)
- [5.3 Restore](../05-recovery/03-restore.md)

---

### 💡 추가 팁

#### **유용한 Alias**

```bash
# .gitconfig에 추가
[alias]
  # 상태 확인
  st = status -sb
  
  # Staging 확인
  staged = diff --cached
  
  # 전체 diff
  diffall = diff HEAD
  
  # Unstage
  unstage = restore --staged
  
  # Discard
  discard = restore
  
  # add -p 간단히
  ap = add -p
```

**사용:**
```bash
git st          # status
git staged      # Staging 확인
git unstage .   # 전체 unstage
git ap file.txt # patch mode
```

---

#### **실전 팁**

**1. 커밋 전 항상 확인**
```bash
git status
git diff --staged
# "정말 이것만 커밋할까?" 확인!
```

**2. 대화형 모드 활용**
```bash
git add -i
# 메뉴 방식으로 편리하게
```

**3. 파일별 diff 확인**
```bash
git diff --stat        # 요약
git diff --name-only   # 파일명만
git diff --word-diff   # 단어 단위
```

---

<div align="center">

**🎉 축하합니다! Git의 3가지 영역을 완전히 마스터했습니다!**

이제 git add와 git commit이 무엇을 하는지 완벽히 이해했습니다!

[⬅️ 이전: 객체 모델](./01-object-model.md) | [README로](../README.md) | [다음: HEAD와 참조 ➡️](./03-head-and-refs.md)

</div>
