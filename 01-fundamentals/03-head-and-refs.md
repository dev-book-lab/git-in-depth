# 1.3 HEAD와 참조 (References)

**난이도: ⭐⭐**

> *"HEAD는 단순한 포인터입니다. 하지만 Git의 거의 모든 동작은 HEAD를 중심으로 일어납니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 HEAD를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 커밋을 체크아웃했는데 이상한 경고가 뜬다!

$ git checkout abc123

Warning: You are in 'detached HEAD' state.
You can look around, make experimental changes and commit them...

팀원: "Detached HEAD가 뭐에요? 위험한 거 아니에요?"
당신: "으... 위험한 건가...?" (당황)

팀원: "HEAD가 대체 뭔데 detached가 되는 거죠?"
당신: "..." (설명 못함)
```

**HEAD를 이해하면:**
```bash
# HEAD = 현재 위치를 가리키는 포인터

# 정상 상태 (Attached HEAD)
HEAD → main → commit123

# Detached HEAD
HEAD → commit123 (브랜치 없음!)

# 왜 위험한지, 어떻게 복구하는지 정확히 앎!
```

> 💡 **핵심:** HEAD는 "지금 어디 있는가"를 가리키는 포인터입니다.
> Git의 모든 명령어는 HEAD를 기준으로 동작합니다!

---

### 📌 HEAD란 무엇인가?

#### **HEAD의 정의**

```
HEAD = "현재 체크아웃된 커밋을 가리키는 포인터"
```

**간단히 말하면:**
- 📍 **"지금 여기"** 를 나타내는 표시
- 🎯 **"현재 작업 중인 커밋"**
- 🔗 **대부분의 경우 브랜치를 가리킴**

---

#### **HEAD의 실체**

```bash
# HEAD는 파일이다!
cat .git/HEAD

# 출력 (일반적인 경우):
ref: refs/heads/main

# 의미:
# HEAD → refs/heads/main → commit_hash
```

**시각화:**
```
.git/HEAD
    ↓ (symbolic reference)
refs/heads/main
    ↓ (contains hash)
abc123def456... (actual commit)
```

---

### 🔍 HEAD의 두 가지 상태

#### **1️⃣ Attached HEAD (정상 상태)**

**"HEAD가 브랜치를 가리키는 상태"**

```bash
# 현재 상태 확인
cat .git/HEAD
# ref: refs/heads/main

# 의미
HEAD → main → commit_abc123
```

**특징:**
- ✅ **가장 일반적인 상태**
- ✅ **커밋하면 브랜치가 자동으로 이동**
- ✅ **안전함**

**시각화:**
```
        HEAD
         ↓
       main
         ↓
    commit_C ← 최신
         ↓
    commit_B
         ↓
    commit_A
```

**커밋 후:**
```bash
git commit -m "New commit"

# HEAD와 main이 함께 이동!
        HEAD
         ↓
       main
         ↓
    commit_D ← 새 커밋!
         ↓
    commit_C
         ↓
    commit_B
```

---

#### **2️⃣ Detached HEAD (분리된 상태)**

**"HEAD가 직접 커밋을 가리키는 상태"**

```bash
# 특정 커밋 체크아웃
git checkout abc123

# HEAD 확인
cat .git/HEAD
# abc123def456...  ← 브랜치가 아닌 커밋 hash!
```

**특징:**
- ⚠️ **HEAD가 브랜치를 거치지 않고 커밋 직접 가리킴**
- ⚠️ **커밋해도 브랜치가 안 움직임**
- ⚠️ **커밋이 "공중에 뜸" (잃어버릴 위험)**

**시각화:**
```
       main
         ↓
    commit_C ← main은 여기
         ↓
    commit_B ← HEAD는 여기!
         ↓      (detached)
    commit_A
```

**커밋 후:**
```bash
git commit -m "Experimental"

# HEAD만 이동, main은 그대로!
       main
         ↓
    commit_C
         ↓
    commit_B
         ↓     commit_X ← HEAD (고아 커밋!)
    commit_A      ↑
                  (브랜치 없음)
```

**위험성:**
```bash
# 다른 브랜치로 이동하면?
git checkout main

# commit_X는 참조가 없어짐!
# 30일 후 Garbage Collection으로 삭제됨! ⚠️
```

---

### 📂 참조 (References)의 종류

Git의 모든 참조는 `.git/refs/` 디렉토리에 저장됩니다:

```bash
.git/refs/
├── heads/           ← 로컬 브랜치
│   ├── main
│   ├── develop
│   └── feature-x
├── remotes/         ← 원격 브랜치
│   └── origin/
│       ├── main
│       └── develop
└── tags/            ← 태그
    ├── v1.0
    └── v2.0
```

---

#### **1️⃣ refs/heads/ (로컬 브랜치)**

**역할:** 로컬 브랜치의 최신 커밋을 가리킴

```bash
# main 브랜치 확인
cat .git/refs/heads/main
# abc123def456...

# 의미: main은 abc123 커밋을 가리킴
```

**실제 내용:**
```bash
# .git/refs/heads/main 파일 내용
abc123def456789...  ← 그냥 커밋 Hash!
```

**브랜치의 본질:**
```
브랜치 = 커밋을 가리키는 포인터 (단순한 파일!)
```

---

#### **2️⃣ refs/remotes/ (원격 브랜치)**

**역할:** 원격 저장소의 브랜치 상태를 추적

```bash
# 원격 main 브랜치
cat .git/refs/remotes/origin/main
# xyz789abc123...

# 의미: 마지막 fetch 시점의 origin/main 상태
```

**로컬 vs 원격:**
```
refs/heads/main          ← 내 로컬 main
refs/remotes/origin/main ← 원격 저장소의 main
```

**차이점:**
```bash
# 로컬에서 커밋
git commit -m "Local work"
# refs/heads/main만 이동
# refs/remotes/origin/main은 그대로

# Push해야 원격도 업데이트
git push
# 이제 둘 다 같은 커밋을 가리킴
```

---

#### **3️⃣ refs/tags/ (태그)**

**역할:** 특정 커밋에 영구적인 이름 부여

```bash
# v1.0 태그
cat .git/refs/tags/v1.0
# def456abc789...

# 의미: v1.0은 def456 커밋
```

**브랜치 vs 태그:**
```
브랜치: 커밋하면 이동함 (동적)
태그:   절대 이동 안 함 (정적)
```

---

### 🔗 Symbolic Reference (심볼릭 참조)

**"참조를 가리키는 참조"**

#### **HEAD는 대표적인 Symbolic Reference**

```bash
# HEAD 파일 내용
cat .git/HEAD
# ref: refs/heads/main

# 의미:
# HEAD → refs/heads/main → commit_hash
```

**일반 참조 vs Symbolic 참조:**
```
일반 참조 (refs/heads/main):
├─ 파일 내용: abc123... (커밋 hash)
└─ 직접 커밋을 가리킨다

Symbolic 참조 (HEAD):
├─ 파일 내용: ref: refs/heads/main
└─ 다른 참조를 가리킨다
```

---

### 🎯 상대 참조 (Relative References)

**"커밋을 상대적으로 가리키기"**

#### **1️⃣ ~ (틀데) - 부모 따라가기**

```bash
HEAD~1  # HEAD의 1세대 부모
HEAD~2  # HEAD의 2세대 부모
HEAD~3  # HEAD의 3세대 부모
```

**시각화:**
```
    HEAD (commit_D)
      ↓
HEAD~1 (commit_C)
      ↓
HEAD~2 (commit_B)
      ↓
HEAD~3 (commit_A)
```

---

#### **2️⃣ ^ (캐럿) - 부모 선택하기**

**Merge 커밋의 경우 부모가 2개!**

```bash
HEAD^1  # 첫 번째 부모 (main 쪽)
HEAD^2  # 두 번째 부모 (branch 쪽)
```

**시각화:**
```
        HEAD (merge commit)
         ↙        ↘
    HEAD^1      HEAD^2
   (main)      (feature)
      ↓            ↓
   commit_C    commit_X
```

---

#### **3️⃣ 조합하기**

```bash
HEAD~2^2    # 2세대 부모의 두 번째 부모
HEAD^^      # HEAD^1^1 (조부모)
HEAD~~~     # HEAD~1~1~1 (증증조부모)
```

**복잡한 예시:**
```
    M (HEAD)      ← Merge commit
   ↙  ↘
  D    X
  ↓    ↓
  C    W
  ↓   ↙ ↘
  B  V   Y
  ↓
  A

HEAD~1   = D
HEAD^2   = X
HEAD~1~1 = C
HEAD^2~1 = W
HEAD^2^1 = V
HEAD^2^2 = Y
```

---

### 🔍 참조 해석 (Reference Resolution)

**Git이 참조를 찾는 순서:**

```bash
# 예: git show develop

1. .git/refs/heads/develop        ← 로컬 브랜치
2. .git/refs/tags/develop          ← 태그
3. .git/refs/remotes/develop       ← 원격 브랜치
4. .git/refs/remotes/develop/HEAD  ← 원격 HEAD
```

**명확하게 지정:**
```bash
git show refs/heads/develop     # 로컬 브랜치
git show refs/tags/develop      # 태그
git show refs/remotes/develop   # 원격 브랜치
```

---

### 📝 .git/HEAD의 변화 추적

**다양한 명령어가 HEAD를 어떻게 바꾸는지:**

```bash
# 1. 초기 상태
cat .git/HEAD
# ref: refs/heads/main

# 2. 브랜치 전환
git switch develop
cat .git/HEAD
# ref: refs/heads/develop

# 3. 커밋 체크아웃 (Detached HEAD)
git checkout abc123
cat .git/HEAD
# abc123def456...

# 4. 브랜치 다시 체크아웃
git switch main
cat .git/HEAD
# ref: refs/heads/main

# 5. Rebase 중
git rebase develop
cat .git/HEAD
# (rebase 중에는 특수 상태)
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 HEAD 관련 명령어

#### **1️⃣ HEAD 확인하기**

```bash
# 현재 HEAD 위치
git rev-parse HEAD
# abc123def456...

# HEAD가 가리키는 브랜치
git symbolic-ref HEAD
# refs/heads/main

# HEAD의 symbolic reference 해석
cat .git/HEAD
# ref: refs/heads/main

# 짧은 커밋 Hash
git rev-parse --short HEAD
# abc123

# 현재 브랜치명만
git branch --show-current
# main
```

---

#### **2️⃣ HEAD 이동하기**

```bash
# 브랜치로 이동 (Attached HEAD)
git switch main
git checkout main

# 커밋으로 이동 (Detached HEAD)
git checkout abc123
git checkout HEAD~3

# 태그로 이동 (Detached HEAD)
git checkout v1.0

# 이전 브랜치로
git switch -
git checkout -
```

---

#### **3️⃣ 참조 조작하기**

```bash
# 브랜치 생성 (HEAD 기준)
git branch feature
# refs/heads/feature 생성

# 브랜치를 특정 커밋으로
git branch feature abc123

# 브랜치 이동 (강제)
git branch -f main HEAD~3
# main을 3커밋 뒤로 이동

# 브랜치 삭제
git branch -d feature
# refs/heads/feature 삭제
```

---

#### **4️⃣ 상대 참조 활용**

```bash
# 1커밋 뒤로 reset
git reset HEAD~1

# 3커밋 뒤 파일 보기
git show HEAD~3:file.txt

# 부모 커밋과 비교
git diff HEAD^

# Merge 커밋의 두 번째 부모
git show HEAD^2
```

---

### 📊 명령어 비교표

#### **HEAD 이동 명령어**

| 명령어 | HEAD 상태 | 브랜치 | Working Directory |
|:------|:----------|:-------|:------------------|
| `git switch main` | Attached | main | 변경됨 |
| `git checkout main` | Attached | main | 변경됨 |
| `git checkout abc123` | Detached | 없음 | 변경됨 |
| `git reset --hard HEAD~1` | 그대로 | 1커밋 뒤로 | 변경됨 |
| `git branch -f main abc` | 그대로 | main만 이동 | 안 바뀜 |

---

#### **참조 종류별 비교**

| 참조 타입 | 위치 | 이동 여부 | 용도 |
|:---------|:-----|:----------|:-----|
| **HEAD** | `.git/HEAD` | 매우 자주 | 현재 위치 |
| **브랜치** | `.git/refs/heads/` | 커밋 시 | 개발 라인 |
| **원격 브랜치** | `.git/refs/remotes/` | fetch 시 | 원격 추적 |
| **태그** | `.git/refs/tags/` | 절대 안 함 | 버전 표시 |

---

### ⚡ 실행 결과 시각화

#### **Scenario: Detached HEAD 발생**

**초기 상태:**
```
        HEAD
         ↓
       main
         ↓
    commit_C
         ↓
    commit_B
         ↓
    commit_A
```

**`git checkout HEAD~2` 실행:**
```
       main
         ↓
    commit_C
         ↓
    commit_B
         ↓
    commit_A ← HEAD (detached!)
```

**파일 시스템 변화:**
```bash
# 이전
cat .git/HEAD
# ref: refs/heads/main

# 이후
cat .git/HEAD
# a1b2c3d4... (commit_A의 hash)
```

---

#### **Scenario: Detached HEAD에서 커밋**

```
# 1. Detached HEAD 상태
       main
         ↓
    commit_C
         ↓
    commit_B ← HEAD
         ↓
    commit_A

# 2. 커밋 생성
git commit -m "Experimental"

       main
         ↓
    commit_C
         ↓
    commit_B
         ↓     commit_X ← HEAD
    commit_A

# 3. main으로 돌아가면?
git switch main

       main, HEAD
         ↓
    commit_C
         ↓
    commit_B
         ↓
    commit_A
    
    commit_X ← 고아! (참조 없음)
    
# 4. 30일 후 GC로 삭제됨!
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: Detached HEAD 안전하게 활용

**목표:** 과거 커밋 탐색 후 안전하게 돌아오기

#### **Problem: 과거 코드를 보고 싶은데, 실수할까 두렵다!**

```bash
# 상황: 2주 전 코드를 보고 싶음
git log --oneline
# abc123 (HEAD -> main) Latest
# def456 Last week
# ghi789 Two weeks ago ← 이거 보고 싶음
# ...
```

#### **Solution: 읽기 전용으로 탐색**

```bash
# 1. 과거 커밋 체크아웃
git checkout ghi789

# 경고 발생 (정상!)
# You are in 'detached HEAD' state...

# 2. 코드 자유롭게 탐색
cat old_file.txt
grep "function" *.py
# 읽기만 하기 (수정 안 함)

# 3. 파일 복사 (필요하면)
cp important.txt ~/backup/

# 4. 안전하게 돌아오기
git switch main
# 또는
git switch -  # 이전 브랜치로

# ✅ 완벽하게 원상복구!
```

**안전 수칙:**
```bash
# Detached HEAD에서:
✅ 읽기만 → 안전
✅ 탐색만 → 안전
✅ 파일 복사 → 안전

⚠️ 커밋 → 위험 (잃어버릴 수 있음)
⚠️ 수정 → 돌아가면 사라짐
```

---

### 💼 Scenario 2: Detached HEAD에서 커밋 복구

**목표:** Detached HEAD에서 만든 커밋 구출하기

#### **Problem: 실험적 커밋을 만들었는데, main으로 돌아왔더니 사라졌다!**

```bash
# 1. Detached HEAD 상태에서 작업
git checkout HEAD~3

echo "Experimental feature" > feature.txt
git add feature.txt
git commit -m "Add experimental feature"

# 커밋 Hash 기억 못함! (실수)

# 2. Main으로 돌아옴
git switch main

# 3. 실험적 커밋이 사라짐!
git log  # 안 보임...
```

#### **Solution: Reflog로 복구**

```bash
# 1. Reflog 확인 (HEAD 이동 기록)
git reflog

# 출력:
# abc123 HEAD@{0}: checkout: moving from xyz789 to main
# xyz789 HEAD@{1}: commit: Add experimental feature ← 찾았다!
# def456 HEAD@{2}: checkout: moving from main to HEAD~3

# 2. 해당 커밋으로 브랜치 생성
git branch experimental xyz789

# 3. 확인
git log experimental --oneline
# xyz789 Add experimental feature
# ...

# ✅ 복구 완료!

# 4. 원하면 merge
git merge experimental
```

**자동화:**
```bash
# 마지막 detached HEAD 커밋을 브랜치로
git branch recover-detached HEAD@{1}
```

---

### 💼 Scenario 3: 브랜치의 본질 이해하기

**목표:** 브랜치를 직접 만들고 조작하기

#### **Problem: 브랜치가 뭔지 정확히 모르겠다!**

```bash
# 브랜치는 그냥... 뭔가 복잡한 거 아닌가요?
```

#### **Solution: 수동으로 브랜치 만들기**

```bash
# 1. 현재 커밋 Hash 확인
CURRENT=$(git rev-parse HEAD)
echo $CURRENT
# abc123def456...

# 2. 브랜치 파일 직접 생성!
echo $CURRENT > .git/refs/heads/my-manual-branch

# 3. 확인
git branch
# * main
#   my-manual-branch  ← 생겼다!

# 4. 체크아웃 가능
git switch my-manual-branch

# 5. 커밋하면?
echo "New file" > new.txt
git add new.txt
git commit -m "New commit"

# 6. 브랜치 파일 확인
cat .git/refs/heads/my-manual-branch
# xyz789abc123...  ← 새 커밋 Hash로 변경됨!

# 7. 원래 커밋 Hash와 비교
echo $CURRENT
# abc123def456...  ← 다름!
```

**배운 점:**
```
브랜치 = 단순한 텍스트 파일
내용 = 커밋 Hash (40자)
위치 = .git/refs/heads/

커밋하면 → 파일 내용이 새 Hash로 변경
그게 전부!
```

---

### 💼 Scenario 4: 복잡한 상대 참조 활용

**목표:** Merge 커밋에서 특정 부모 선택하기

#### **Problem: Merge 커밋의 변경사항을 부모별로 보고 싶다!**

```bash
# 상황: feature를 main에 merge함
git merge feature

# Merge 커밋 생성됨
#     M (HEAD, main)
#    ↙  ↘
#   C    X (feature)
#   ↓    ↓
#   B    W
```

#### **Solution: ^ 연산자로 부모 선택**

```bash
# 1. Merge 커밋 확인
git log --oneline -1
# abc123 (HEAD -> main) Merge branch 'feature'

# 2. 첫 번째 부모와 비교 (main 쪽)
git diff HEAD^1
# main에서 변경된 내용

# 3. 두 번째 부모와 비교 (feature 쪽)
git diff HEAD^2
# feature에서 변경된 내용

# 4. 각 부모 커밋 보기
git show HEAD^1  # main의 마지막 커밋
git show HEAD^2  # feature의 마지막 커밋

# 5. 특정 부모로 reset
git reset --hard HEAD^1  # feature merge 취소
# 또는
git reset --hard HEAD^2  # main 대신 feature로
```

**복잡한 예시:**
```bash
# 3세대 조상의 두 번째 부모
git show HEAD~3^2

# 의미:
# HEAD~3 → 3세대 조상 (증증조부모)
# ^2     → 그것의 두 번째 부모

# 시각화:
#       HEAD
#         ↓
#      commit_A
#         ↓
#      commit_B
#         ↓
#      commit_C (HEAD~3)
#       ↙    ↘
#    main   feature (HEAD~3^2)
```

---

### 💼 Scenario 5: HEAD 기록 분석

**목표:** Reflog로 작업 기록 추적하기

#### **Problem: 아까 뭐 했더라? 실수한 게 있나?**

```bash
# 여러 작업을 했는데... 기억이 안 남
```

#### **Solution: Reflog로 타임머신**

```bash
# 1. HEAD 이동 기록 전체 보기
git reflog

# 출력:
# abc123 HEAD@{0}: commit: Add feature
# def456 HEAD@{1}: checkout: moving from main to feature
# ghi789 HEAD@{2}: reset: moving to HEAD~1
# jkl012 HEAD@{3}: commit: Wrong commit (실수!)
# mno345 HEAD@{4}: commit: Good commit

# 2. 특정 시점으로 돌아가기
git reset --hard HEAD@{4}
# "Good commit" 시점으로!

# 3. 또는 특정 시간 기준
git reflog --date=relative

# 출력:
# abc123 HEAD@{5 minutes ago}: commit
# def456 HEAD@{1 hour ago}: checkout
# ghi789 HEAD@{yesterday}: reset

# 4. 어제 상태로 복구
git reset --hard HEAD@{yesterday}

# 5. Reflog 검색
git reflog | grep "commit"
# 모든 커밋 시점 찾기
```

**Reflog 항목:**
```bash
git reflog show --all

# 출력 형식:
# [hash] HEAD@{number}: [action]: [description]

# 예시:
# abc123 HEAD@{0}: commit: Add feature
# ↑      ↑         ↑        ↑
# Hash   참조      동작     설명
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: Detached HEAD에서 커밋 후 브랜치 안 만듦**

```bash
# 위험한 패턴
git checkout HEAD~3
# ... 작업 ...
git commit -m "Important work"
git switch main  # ❌ 커밋 잃어버림!
```

**올바른 방법:**
```bash
git checkout HEAD~3
# ... 작업 ...
git commit -m "Important work"

# 즉시 브랜치 생성!
git branch temp-work
git switch temp-work

# 또는 한 번에
git switch -c temp-work
```

---

#### ❌ **실수 2: HEAD^와 HEAD~를 혼동**

```bash
# 일반 커밋에서는 같음
HEAD^  = HEAD~1  # 부모

# Merge 커밋에서는 다름!
HEAD^1 = 첫 번째 부모 (main)
HEAD^2 = 두 번째 부모 (branch)
HEAD~1 = 첫 번째 부모 (= HEAD^1)
```

**기억법:**
```
^ (캐럿) = 부모 "선택" (merge 시 여러 부모)
~ (틸데) = 부모 "따라가기" (항상 첫 번째 부모)

일반 커밋: HEAD^ = HEAD~
Merge 커밋: HEAD^2 존재, HEAD~2는 조부모
```

---

#### ❌ **실수 3: Symbolic reference를 Hash로 착각**

```bash
# 잘못된 생각
cat .git/HEAD
# ref: refs/heads/main
# "이게 커밋 Hash인가?"  ❌

# 올바른 이해
cat .git/HEAD
# ref: refs/heads/main  ← 다른 참조를 가리킴

cat .git/refs/heads/main
# abc123def456...  ← 실제 커밋 Hash
```

**해결:**
```bash
# Symbolic reference 해석
git symbolic-ref HEAD
# refs/heads/main

# 최종 커밋 Hash 얻기
git rev-parse HEAD
# abc123def456...
```

---

#### ❌ **실수 4: Reflog를 만능이라고 착각**

```bash
# Reflog의 한계
git reflog
# ⚠️ 로컬에만 존재
# ⚠️ 기본 90일 후 만료
# ⚠️ git gc로 정리될 수 있음
```

**안전 수칙:**
```bash
# 중요한 작업은 항상 브랜치로!
git branch backup-$(date +%Y%m%d)

# Reflog 만료 기간 연장
git config gc.reflogExpire 365

# Reflog 확인
git reflog expire --dry-run --all
```

---

### 🎯 실습 미션

#### **미션 1: Detached HEAD 마스터** 🎯

**목표:** Detached HEAD 상태를 완벽히 이해하기

```bash
# 1. 저장소 생성
mkdir detached-master && cd detached-master
git init

# 2. 여러 커밋 생성
for i in {1..5}; do
  echo "Version $i" > file.txt
  git add file.txt
  git commit -m "Version $i"
done

# 3. 미션:
# - 3번째 커밋으로 이동 (Detached HEAD)
# - 새 파일 생성 후 커밋
# - main으로 돌아오기
# - 방금 만든 커밋을 'experiment' 브랜치로 복구

# 힌트:
# git checkout HEAD~2
# git reflog
# git branch experiment [hash]
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 3번째 커밋으로 이동
git checkout HEAD~2
# Detached HEAD!

# 2. 새 파일 커밋
echo "Experiment" > exp.txt
git add exp.txt
git commit -m "Experimental feature"

# 커밋 Hash 확인 (중요!)
git rev-parse HEAD
# xyz789...

# 3. main으로 돌아오기
git switch main

# 4. Reflog로 찾기
git reflog
# xyz789 HEAD@{1}: commit: Experimental feature

# 5. 브랜치 생성
git branch experiment xyz789

# 6. 확인
git log experiment --oneline
# xyz789 Experimental feature
# ...

# 성공! ✅
```

</details>

---

#### **미션 2: 브랜치 포인터 조작** 🎨

**목표:** 브랜치를 수동으로 이동시키기

```bash
# 1. 저장소 준비
git init branch-pointer && cd branch-pointer

for i in {1..10}; do
  echo "Commit $i" > file.txt
  git add file.txt
  git commit -m "Commit $i"
done

# 2. 미션:
# - 현재 main은 10번째 커밋
# - main을 5번째 커밋으로 강제 이동
# - HEAD는 그대로 (10번째 커밋)
# - main을 다시 HEAD 위치로 이동

# 힌트:
# git branch -f main HEAD~5
# git branch -f main HEAD
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 현재 상태 확인
git log --oneline
# abc123 (HEAD -> main) Commit 10
# ...

# 2. main을 5번째 커밋으로
git branch -f main HEAD~5

# 3. 상태 확인
git log --oneline --all
# abc123 (HEAD) Commit 10
# ...
# def456 (main) Commit 5 ← main이 여기!
# ...

# 4. HEAD는 Detached 상태!
cat .git/HEAD
# abc123...

# 5. main을 다시 HEAD로
git branch -f main HEAD

# 6. main 체크아웃
git switch main

# 성공! ✅
```

</details>

---

#### **미션 3: Reflog 타임머신** ⏰

**목표:** Reflog로 과거 상태 복구하기

```bash
# 1. 저장소 생성 및 작업
git init time-machine && cd time-machine

# 2. 시나리오 실행:
echo "V1" > file.txt && git add . && git commit -m "V1"
echo "V2" > file.txt && git add . && git commit -m "V2"
echo "V3" > file.txt && git add . && git commit -m "V3"

# 실수: reset으로 V1으로 돌아감
git reset --hard HEAD~2

# 3. 미션:
# - 현재 V1 상태
# - Reflog로 V3 커밋 찾기
# - V3 상태로 복구하기

# 힌트:
# git reflog
# git reset --hard HEAD@{1}
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 현재 상태
cat file.txt
# V1

git log --oneline
# abc123 V1

# 2. Reflog 확인
git reflog
# abc123 HEAD@{0}: reset: moving to HEAD~2
# def456 HEAD@{1}: commit: V3 ← 찾았다!
# ghi789 HEAD@{2}: commit: V2

# 3. V3로 복구
git reset --hard HEAD@{1}

# 또는 직접 Hash로
git reset --hard def456

# 4. 확인
cat file.txt
# V3

git log --oneline
# def456 V3
# ghi789 V2
# abc123 V1

# 완벽 복구! ✅
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === HEAD 확인 ===
git rev-parse HEAD              # 커밋 Hash
git symbolic-ref HEAD           # Symbolic reference
git branch --show-current       # 현재 브랜치명
cat .git/HEAD                   # 직접 확인

# === HEAD 이동 ===
git switch <branch>             # 브랜치로 (Attached)
git checkout <commit>           # 커밋으로 (Detached)
git switch -                    # 이전 브랜치로

# === 상대 참조 ===
HEAD~1, HEAD~2, HEAD~3          # 부모 따라가기
HEAD^1, HEAD^2                  # 부모 선택 (merge)
HEAD^^, HEAD~~~                 # 조합

# === Reflog ===
git reflog                      # HEAD 기록
git reflog show <branch>        # 브랜치 기록
git reset --hard HEAD@{N}       # N번째 이전 상태로

# === 브랜치 조작 ===
git branch <name>               # 브랜치 생성
git branch -f <name> <commit>   # 브랜치 강제 이동
git branch -d <name>            # 브랜치 삭제

# === Detached HEAD 복구 ===
git branch <name> <commit>      # 브랜치로 저장
git branch <name> HEAD@{1}      # Reflog로 복구
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [1.1 Git 객체 모델](./01-object-model.md)
- [1.2 3가지 영역](./02-three-areas.md)

**다음 학습:**
- [1.4 커밋 해부하기](./04-commit-anatomy.md)

**실전 응용:**
- [2.1 브랜치 기본](../02-branching/01-branch-basics.md)
- [4.1 Rebase 기초](../04-rebase/01-rebase-basics.md)
- [5.5 Reflog 활용](../05-recovery/05-reflog.md)

---

### 💡 추가 팁

#### **유용한 Alias**

```bash
# .gitconfig에 추가
[alias]
  # HEAD 정보
  head = rev-parse HEAD
  current = branch --show-current
  
  # Reflog 간편하게
  undo = reset HEAD@{1}
  timeline = reflog --date=relative
  
  # Detached HEAD 복구
  save = "!f() { git branch ${1:-temp-$(date +%Y%m%d-%H%M%S)}; }; f"
  
  # 브랜치 포인터 이동
  move-branch = branch -f
```

**사용:**
```bash
git head          # 현재 Hash
git current       # 현재 브랜치
git timeline      # 시간순 reflog
git save backup   # Detached HEAD 저장
```

---

#### **실전 워크플로우**

**과거 코드 탐색:**
```bash
# 1. 안전하게 과거로
git checkout HEAD~10

# 2. 읽기만 하기
cat old_code.py
git log

# 3. 필요하면 복사
cp important.txt ~/backup/

# 4. 돌아오기
git switch -
```

**실험적 작업:**
```bash
# 1. Detached HEAD로 시작
git checkout HEAD~3

# 2. 실험
echo "Test" > test.txt
git add test.txt
git commit -m "Experiment"

# 3. 즉시 브랜치로!
git switch -c experiment

# 4. 나중에 merge
git switch main
git merge experiment
```

---

#### **디버깅 팁**

```bash
# HEAD가 이상할 때
git fsck --full
# HEAD 무결성 검사

# Symbolic reference 복구
echo "ref: refs/heads/main" > .git/HEAD

# Reflog로 모든 기록 추적
git log --walk-reflogs --all
```

---

<div align="center">

**🎉 축하합니다! HEAD와 참조를 완전히 마스터했습니다!**

이제 Detached HEAD를 두려워하지 않고 자유롭게 탐색할 수 있습니다!

[⬅️ 이전: 3가지 영역](./02-three-areas.md) | [README로](../README.md) | [다음: 커밋 해부하기 ➡️](./04-commit-anatomy.md)

</div>
