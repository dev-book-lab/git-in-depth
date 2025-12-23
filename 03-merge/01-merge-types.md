# 3.1 Merge 타입 (Merge Types)

**난이도: ⭐⭐**

> *"Merge는 Git의 핵심입니다. Merge 타입을 이해하면 협업의 90%를 이해한 것입니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Merge 타입을 알아야 하나?

**실제 개발 시나리오:**
```
상황: 팀 프로젝트 협업

Feature 브랜치에서 작업 완료
→ main으로 merge 필요

문제:
개발자 A: "Merge했는데 히스토리가 이상해요"
개발자 B: "Merge 커밋이 없어요?"
개발자 C: "왜 제 커밋이 사라졌죠?"
팀장: "히스토리가 왜 이렇게 복잡해요?"

이유:
→ Merge 타입을 몰라서!
→ 상황에 맞는 merge를 안 해서!
```

**Merge 타입을 이해하면:**
```bash
Fast-forward:
main ─●───────●─
      └─feat─┘
선형적, 깔끔!

3-way merge:
main ─●─────●─
      │     ↗
feat  └─●─┘
히스토리 보존!

Squash merge:
main ─●───●─
      │   ↑
feat  └───┘ (압축)
깔끔하지만 히스토리 손실!

✅ 상황별 최적 선택
✅ 히스토리 제어
✅ 팀 협업 원활
```

> 💡 **핵심:** Merge 타입은 히스토리를 어떻게 보존/통합할지 결정합니다!

---

### 📌 Merge의 3가지 타입

```
┌─────────────────────────────────────────────────┐
│              Merge 타입 비교                      │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. Fast-forward                                │
│     - 선형 히스토리                                │
│     - Merge 커밋 없음                             │
│     - 가장 단순                                   │
│                                                 │
│  2. 3-way Merge                                 │
│     - Merge 커밋 생성                             │
│     - 히스토리 보존                                │
│     - 브랜치 흔적 남음                              │
│                                                 │
│  3. Squash Merge                                │
│     - 커밋들을 하나로 압축                           │
│     - 깔끔한 히스토리                               │
│     - 중간 과정 손실                               │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

### 🚀 Fast-forward Merge

#### **정의**

```
조건:
- 브랜치가 일직선상에 있을 때
- main이 그대로, feature만 앞으로

동작:
- Merge 커밋 생성 안 함
- 포인터만 이동
- 선형 히스토리 유지
```

---

#### **내부 동작**

```
초기 상태:
main ─●─●─●─  (C)
      └─────→ feature (같은 위치)

Feature에서 작업:
main ─●─●─●─  (C)
      │
feature   └─●─●─  (E)

Merge:
git switch main
git merge feature

결과 (Fast-forward):
main, feature ─●─●─●─●─●─  (E)
               A  B  C  D  E

포인터만 이동!
main이 E로 이동
```

---

#### **특징**

```
✅ 장점:
- 선형 히스토리
- 이해하기 쉬움
- log가 깔끔
- 빠름 (커밋 생성 안 함)

❌ 단점:
- 브랜치 흔적 없음
- "어디서 feature가 시작됐지?" 불명확
- Revert 어려움
- 협업 시 누가 작업했는지 불명

사용 시기:
✅ 혼자 작업
✅ 짧은 브랜치
✅ 실험적 브랜치
✅ 선형 히스토리 원할 때

❌ 피해야 할 때:
- 팀 협업
- 브랜치 추적 필요
- Audit trail 필요
```

---

#### **Fast-forward 시각화**

```
Before:
    main     feature
     ↓         ↓
    C1       C3
     ↓         ↓
    C2       C2
     ↓         ↓
    C1       C1

After (git merge feature):
         main, feature
              ↓
             C3
              ↓
             C2
              ↓
             C1

히스토리:
C3 - feat: Add feature
C2 - feat: Add login
C1 - initial commit

깔끔! 하지만 "어디가 feature?"
```

---

### 🔀 3-way Merge

#### **정의**

```
조건:
- 브랜치가 갈라졌을 때
- main과 feature 모두 진행

동작:
- Merge 커밋 생성
- 두 부모 커밋 보유
- 히스토리 보존
```

---

#### **왜 "3-way"인가?**

```
비교 대상 3개:
1. Base (공통 조상)
2. Ours (main)
3. Theirs (feature)

예시:
        Base (C0)
         ↙    ↘
    Ours(C1) Theirs(C2)
         ↘    ↙
        Merge(C3)

C3는 C1과 C2를 비교하되,
C0를 기준으로 "누가 뭘 바꿨는지" 판단!
```

---

#### **내부 동작**

```
초기:
main ─●─  (C0)
      └→ feature

양쪽 작업:
      feature
         ↓
        C2
         ↓
         │  main
         │   ↓
         │  C1
         ↓   ↓
        C0 ←┘

Merge:
git switch main
git merge feature

결과 (3-way):
         main
          ↓
        C3 (Merge!)
       ↙  ↘
     C1   C2
       ↘  ↙
        C0

C3 특징:
- 부모가 2개 (C1, C2)
- Merge 커밋
- 메시지: "Merge branch 'feature'"
```

---

#### **특징**

```
✅ 장점:
- 전체 히스토리 보존
- 브랜치 흔적 명확
- 누가 언제 작업했는지 추적
- Revert 쉬움
- Audit trail 완벽

❌ 단점:
- 히스토리 복잡
- Merge 커밋 많아질 수 있음
- Log 읽기 어려울 수 있음

사용 시기:
✅ 팀 협업
✅ 긴 feature 브랜치
✅ 히스토리 추적 중요
✅ 규제 산업 (audit)

❌ 피해야 할 때:
- 선형 히스토리 원할 때
- 개인 실험
```

---

#### **3-way Merge 시각화**

```
Before:
      feature
         ↓
      feat_C
         ↓
         │  main
         │   ↓
         │ main_B
         ↓   ↓
      base_A

After (git merge feature):
         main
          ↓
      merge_D
       ↙    ↘
   main_B  feat_C
       ↘    ↙
      base_A

히스토리:
merge_D - Merge branch 'feature'
  ↙      ↘
main_B    feat_C
  ↓       ↓
base_A ←─┘

명확! "feature가 여기서 merge됨"
```

---

### 🔄 Squash Merge

#### **정의**

```
동작:
- Feature의 모든 커밋을 하나로 압축
- 새 커밋 1개만 main에 추가
- Feature 히스토리는 사라짐

특징:
- 깔끔한 main 히스토리
- 중간 과정 숨김
- Feature 브랜치는 보존 (삭제 전까지)
```

---

#### **내부 동작**

```
Before:
      feature
         ↓
        C3
         ↓
        C2
         ↓
         │  main
         │   ↓
         │  C1
         ↓   ↓
        C0 ←┘

Squash Merge:
git switch main
git merge --squash feature
git commit -m "Add feature (squashed)"

결과:
main ─●─●─●─
      C0 C1 C4 (squashed!)
             ↑
      (C2+C3의 변경사항 모두 포함)

feature는 그대로 존재:
feature ─●─●─●─
        C0 C2 C3

하지만 main에는 C4만!
```

---

#### **특징**

```
✅ 장점:
- Main 히스토리 깔끔
- 실험적 커밋 숨김
- WIP 커밋 제거
- 리뷰하기 쉬움
- "기능 단위" 히스토리

❌ 단점:
- 중간 과정 손실
- 누가 뭘 했는지 불명확
- Revert 시 전부 또는 전무
- Git log로 feature 추적 불가

사용 시기:
✅ PR 리뷰 후 merge
✅ 많은 WIP 커밋
✅ 실험적 커밋 많음
✅ 깔끔한 main 원할 때

❌ 피해야 할 때:
- 상세 히스토리 필요
- 감사 추적 필요
- 기여자 크레딧 중요
```

---

#### **Squash Merge 시각화**

```
Feature 브랜치 (10개 커밋):
feat ─●─●─●─●─●─●─●─●─●─●─
      WIP 1
      WIP 2
      Fix typo
      WIP 3
      Refactor
      Fix bug
      Add feature
      Update docs
      Fix lint
      Final

Squash 후 main:
main ─●───────────●─
      │           ↑
      │    "Add user feature"
      │    (10개 커밋 압축)
      └─ 이전 상태

깔끔! 하지만 10개 커밋 내역은 사라짐
```

---

### 📊 Merge 타입 비교

| 특성 | Fast-forward | 3-way Merge | Squash Merge |
|:-----|:------------|:-----------|:------------|
| **Merge 커밋** | 없음 | 있음 | 없음 (1개 새 커밋) |
| **히스토리** | 선형 | 비선형 | 선형 |
| **Feature 흔적** | 없음 | 명확 | 없음 |
| **커밋 수** | 모두 보존 | 모두 보존 | 1개로 압축 |
| **Revert** | 커밋별 | 브랜치 단위 | 전체 한번에 |
| **복잡도** | 낮음 | 중간 | 낮음 |
| **사용** | 개인 | 팀 | PR 리뷰 |

---

### 🎯 --no-ff 옵션

#### **Fast-forward 방지**

```bash
# 기본 (Fast-forward 가능 시 FF)
git merge feature

# Fast-forward 방지
git merge --no-ff feature
```

---

#### **왜 사용하나?**

```
문제:
Fast-forward로 merge하면
브랜치 흔적이 없어짐

해결:
--no-ff로 강제로 merge 커밋 생성
→ 브랜치 히스토리 보존!

예시:
Fast-forward:
main ─●─●─●─●─●─
      A B C D E
      "어디가 feature?"

--no-ff:
main ─●─────●─
      │     ↗ (Merge feature)
feat  └─●─●─
        D E
      "feature가 여기!"
```

---

#### **사용 시나리오**

```
✅ 사용해야 할 때:
- 팀 협업
- Feature 추적 필요
- Git Flow, GitHub Flow
- Revert 쉽게 하고 싶을 때

❌ 불필요한 경우:
- 개인 브랜치
- 실험적 브랜치
- 선형 히스토리 원할 때
- Trunk-Based Development
```

---

### 🔍 Merge Base 이해하기

#### **Merge Base = 공통 조상**

```
브랜치:
      feature
         ↓
        C3
         ↓
        C2
         ↓
         │  main
         │   ↓
         │  C1
         ↓   ↓
        C0 ←┘ (Merge Base!)

C0가 Merge Base:
- main과 feature의 마지막 공통 커밋
- 3-way merge의 "Base"
```

---

#### **확인 방법**

```bash
# Merge base 확인
git merge-base main feature
# 출력: abc123... (C0의 Hash)

# 시각화
git log --oneline --graph main feature
```

---

#### **왜 중요한가?**

```
Merge base가 있어야:
1. 3-way merge 가능
2. 누가 뭘 바꿨는지 판단
3. 충돌 해결 가능

예시:
Base: line = "hello"
Main: line = "hello world"
Feature: line = "hi world"

→ 충돌! (둘 다 바꿈)

Base: line = "hello"
Main: line = "hello world"
Feature: line = "hello"

→ 충돌 없음 (main만 바꿈)
→ main 버전 채택
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 기본 Merge 명령어

#### **1️⃣ 기본 Merge**

```bash
# Feature를 현재 브랜치로
git merge feature

# Git이 자동 선택:
# - Fast-forward 가능 → FF
# - 불가능 → 3-way merge
```

---

#### **2️⃣ Fast-forward 제어**

```bash
# Fast-forward만 허용
git merge --ff-only feature
# FF 불가능하면 에러!

# Fast-forward 금지
git merge --no-ff feature
# 항상 merge 커밋 생성

# 기본 동작 (자동)
git merge feature
```

---

#### **3️⃣ Squash Merge**

```bash
# 1. Squash (아직 커밋 안 됨)
git merge --squash feature

# 2. Staged 상태 확인
git status
# Changes to be committed:
#   (feature의 모든 변경사항)

# 3. 커밋 (수동!)
git commit -m "feat: Add user feature

Squashed commits:
- Add user model
- Add user API
- Add user tests
- Update docs"

# 주의: --squash는 커밋 안 함!
# 반드시 git commit 필요!
```

---

#### **4️⃣ Merge 메시지 수정**

```bash
# 자동 메시지
git merge feature
# "Merge branch 'feature'"

# 커스텀 메시지
git merge feature -m "Merge feature: Add user authentication"

# 에디터로 수정
git merge feature
# (에디터 열림)
# 메시지 수정 후 저장
```

---

### 📊 Merge 전략 옵션

#### **1️⃣ -X 옵션 (Strategy Options)**

```bash
# Theirs 우선 (충돌 시 feature 선택)
git merge -X theirs feature

# Ours 우선 (충돌 시 main 선택)
git merge -X ours feature

# Patience (더 나은 diff 알고리즘)
git merge -X patience feature

# Ignore whitespace
git merge -X ignore-all-space feature
```

---

#### **2️⃣ --strategy 옵션**

```bash
# Recursive (기본)
git merge --strategy=recursive feature

# Ours (feature 무시, main만)
git merge --strategy=ours feature

# Octopus (3개 이상 브랜치)
git merge --strategy=octopus feat1 feat2 feat3
```

---

### 🔍 Merge 정보 확인

#### **1️⃣ Merge 가능 여부**

```bash
# Dry-run (실제 merge 안 함)
git merge --no-commit --no-ff feature
# 충돌 확인
git diff --cached
# 취소
git merge --abort

# Merge base 확인
git merge-base main feature

# 변경사항 미리보기
git diff main...feature
```

---

#### **2️⃣ Merge 후 확인**

```bash
# 마지막 merge 확인
git show
# Merge 커밋 내용

# Merge 커밋 목록
git log --merges

# Merge 아닌 커밋만
git log --no-merges

# 특정 merge 찾기
git log --grep="Merge branch"
```

---

### ⚡ 실행 결과 시각화

#### **Scenario 1: Fast-forward**

```bash
# 초기
git init
git commit -m "A"
git commit -m "B"

# 브랜치
git switch -c feature
git commit -m "C"
git commit -m "D"

# 상태
main ─●─●─
      A B
      
feat    └─●─●─
          C D

# Merge
git switch main
git merge feature

# 결과 (FF)
main, feat ─●─●─●─●─
            A B C D

# 로그
git log --oneline
# D feat: Feature D
# C feat: Feature C
# B Initial B
# A Initial A

# 선형!
```

---

#### **Scenario 2: 3-way Merge**

```bash
# 초기
git init
git commit -m "A"

# 브랜치
git switch -c feature
git commit -m "B"

# Main도 작업
git switch main
git commit -m "C"

# 상태
    feat
      ↓
      B
      ↓
      │ main
      │  ↓
      │  C
      ↓  ↓
      A ←┘

# Merge
git switch main
git merge feature

# 결과 (3-way)
      main
       ↓
      D (Merge)
     ↙  ↘
    C    B
     ↘  ↙
      A

# 로그
git log --oneline --graph
# *   D Merge branch 'feature'
# |\  
# | * B feat: Feature B
# * | C chore: Main work
# |/  
# * A Initial

# 비선형!
```

---

#### **Scenario 3: Squash Merge**

```bash
# Feature에 여러 커밋
git switch -c feature
git commit -m "WIP: Start"
git commit -m "WIP: Progress"
git commit -m "feat: Done"

# 상태
feat ─●─●─●─
      │ │ │
      1 2 3

# Squash
git switch main
git merge --squash feature
git commit -m "feat: Complete feature"

# 결과
main ─●───●─
      │   ↑
      A   (1+2+3 압축)

feat ─●─●─●─
      1 2 3 (그대로 남음)

# 로그 (main)
git log --oneline
# abc123 feat: Complete feature
# def456 Initial

# 로그 (feature)
git log --oneline feature
# 789abc feat: Done
# 456def WIP: Progress
# 123abc WIP: Start
# def456 Initial

# Main은 1개, feature는 3개!
```

---

### 📊 명령어 비교표

#### **Merge 옵션**

| 명령어 | Fast-forward | Merge 커밋 | 히스토리 |
|:------|:------------|:----------|:--------|
| `git merge` | 가능시 FF | 필요시 생성 | 자동 |
| `git merge --ff-only` | FF만 | 없음 | 선형 |
| `git merge --no-ff` | 금지 | 항상 생성 | 비선형 |
| `git merge --squash` | N/A | 없음 | 선형 (압축) |

---

#### **Merge 전략**

| 전략 | 용도 | 복잡도 |
|:-----|:-----|:------|
| **recursive** | 기본 (2개 브랜치) | 보통 |
| **ours** | 현재 브랜치만 유지 | 낮음 |
| **theirs** | 다른 브랜치 우선 | 낮음 |
| **octopus** | 3개 이상 브랜치 | 높음 |
| **subtree** | 서브프로젝트 | 높음 |

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 적절한 Merge 타입 선택

**목표:** 상황별 최적의 merge 선택

#### **Problem: 어떤 merge를 써야 하나?**

```bash
# 상황 1: 개인 실험 브랜치
git switch -c experiment
# ... 3개 커밋 ...

# 상황 2: 팀 feature 브랜치
git switch -c feature/user-auth
# ... 10개 커밋 (팀 작업) ...

# 상황 3: PR 리뷰 후 merge
git switch -c fix/typo
# ... 5개 WIP 커밋 ...

# 어떻게 merge?
```

#### **Solution: 상황별 전략**

```bash
# ===== 상황 1: 실험 브랜치 (Fast-forward) =====

git switch -c experiment
echo "Experiment 1" > exp.txt
git add exp.txt
git commit -m "exp: Try approach 1"

echo "Experiment 2" >> exp.txt
git add exp.txt
git commit -m "exp: Try approach 2"

echo "Experiment 3" >> exp.txt
git add exp.txt
git commit -m "exp: Final approach"

# Main으로 merge (FF)
git switch main
git merge experiment
# Fast-forward (깔끔!)

# 이유:
# - 혼자 작업
# - 선형 히스토리
# - 브랜치 추적 불필요

git log --oneline
# abc123 exp: Final approach
# def456 exp: Try approach 2
# ghi789 exp: Try approach 1
# ...

# ✅ 깔끔한 선형 히스토리!

# ===== 상황 2: 팀 Feature (--no-ff) =====

git switch -c feature/user-auth

# 개발자 A
git commit -m "feat(auth): Add login model"
git commit -m "feat(auth): Add login API"

# 개발자 B
git commit -m "feat(auth): Add login UI"
git commit -m "test(auth): Add login tests"

# ... 10개 커밋 ...

# Main으로 merge (--no-ff)
git switch main
git merge --no-ff feature/user-auth \
  -m "Merge feature: User authentication

Implements complete user authentication system

Contributors:
- Developer A (backend)
- Developer B (frontend)

Closes #123"

# 이유:
# - 팀 작업
# - 기여자 추적
# - Revert 쉽게
# - Audit trail

git log --oneline --graph
# *   jkl012 Merge feature: User authentication
# |\  
# | * mno345 test(auth): Add tests
# | * pqr678 feat(auth): Add UI
# | * stu901 feat(auth): Add API
# | * vwx234 feat(auth): Add model
# |/  
# * yz567 Previous work

# ✅ 브랜치 흔적 명확!

# ===== 상황 3: PR 리뷰 (Squash) =====

git switch -c fix/typo

git commit -m "WIP: Fix typo in header"
git commit -m "WIP: More typos"
git commit -m "Fix: Missed one"
git commit -m "Fix: Lint error"
git commit -m "Final: All typos fixed"

# 5개 WIP 커밋... 지저분!

# Squash merge
git switch main
git merge --squash fix/typo

git commit -m "docs: Fix typos in documentation

Fixed multiple typos across docs:
- header.md
- api.md
- guide.md

Reviewed-by: @reviewer
Closes #456"

# 이유:
# - WIP 커밋 숨김
# - 깔끔한 main
# - 리뷰 후 1개 커밋
# - PR 워크플로우

git log --oneline
# abc123 docs: Fix typos
# def456 Previous work

# ✅ 깔끔! WIP 커밋 없음!

# ===== 결과 비교 =====

# Fast-forward:
# - 실험: 선형, 간단
# - 로그: exp1, exp2, exp3

# --no-ff:
# - Feature: 브랜치 추적
# - 로그: Merge commit + 10개 커밋

# Squash:
# - PR: 깔끔함
# - 로그: 1개 커밋 (WIP 숨김)
```

**의사결정 트리:**
```
브랜치 상황
  ↓
혼자 작업? → Yes → Fast-forward
  ↓ No
팀 작업? → Yes → --no-ff
  ↓ No
PR 리뷰? → Yes → Squash
  ↓ No
기본 merge
```

---

### 💼 Scenario 2: Merge 히스토리 정리

**목표:** 복잡한 히스토리를 읽기 쉽게

#### **Problem: 히스토리가 너무 복잡해!**

```bash
# 현재 상태 (3개월 개발)
git log --oneline --graph

# *   Merge branch 'fix/bug1'
# |\  
# | * Fix bug1
# * |   Merge branch 'feat/a'
# |\ \  
# | * | Add feature a
# * | |   Merge branch 'fix/bug2'
# |\ \ \  
# | * | | Fix bug2
# * | | |   Merge branch 'feat/b'
# ...
# (100줄 이상 계속...)

# 읽을 수가 없음!
```

#### **Solution: 전략적 Merge**

```bash
# ===== 문제의 원인 =====

# 모든 브랜치를 --no-ff로 merge
git merge --no-ff fix/typo
git merge --no-ff feat/small
git merge --no-ff docs/update

# → Merge 커밋 폭탄!

# ===== 해결책 1: 브랜치 크기별 전략 =====

# 큰 feature (--no-ff)
git switch -c feature/major-feature
# ... 2주 작업, 50개 커밋 ...

git switch main
git merge --no-ff feature/major-feature \
  -m "Merge feature: Major feature

- Part A completed
- Part B completed
- 50+ commits

Closes #major-123"

# 작은 fix (Fast-forward)
git switch -c fix/typo
# ... 1개 커밋 ...

git switch main
git merge fix/typo  # FF!

# PR (Squash)
git switch -c feat/small
# ... 5개 WIP 커밋 ...

git switch main
git merge --squash feat/small
git commit -m "feat: Small feature"

# 결과:
git log --oneline --graph
# * feat: Small feature (squash)
# * fix: Typo (FF)
# *   Merge feature: Major feature
# |\  
# | * (50 commits...)
# |/  
# * Previous

# 훨씬 깔끔!

# ===== 해결책 2: Linear 히스토리 (Rebase) =====

# Feature 개발
git switch -c feature/linear
git commit -m "feat: Part 1"
git commit -m "feat: Part 2"

# Main 업데이트됨
git switch main
git commit -m "chore: Update"

# Rebase 후 merge (선형!)
git switch feature/linear
git rebase main
git switch main
git merge feature/linear  # FF!

# 결과:
git log --oneline
# * feat: Part 2
# * feat: Part 1
# * chore: Update

# 완전 선형!

# ===== 해결책 3: Squash + --no-ff 혼합 =====

# 큰 feature는 squash
git merge --squash feature/big
git commit -m "feat: Big feature (100 commits)"

# 하지만 branch 추적은 원함
# → 수동으로 merge commit

# 1. Squash
git merge --squash feature/big
git commit -m "feat: Big feature"

# 2. Merge commit (수동)
git commit --allow-empty -m "Merge: Big feature

This is a squashed merge of feature/big

Original commits: 100+
Contributors: 5 developers
Duration: 1 month

Closes #big-feature"

# 결과:
# - Main은 깔끔 (1개 커밋)
# - Merge 기록은 남음
# - Feature 브랜치는 보존

# ✅ 최선!
```

**정리 전략:**
```
브랜치 크기:
- 1-2 커밋 → Fast-forward
- 3-10 커밋 → Squash
- 10+ 커밋 → --no-ff

중요도:
- 사소한 것 → FF or Squash
- 중요한 것 → --no-ff

팀:
- 개인 → FF
- 팀 → --no-ff
- PR → Squash
```

---

### 💼 Scenario 3: Merge 실수 복구

**목표:** 잘못된 merge 되돌리기

#### **Problem: 잘못된 브랜치를 merge했다!**

```bash
# 상황
git switch main
git merge wrong-branch  # ❌ 실수!

# 아직 push 안 함
# 어떻게 되돌리지?
```

#### **Solution: 상황별 복구**

```bash
# ===== Case 1: Fast-forward Merge (쉬움) =====

# 실수
git switch main
git merge experiment  # FF 발생

# 복구
git reset --hard HEAD~3
# 또는
git reset --hard ORIG_HEAD

# ORIG_HEAD = merge 전 위치
# Git이 자동으로 저장함!

# 확인
git log --oneline
# merge 전 상태로 복구!

# ✅ 완벽!

# ===== Case 2: 3-way Merge (간단) =====

# 실수
git switch main
git merge feature  # Merge 커밋 생성

# 복구 1: Reset
git reset --hard HEAD~1
# Merge 커밋 제거

# 복구 2: ORIG_HEAD
git reset --hard ORIG_HEAD

# 확인
git log --oneline
# merge 커밋 없음!

# ✅ 복구 완료!

# ===== Case 3: Squash Merge (주의!) =====

# 실수
git merge --squash feature
git commit -m "Wrong squash"

# 복구
git reset --hard HEAD~1

# 주의: Squash는 일반 커밋!
# Merge 커밋 아님!
# 그냥 reset으로 제거

# ===== Case 4: 이미 Push한 경우 (Revert) =====

# 실수
git merge feature
git push origin main  # 이미 push!

# 복구: Reset 불가! (히스토리 변경)
# → Revert 사용

git revert -m 1 HEAD
# -m 1: 첫 번째 부모 (main) 유지
# -m 2: 두 번째 부모 (feature) 유지

# 새 커밋 생성:
git log --oneline
# abc123 Revert "Merge branch 'feature'"
# def456 Merge branch 'feature'

# Feature 변경사항 모두 취소!

# Push
git push origin main

# ✅ 안전하게 복구!

# ===== Case 5: 부분만 취소 (Cherry-pick) =====

# Merge 후 일부만 문제
git log
# Merge 커밋: abc123
# 문제 커밋: def456

# 전체 revert 후 필요한 것만 재적용
git revert -m 1 abc123
git cherry-pick good-commit-1
git cherry-pick good-commit-2

# ✅ 선택적 복구!
```

**복구 결정 트리:**
```
Merge 실수
  ↓
Push 했나? → No → git reset --hard ORIG_HEAD
  ↓ Yes
공개 브랜치? → Yes → git revert -m 1 HEAD
  ↓ No
  git push --force (조심!)
```

---

### 💼 Scenario 4: 복잡한 Merge 전략

**목표:** 특수한 상황의 merge

#### **Problem: 여러 브랜치를 한번에 merge!**

```bash
# 상황
# - feature-a (UI)
# - feature-b (API)
# - feature-c (DB)
# - 모두 독립적
# - 한번에 merge 필요!
```

#### **Solution: Octopus Merge**

```bash
# ===== 일반적 방법 (3번 merge) =====

git switch main
git merge feature-a
git merge feature-b
git merge feature-c

# 결과: Merge 커밋 3개
git log --oneline --graph
# *   Merge branch 'feature-c'
# |\  
# * |   Merge branch 'feature-b'
# |\ \  
# * | |   Merge branch 'feature-a'
# ...

# ===== Octopus Merge (1번에!) =====

git switch main
git merge feature-a feature-b feature-c

# 또는
git merge --strategy=octopus feature-a feature-b feature-c

# 결과: Merge 커밋 1개!
git log --oneline --graph
# *---. Merge branches 'feature-a', 'feature-b', 'feature-c'
# |\ \ \
# | | | * feature-c commit
# | | * | feature-b commit
# | * | | feature-a commit
# |/ / /  
# * Previous

# 부모가 4개! (main + 3 features)

# ===== 제한사항 =====

# Octopus는 충돌 불가!
git merge feat-a feat-b feat-c
# error: 충돌 발생!
# hint: Octopus는 자동 merge만 가능

# 해결:
# 1. 충돌 없는 것들만 octopus
git merge feat-a feat-c

# 2. 충돌 있는 것 별도
git merge feat-b
# ... 충돌 해결 ...

# ===== 실용성 =====

# 장점:
# ✅ 히스토리 깔끔
# ✅ 1개 merge 커밋
# ✅ 여러 feature 한번에

# 단점:
# ❌ 충돌 불가
# ❌ 복잡함
# ❌ Revert 어려움

# 사용 시기:
# - 독립적인 feature들
# - 충돌 없음 확신
# - 동시 출시
```

---

### 💼 Scenario 5: Git Flow와 Merge 전략

**목표:** Git Flow에서 적절한 merge

#### **Problem: Git Flow 각 단계에서 어떻게 merge?**

```bash
# Git Flow 브랜치:
# - feature/* → develop
# - develop → release/*
# - release/* → main
# - hotfix/* → main + develop
```

#### **Solution: 단계별 전략**

```bash
# ===== Feature → Develop (--no-ff) =====

git switch develop
git merge --no-ff feature/user-auth \
  -m "Merge feature: User authentication

Closes #123"

# 이유:
# - 팀 작업
# - Feature 추적
# - Revert 쉽게

# ===== Develop → Release (--no-ff) =====

git switch -c release/1.2.0 develop
# Release 브랜치는 자동으로 생성
# Merge 불필요!

# ===== Release → Main (--no-ff + Tag) =====

git switch main
git merge --no-ff release/1.2.0 \
  -m "Release version 1.2.0"

git tag -a v1.2.0 -m "Version 1.2.0"

# 이유:
# - 릴리스 추적
# - 버전 명확
# - Audit trail

# ===== Release → Develop (--no-ff) =====

git switch develop
git merge --no-ff release/1.2.0 \
  -m "Merge release 1.2.0 back to develop"

# 버그 수정사항 develop에 반영

# ===== Hotfix → Main (--no-ff) =====

git switch main
git merge --no-ff hotfix/critical-bug \
  -m "Hotfix: Critical bug fix"

git tag -a v1.2.1 -m "Hotfix 1.2.1"

# ===== Hotfix → Develop (--no-ff) =====

git switch develop
git merge --no-ff hotfix/critical-bug

# ===== 요약 =====

# Git Flow에서는:
# 모든 merge → --no-ff
# 이유: 히스토리 추적, Audit trail

# 예외 없음!
# 항상 --no-ff!

git config branch.develop.mergeoptions "--no-ff"
git config branch.main.mergeoptions "--no-ff"

# 자동으로 --no-ff 적용!
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: Squash 후 브랜치 재사용**

```bash
# 위험한 패턴
git merge --squash feature
git commit -m "Squash feature"

# Feature에서 계속 작업
git switch feature
git commit -m "More work"

git switch main
git merge feature
# ❌ 충돌! 또는 중복 커밋!

# 이유:
# Squash는 feature 히스토리 유지
# Main에만 압축 커밋
# → Feature와 main이 다름!
```

**올바른 방법:**
```bash
# Squash 후 브랜치 삭제!
git merge --squash feature
git commit -m "Squash"
git branch -D feature  # ✅

# 또는 새 브랜치
git switch -c feature-v2
```

---

#### ❌ **실수 2: --no-ff를 모든 곳에**

```bash
# 과도한 사용
git merge --no-ff tiny-fix
git merge --no-ff typo-fix
git merge --no-ff doc-update

# 결과: Merge 커밋 폭탄!
git log --oneline --graph
# *   Merge: doc
# |\  
# * |   Merge: typo
# |\ \  
# * | |   Merge: fix
# ...
```

**올바른 방법:**
```bash
# 중요한 것만 --no-ff
git merge --no-ff major-feature  # ✅
git merge tiny-fix               # FF (✅)
```

---

#### ❌ **실수 3: Merge 전 rebase 안 함**

```bash
# 위험한 패턴
git switch feature
# ... 2주 작업 ...
git switch main
git merge feature
# 충돌 폭탄!
```

**올바른 방법:**
```bash
# 주기적 rebase
git switch feature
git rebase main  # 매일!

git switch main
git merge feature
# 충돌 최소!
```

---

#### ❌ **실수 4: Fast-forward 혼동**

```bash
# 혼동
git merge feature  # FF 발생
# "Merge 커밋이 없네? 버그?"

# 확인
git log --oneline
# abc123 feat: Feature
# def456 Previous

# "어? feature 커밋이 main에!"
```

**이해:**
```bash
# FF는 정상!
# 포인터만 이동
# Merge 커밋 없음이 맞음!

# Merge 커밋 원하면
git merge --no-ff feature
```

---

### 🎯 실습 미션

#### **미션 1: 3가지 Merge 타입 체험** 🎯

**목표:** FF, 3-way, Squash 직접 실습

```bash
# 준비
mkdir merge-types && cd merge-types
git init

# 미션:
# 1. Fast-forward merge
# 2. 3-way merge
# 3. Squash merge
# 각각 확인!
```

<details>
<summary>정답 보기</summary>

```bash
# ===== 1. Fast-forward =====
git commit --allow-empty -m "A"
git commit --allow-empty -m "B"

git switch -c feature-ff
git commit --allow-empty -m "C"
git commit --allow-empty -m "D"

git switch main
git merge feature-ff

git log --oneline
# D
# C
# B
# A
# FF 확인! (선형)

# ===== 2. 3-way =====
git switch -c feature-3way
git commit --allow-empty -m "E"

git switch main
git commit --allow-empty -m "F"

git switch main
git merge feature-3way

git log --oneline --graph
# *   Merge branch 'feature-3way'
# |\  
# | * E
# * | F
# |/  
# * D
# 3-way 확인! (Merge 커밋)

# ===== 3. Squash =====
git switch -c feature-squash
git commit --allow-empty -m "G"
git commit --allow-empty -m "H"
git commit --allow-empty -m "I"

git switch main
git merge --squash feature-squash
git commit -m "Squash: G+H+I"

git log --oneline
# abc123 Squash: G+H+I
# ...
# Squash 확인! (1개 커밋)

git log --oneline feature-squash
# I
# H
# G
# Feature는 3개 유지!

# ✅ 완료!
```

</details>

---

#### **미션 2: --no-ff 효과 확인** 🔧

**목표:** --no-ff 유무 비교

```bash
# 시나리오:
# 같은 상황에서
# 1. 기본 merge
# 2. --no-ff merge
# 히스토리 비교!
```

<details>
<summary>정답 보기</summary>

```bash
# ===== Case 1: 기본 merge =====
git init test1 && cd test1
git commit --allow-empty -m "A"

git switch -c feature
git commit --allow-empty -m "B"
git commit --allow-empty -m "C"

git switch main
git merge feature

git log --oneline --graph
# * C
# * B
# * A
# FF! 선형!

cd ..

# ===== Case 2: --no-ff =====
git init test2 && cd test2
git commit --allow-empty -m "A"

git switch -c feature
git commit --allow-empty -m "B"
git commit --allow-empty -m "C"

git switch main
git merge --no-ff feature

git log --oneline --graph
# *   Merge branch 'feature'
# |\  
# | * C
# | * B
# |/  
# * A
# Merge 커밋! 비선형!

# ===== 비교 =====
# 기본: 선형, 간단
# --no-ff: 브랜치 추적, 복잡

# ✅ 차이 확인!
```

</details>

---

#### **미션 3: Merge 복구 실습** 🔄

**목표:** 잘못된 merge 되돌리기

```bash
# 시나리오:
# 1. 실수로 wrong-branch merge
# 2. 복구 (reset)
# 3. 올바른 브랜치 merge
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
git commit --allow-empty -m "A"
git commit --allow-empty -m "B"

git switch -c right-branch
git commit --allow-empty -m "Right"

git switch main
git switch -c wrong-branch
git commit --allow-empty -m "Wrong"

git switch main

# 1. 실수!
git merge wrong-branch

git log --oneline
# abc123 Wrong
# def456 B
# ghi789 A
# ❌ Wrong branch!

# 2. 복구
git reset --hard ORIG_HEAD

git log --oneline
# def456 B
# ghi789 A
# ✅ 복구됨!

# 3. 올바른 merge
git merge right-branch

git log --oneline
# jkl012 Right
# def456 B
# ghi789 A
# ✅ 정상!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 기본 Merge ===
git merge feature                # 자동 선택
git merge --ff-only feature      # FF만
git merge --no-ff feature        # 항상 merge 커밋
git merge --squash feature       # 압축

# === Merge 제어 ===
git merge -X theirs feature      # 충돌 시 feature 우선
git merge -X ours feature        # 충돌 시 main 우선

# === Merge 확인 ===
git merge --no-commit feature    # Dry-run
git merge --abort                # 취소
git diff main...feature          # 미리보기

# === Merge 복구 ===
git reset --hard ORIG_HEAD       # 취소 (push 전)
git revert -m 1 HEAD             # 취소 (push 후)
```

---

### 🎨 Merge 선택 가이드

```
상황별 Merge:

혼자 작업:
→ Fast-forward (기본)

팀 협업:
→ --no-ff

PR 리뷰:
→ Squash

Git Flow:
→ 항상 --no-ff

Trunk-Based:
→ Squash 또는 Rebase
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [2.5 Release 관리](../02-branching/05-release-management.md)

**다음 학습:**
- [3.2 충돌의 원리](./02-conflict-basics.md)

**관련 주제:**
- [2.1 브랜치 기본](../02-branching/01-branch-basics.md)
- [4.1 Rebase 기초](../04-rebase/01-rebase-basics.md)

---

<div align="center">

**🎉 축하합니다! Merge 타입을 완전히 마스터했습니다!**

이제 상황에 맞는 최적의 merge를 선택할 수 있습니다!

[⬅️ 이전: Release 관리](../02-branching/05-release-management.md) | [README로](../README.md) | [다음: 충돌의 원리 ➡️](./02-conflict-basics.md)

</div>
