# 3.4 Merge 전략 (Merge Strategies)

**난이도: ⭐⭐⭐**

> *"Merge 전략은 Git이 충돌을 해결하는 방법입니다. 올바른 전략 선택이 성공적인 merge를 만듭니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Merge 전략을 알아야 하나?

**실제 개발 시나리오:**
```
상황: 복잡한 Merge 상황

Scenario 1: 실수로 merge한 브랜치
→ 되돌리고 싶은데 히스토리 유지?
→ ours 전략!

Scenario 2: 3개 브랜치 동시 merge
→ 일일이 merge하기 번거로움
→ octopus 전략!

Scenario 3: 외부 라이브러리 통합
→ 히스토리 분리하고 싶음
→ subtree 전략!

Scenario 4: 충돌 자동 해결
→ 항상 내 버전 우선
→ -X ours 옵션!

개발자: "기본 merge만 알았는데..."
팀장: "이런 전략이 있었어요!"
```

**Merge 전략을 이해하면:**
```bash
기본 전략:
git merge feature
→ recursive (자동)

ours 전략:
git merge -s ours old-branch
→ 히스토리만 merge, 내용 무시

theirs 전략:
git merge -X theirs feature
→ 충돌 시 자동으로 상대방 선택

octopus:
git merge feat1 feat2 feat3
→ 여러 브랜치 한번에

✅ 상황별 최적 선택
✅ 자동화 가능
✅ 충돌 최소화
```

> 💡 **핵심:** Merge 전략은 Git이 충돌을 처리하는 알고리즘입니다!

---

### 📌 Merge 전략의 5가지 타입

```
┌─────────────────────────────────────────────────┐
│           Merge 전략 종류                         │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. recursive (기본)                             │
│     - 2개 브랜치 merge                            │
│     - 3-way merge                               │
│     - 가장 흔함                                   │
│                                                 │
│  2. ours                                        │
│     - 히스토리만 merge                             │
│     - 내용은 현재 브랜치 유지                         │
│                                                 │
│  3. octopus                                     │
│     - 3개 이상 브랜치                              │
│     - 충돌 불가                                   │
│                                                 │
│  4. resolve (레거시)                              │
│     - 간단한 3-way                                │
│     - recursive 이전 기본                         │
│                                                 │
│  5. subtree                                     │
│     - 서브트리 통합                                │
│     - 외부 프로젝트                                │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

### 🔄 Recursive 전략 (기본)

#### **정의**

```
Git의 기본 merge 전략
2개 브랜치를 3-way merge

특징:
- 가장 안정적
- 충돌 처리 우수
- Rename 감지
- 재귀적 merge base 처리
```

---

#### **동작 원리**

```
일반적인 경우:
        Base
         ↓
        C0
       ↙  ↘
    Ours  Theirs
      ↓      ↓
     C1     C2

Merge:
C0를 기준으로 C1과 C2 비교
→ 3-way merge

복잡한 경우 (여러 merge base):
        C0
       ↙  ↘
      C1   C2
       ↘  ↙
        C3  ← 가상 merge base 생성!
       ↙  ↘
      C4   C5

Recursive:
1. C0로 C1과 C2를 merge → C3 (가상)
2. C3로 C4와 C5를 merge → 최종
```

---

#### **옵션들**

```bash
# 기본 recursive
git merge feature

# ours 우선 (충돌 시)
git merge -X ours feature

# theirs 우선 (충돌 시)
git merge -X theirs feature

# 공백 무시
git merge -X ignore-space-change feature

# 모든 공백 무시
git merge -X ignore-all-space feature

# Rename 임계값
git merge -X rename-threshold=50% feature
```

---

#### **-X ours vs -X theirs**

```
중요: -s ours와 다름!

-X ours:
충돌이 있는 부분만 ours 선택
나머지는 정상 merge

예시:
file1.txt: 충돌 없음 → 양쪽 merge ✅
file2.txt: 충돌 있음 → ours 선택 ✅

-s ours:
모든 변경사항 무시
히스토리만 merge
```

---

### 🎯 Ours 전략

#### **정의**

```
히스토리만 merge
내용은 현재 브랜치 유지

용도:
- 실수로 merge한 브랜치 무시
- 레거시 브랜치 히스토리 통합
- 브랜치 정리
```

---

#### **동작 원리**

```
Before:
      main
       ↓
      C1
       
      old
       ↓
      C2

git merge -s ours old:

After:
      main
       ↓
      C3 (merge commit)
     ↙  ↘
   C1    C2
   
내용:
C3 = C1 (C2 내용 무시!)
히스토리:
C3는 C1과 C2의 부모 (기록만)
```

---

#### **사용 시나리오**

```
Scenario 1: 실험 브랜치 닫기
git switch main
git merge -s ours experiment
# experiment 히스토리 기록
# 내용은 무시

Scenario 2: 레거시 통합
git merge -s ours legacy-v1
# 히스토리 연결
# 코드는 현재 유지

Scenario 3: 잘못된 브랜치 무효화
git merge -s ours wrong-feature
# "이 브랜치는 merge됨" 표시
# 실제 내용은 반영 안 됨
```

---

### 🐙 Octopus 전략

#### **정의**

```
3개 이상의 브랜치를 동시에 merge

특징:
- 충돌 불가 (자동 merge만)
- 여러 독립 feature
- 깔끔한 히스토리
```

---

#### **동작 원리**

```
Before:
      main
       ↓
      C0
      
feat-a   feat-b   feat-c
  ↓        ↓        ↓
 C1       C2       C3

git merge feat-a feat-b feat-c:

After:
        main
         ↓
        C4
      ↙ | ↘
    C1 C2 C3
      ↘ | ↙
        C0

C4의 부모: 4개! (C0, C1, C2, C3)
```

---

#### **제한사항**

```
충돌 발생 시 실패:
git merge feat-a feat-b
# error: merge conflict
# Auto-merging failed

해결:
1. 충돌 없는 것만
2. 충돌 있는 것은 별도
```

---

#### **사용 시나리오**

```
Scenario 1: 독립 feature들
feat-ui: UI 변경
feat-api: API 추가
feat-docs: 문서 업데이트

git merge feat-ui feat-api feat-docs
# 한번에 merge!

Scenario 2: 팀별 작업
team-frontend: 프론트 작업
team-backend: 백엔드 작업
team-devops: 인프라 작업

git merge team-frontend team-backend team-devops
```

---

### 🌳 Subtree 전략

#### **정의**

```
외부 프로젝트를 서브디렉토리로 통합

vs Submodule:
- Submodule: 별도 저장소 참조
- Subtree: 히스토리 통합

장점:
- 하나의 저장소
- Clone 간단
- 독립적 수정 가능
```

---

#### **동작 원리**

```
외부 프로젝트:
external-lib/
  ├─ lib.js
  └─ README.md

통합:
my-project/
  ├─ src/
  ├─ vendor/
  │   └─ external-lib/  ← subtree
  │       ├─ lib.js
  │       └─ README.md
  └─ package.json

히스토리:
my-project와 external-lib
완전히 통합!
```

---

#### **Subtree Merge vs Subtree Add**

```
Subtree Add:
git subtree add --prefix=vendor/lib \
  https://github.com/user/lib.git main

→ lib의 히스토리 전체 추가

Subtree Merge:
git merge -s subtree lib/main

→ 기존 subtree와 merge
```

---

### 🔧 Resolve 전략 (레거시)

#### **정의**

```
간단한 3-way merge
recursive 이전의 기본 전략

차이:
- Rename 감지 없음
- 단순한 알고리즘
- 빠름

현재:
거의 사용 안 함
특수한 경우만
```

---

### 📊 전략 비교표

| 전략 | 브랜치 수 | 충돌 처리 | 용도 | 복잡도 |
|:-----|:----------|:---------|:-----|:------|
| **recursive** | 2개 | 우수 | 일반적 | 중간 |
| **ours** | 2개+ | N/A | 히스토리만 | 낮음 |
| **octopus** | 3개+ | 불가 | 독립 feature | 중간 |
| **resolve** | 2개 | 기본 | 레거시 | 낮음 |
| **subtree** | 2개 | 특수 | 외부 통합 | 높음 |

---

### 🎯 전략 선택 가이드

```
일반적인 merge:
→ recursive (기본)

실험 브랜치 무시:
→ ours

여러 독립 feature:
→ octopus

외부 라이브러리 통합:
→ subtree

충돌 자동 해결:
→ recursive -X ours/theirs
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 Recursive 전략

#### **기본 사용**

```bash
# 기본 (recursive 자동)
git merge feature

# 명시적 지정
git merge -s recursive feature

# 전략 옵션 결합
git merge -s recursive -X ours feature
```

---

#### **옵션 상세**

```bash
# ours 우선 (충돌 시)
git merge -X ours feature
# file1.txt: 충돌 → ours
# file2.txt: 충돌 없음 → 정상 merge

# theirs 우선
git merge -X theirs feature
# file1.txt: 충돌 → theirs
# file2.txt: 충돌 없음 → 정상 merge

# 공백 변경 무시
git merge -X ignore-space-change feature

# 모든 공백 무시
git merge -X ignore-all-space feature

# EOL 차이 무시
git merge -X ignore-cr-at-eol feature

# Rename 감지 임계값
git merge -X rename-threshold=40% feature
# 기본: 50% 유사도
# 낮추면: 더 많은 rename 감지

# Patience diff 알고리즘
git merge -X patience feature
# 더 나은 diff, 느림
```

---

#### **여러 옵션 결합**

```bash
# 공백 무시 + theirs 우선
git merge -X ignore-all-space -X theirs feature

# Patience + ours 우선
git merge -X patience -X ours feature
```

---

### 🎯 Ours 전략

#### **기본 사용**

```bash
# Ours 전략
git merge -s ours old-branch

# 효과:
# - Merge commit 생성
# - 내용은 현재 브랜치 유지
# - 히스토리만 연결
```

---

#### **실제 예시**

```bash
# Before
git log --oneline --graph
# * c1 (main)
# * c0
# 
# * c2 (experiment)
# * c0

# Merge
git merge -s ours experiment

# After
git log --oneline --graph
# *   c3 (main) Merge 'experiment'
# |\  
# | * c2 (experiment)
# * | c1
# |/  
# * c0

# 내용 확인
git show c3:file.txt
# = c1의 내용 (c2 반영 안 됨)
```

---

#### **Theirs 전략 에뮬레이션**

```
주의: -s theirs는 없음!

대안:
git merge -X theirs feature

또는:
git checkout feature
git merge -s ours main
git checkout main
git merge feature
# Fast-forward
```

---

### 🐙 Octopus 전략

#### **기본 사용**

```bash
# 여러 브랜치 merge
git merge feat-a feat-b feat-c

# 자동으로 octopus 선택
# (3개 이상일 때)

# 명시적 지정
git merge -s octopus feat-a feat-b feat-c
```

---

#### **충돌 처리**

```bash
# 충돌 발생 시
git merge feat-a feat-b feat-c
# error: merge conflict
# fatal: merge failed

# 해결:
# 1. 충돌 없는 것만
git merge feat-a feat-c

# 2. 충돌 있는 것 별도
git merge feat-b
# ... 충돌 해결 ...

# 또는
# 1. 순차적 merge
git merge feat-a
git merge feat-b
git merge feat-c
```

---

#### **결과 확인**

```bash
# Octopus merge 확인
git log --oneline --graph -5

# *     merge-commit (4개 부모!)
# |\|\
# | | * feat-c
# | * | feat-b
# * | | feat-a
#  \|/
#   * base

# 부모 확인
git show --format="%P" merge-commit
# parent1 parent2 parent3 parent4
```

---

### 🌳 Subtree 전략

#### **Subtree Add**

```bash
# 1. Remote 추가
git remote add lib-remote \
  https://github.com/user/lib.git

# 2. Fetch
git fetch lib-remote

# 3. Subtree add
git subtree add \
  --prefix=vendor/lib \
  lib-remote main \
  --squash

# 옵션:
# --squash: 히스토리 압축
# --prefix: 디렉토리 위치
```

---

#### **Subtree Pull**

```bash
# 업데이트
git subtree pull \
  --prefix=vendor/lib \
  lib-remote main \
  --squash

# 변경사항 가져오기
# 충돌 가능
```

---

#### **Subtree Push**

```bash
# 로컬 변경사항 push
git subtree push \
  --prefix=vendor/lib \
  lib-remote feature-branch

# vendor/lib 변경사항만
# lib-remote로 push
```

---

#### **Subtree Split**

```bash
# Subtree를 별도 브랜치로
git subtree split \
  --prefix=vendor/lib \
  -b lib-only

# lib-only 브랜치:
# vendor/lib 내용만 포함
# 독립적인 히스토리
```

---

### 📊 명령어 비교표

#### **전략 지정**

| 명령어 | 전략 | 브랜치 수 |
|:------|:-----|:---------|
| `git merge feature` | recursive (자동) | 2개 |
| `git merge -s ours feat` | ours | 2개+ |
| `git merge feat-a feat-b` | octopus (자동) | 3개+ |
| `git merge -s subtree lib` | subtree | 2개 |

---

#### **Recursive 옵션**

| 옵션 | 효과 | 사용 시기 |
|:-----|:-----|:---------|
| `-X ours` | 충돌 시 ours | 내 버전 우선 |
| `-X theirs` | 충돌 시 theirs | 상대 우선 |
| `-X ignore-space-change` | 공백 변경 무시 | 포매팅 차이 |
| `-X patience` | 더 나은 diff | 복잡한 변경 |

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 실험 브랜치 정리

**목표:** Ours 전략으로 히스토리만 통합

#### **Problem: 실패한 실험 브랜치를 정리하고 싶다!**

```bash
# 상황
experiment-failed 브랜치
- 2주간 작업
- 실패한 접근
- 삭제하면 히스토리 손실
- 기록은 남기고 싶음
```

#### **Solution: Ours 전략**

```bash
# ===== Step 1: 현재 상황 =====

git log --oneline --graph --all
# * abc123 (main) Current work
# | * def456 (experiment-failed) Failed approach
# | * ghi789 More experiments
# | * jkl012 Initial experiment
# |/  
# * mno345 Base

# experiment-failed:
# - 30개 커밋
# - 실패한 접근
# - 코드는 사용 안 함

# ===== Step 2: Ours Merge =====

git switch main

git merge -s ours experiment-failed \
  -m "Merge experiment-failed (ours strategy)

This experiment was unsuccessful but we're
keeping the history for reference.

Approach: Alternative algorithm
Result: Performance issues
Conclusion: Keep current implementation

The code is not integrated (ours strategy).
History preserved for learning purposes."

# ===== Step 3: 결과 확인 =====

git log --oneline --graph -5
# *   pqr678 (main) Merge experiment-failed
# |\  
# | * def456 (experiment-failed) Failed approach
# | * ghi789 More experiments
# * | abc123 Current work
# |/  
# * mno345 Base

# 내용 확인
git diff HEAD~1 HEAD
# (출력 없음 = 내용 변경 없음!)

# ===== Step 4: 브랜치 정리 =====

# 브랜치 삭제 가능 (히스토리는 남음)
git branch -d experiment-failed
# Deleted branch experiment-failed

# 확인
git log --oneline --graph -5
# *   pqr678 (main) Merge experiment-failed
# |\  
# | * def456 Failed approach
# | * ghi789 More experiments
# * | abc123 Current work
# |/  
# * mno345 Base

# ✅ 히스토리 보존, 코드 영향 없음!

# ===== Step 5: 문서화 =====

cat > docs/EXPERIMENTS.md << 'EOF'
# Experiments Log

## Failed Experiments

### Alternative Algorithm (2024-12)
**Branch:** experiment-failed  
**Commits:** 30  
**Duration:** 2 weeks  
**Result:** Failed

**Why it failed:**
- Performance issues (5x slower)
- Memory overhead (3x more)
- Complex implementation

**What we learned:**
- Current approach is optimal
- Memory efficiency is critical
- Simplicity matters

**Merge:** Using ours strategy
- History preserved
- Code not integrated
- Learning documented
EOF

git add docs/EXPERIMENTS.md
git commit -m "docs: Document failed experiment"

# ===== 추가 예시: 여러 실험 브랜치 =====

# 3개 실험 모두 실패
git merge -s ours experiment-1
git merge -s ours experiment-2
git merge -s ours experiment-3

# 히스토리 정리
git log --oneline --graph --all
# *   Merge experiment-3
# |\  
# | * experiment-3 work
# *   Merge experiment-2
# |\  
# | * experiment-2 work
# *   Merge experiment-1
# |\  
# | * experiment-1 work
# |/  
# * main work

# ✅ 모든 실험 기록 보존!
```

**Ours 전략 사용 시나리오:**
```
✅ 실패한 실험
✅ 레거시 브랜치
✅ 잘못된 feature
✅ POC (Proof of Concept)
✅ 아카이빙

장점:
- 히스토리 보존
- 코드 영향 없음
- 브랜치 정리
- 학습 기록
```

---

### 💼 Scenario 2: 독립 Feature 일괄 통합

**목표:** Octopus로 여러 브랜치 한번에

#### **Problem: 5개 팀의 작업을 동시에 merge!**

```bash
# 상황
team-ui: UI 개선
team-api: API 추가
team-docs: 문서 업데이트
team-tests: 테스트 추가
team-config: 설정 변경

모두 독립적!
충돌 없음!
```

#### **Solution: Octopus Merge**

```bash
# ===== Step 1: 사전 확인 =====

# 각 브랜치 상태
git log --oneline team-ui -3
# * abc123 Update button styles
# * def456 Add dark mode
# * ghi789 Refactor components

git log --oneline team-api -3
# * jkl012 Add user API
# * mno345 Add auth middleware
# * pqr678 Add validation

# 나머지도 확인...

# ===== Step 2: 충돌 테스트 =====

# Dry-run으로 충돌 확인
git merge --no-commit --no-ff \
  team-ui team-api team-docs team-tests team-config

# 충돌 확인
git status
# On branch main
# All conflicts fixed but you are still merging.

# 취소
git merge --abort

# ✅ 충돌 없음!

# ===== Step 3: Octopus Merge =====

git switch main

git merge \
  team-ui \
  team-api \
  team-docs \
  team-tests \
  team-config \
  -m "Merge Q4 sprint: All team contributions

UI Team:
- Dark mode support
- Button redesign
- Responsive improvements

API Team:
- User management API
- Authentication middleware
- Input validation

Docs Team:
- API documentation
- User guides
- Architecture diagrams

Tests Team:
- Unit tests (coverage +15%)
- Integration tests
- E2E scenarios

Config Team:
- Production settings
- CI/CD updates
- Environment configs

All teams: Great work! 🎉"

# Git 자동 선택: octopus strategy
# Trying simple merge with team-ui
# Trying simple merge with team-api
# Trying simple merge with team-docs
# Trying simple merge with team-tests
# Trying simple merge with team-config
# Merge made by the 'octopus' strategy.

# ===== Step 4: 결과 확인 =====

git log --oneline --graph -8
# *-----. (main) Merge Q4 sprint
# |\ \ \ \
# | | | | * team-config changes
# | | | * | team-tests additions
# | | * | | team-docs updates
# | * | | | team-api additions
# * | | | | team-ui improvements
#  \| | | |
#   | | | * previous work
#   ...

# 부모 확인
git show --format="%P" HEAD
# parent1 parent2 parent3 parent4 parent5 parent6
# (6개 부모: main + 5 teams)

# ===== Step 5: 통계 =====

# 변경 통계
git diff --stat HEAD~1 HEAD
# ui/Button.jsx                | 50 ++++
# api/users.js                 | 120 +++++
# docs/API.md                  | 200 +++++++
# tests/integration/user.test.js | 80 +++
# config/production.json       | 30 ++
# 20 files changed, 800 insertions(+)

# 각 팀 기여도
git log --oneline HEAD^2..HEAD^2 | wc -l  # team-ui
# 15 commits

git log --oneline HEAD^3..HEAD^3 | wc -l  # team-api
# 20 commits

# ===== Step 6: 브랜치 정리 =====

# 모든 팀 브랜치 삭제
git branch -d \
  team-ui \
  team-api \
  team-docs \
  team-tests \
  team-config

# 확인
git branch
# * main

# ===== Step 7: 검증 =====

# 빌드
npm run build
# ✅ Success

# 테스트
npm test
# ✅ All tests passed

# Lint
npm run lint
# ✅ No issues

# ===== Step 8: 태그 =====

git tag -a v1.4.0 -m "Release v1.4.0: Q4 Sprint

All team contributions merged:
- UI improvements
- API additions
- Documentation
- Test coverage
- Configuration

Teams: 5
Commits: 85
Files changed: 20
Lines added: 800"

# ===== 결과 =====

# Before:
# 5번 merge 필요
# 5개 merge commit
# 복잡한 히스토리

# After:
# 1번 merge
# 1개 merge commit (6 parents)
# 깔끔한 히스토리

# ✅ 효율적인 통합!

# ===== 자동화 스크립트 =====

cat > scripts/merge-sprint.sh << 'EOF'
#!/bin/bash

echo "=== Sprint Merge Tool ==="

# 브랜치 목록
branches="team-ui team-api team-docs team-tests team-config"

# 충돌 테스트
echo "Testing for conflicts..."
git merge --no-commit --no-ff $branches

if [ $? -ne 0 ]; then
  echo "❌ Conflicts detected!"
  git merge --abort
  exit 1
fi

git merge --abort
echo "✅ No conflicts"

# 실제 merge
echo "Merging all branches..."
git merge $branches -m "Merge sprint: All team contributions"

if [ $? -eq 0 ]; then
  echo "✅ Merge successful"
  
  # 브랜치 정리
  echo "Cleaning up branches..."
  git branch -d $branches
  
  echo "Done!"
else
  echo "❌ Merge failed"
  exit 1
fi
EOF

chmod +x scripts/merge-sprint.sh
```

**Octopus 장점:**
```
✅ 한번에 merge
✅ 깔끔한 히스토리
✅ 명확한 통합 시점
✅ 팀 간 독립성

사용 조건:
- 충돌 없음 (필수!)
- 독립적 작업
- 동시 출시
```

---

### 💼 Scenario 3: 외부 라이브러리 통합

**목표:** Subtree로 라이브러리 히스토리 통합

#### **Problem: 외부 라이브러리를 프로젝트에 통합!**

```bash
# 요구사항
# - UI 컴포넌트 라이브러리
# - 독립적 개발
# - 프로젝트에 통합
# - 커스터마이징 가능
# - 업스트림 업데이트 가능
```

#### **Solution: Subtree 전략**

```bash
# ===== Step 1: 초기 설정 =====

# 프로젝트 구조
my-app/
  ├─ src/
  ├─ public/
  └─ package.json

# Remote 추가
git remote add ui-lib \
  https://github.com/company/ui-library.git

git fetch ui-lib

# ===== Step 2: Subtree Add =====

git subtree add \
  --prefix=vendor/ui-lib \
  ui-lib main \
  --squash

# 옵션:
# --prefix: 디렉토리 위치
# ui-lib: remote 이름
# main: 브랜치
# --squash: 히스토리 압축 (권장)

# 결과:
my-app/
  ├─ src/
  ├─ vendor/
  │   └─ ui-lib/  ← 추가됨!
  │       ├─ Button.jsx
  │       ├─ Input.jsx
  │       └─ package.json
  └─ package.json

# ===== Step 3: 로컬 수정 =====

# 라이브러리 커스터마이징
vim vendor/ui-lib/Button.jsx
# ... 스타일 변경 ...

git add vendor/ui-lib/Button.jsx
git commit -m "custom: Adjust button styles for our brand"

# 추가 기능
vim vendor/ui-lib/OurButton.jsx
# ... 새 컴포넌트 ...

git add vendor/ui-lib/OurButton.jsx
git commit -m "feat: Add custom button component"

# ===== Step 4: 업스트림 업데이트 =====

# 라이브러리 업데이트됨
# → 가져오기

git fetch ui-lib

git subtree pull \
  --prefix=vendor/ui-lib \
  ui-lib main \
  --squash

# 충돌 가능!
# Auto-merging vendor/ui-lib/Button.jsx
# CONFLICT (content): Merge conflict

# 해결:
vim vendor/ui-lib/Button.jsx
# ... 충돌 해결 ...
# 우리 커스터마이징 + 업스트림 개선

git add vendor/ui-lib/Button.jsx
git commit -m "merge: Integrate ui-lib updates

Upstream changes:
- Performance improvements
- New props support
- Bug fixes

Local customizations:
- Brand colors maintained
- Custom styling preserved"

# ===== Step 5: 기여 (Push) =====

# 우리의 개선사항을 업스트림에

git subtree push \
  --prefix=vendor/ui-lib \
  ui-lib feature/new-component

# vendor/ui-lib의 변경사항만
# ui-lib의 feature/new-component로 push

# PR 생성
# → 업스트림에 기여!

# ===== Step 6: Split (선택) =====

# Subtree를 독립 브랜치로
git subtree split \
  --prefix=vendor/ui-lib \
  -b ui-lib-local

# ui-lib-local 브랜치:
# vendor/ui-lib 내용만
# 독립적인 히스토리

# 용도:
# - 별도 저장소로 추출
# - 히스토리 분석
# - 라이브러리 버전 관리

# ===== 결과 비교 =====

echo "=== Subtree vs Submodule ==="

# Subtree:
# ✅ 단일 저장소
# ✅ Clone 간단
# ✅ 커밋 일반적
# ✅ 커스터마이징 쉬움
# ⚠️ 히스토리 복잡
# ⚠️ 업데이트 명시적

# Submodule:
# ✅ 명확한 버전
# ✅ 작은 저장소
# ✅ 독립적 업데이트
# ⚠️ Clone 복잡
# ⚠️ 별도 관리

# ===== Step 7: 자동화 =====

cat > scripts/update-ui-lib.sh << 'EOF'
#!/bin/bash

echo "=== UI Library Update ==="

# Fetch
git fetch ui-lib

# Pull with squash
git subtree pull \
  --prefix=vendor/ui-lib \
  ui-lib main \
  --squash

if [ $? -eq 0 ]; then
  echo "✅ Update successful"
  
  # Test
  npm test
  if [ $? -eq 0 ]; then
    echo "✅ Tests passed"
  else
    echo "❌ Tests failed, rolling back..."
    git reset --hard HEAD~1
  fi
else
  echo "❌ Update failed (conflicts)"
  echo "Please resolve manually"
fi
EOF

chmod +x scripts/update-ui-lib.sh

# 사용:
# ./scripts/update-ui-lib.sh

# ===== Step 8: 문서화 =====

cat > vendor/ui-lib/README.local.md << 'EOF'
# UI Library (Local Integration)

## Source
Upstream: https://github.com/company/ui-library

## Integration
Method: Git Subtree
Directory: `vendor/ui-lib/`

## Updates
```bash
# Pull upstream changes
git subtree pull \
  --prefix=vendor/ui-lib \
  ui-lib main \
  --squash

# Push our changes
git subtree push \
  --prefix=vendor/ui-lib \
  ui-lib feature-branch
```

## Local Customizations
- `Button.jsx`: Brand colors
- `OurButton.jsx`: Custom component

## Maintenance
Script: `scripts/update-ui-lib.sh`
Frequency: Monthly or as needed
EOF

git add vendor/ui-lib/README.local.md
git commit -m "docs: Add subtree integration guide"

# ✅ 완전한 통합!
```

**Subtree 사용 케이스:**
```
✅ 외부 라이브러리 통합
✅ 공통 컴포넌트
✅ 플러그인 시스템
✅ 멀티 프로젝트 공유

장점:
- 단일 저장소
- 커스터마이징 자유
- Clone 간단

단점:
- 히스토리 복잡
- 업데이트 명시적
- 학습 곡선
```

---

### 💼 Scenario 4: 자동 충돌 해결

**목표:** -X 옵션으로 충돌 자동 처리

#### **Problem: 100개 파일 충돌, 모두 내 버전 우선!**

```bash
# 상황
# - 대규모 리팩토링
# - 동시에 feature 개발
# - 100개 파일 충돌
# - 리팩토링 우선 원함
```

#### **Solution: -X ours 옵션**

```bash
# ===== Step 1: 일반 Merge (실패) =====

git merge feature
# Auto-merging file1.js
# Auto-merging file2.js
# ...
# CONFLICT (content): 100 files!
# Automatic merge failed

git merge --abort

# ===== Step 2: -X ours 전략 =====

git merge -X ours feature

# Auto-merging file1.js
# Auto-merging file2.js
# ...
# CONFLICT (content): 10 files
# (90개는 자동 해결!)

# 남은 10개만 수동 해결
vim file1.js
# ...
git add file1.js

# ... 9개 더 ...

git commit

# ===== 결과 비교 =====

# 일반 merge:
# 100개 충돌 → 2-3시간

# -X ours:
# 10개 충돌 → 20분

# ✅ 90% 시간 절약!

# ===== Step 3: 자동화 스크립트 =====

cat > scripts/merge-with-ours.sh << 'EOF'
#!/bin/bash

BRANCH=$1

echo "=== Merging $BRANCH with 'ours' preference ==="

# Merge with ours
git merge -X ours $BRANCH

if [ $? -eq 0 ]; then
  echo "✅ Merge completed (no conflicts)"
  exit 0
fi

# 충돌 있음
echo "⚠️  Conflicts remaining:"
git diff --name-only --diff-filter=U

echo ""
echo "Resolve these manually, then:"
echo "  git add <files>"
echo "  git commit"
EOF

chmod +x scripts/merge-with-ours.sh

# 사용:
# ./scripts/merge-with-ours.sh feature-branch

# ===== Step 4: 다양한 시나리오 =====

# 시나리오 1: 포매팅 충돌
# (코드 포매터 적용)
git merge -X ours \
  -X ignore-all-space \
  formatting-update

# 공백 차이 무시
# 충돌 90% 감소

# 시나리오 2: 리팩토링 우선
# (변수명 변경)
git merge -X ours refactor
# 리팩토링 버전 유지

# 시나리오 3: Feature 우선
# (새 기능 중요)
git merge -X theirs new-feature
# Feature 버전 우선

# 시나리오 4: Patience diff
# (복잡한 변경)
git merge -X patience \
  -X ours \
  complex-changes

# 더 나은 diff 알고리즘
# + ours 우선

# ===== Step 5: 검증 =====

# Merge 후 테스트
npm test
npm run lint

# 실패 시 특정 파일만 theirs로
git checkout --theirs src/problematic.js
git add src/problematic.js
git commit --amend --no-edit
```

**-X 옵션 활용:**
```
-X ours:
✅ 리팩토링 후 feature merge
✅ 내 변경 우선
✅ 충돌 자동 해결

-X theirs:
✅ 긴급 feature 통합
✅ 상대방 우선
✅ 빠른 통합

-X ignore-all-space:
✅ 포매팅 변경
✅ 공백 차이 무시
✅ 의미있는 충돌만

-X patience:
✅ 복잡한 변경
✅ 더 나은 diff
✅ 정확한 merge
```

---

### 💼 Scenario 5: 전략 조합

**목표:** 여러 전략을 상황에 맞게 조합

#### **Problem: 복잡한 Merge 상황!**

```bash
# 상황
# 1. 실패한 실험: ours
# 2. 독립 feature 3개: octopus
# 3. 외부 라이브러리: subtree
# 4. 충돌 많은 리팩토링: -X ours
```

#### **Solution: 전략적 조합**

```bash
# ===== Phase 1: 실험 정리 =====

# 실패한 실험들 히스토리만
git merge -s ours experiment-1
git merge -s ours experiment-2

git log --oneline --graph -5
# *   Merge experiment-2 (ours)
# |\  
# | * experiment-2 work
# *   Merge experiment-1 (ours)
# |\  
# | * experiment-1 work
# * | current work

# ===== Phase 2: Feature 통합 =====

# 독립적인 3개 feature
git merge feat-ui feat-api feat-docs

# Octopus 자동 선택
# *-----. Merge 3 features
# |\ \ \
# | | | * feat-docs
# | | * | feat-api
# | * | | feat-ui

# ===== Phase 3: 라이브러리 =====

# 외부 컴포넌트 통합
git remote add components \
  https://github.com/company/components.git

git fetch components

git subtree add \
  --prefix=vendor/components \
  components main \
  --squash

# ===== Phase 4: 리팩토링 =====

# 대규모 리팩토링 merge
git merge -X ours \
  -X ignore-all-space \
  massive-refactor

# 충돌 최소화
# 리팩토링 우선

# ===== 결과 =====

git log --oneline --graph -20
# Complex but organized history

# ===== 자동화: 릴리스 프로세스 =====

cat > scripts/release-merge.sh << 'EOF'
#!/bin/bash

echo "=== Release Merge Process ==="

# 1. Cleanup experiments
echo "Phase 1: Cleaning up experiments..."
for exp in experiment-*; do
  git merge -s ours $exp \
    -m "Archive experiment: $exp"
done

# 2. Merge features (octopus)
echo "Phase 2: Merging features..."
features=$(git branch | grep "^  feature-" | tr -d ' ')

if [ -n "$features" ]; then
  git merge $features \
    -m "Release: Merge all features"
fi

# 3. Update libraries
echo "Phase 3: Updating libraries..."
for lib in vendor/*; do
  if [ -f "$lib/.gitsubtree" ]; then
    remote=$(cat "$lib/.gitsubtree")
    git subtree pull \
      --prefix=$lib \
      $remote main \
      --squash
  fi
done

# 4. Merge refactoring
echo "Phase 4: Merging refactoring..."
if git rev-parse --verify refactor >/dev/null 2>&1; then
  git merge -X ours \
    -X ignore-all-space \
    refactor \
    -m "Release: Merge refactoring"
fi

# 5. Verification
echo "Phase 5: Verification..."
npm test
npm run lint
npm run build

if [ $? -eq 0 ]; then
  echo "✅ Release merge complete!"
  echo ""
  echo "Next steps:"
  echo "1. Review changes"
  echo "2. Update version"
  echo "3. Create release tag"
else
  echo "❌ Verification failed"
  exit 1
fi
EOF

chmod +x scripts/release-merge.sh
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: -s ours와 -X ours 혼동**

```bash
# 위험한 혼동
git merge -s ours feature
# 모든 변경 무시! (의도한 것?)

git merge -X ours feature
# 충돌만 ours 선택 (보통 원하는 것)
```

**올바른 이해:**
```bash
# -s ours: 전략
# → 히스토리만, 내용 무시

# -X ours: 옵션
# → 충돌 시 ours 선택
# → 나머지는 정상 merge
```

---

#### ❌ **실수 2: Octopus에서 충돌**

```bash
# 실수
git merge feat-a feat-b feat-c
# error: merge conflict
# fatal: merge failed

# 당황해서 --abort
git merge --abort
```

**올바른 방법:**
```bash
# 충돌 없는 것부터
git merge feat-a feat-c
# Success

# 충돌 있는 것 별도
git merge feat-b
# Conflict - 수동 해결
```

---

#### ❌ **실수 3: Subtree prefix 오류**

```bash
# 실수
git subtree add \
  --prefix=lib \  # 잘못된 위치
  lib-remote main

# lib/ 없음
# → 루트에 파일들 추가됨!
```

**올바른 방법:**
```bash
# 디렉토리 생성
mkdir -p vendor/lib

# 올바른 prefix
git subtree add \
  --prefix=vendor/lib \
  lib-remote main
```

---

#### ❌ **실수 4: 과도한 자동화**

```bash
# 위험한 자동화
git merge -X theirs feature
# 모든 충돌 자동 해결
# → 중요한 변경 손실 가능!
```

**올바른 방법:**
```bash
# 중요 파일은 제외
git merge -X theirs feature

# 중요 파일만 수동
git checkout --ours critical-file.js
git add critical-file.js
```

---

### 🎯 실습 미션

#### **미션 1: Ours 전략 실습** 🎯

**목표:** 히스토리 통합 경험

```bash
# 준비
mkdir strategy-practice && cd strategy-practice
git init

# 미션:
# 1. 실험 브랜치 생성
# 2. Ours로 merge
# 3. 히스토리 확인
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Main
echo "Main work" > main.txt
git add main.txt
git commit -m "Main work"

# 2. Experiment
git switch -c experiment
echo "Experiment work" > exp.txt
git add exp.txt
git commit -m "Experiment 1"
git commit --allow-empty -m "Experiment 2"

# 3. Main으로
git switch main

# 4. Ours merge
git merge -s ours experiment \
  -m "Archive experiment"

# 5. 확인
git log --oneline --graph
# *   Archive experiment
# |\  
# | * Experiment 2
# | * Experiment 1
# * | Main work

# 내용 확인
ls
# main.txt (exp.txt 없음!)

# ✅ 완료!
```

</details>

---

#### **미션 2: Octopus 실습** 🐙

**목표:** 여러 브랜치 동시 merge

```bash
# 시나리오:
# 3개 독립 브랜치
# Octopus로 한번에 merge
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "Base" > base.txt
git add base.txt
git commit -m "Base"

# Feature A
git switch -c feat-a
echo "A" > a.txt
git add a.txt
git commit -m "Feature A"

# Feature B
git switch main
git switch -c feat-b
echo "B" > b.txt
git add b.txt
git commit -m "Feature B"

# Feature C
git switch main
git switch -c feat-c
echo "C" > c.txt
git add c.txt
git commit -m "Feature C"

# Octopus!
git switch main
git merge feat-a feat-b feat-c

# 확인
git log --oneline --graph
# *---. Merge
# |\ \ \
# | | | * Feature C
# | | * | Feature B
# | * | | Feature A
# |/ / /  
# * Base

ls
# a.txt b.txt c.txt base.txt

# ✅ 완료!
```

</details>

---

#### **미션 3: -X 옵션 비교** 🔧

**목표:** ours vs theirs 차이 이해

```bash
# 시나리오:
# 같은 파일 충돌
# ours vs theirs 비교
```

<details>
<summary>정답 보기</summary>

```bash
# === Test 1: -X ours ===
git init test-ours && cd test-ours

echo "Base" > file.txt
git add file.txt
git commit -m "Base"

git switch -c feature
echo "Feature" > file.txt
git add file.txt
git commit -m "Feature"

git switch main
echo "Main" > file.txt
git add file.txt
git commit -m "Main"

# Merge with ours
git merge -X ours feature

cat file.txt
# Main (ours!)

cd ..

# === Test 2: -X theirs ===
git init test-theirs && cd test-theirs

echo "Base" > file.txt
git add file.txt
git commit -m "Base"

git switch -c feature
echo "Feature" > file.txt
git add file.txt
git commit -m "Feature"

git switch main
echo "Main" > file.txt
git add file.txt
git commit -m "Main"

# Merge with theirs
git merge -X theirs feature

cat file.txt
# Feature (theirs!)

# ✅ 차이 이해!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === Recursive (기본) ===
git merge feature                    # 기본
git merge -X ours feature           # 충돌→ours
git merge -X theirs feature         # 충돌→theirs

# === Ours 전략 ===
git merge -s ours old-branch        # 히스토리만

# === Octopus ===
git merge feat-a feat-b feat-c      # 여러개

# === Subtree ===
git subtree add --prefix=vendor/lib \
  lib-remote main --squash          # 추가

git subtree pull --prefix=vendor/lib \
  lib-remote main --squash          # 업데이트
```

---

### 🎨 전략 선택 플로우

```
Merge 상황
  ↓
브랜치 수?
├─ 2개
│  ├─ 일반 → recursive (기본)
│  ├─ 무시 → ours
│  └─ 외부 → subtree
│
└─ 3개+
   └─ 독립 → octopus
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [3.3 복잡한 충돌](./03-complex-conflicts.md)

**다음 학습:**
- [3.5 Merge Tools](./05-merge-tools.md)

**관련 주제:**
- [3.1 Merge 타입](./01-merge-types.md)
- [3.2 충돌의 원리](./02-conflict-basics.md)

---

<div align="center">

**🎉 축하합니다! Merge 전략을 완전히 마스터했습니다!**

이제 모든 상황에서 최적의 전략을 선택할 수 있습니다!

[⬅️ 이전: 복잡한 충돌](./03-complex-conflicts.md) | [README로](../README.md) | [다음: Merge Tools ➡️](./05-merge-tools.md)

</div>
