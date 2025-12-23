# 2.1 브랜치 기본 (Branch Basics)

**난이도: ⭐**

> *"브랜치는 Git의 킬러 기능입니다. 브랜치를 이해하면 Git의 90%를 이해한 것입니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 브랜치를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 새 기능 개발 중인데 긴급 버그 발견!

팀장: "지금 하던 거 멈추고 긴급 버그부터 고쳐!"
당신: "으... 지금 코드가 반만 완성됐는데..."
팀장: "그럼 커밋해!"
당신: "빌드도 안 되는 코드를 커밋하라고요?"
팀장: "..."

# 브랜치 없이 개발
main 브랜치:
- 작업 중인 코드 (빌드 안 됨)
- 긴급 버그는 어떻게 고치지?
- 다른 팀원은 작업 못 함 (코드 깨짐)
```

**브랜치를 사용하면:**
```bash
# 1. 새 기능 개발 중
git switch -c feature/new-feature
# ... 작업 중 (main은 안전함)

# 2. 긴급 버그 발견!
git switch main              # main으로 전환
git switch -c hotfix/urgent  # 긴급 수정 브랜치
# ... 버그 수정
git commit -m "fix: Urgent bug"

# 3. main에 즉시 배포
git switch main
git merge hotfix/urgent
git push

# 4. 다시 기능 개발
git switch feature/new-feature
# ... 계속 작업

# ✅ main은 항상 안정적
# ✅ 동시에 여러 작업 가능
# ✅ 서로 간섭 없음
```

> 💡 **핵심:** 브랜치는 독립적인 작업 공간입니다.
> 동시에 여러 작업을 안전하게 진행할 수 있습니다!

---

### 📌 브랜치의 본질

#### **브랜치 = 커밋을 가리키는 포인터**

```bash
# 브랜치 파일 확인
cat .git/refs/heads/main
# abc123def456789...

# 이게 전부!
# 브랜치 = 커밋 Hash를 담은 텍스트 파일
```

**시각화:**
```
main 브랜치 파일:
┌─────────────────┐
│ abc123def456... │ ← 커밋 Hash
└─────────────────┘
       ↓
   commit_C
```

**브랜치의 진실:**
```
✅ 브랜치 = 40바이트 텍스트 파일
✅ 생성 비용 = 거의 0
✅ 수천 개 만들어도 OK
✅ 메모리/디스크 부담 없음

❌ "복사본" 아님!
❌ "디렉토리" 아님!
❌ "무거운 것" 아님!
```

---

### 🔍 브랜치 생성의 내부 동작

**명령어:**
```bash
git branch feature
```

**Git 내부:**
```bash
# 1. 현재 커밋 Hash 가져오기
CURRENT=$(git rev-parse HEAD)
# abc123def456...

# 2. 새 파일 생성
echo $CURRENT > .git/refs/heads/feature

# 끝!
```

**Before:**
```
.git/refs/heads/
└── main (abc123...)

    main
     ↓
commit_C
```

**After:**
```
.git/refs/heads/
├── main (abc123...)
└── feature (abc123...)  ← 새 파일!

    main, feature
         ↓
    commit_C
```

---

### 🌳 브랜치의 독립성

**시나리오: 두 브랜치에서 동시 작업**

```bash
# 초기 상태
    main
     ↓
commit_C
     ↓
commit_B
     ↓
commit_A

# feature 브랜치 생성
git switch -c feature

    main, feature
         ↓
    commit_C

# feature에서 커밋
echo "Feature work" > feature.txt
git add feature.txt
git commit -m "feat: Add feature"

    main        feature
     ↓            ↓
commit_C    commit_D (new!)
     ↓            ↓
commit_B    commit_C
     ↓            ↓
commit_A    commit_B

# main으로 전환
git switch main
ls
# feature.txt 없음! ← 독립적!

# main에서 커밋
echo "Main work" > main.txt
git add main.txt
git commit -m "chore: Update main"

          feature
             ↓
        commit_D
             ↓
    main     │
     ↓       │
commit_E    commit_C
     ↓       ↓
commit_C    commit_B
     ↓       ↓
commit_B    commit_A
```

**독립성:**
```
✅ 각 브랜치는 독립적인 커밋 히스토리
✅ 한 브랜치 변경이 다른 브랜치에 영향 없음
✅ 동시 작업 가능
✅ 나중에 merge로 합침
```

---

### 🔀 Fast-forward vs 3-way Merge

#### **1️⃣ Fast-forward Merge**

**조건:** 브랜치가 일직선상에 있을 때

```bash
# 초기 상태
    main, feature
         ↓
    commit_C

# feature에서만 작업
git switch feature
git commit -m "feat: New feature"

    main     feature
     ↓         ↓
commit_C  commit_D

# Merge
git switch main
git merge feature

# Fast-forward!
         main, feature
              ↓
         commit_D
              ↓
         commit_C
```

**특징:**
```
✅ 새 커밋 생성 안 함
✅ 포인터만 이동
✅ 히스토리 선형적
✅ 빠름
✅ 간단함

사용 시기:
- 혼자 작업
- 짧은 기간 브랜치
- main 업데이트 없을 때
```

---

#### **2️⃣ 3-way Merge**

**조건:** 브랜치가 갈라졌을 때

```bash
# 초기 상태
    main, feature
         ↓
    commit_C

# 양쪽에서 작업
# main:
git switch main
git commit -m "chore: Update"

# feature:
git switch feature
git commit -m "feat: Add feature"

# 상태:
      feature
         ↓
    commit_E
         ↓
         │    main
         │     ↓
         │  commit_D
         │     ↓
    commit_C ←┘

# Merge
git switch main
git merge feature

# 3-way Merge!
         main
          ↓
    commit_F (Merge!)
       ↙    ↘
commit_D  commit_E
       ↘    ↙
      commit_C
```

**특징:**
```
✅ 새 Merge 커밋 생성
✅ 부모가 2개 (main, feature)
✅ 히스토리 보존
✅ 명확한 merge 지점

사용 시기:
- 협업
- 긴 기간 브랜치
- 동시 개발
```

---

### 📊 Fast-forward vs 3-way 비교

| 특성 | Fast-forward | 3-way Merge |
|:-----|:------------|:-----------|
| **새 커밋** | 없음 | Merge 커밋 생성 |
| **히스토리** | 선형 | 비선형 (갈라짐) |
| **조건** | 일직선 | 갈라진 브랜치 |
| **속도** | 빠름 | 보통 |
| **복잡도** | 낮음 | 높음 |
| **충돌** | 없음 | 가능 |
| **사용** | 개인 작업 | 팀 협업 |

---

### ⚙️ --no-ff 옵션

**Fast-forward 강제로 막기:**

```bash
git merge --no-ff feature
```

**이유:**
```
✅ Merge 히스토리 명확
✅ 브랜치 흔적 남음
✅ Revert 쉬움
✅ 팀 협업 시 유용

# Fast-forward:
commit_D feat: Feature
commit_C main
commit_B
commit_A
# "어디서 feature 시작했지?"

# --no-ff:
commit_E (Merge feature)
   ↙    ↘
commit_D  commit_C
   ↓      ↓
feature  main
# "Feature가 여기서 merge됨!" 명확!
```

---

### 🏷️ 브랜치 네이밍 컨벤션

#### **일반적인 패턴:**

```
feature/기능명
fix/버그명
hotfix/긴급수정
release/버전
docs/문서작업
refactor/리팩토링
test/테스트
chore/기타작업
```

**예시:**
```bash
# 기능 개발
feature/user-authentication
feature/payment-integration
feature/dark-mode

# 버그 수정
fix/login-timeout
fix/null-pointer-exception

# 긴급 수정
hotfix/security-patch
hotfix/critical-bug

# 릴리스
release/v1.2.0
release/2024-Q1

# 문서
docs/api-documentation
docs/setup-guide

# 리팩토링
refactor/database-layer
refactor/api-structure

# 테스트
test/integration-tests
test/e2e-payment
```

---

#### **네이밍 규칙:**

```
✅ DO:
- 소문자 사용
- 하이픈(-) 구분자
- 명확하고 설명적
- 이슈 번호 포함 (선택)

❌ DON'T:
- 공백 사용
- 특수문자 (!@#$%)
- 너무 길게 (50자 넘기지 말기)
- 모호한 이름 (temp, test, branch1)

예시:
✅ feature/add-oauth-login
✅ fix/resolve-memory-leak-#123
✅ hotfix/xss-vulnerability

❌ feature/New Feature
❌ fix bug
❌ temp_branch_for_testing_stuff_20240101
```

---

#### **팀별 컨벤션 예시:**

**스타트업:**
```bash
<type>/<description>

feature/quick-checkout
fix/payment-error
```

**대기업:**
```bash
<team>/<type>/<ticket>-<description>

frontend/feature/JIRA-123-add-dashboard
backend/fix/JIRA-456-api-timeout
```

**오픈소스:**
```bash
<username>/<type>/<description>

john/feature/add-dark-mode
jane/fix/typo-in-readme
```

---

### 🔄 브랜치 라이프사이클

```
1. 생성 (Create)
   git switch -c feature/new-feature

2. 개발 (Develop)
   # ... 작업 ...
   git commit -m "feat: ..."

3. Push (Share)
   git push -u origin feature/new-feature

4. Pull Request (Review)
   # GitHub/GitLab에서 PR 생성
   # 코드 리뷰

5. Merge (Integrate)
   git switch main
   git merge feature/new-feature

6. 삭제 (Cleanup)
   git branch -d feature/new-feature
   git push origin --delete feature/new-feature

완료! ✅
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 브랜치 생성과 전환

#### **1️⃣ 브랜치 생성**

```bash
# 브랜치 생성 (전환 안 함)
git branch feature

# 특정 커밋에서 브랜치 생성
git branch feature abc123

# 원격 브랜치 기반으로 생성
git branch feature origin/develop
```

---

#### **2️⃣ 브랜치 전환**

```bash
# 최신 방식 (Git 2.23+)
git switch feature

# 생성하면서 전환
git switch -c feature

# 특정 커밋에서 생성+전환
git switch -c feature abc123

# 이전 브랜치로
git switch -

# 옛날 방식 (여전히 작동)
git checkout feature
git checkout -b feature
```

---

#### **3️⃣ switch vs checkout**

```bash
# switch: 브랜치 전환 전용
git switch main           # 브랜치로만 전환
git switch -c feature     # 생성+전환

# checkout: 만능 (헷갈림)
git checkout main         # 브랜치 전환
git checkout abc123       # 커밋 전환 (Detached!)
git checkout file.txt     # 파일 복원 (???)

# 권장: switch 사용!
✅ git switch main        # 명확함
✅ git restore file.txt   # 명확함

❌ git checkout main      # 뭐하는 건지 애매
```

---

### 🗑️ 브랜치 삭제

```bash
# 로컬 브랜치 삭제
git branch -d feature      # 안전 (merge 확인)
git branch -D feature      # 강제

# 원격 브랜치 삭제
git push origin --delete feature

# 여러 브랜치 한 번에
git branch -d feature1 feature2 feature3

# Merged 브랜치만 삭제
git branch --merged | grep -v "\*" | xargs git branch -d
```

---

### 📋 브랜치 조회

```bash
# 로컬 브랜치 목록
git branch

# 원격 브랜치 포함
git branch -a

# 원격 브랜치만
git branch -r

# 상세 정보 (마지막 커밋)
git branch -v

# Merge 여부 확인
git branch --merged      # Merge된 것
git branch --no-merged   # 안 된 것

# 특정 커밋 포함 브랜치
git branch --contains abc123

# 패턴으로 검색
git branch --list "feature/*"
```

---

### 🔀 브랜치 Merge

```bash
# 기본 Merge
git merge feature

# Fast-forward만
git merge --ff-only feature

# 항상 Merge 커밋
git merge --no-ff feature

# Merge 메시지 수정
git merge -m "Merge feature branch" feature

# Abort merge
git merge --abort

# Merge 후 브랜치 삭제
git merge feature && git branch -d feature
```

---

### 📊 명령어 비교표

#### **브랜치 전환 명령어**

| 명령어 | 동작 | 브랜치 없으면 | 권장도 |
|:------|:-----|:------------|:------|
| `switch <branch>` | 전환 | 에러 | ⭐⭐⭐ |
| `switch -c <branch>` | 생성+전환 | 생성 | ⭐⭐⭐ |
| `checkout <branch>` | 전환 | 에러 | ⭐ |
| `checkout -b <branch>` | 생성+전환 | 생성 | ⭐⭐ |

---

#### **브랜치 삭제 옵션**

| 옵션 | 조건 | 위험도 | 용도 |
|:-----|:-----|:------|:-----|
| `-d` | Merge 확인 | 안전 | 일반적 |
| `-D` | 강제 | 위험 | 버릴 브랜치 |
| `--delete` | `-d`와 같음 | 안전 | 가독성 |
| `--force` | `-D`와 같음 | 위험 | 명시적 |

---

### ⚡ 실행 결과 시각화

#### **Scenario: 브랜치 생성과 전환**

**초기:**
```
    main (HEAD)
     ↓
commit_C
```

**브랜치 생성:**
```bash
git branch feature
```

```
    main (HEAD), feature
         ↓
    commit_C
```

**전환:**
```bash
git switch feature
```

```
    main, feature (HEAD)
         ↓
    commit_C
```

**커밋:**
```bash
echo "new" > file.txt
git add file.txt
git commit -m "feat: Add feature"
```

```
    main      feature (HEAD)
     ↓           ↓
commit_C    commit_D
```

---

#### **Scenario: Fast-forward vs 3-way**

**Fast-forward:**
```
Before:
    main     feature
     ↓         ↓
commit_C  commit_D

git merge feature

After:
         main, feature
              ↓
         commit_D
              ↓
         commit_C
```

**3-way:**
```
Before:
      feature
         ↓
    commit_E
         ↓
         │    main
         │     ↓
         │  commit_D
         │     ↓
    commit_C ←┘

git merge feature

After:
         main
          ↓
    commit_F (Merge)
       ↙    ↘
commit_D  commit_E
       ↘    ↙
      commit_C
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 기능 개발 워크플로우

**목표:** 새 기능을 독립적으로 개발하고 안전하게 통합

#### **Problem: 새 기능 개발 중 main이 계속 업데이트된다!**

```bash
# 상황
# - 로그인 기능 개발 중 (3일 소요 예상)
# - 매일 main에 다른 팀원 커밋
# - 어떻게 독립적으로 작업하고 나중에 합치지?
```

#### **Solution: Feature Branch 워크플로우**

```bash
# Day 1: 브랜치 생성 및 작업 시작
git switch -c feature/user-login

echo "Login Page" > login.html
git add login.html
git commit -m "feat(auth): Add login page UI"

echo "Validation" > validation.js
git add validation.js
git commit -m "feat(auth): Add input validation"

# 상태:
#       feature/user-login
#            ↓
#       commit_B (validation)
#            ↓
#       commit_A (UI)
#            ↓
#  main ──→ commit_0

# Day 2: main 업데이트됨 (다른 팀원)
# main:
#       commit_X (팀원 작업)
#            ↓
#       commit_0

# feature는 독립적!
#       feature/user-login
#            ↓
#       commit_B
#            ↓
#       commit_A
#            ↓
#  main ──→ commit_X
#            ↓
#       commit_0

# Day 3: 계속 작업
echo "API" > api.js
git add api.js
git commit -m "feat(auth): Add login API integration"

# Day 4: 완성! Merge
git switch main
git pull  # 최신 main 가져오기

# Merge
git merge feature/user-login

# 충돌 없으면 완료!
# 충돌 있으면 해결 후:
git add .
git commit

# 정리
git branch -d feature/user-login
git push origin --delete feature/user-login

# ✅ 완료!
```

**베스트 프랙티스:**
```
1. 브랜치 이름 명확히
   feature/user-login (O)
   temp (X)

2. 작은 단위로 커밋
   매일 여러 커밋 (O)
   3일치 한 번에 (X)

3. 주기적으로 main 동기화
   git switch main
   git pull
   git switch feature/user-login
   git merge main

4. Merge 전 테스트
   완전히 동작 확인 후 merge
```

---

### 💼 Scenario 2: 긴급 버그 수정

**목표:** 작업 중단하지 않고 긴급 버그 처리

#### **Problem: 기능 개발 중인데 프로덕션에 긴급 버그!**

```bash
# 상황
# - feature/new-dashboard 개발 중
# - 코드 반만 완성 (빌드 안 됨)
# - 프로덕션에 결제 버그 발견!
# - 지금 즉시 수정 필요!
```

#### **Solution: Stash + Hotfix 브랜치**

```bash
# 1. 현재 작업 임시 저장
git stash push -m "WIP: dashboard development"

# 2. main으로 전환
git switch main
git pull

# 3. Hotfix 브랜치 생성
git switch -c hotfix/payment-bug

# 4. 버그 수정
echo "Fixed payment logic" >> payment.js
git add payment.js
git commit -m "fix(payment): Resolve double charge issue

Fix race condition causing duplicate charges
when users click submit multiple times.

Fixes #789
Priority: Critical"

# 5. main에 즉시 Merge
git switch main
git merge hotfix/payment-bug

# 6. 배포
git push
git tag v1.2.1
git push --tags

# 7. develop에도 Merge (Git Flow 사용 시)
git switch develop
git merge hotfix/payment-bug
git push

# 8. Hotfix 브랜치 정리
git branch -d hotfix/payment-bug

# 9. 원래 작업으로 복귀
git switch feature/new-dashboard
git stash pop

# ✅ 긴급 버그 처리 완료!
# ✅ 원래 작업 계속 가능!
```

**타임라인:**
```
10:00 - 기능 개발 중
10:30 - 긴급 버그 발견!
10:32 - Stash로 작업 임시 저장
10:35 - Hotfix 브랜치 생성
10:50 - 버그 수정 완료
11:00 - Main에 merge + 배포
11:10 - 원래 작업 복귀

소요 시간: 40분
원래 작업: 손실 없음 ✅
```

---

### 💼 Scenario 3: 동시 다발적 기능 개발

**목표:** 여러 기능을 동시에 독립적으로 개발

#### **Problem: 3개 기능을 동시에 개발해야 한다!**

```bash
# 요구사항
# 1. 사용자 프로필 편집 (2주)
# 2. 알림 시스템 (1주)
# 3. 다크 모드 (3일)

# 모두 동시 진행!
```

#### **Solution: 멀티 브랜치 전략**

```bash
# 1. 각 기능별 브랜치 생성
git switch -c feature/user-profile
git switch -c feature/notification
git switch -c feature/dark-mode

# 2. 다크 모드부터 (빠름)
git switch feature/dark-mode

# Day 1
git commit -m "feat(ui): Add dark mode toggle"
git commit -m "feat(ui): Apply dark theme to navbar"

# Day 2
git commit -m "feat(ui): Apply dark theme to all pages"

# Day 3
git commit -m "feat(ui): Add theme persistence"

# 완성! Merge
git switch main
git merge feature/dark-mode
git push
git branch -d feature/dark-mode

# 3. 알림 시스템 (중간)
git switch feature/notification

# Week 1
git commit -m "feat(notification): Add WebSocket connection"
git commit -m "feat(notification): Add notification UI"
git commit -m "feat(notification): Add notification API"

# 완성! Merge
git switch main
git merge feature/notification
git push
git branch -d feature/notification

# 4. 사용자 프로필 (느림)
git switch feature/user-profile

# Week 1
git commit -m "feat(profile): Add profile edit form"
git commit -m "feat(profile): Add avatar upload"

# Week 2
git commit -m "feat(profile): Add validation"
git commit -m "feat(profile): Add API integration"

# 완성! Merge
git switch main
git merge feature/user-profile
git push
git branch -d feature/user-profile

# ✅ 3개 기능 모두 완성!
# ✅ 독립적으로 개발
# ✅ 완성된 것부터 배포
```

**장점:**
```
✅ 빠른 것부터 배포
   - 다크 모드: 3일 후 배포
   - 알림: 1주 후 배포
   - 프로필: 2주 후 배포

✅ 서로 간섭 없음
   - 각자 독립적 개발
   - 충돌 최소화

✅ 유연한 우선순위
   - 긴급 요청 시 순서 변경 가능
   - 일부 기능 보류 가능
```

---

### 💼 Scenario 4: Long-Running 브랜치 관리

**목표:** 장기간 유지되는 브랜치 관리

#### **Problem: develop 브랜치가 main과 점점 멀어진다!**

```bash
# 상황
# - develop 브랜치에서 2주간 개발
# - 그동안 main에도 여러 커밋
# - develop과 main이 너무 달라짐
# - Merge 시 충돌 위험!
```

#### **Solution: 주기적 동기화**

```bash
# Week 1: 개발 시작
git switch -c develop
git commit -m "feat: Feature A"
git commit -m "feat: Feature B"

# main 업데이트 확인
git switch main
git pull
# 새 커밋 있음!

# develop에 main 변경사항 반영
git switch develop
git merge main
# 충돌 해결 (있다면)

# Week 2: 계속 개발
git commit -m "feat: Feature C"
git commit -m "feat: Feature D"

# 다시 main 동기화
git switch main
git pull

git switch develop
git merge main
# 또 충돌 해결

# 최종 Merge
git switch main
git merge develop
# 충돌 최소! (주기적 동기화 덕분)

# ✅ 안전하게 통합!
```

**동기화 전략:**
```
매일 동기화:
  git switch develop
  git merge main
  
  장점: 충돌 최소
  단점: 번거로움

주 1회 동기화:
  매주 금요일 main merge
  
  장점: 적당함
  단점: 가끔 충돌

Merge 직전만:
  git merge main
  
  장점: 간단함
  단점: 충돌 폭탄! ⚠️
```

---

### 💼 Scenario 5: 브랜치 이름 변경

**목표:** 잘못된 브랜치 이름 수정

#### **Problem: 브랜치 이름을 잘못 지었다!**

```bash
# 현재 브랜치
feture/user-login  # 오타! (feature → feture)

# Push도 이미 했음...
```

#### **Solution: 로컬 + 원격 모두 변경**

```bash
# 1. 로컬 브랜치 이름 변경
git branch -m feture/user-login feature/user-login

# 2. 원격에 새 이름으로 push
git push origin feature/user-login

# 3. Upstream 설정
git push --set-upstream origin feature/user-login

# 4. 옛날 이름 삭제
git push origin --delete feture/user-login

# ✅ 완료!

# 팀원들에게 알림
# "feture/user-login → feature/user-login으로 변경됨"
```

**현재 브랜치 이름 변경:**
```bash
# 현재 브랜치가 feture/user-login일 때
git branch -m feature/user-login

# 끝!
```

**다른 브랜치 이름 변경:**
```bash
# 현재 main인데 feature 브랜치 이름 변경
git branch -m old-name new-name
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: Detached HEAD에서 커밋**

```bash
# 위험한 패턴
git checkout abc123  # Detached HEAD!
# ... 작업 ...
git commit -m "Work"
git switch main      # ❌ 커밋 잃어버림!
```

**올바른 방법:**
```bash
# 브랜치로 전환
git switch main      # ✅

# 커밋 체크아웃 필요하면
git checkout abc123
# 즉시 브랜치 생성!
git switch -c temp-branch
```

---

#### ❌ **실수 2: 잘못된 브랜치에서 작업**

```bash
# main에서 작업하고 있었다!
git switch main
# ... 작업 ...
git commit -m "feat: New feature"  # ❌ main에 직접 커밋!
```

**복구 방법:**
```bash
# 1. 브랜치 생성 (현재 위치에서)
git branch feature/new-feature

# 2. main을 이전 커밋으로
git reset --hard HEAD~1

# 3. Feature 브랜치로 전환
git switch feature/new-feature

# ✅ 커밋이 feature로 이동됨!
```

---

#### ❌ **실수 3: Merge 전 pull 안 함**

```bash
# 위험한 패턴
git switch main
git merge feature    # ❌ 옛날 main!

# Push 시
git push             # ❌ Rejected!
```

**올바른 방법:**
```bash
# 항상 pull 먼저!
git switch main
git pull             # ✅ 최신 main

git merge feature
git push
```

---

#### ❌ **실수 4: 브랜치 삭제 후 복구 못함**

```bash
# 실수로 삭제
git branch -D important-feature  # ❌ 작업 날아감?
```

**복구 방법:**
```bash
# Reflog로 찾기
git reflog
# abc123 HEAD@{5}: commit: Last commit on important-feature

# 브랜치 재생성
git branch important-feature abc123

# ✅ 복구 완료!

# 또는 원격에 있다면
git fetch
git switch important-feature
```

---

### 🎯 실습 미션

#### **미션 1: 브랜치 워크플로우 마스터** 🎯

**목표:** 완전한 feature 브랜치 워크플로우 실습

```bash
# 1. 저장소 생성
git init branch-workflow && cd branch-workflow

# 2. Main 브랜치 초기화
echo "# Project" > README.md
git add README.md
git commit -m "docs: Initial commit"

# 3. 미션:
# - feature/login 브랜치 생성
# - 3개 파일 생성 (login.html, login.js, login.css)
# - 각 파일마다 커밋 (Conventional Commits)
# - main으로 merge
# - 브랜치 삭제

# 확인:
# git log --oneline --graph
# 4개 커밋 확인
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Feature 브랜치 생성
git switch -c feature/login

# 2. 파일 생성 및 커밋
echo "Login Page" > login.html
git add login.html
git commit -m "feat(auth): Add login page HTML"

echo "Login Logic" > login.js
git add login.js
git commit -m "feat(auth): Add login JavaScript logic"

echo "Login Style" > login.css
git add login.css
git commit -m "style(auth): Add login page styles"

# 3. Main으로 merge
git switch main
git merge feature/login

# 4. 브랜치 삭제
git branch -d feature/login

# 5. 확인
git log --oneline --graph
# * abc123 (HEAD -> main) style(auth): Add login page styles
# * def456 feat(auth): Add login JavaScript logic
# * ghi789 feat(auth): Add login page HTML
# * jkl012 docs: Initial commit

# ✅ 완벽!
```

</details>

---

#### **미션 2: Fast-forward vs 3-way 체험** 🔧

**목표:** 두 가지 merge 방식 직접 확인

```bash
# 준비
git init merge-types && cd merge-types
echo "V1" > file.txt
git add file.txt
git commit -m "Initial"

# 미션 Part 1: Fast-forward
# - feature1 브랜치 생성
# - 파일 수정 후 커밋
# - main에 merge
# - Fast-forward인지 확인 (git log --graph)

# 미션 Part 2: 3-way
# - feature2 브랜치 생성
# - main에서도 커밋
# - feature2에서도 커밋
# - main에 merge
# - Merge 커밋 생성 확인
```

<details>
<summary>정답 보기</summary>

```bash
# Part 1: Fast-forward
git switch -c feature1
echo "V2" >> file.txt
git add file.txt
git commit -m "feat: Update to V2"

git switch main
git merge feature1

git log --oneline --graph
# * abc123 (HEAD -> main, feature1) feat: Update to V2
# * def456 Initial
# Fast-forward! (선형적)

# Part 2: 3-way
git switch -c feature2
echo "Feature 2" > feature2.txt
git add feature2.txt
git commit -m "feat: Add feature 2"

git switch main
echo "Main work" > main.txt
git add main.txt
git commit -m "chore: Main work"

git merge feature2
# Merge commit 생성됨!

git log --oneline --graph
# *   ghi789 (HEAD -> main) Merge branch 'feature2'
# |\  
# | * jkl012 (feature2) feat: Add feature 2
# * | mno345 chore: Main work
# |/  
# * abc123 feat: Update to V2
# * def456 Initial
# 3-way merge! (갈라짐 보임)

# ✅ 차이 확인 완료!
```

</details>

---

#### **미션 3: 긴급 버그 수정 시뮬레이션** 🚨

**목표:** 실전 hotfix 워크플로우

```bash
# 시나리오:
# 1. feature/dashboard 개발 중
# 2. 파일 3개 생성 (아직 완성 안 됨)
# 3. 프로덕션 버그 발견!
# 4. Stash로 작업 저장
# 5. hotfix/critical 브랜치에서 버그 수정
# 6. main에 merge
# 7. 원래 작업 복귀

# 준비
git init hotfix-simulation && cd hotfix-simulation
echo "App" > app.js
git add app.js
git commit -m "Initial app"
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Feature 개발 시작
git switch -c feature/dashboard

echo "Dashboard 1" > dash1.js
git add dash1.js
git commit -m "wip: Dashboard part 1"

echo "Dashboard 2" > dash2.js
git add dash2.js
# Staged but not committed

echo "Dashboard 3" > dash3.js
# Untracked

# 2. 긴급 버그 발견! Stash
git stash push -m "WIP: Dashboard development"

# 3. Hotfix
git switch main
git switch -c hotfix/critical

echo "Bug fixed!" >> app.js
git add app.js
git commit -m "fix: Resolve critical production bug

Fix null pointer exception in payment processing.

Fixes #999
Priority: P0"

# 4. Main에 merge
git switch main
git merge hotfix/critical
git branch -d hotfix/critical

# 5. 원래 작업 복귀
git switch feature/dashboard
git stash pop

# 6. 확인
ls
# app.js dash1.js dash2.js dash3.js ← 모두 복구!

git status
# Changes to be committed: dash2.js
# Untracked files: dash3.js

# ✅ 완벽한 hotfix 워크플로우!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 브랜치 생성 ===
git branch <name>                # 생성만
git switch -c <name>             # 생성+전환
git branch <name> <commit>       # 특정 커밋에서

# === 브랜치 전환 ===
git switch <name>                # 브랜치로
git switch -                     # 이전 브랜치로

# === 브랜치 삭제 ===
git branch -d <name>             # 안전 삭제
git branch -D <name>             # 강제 삭제
git push origin --delete <name>  # 원격 삭제

# === 브랜치 조회 ===
git branch                       # 로컬
git branch -a                    # 전체
git branch -v                    # 상세
git branch --merged              # Merge 확인

# === Merge ===
git merge <branch>               # 기본
git merge --no-ff <branch>       # 항상 merge 커밋
git merge --ff-only <branch>     # Fast-forward만
git merge --abort                # 취소
```

---

### 🎨 네이밍 템플릿

```
feature/<description>     새 기능
fix/<description>         버그 수정
hotfix/<description>      긴급 수정
release/<version>         릴리스
docs/<description>        문서
refactor/<description>    리팩토링
test/<description>        테스트
chore/<description>       기타

예시:
feature/user-authentication
fix/payment-timeout
hotfix/security-patch-cve-2024
release/v2.0.0
docs/api-documentation
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [1.4 커밋 해부하기](../01-fundamentals/04-commit-anatomy.md)

**다음 학습:**
- [2.2 Git Flow](./02-gitflow.md)
- [2.3 GitHub Flow](./03-github-flow.md)

**관련 주제:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)
- [4.1 Rebase 기초](../04-rebase/01-rebase-basics.md)

---

### 💡 추가 팁

#### **유용한 Alias**

```bash
# .gitconfig에 추가
[alias]
  # 브랜치 관련
  br = branch
  sw = switch
  swc = switch -c
  
  # 빠른 전환
  co = checkout
  cob = checkout -b
  
  # 브랜치 정리
  brd = branch -d
  brD = branch -D
  
  # Merge
  mg = merge
  mgnf = merge --no-ff
  
  # 브랜치 목록
  bra = branch -a
  brv = branch -v
```

---

<div align="center">

**🎉 축하합니다! 브랜치 기본을 완전히 마스터했습니다!**

이제 Git의 가장 강력한 기능을 자유자재로 사용할 수 있습니다!

[⬅️ 이전: 커밋 해부](../01-fundamentals/04-commit-anatomy.md) | [README로](../README.md) | [다음: Git Flow ➡️](./02-gitflow.md)

</div>
