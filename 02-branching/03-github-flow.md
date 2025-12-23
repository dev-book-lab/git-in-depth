# 2.3 GitHub Flow

**난이도: ⭐⭐**

> *"GitHub Flow는 단순합니다. 하지만 그 단순함이 빠른 배포와 지속적 통합을 가능하게 합니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 GitHub Flow를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 빠르게 성장하는 스타트업

현재 문제:
- Git Flow는 너무 복잡
- develop, release, hotfix... 헷갈림
- 하루에 여러 번 배포하고 싶음
- QA 프로세스가 자동화되어 있음
- 빠른 실험과 롤백 필요

CTO: "우리한테 Git Flow는 과해..."
개발자: "브랜치가 너무 많아요"
DevOps: "배포가 너무 느려요"
CEO: "경쟁사보다 빠르게 움직여야 해요!"
```

**GitHub Flow를 사용하면:**
```bash
main (단 하나의 브랜치!)
  ↓
항상 배포 가능
  ↓
모든 배포는 main에서

feature/new-feature
  ↓
PR 생성 → 리뷰 → Merge → 자동 배포
  ↓
완료!

✅ 단순함 (브랜치 1개!)
✅ 빠른 배포 (하루 10번+)
✅ PR 중심 협업
✅ 자동화 친화적
```

> 💡 **핵심:** GitHub Flow는 main 브랜치 하나로 
> 빠른 배포와 지속적 통합을 실현합니다!

---

### 📌 GitHub Flow의 핵심 원칙

```
┌─────────────────────────────────────────────────┐
│            GitHub Flow 핵심 6원칙                 │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. main은 항상 배포 가능                           │
│  2. 기능별로 브랜치 생성                             │
│  3. 정기적으로 커밋                                 │
│  4. Pull Request 생성                            │
│  5. 리뷰 후 Merge                                │
│  6. 즉시 배포                                     │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

#### **원칙 1: main은 항상 배포 가능**

```
특징:
✅ 언제든 배포 가능
✅ 모든 테스트 통과
✅ CI 성공 상태
✅ 직접 커밋 금지

Git Flow와 비교:
Git Flow: master (배포) + develop (개발)
GitHub Flow: main (배포 = 개발)

왜 가능한가?
→ 철저한 자동화 테스트
→ PR 리뷰 프로세스
→ Feature Flags
```

---

#### **원칙 2: 기능별로 브랜치 생성**

```
네이밍:
feature/user-login
fix/payment-bug
docs/update-readme
refactor/database-layer

규칙:
✅ main에서 분기
✅ 설명적인 이름
✅ 작게 나누기 (1-2일)
✅ 완성 후 삭제

Git Flow와 비교:
Git Flow: feature/, release/, hotfix/
GitHub Flow: 모두 동일하게 취급
```

---

#### **원칙 3: 정기적으로 커밋**

```
전략:
✅ 작은 단위로 커밋
✅ 자주 커밋
✅ 의미 있는 메시지
✅ Push도 자주

이유:
- 작업 내용 공유
- 협업 활성화
- 백업 효과
- 리뷰 부담 감소

Anti-pattern:
❌ 3일치 작업을 한 번에 커밋
❌ "WIP" 커밋만 반복
❌ Push 안 하고 로컬에만
```

---

#### **원칙 4: Pull Request 생성**

```
PR의 역할:
✅ 코드 리뷰 플랫폼
✅ 논의 공간
✅ CI/CD 트리거
✅ 문서화

PR 생성 시점:
- 완성 후? ❌
- 시작할 때! ✅ (Draft PR)

장점:
- 일찍 피드백
- 방향 논의
- 협업 활성화
```

---

#### **원칙 5: 리뷰 후 Merge**

```
리뷰 프로세스:
1. PR 생성
2. CI 자동 실행
3. 팀원 리뷰
4. 수정 반영
5. Approve
6. Merge

필수 조건:
✅ CI 통과
✅ 1명 이상 Approve
✅ 충돌 해결
✅ 코드 규칙 준수

Merge 방법:
- Merge commit (기본)
- Squash and merge
- Rebase and merge
```

---

#### **원칙 6: 즉시 배포**

```
배포 전략:
main merge → 자동 배포

자동화:
GitHub Actions/Circle CI
 ↓
테스트 실행
 ↓
빌드
 ↓
배포 (Staging)
 ↓
검증
 ↓
배포 (Production)

롤백:
문제 발견 시 즉시 revert
또는 Hotfix PR
```

---

### 🔄 GitHub Flow 전체 흐름

```
1. main에서 브랜치 생성
   git switch -c feature/new-feature

2. 개발 및 커밋
   git commit -m "feat: Add feature"
   git push

3. Pull Request 생성
   GitHub에서 PR 생성
   
4. 코드 리뷰
   팀원 리뷰 및 논의
   CI 자동 실행

5. 수정 반영
   피드백에 따라 수정
   추가 커밋

6. Merge
   Approve 받으면 merge
   main에 통합

7. 자동 배포
   CI/CD가 자동 배포
   
8. 브랜치 삭제
   merge 후 자동 삭제
```

**시각화:**
```
main  ─●─────────────●─────────●─
        │            │         │
        └─feature/A──┘  배포   delete

시간 순서:
1. 브랜치 생성
2. 개발 (여러 커밋)
3. PR 생성
4. 리뷰
5. Merge
6. 자동 배포
7. 삭제
```

---

### 📊 Git Flow vs GitHub Flow

| 특성 | Git Flow | GitHub Flow |
|:-----|:---------|:-----------|
| **브랜치 수** | 5종류 | 사실상 1종류 |
| **복잡도** | 높음 | 낮음 |
| **배포 속도** | 느림 (주/월) | 빠름 (하루 여러 번) |
| **릴리스 주기** | 계획적 | 지속적 |
| **QA** | Release 브랜치 | 자동화 테스트 |
| **Hotfix** | 별도 브랜치 | 일반 PR |
| **버전 관리** | 태그 | 커밋 Hash |
| **팀 크기** | 대규모 | 소-중규모 |
| **적합한 곳** | 패키지 SW | 웹 서비스 |

---

### 🎯 GitHub Flow의 장단점

#### **장점 ✅**

```
1. 단순성
   - 브랜치 1개 (main)
   - 학습 곡선 낮음
   - 헷갈릴 일 없음

2. 빠른 배포
   - PR merge → 즉시 배포
   - 하루 10번+ 가능
   - 실험과 롤백 쉬움

3. PR 중심 협업
   - 모든 변경이 PR로
   - 코드 리뷰 자연스러움
   - 논의 기록 남음

4. 자동화 친화적
   - CI/CD 설정 간단
   - 테스트 자동 실행
   - 배포 자동화

5. 작은 팀에 적합
   - 오버헤드 최소
   - 빠른 의사결정
   - 유연한 프로세스
```

---

#### **단점 ❌**

```
1. 버전 관리 어려움
   - 명확한 버전 없음
   - 여러 버전 동시 지원 불가
   - 롤백 복잡할 수 있음

2. 스테이징 없음
   - QA 환경 별도 구성 필요
   - main = production
   - 테스트 부담 큼

3. 대규모 팀 어려움
   - PR 병목 현상
   - 리뷰 프로세스 부담
   - 충돌 가능성

4. 자동화 의존
   - 테스트 커버리지 필수
   - CI/CD 구축 필요
   - 자동화 없으면 위험

5. Feature Flags 필요
   - 미완성 코드 배포 방지
   - 점진적 롤아웃
   - 추가 복잡성
```

---

### 🏢 GitHub Flow 적합한 프로젝트

#### **✅ GitHub Flow를 써야 할 때:**

```
1. 웹 서비스/SaaS
   - 단일 프로덕션 환경
   - 사용자가 버전 선택 안 함
   - 즉시 업데이트 가능

2. 빠른 배포 필요
   - 하루 여러 번 배포
   - A/B 테스트 빈번
   - 빠른 실험

3. 자동화 구축됨
   - CI/CD 파이프라인
   - 자동 테스트 충분
   - 모니터링 체계

4. 작은 팀
   - 2-10명
   - 빠른 의사결정
   - 단순한 프로세스 선호

예시:
- SaaS 플랫폼
- 웹 애플리케이션
- API 서비스
- 스타트업 제품
- 내부 도구
```

---

#### **❌ GitHub Flow를 피해야 할 때:**

```
1. 여러 버전 지원
   - v1.x, v2.x 동시 운영
   - 고객별 버전 다름
   - 장기 지원 필요

2. 느린 릴리스 주기
   - 월/분기 단위 배포
   - 긴 QA 프로세스
   - 승인 절차 복잡

3. 자동화 부족
   - 수동 테스트
   - 배포 수동
   - 모니터링 미흡

4. 대규모 팀
   - 10명 이상 개발팀
   - 여러 팀 협업
   - 명확한 역할 필요

대안:
- Git Flow: 여러 버전 지원
- GitLab Flow: 환경별 브랜치
- Trunk-Based: 더 빠른 통합
```

---

### 🔑 GitHub Flow 성공 요소

#### **1️⃣ 철저한 자동화**

```
필수 자동화:
✅ 단위 테스트
✅ 통합 테스트
✅ 린트/포맷 체크
✅ 보안 스캔
✅ 빌드
✅ 배포

CI/CD 파이프라인:
PR 생성
  ↓
테스트 실행 (자동)
  ↓
코드 리뷰 (수동)
  ↓
Merge
  ↓
배포 (자동)
  ↓
모니터링 (자동)
```

---

#### **2️⃣ 높은 테스트 커버리지**

```
권장 수치:
- 단위 테스트: 80%+
- 통합 테스트: 주요 플로우
- E2E 테스트: 핵심 시나리오

이유:
main이 곧 production
→ 버그 있으면 즉시 영향
→ 테스트가 유일한 안전망
```

---

#### **3️⃣ Feature Flags**

```
역할:
- 미완성 코드 숨기기
- 점진적 롤아웃
- A/B 테스트
- 긴급 킬 스위치

예시:
if (featureFlags.newPayment) {
  // 새 결제 시스템
} else {
  // 기존 결제 시스템
}

장점:
- main에 merge해도 안전
- 사용자별 활성화
- 빠른 롤백
```

---

#### **4️⃣ 빠른 리뷰 문화**

```
목표:
PR 생성 → 리뷰 완료: 24시간 이내

방법:
- 작은 PR (200줄 이내)
- 명확한 설명
- 담당자 지정
- 자동 알림

리뷰 체크리스트:
□ 코드 품질
□ 테스트 충분
□ 문서화
□ 성능 영향
□ 보안 이슈
```

---

#### **5️⃣ 모니터링 & 알람**

```
필수 모니터링:
✅ 에러율
✅ 응답 시간
✅ 사용자 행동
✅ 서버 리소스

알람 설정:
- 에러 급증
- 응답 시간 증가
- 배포 실패
- 테스트 실패

목적:
문제 조기 발견
→ 빠른 롤백
→ 영향 최소화
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 GitHub Flow 워크플로우 명령어

#### **1️⃣ 브랜치 생성 및 작업**

```bash
# main 최신화
git switch main
git pull origin main

# 새 브랜치 생성
git switch -c feature/user-profile

# 개발 진행
git add .
git commit -m "feat: Add user profile page"

# 정기적으로 push
git push -u origin feature/user-profile
```

---

#### **2️⃣ 지속적 개발**

```bash
# 계속 작업
git add .
git commit -m "feat: Add profile edit form"
git push

# main 변경사항 반영 (선택)
git switch main
git pull
git switch feature/user-profile
git merge main

# 또는 rebase
git rebase main
```

---

#### **3️⃣ Pull Request 준비**

```bash
# 최종 정리 (선택)
git rebase -i main  # Interactive rebase

# 최신 main 반영
git switch main
git pull
git switch feature/user-profile
git rebase main

# 강제 push (rebase 후)
git push --force-with-lease
```

---

#### **4️⃣ Merge 후 정리**

```bash
# main 업데이트
git switch main
git pull

# 브랜치 삭제 (로컬)
git branch -d feature/user-profile

# 브랜치 삭제 (원격)
git push origin --delete feature/user-profile

# 또는 GitHub에서 자동 삭제 설정
```

---

### 📋 GitHub PR 명령어 (CLI)

#### **GitHub CLI 설치**

```bash
# macOS
brew install gh

# Ubuntu
sudo apt install gh

# Windows
winget install GitHub.cli

# 인증
gh auth login
```

---

#### **PR 생성 및 관리**

```bash
# PR 생성
gh pr create --title "Add user profile" --body "Implements user profile page"

# Draft PR 생성
gh pr create --draft

# PR 목록
gh pr list

# PR 상태 확인
gh pr status

# PR 체크아웃
gh pr checkout 123

# PR merge
gh pr merge 123

# PR 리뷰
gh pr review 123 --approve
gh pr review 123 --comment -b "Looks good!"
gh pr review 123 --request-changes -b "Please fix..."
```

---

### 🔀 Merge 전략

#### **1️⃣ Merge Commit (기본)**

```bash
# GitHub에서 "Merge pull request" 클릭

# 결과:
main  ─●─────────●─
        │         ↗
        └─feat──┘

# 특징:
✅ 전체 히스토리 보존
✅ PR 흔적 명확
❌ 히스토리 복잡할 수 있음
```

---

#### **2️⃣ Squash and Merge**

```bash
# GitHub에서 "Squash and merge" 선택

# 결과:
main  ─●───────●─
               ↑
          (모든 커밋 합쳐짐)

# 특징:
✅ 깔끔한 히스토리
✅ 하나의 커밋으로
❌ 중간 히스토리 손실
```

**언제 사용:**
```
✅ 작은 PR
✅ 실험적 커밋 많음
✅ 깔끔한 히스토리 원함

예시:
- WIP 커밋 10개 → 1개로
- "Fix typo" 커밋들 제거
```

---

#### **3️⃣ Rebase and Merge**

```bash
# GitHub에서 "Rebase and merge" 선택

# 결과:
main  ─●─●─●─●─
       ↑ ↑ ↑ ↑
     feat 커밋들이 main 위에

# 특징:
✅ 선형 히스토리
✅ 커밋 개별 보존
❌ Merge 커밋 없음
```

**언제 사용:**
```
✅ 깔끔한 커밋 히스토리
✅ 선형적 히스토리 원함
✅ 각 커밋이 의미 있음

주의:
- 공개 브랜치는 주의
- 히스토리 재작성됨
```

---

### 📊 명령어 비교표

#### **Merge 전략 비교**

| 전략 | 히스토리 | 커밋 수 | PR 흔적 | 복잡도 |
|:-----|:---------|:--------|:--------|:------|
| **Merge** | 비선형 | 모두 보존 | 명확 | 높음 |
| **Squash** | 선형 | 1개로 합침 | 없음 | 낮음 |
| **Rebase** | 선형 | 모두 보존 | 없음 | 중간 |

---

#### **권장 전략**

| 상황 | 권장 전략 | 이유 |
|:-----|:----------|:-----|
| 작은 PR | Squash | 깔끔한 히스토리 |
| 큰 PR | Merge | 전체 맥락 보존 |
| 완벽한 커밋 | Rebase | 선형 + 보존 |
| 긴급 Hotfix | Squash | 빠른 롤백 |

---

### ⚡ 실행 결과 시각화

#### **Scenario: 전체 GitHub Flow**

**1. 브랜치 생성:**
```
main   ─●─
        │
feature └─●
```

**2. 개발 진행:**
```
main   ─●─────────
        │
feature └─●─●─●─●
```

**3. PR 생성 및 리뷰:**
```
main   ─●─────────────
        │
feature └─●─●─●─●─●─
              ↑
        리뷰 & 수정
```

**4. Merge (Squash):**
```
main  ─●─────────────●─
                     ↑
             (feature 전체 squash)

feature 브랜치 삭제됨
```

**5. 배포:**
```
main  ─●─────────────●─
                     ↓
                  자동 배포
                     ↓
                 Production
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 첫 PR 생성부터 배포까지

**목표:** GitHub Flow 전체 사이클 경험

#### **Problem: 처음으로 기능을 개발하고 배포한다!**

```bash
# 요구사항
# - 로그인 페이지 추가
# - 코드 리뷰 받기
# - 배포까지 완료
```

#### **Solution: 완벽한 GitHub Flow**

```bash
# ===== 1. 최신 main 가져오기 =====
git switch main
git pull origin main

# ===== 2. Feature 브랜치 생성 =====
git switch -c feature/login-page

# ===== 3. 개발 시작 =====
# login.html 생성
cat > login.html << 'EOF'
<!DOCTYPE html>
<html>
<head><title>Login</title></head>
<body>
  <h1>Login Page</h1>
  <form id="login-form">
    <input type="email" placeholder="Email">
    <input type="password" placeholder="Password">
    <button type="submit">Login</button>
  </form>
</body>
</html>
EOF

git add login.html
git commit -m "feat(auth): Add login page HTML structure"

# ===== 4. 계속 개발 =====
# login.js 생성
cat > login.js << 'EOF'
document.getElementById('login-form').addEventListener('submit', (e) => {
  e.preventDefault();
  // Login logic
});
EOF

git add login.js
git commit -m "feat(auth): Add login form validation"

# login.css 생성
cat > login.css << 'EOF'
.login-form {
  max-width: 400px;
  margin: 0 auto;
}
EOF

git add login.css
git commit -m "style(auth): Add login page styling"

# ===== 5. 테스트 작성 =====
cat > login.test.js << 'EOF'
test('login form validation', () => {
  // Test code
});
EOF

git add login.test.js
git commit -m "test(auth): Add login form tests"

# ===== 6. Push =====
git push -u origin feature/login-page

# ===== 7. Draft PR 생성 (일찍!) =====
gh pr create --draft \
  --title "feat(auth): Add login page" \
  --body "## What
  
Add user login functionality
  
## Why

Users need to authenticate to access protected features

## How

- HTML form structure
- JavaScript validation
- CSS styling
- Unit tests

## Testing

- [ ] Manual testing
- [x] Unit tests
- [ ] E2E tests

## Screenshots

(Add screenshots here)"

# ===== 8. 피드백 반영 =====
# 리뷰어: "Please add error handling"

cat >> login.js << 'EOF'

function handleError(error) {
  alert('Login failed: ' + error.message);
}
EOF

git add login.js
git commit -m "feat(auth): Add error handling for login"
git push

# ===== 9. Draft → Ready =====
gh pr ready

# ===== 10. CI 자동 실행 =====
# GitHub Actions:
# ✅ Lint check
# ✅ Unit tests
# ✅ Build
# ✅ Security scan

# ===== 11. 리뷰 받기 =====
# 리뷰어 1: Approved ✅
# 리뷰어 2: Approved ✅

# ===== 12. Merge =====
gh pr merge --squash

# ===== 13. 자동 배포 트리거 =====
# GitHub Actions:
# 1. Build production bundle
# 2. Run integration tests
# 3. Deploy to staging
# 4. Smoke test
# 5. Deploy to production
# 6. Notify team

# ===== 14. 정리 =====
git switch main
git pull
git branch -d feature/login-page

# ===== 15. 모니터링 =====
# - 에러율 확인
# - 로그인 성공률 추적
# - 사용자 피드백 확인

# ✅ 완료! 첫 기능 배포 성공!
```

**타임라인:**
```
Day 1 오전:
- 브랜치 생성
- 기본 구조 개발
- Draft PR 생성

Day 1 오후:
- 기능 완성
- 테스트 작성
- PR Ready

Day 2:
- 코드 리뷰
- 피드백 반영
- Merge & 배포

총 소요: 1-2일
```

---

### 💼 Scenario 2: 빠른 Hotfix

**목표:** 프로덕션 버그 긴급 수정

#### **Problem: 프로덕션에서 로그인 버그 발견!**

```bash
# 상황
# - 시간: 월요일 오전 10시
# - 증상: 특정 브라우저에서 로그인 실패
# - 우선순위: P0 (최고)
# - 영향: 전체 사용자의 15%
```

#### **Solution: 긴급 Hotfix PR**

```bash
# ===== 10:00 - 버그 리포트 =====
# Slack 알림: "Login failing on Safari"

# ===== 10:05 - 재현 =====
# Safari에서 로그인 시도 → 실패 확인

# ===== 10:10 - Hotfix 시작 =====
git switch main
git pull
git switch -c hotfix/safari-login

# ===== 10:15 - 버그 원인 파악 =====
# login.js 확인
# 문제: ES6 구문 Safari 미지원

# ===== 10:20 - 수정 =====
# Before:
# const form = document.getElementById('login-form');
# form?.addEventListener('submit', handleSubmit);

# After:
# var form = document.getElementById('login-form');
# if (form) {
#   form.addEventListener('submit', handleSubmit);
# }

git add login.js
git commit -m "fix(auth): Fix Safari compatibility issue

Use ES5 syntax for Safari 12 compatibility.
Affects ~15% of users.

Root cause: Optional chaining not supported
Solution: Use explicit null check

Fixes #123
Priority: P0"

# ===== 10:30 - 테스트 =====
# Safari 12, 13, 14 테스트
# ✅ 모두 정상 동작

git add login.test.js
git commit -m "test(auth): Add Safari compatibility test"

# ===== 10:40 - PR 생성 =====
git push -u origin hotfix/safari-login

gh pr create \
  --title "🚨 Hotfix: Safari login compatibility" \
  --body "## Problem

Login failing on Safari 12-14 (~15% users)

## Root Cause

Optional chaining (?.) not supported in Safari 12

## Solution

Use explicit null check (ES5 compatible)

## Testing

- [x] Safari 12 ✅
- [x] Safari 13 ✅
- [x] Safari 14 ✅
- [x] Chrome (regression) ✅
- [x] Firefox (regression) ✅

## Impact

Fixes login for ~50,000 users

## Rollback Plan

Revert this PR if needed" \
  --label "hotfix,priority:P0"

# ===== 10:45 - 긴급 리뷰 요청 =====
gh pr review --request @tech-lead

# ===== 10:50 - 리뷰 완료 =====
# Tech Lead: "LGTM! Merge ASAP"

# ===== 10:55 - Merge =====
gh pr merge --squash

# ===== 11:00 - 자동 배포 =====
# CI/CD:
# ✅ Build
# ✅ Test
# ✅ Deploy to staging
# ✅ Smoke test
# ✅ Deploy to production

# ===== 11:10 - 검증 =====
# 모니터링:
# - Safari 로그인 성공률: 60% → 98% ✅
# - 에러율 감소: -95% ✅

# ===== 11:15 - 공지 =====
# Slack: "Hotfix deployed. Safari login fixed."

# ✅ 1시간 15분 만에 해결!
```

**Hotfix 체크리스트:**
```
□ 버그 재현
□ 원인 파악
□ 최소 변경으로 수정
□ 테스트 추가
□ 회귀 테스트
□ 긴급 리뷰
□ Merge
□ 배포
□ 검증
□ 모니터링
□ 팀 공지
```

---

### 💼 Scenario 3: Feature Flags 활용

**목표:** 미완성 기능을 안전하게 main에 merge

#### **Problem: 새 결제 시스템 개발 중인데 2주 걸린다!**

```bash
# 문제
# - 새 결제 시스템 개발 (2주 소요)
# - main과 점점 멀어짐
# - 충돌 위험 증가
# - 하지만 완성 전에 배포할 수 없음!
```

#### **Solution: Feature Flags로 안전하게**

```bash
# ===== Week 1 Day 1: Feature Flag 설정 =====
# featureFlags.js 생성
cat > featureFlags.js << 'EOF'
const featureFlags = {
  newPaymentSystem: process.env.ENABLE_NEW_PAYMENT === 'true'
};

export default featureFlags;
EOF

git add featureFlags.js
git commit -m "feat(config): Add feature flags system"
git push origin main
# 즉시 merge (아무 영향 없음)

# ===== Week 1 Day 2-3: 결제 시스템 개발 시작 =====
git switch -c feature/new-payment

# 새 결제 모듈 개발
cat > newPayment.js << 'EOF'
export function processPayment(order) {
  // 새로운 결제 로직
  // 아직 미완성...
}
EOF

git add newPayment.js
git commit -m "feat(payment): Add new payment module (WIP)"

# ===== Week 1 Day 4: Feature Flag 적용 =====
cat > checkout.js << 'EOF'
import featureFlags from './featureFlags.js';
import { processPayment as oldPayment } from './oldPayment.js';
import { processPayment as newPayment } from './newPayment.js';

function checkout(order) {
  if (featureFlags.newPaymentSystem) {
    return newPayment(order);  // 새 시스템 (개발 중)
  } else {
    return oldPayment(order);   // 기존 시스템 (안정적)
  }
}
EOF

git add checkout.js
git commit -m "feat(payment): Integrate new payment with feature flag"

# ===== Week 1 Day 5: PR 생성 및 Merge! =====
git push origin feature/new-payment

gh pr create --title "feat(payment): Add new payment system (behind flag)"

# 리뷰어: "미완성이지만 feature flag로 숨겨져 있으니 OK!"
gh pr merge --squash

# ✅ main에 merge됨!
# ✅ 하지만 사용자는 영향 없음 (flag OFF)

# ===== Week 2: 계속 개발 =====
# 이제 main에서 작업!
git switch main
git pull

# 새 기능 추가
git switch -c feature/payment-refund
# ... 개발 ...
git commit -m "feat(payment): Add refund support"

# 매일 merge
gh pr create --title "feat(payment): Add refund"
gh pr merge --squash

# main과 동기화 문제 없음!

# ===== Week 2 말: 완성 =====
# 모든 기능 완료
# 테스트 완료

# ===== Week 3: 점진적 롤아웃 =====
# 1. 내부 테스트 (개발팀만)
# ENV: ENABLE_NEW_PAYMENT=true

# 2. 베타 테스터 (5%)
cat > featureFlags.js << 'EOF'
const featureFlags = {
  newPaymentSystem: 
    process.env.ENABLE_NEW_PAYMENT === 'true' ||
    Math.random() < 0.05  // 5% 사용자
};
EOF

# 3. 25% 롤아웃
# Math.random() < 0.25

# 4. 50% 롤아웃
# Math.random() < 0.50

# 5. 100% 롤아웃
cat > featureFlags.js << 'EOF'
const featureFlags = {
  newPaymentSystem: true  // 전체 활성화!
};
EOF

# ===== Week 4: Flag 제거 =====
# 이제 안정적으로 동작 확인
# Feature flag 제거 가능

cat > checkout.js << 'EOF'
import { processPayment } from './newPayment.js';

function checkout(order) {
  return processPayment(order);  // 새 시스템만!
}
EOF

git add checkout.js
git commit -m "refactor(payment): Remove feature flag (100% rollout)"

# ✅ 완료!
# ✅ 안전한 롤아웃
# ✅ 쉬운 롤백 (flag OFF)
```

**Feature Flags 장점:**
```
✅ 미완성 코드도 main에
✅ 충돌 최소화
✅ 점진적 롤아웃
✅ 빠른 롤백
✅ A/B 테스트 가능
✅ 사용자별 활성화
```

---

### 💼 Scenario 4: PR 리뷰 최적화

**목표:** 효과적인 코드 리뷰 프로세스

#### **Problem: PR 리뷰가 병목이다!**

```bash
# 문제점
# - PR이 일주일씩 대기
# - 리뷰어가 바빠서 못 봄
# - PR이 너무 커서 리뷰 힘듦
# - 피드백 반영에 또 시간 소요
```

#### **Solution: 작은 PR + 빠른 리뷰**

```bash
# ===== 전략 1: PR 작게 나누기 =====

# ❌ 나쁜 예: 하나의 거대한 PR
git switch -c feature/entire-user-system
# 30개 파일, 3000줄 변경
# 리뷰어: "이거 언제 다 봐..." (포기)

# ✅ 좋은 예: 여러 작은 PR

# PR 1: 데이터 모델
git switch -c feature/user-model
# 3개 파일, 150줄
git commit -m "feat(user): Add user data model"
gh pr create --title "Add user model (1/5)"
# 리뷰: 30분 ✅

# PR 2: API 엔드포인트
git switch main && git pull
git switch -c feature/user-api
# 4개 파일, 200줄
git commit -m "feat(user): Add user API endpoints"
gh pr create --title "Add user API (2/5)"
# 리뷰: 40분 ✅

# PR 3: 인증 로직
git switch main && git pull
git switch -c feature/user-auth
# 3개 파일, 180줄
git commit -m "feat(user): Add authentication"
gh pr create --title "Add authentication (3/5)"
# 리뷰: 35분 ✅

# PR 4: UI 컴포넌트
git switch main && git pull
git switch -c feature/user-ui
# 5개 파일, 250줄
git commit -m "feat(user): Add user UI components"
gh pr create --title "Add user UI (4/5)"
# 리뷰: 45분 ✅

# PR 5: 통합 테스트
git switch main && git pull
git switch -c feature/user-tests
# 2개 파일, 150줄
git commit -m "test(user): Add integration tests"
gh pr create --title "Add user tests (5/5)"
# 리뷰: 30분 ✅

# 결과:
# - 5개 PR, 각각 하루 안에 리뷰 완료
# - 총 소요: 5일 → 5일 (변동 없음)
# - 하지만 병렬 진행 가능!
# - 리뷰 품질 ↑ (집중 가능)

# ===== 전략 2: Draft PR 일찍 만들기 =====

# 작업 시작하자마자
git switch -c feature/new-dashboard
echo "// WIP" > dashboard.js
git add dashboard.js
git commit -m "feat(dashboard): Initial structure"
git push origin feature/new-dashboard

# Draft PR 생성
gh pr create --draft \
  --title "WIP: New dashboard" \
  --body "## Plan

1. Layout component
2. Widget system
3. Data fetching
4. Real-time updates

## Progress

- [x] Initial structure
- [ ] Layout
- [ ] Widgets
- [ ] Data
- [ ] Real-time

Feedback welcome on the approach!"

# 장점:
# - 방향성 피드백 일찍 받음
# - "이거 잘못된 방향이에요" → 초기에 발견
# - 리뷰어가 진행 상황 추적 가능

# ===== 전략 3: 자동화로 리뷰 부담 줄이기 =====

# .github/workflows/pr-checks.yml
cat > .github/workflows/pr-checks.yml << 'EOF'
name: PR Checks
on: pull_request

jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm run lint
      
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm test
      
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm audit
EOF

# 자동으로 체크:
# ✅ Lint (스타일)
# ✅ Test (기능)
# ✅ Security (보안)
# ✅ Coverage (커버리지)

# 리뷰어는 로직만 집중!

# ===== 전략 4: PR 템플릿 =====

# .github/pull_request_template.md
cat > .github/pull_request_template.md << 'EOF'
## What

<!-- 무엇을 변경했나요? -->

## Why

<!-- 왜 이 변경이 필요한가요? -->

## How

<!-- 어떻게 구현했나요? -->

## Testing

- [ ] Unit tests added
- [ ] Integration tests added
- [ ] Manual testing done

## Screenshots

<!-- UI 변경이 있다면 스크린샷 -->

## Checklist

- [ ] Tests pass
- [ ] Documentation updated
- [ ] No breaking changes
- [ ] Backward compatible
EOF

# PR 생성 시 자동으로 템플릿 적용
# → 리뷰어가 맥락 빠르게 파악

# ===== 전략 5: 리뷰 응답 시간 목표 =====

# 팀 규칙 설정
# - 긴급 PR: 2시간 이내
# - 일반 PR: 24시간 이내
# - 대규모 PR: 48시간 이내

# Slack 알림 설정
# - PR 생성 시: #code-review 채널 알림
# - 24시간 경과: 리마인더
# - Approve 필요: 담당자 멘션

# ✅ 결과:
# - 리뷰 대기 시간: 7일 → 1일
# - 리뷰 품질 향상
# - 팀 생산성 증가
```

**PR 리뷰 베스트 프랙티스:**
```
크기:
✅ 200줄 이내
✅ 1-2일 작업량
✅ 하나의 목적

설명:
✅ What/Why/How 명확
✅ 테스트 방법 제시
✅ 스크린샷 (UI 변경 시)

리뷰:
✅ 24시간 이내 응답
✅ 구체적 피드백
✅ 긍정적 표현
```

---

### 💼 Scenario 5: 롤백 프로세스

**목표:** 문제 발생 시 빠른 롤백

#### **Problem: 배포 후 심각한 버그 발견!**

```bash
# 상황
# - 새 기능 배포 (커밋 abc123)
# - 10분 후 에러율 급증
# - 사용자 불만 쇄도
# - 즉시 롤백 필요!
```

#### **Solution: Revert PR**

```bash
# ===== 방법 1: Revert PR (권장) =====

# 1. 문제 커밋 확인
git log --oneline -5
# abc123 feat(payment): Add new payment
# def456 fix(auth): ...
# ...

# 2. main에서 Revert
git switch main
git pull
git revert abc123 -m "Revert: Add new payment

Reverting due to critical bug causing payment failures.

Issue: #789
Rolling back to investigate"

git push origin main

# 3. PR 생성
gh pr create --title "🔥 Revert: New payment feature" \
  --body "## Problem

New payment feature causing:
- Payment failure rate: 5% → 45%
- Error: 'undefined is not a function'

## Solution

Revert commit abc123 to restore stability

## Impact

- Reverts new payment UI
- Restores old payment flow
- All payments working again

## Next Steps

1. Investigate root cause
2. Fix in separate PR
3. Add more tests
4. Re-deploy with fix"

# 4. 즉시 Merge
gh pr merge --squash

# 5. 자동 배포
# CI/CD가 자동으로 revert 배포

# 6. 검증
# - 에러율: 45% → 5% ✅
# - 결제 성공률 정상 ✅

# ✅ 5분 만에 롤백 완료!

# ===== 방법 2: 이전 커밋으로 강제 배포 (긴급) =====

# 더 긴급한 경우
git switch main
git reset --hard def456  # 이전 정상 커밋
git push --force origin main

# ⚠️ 주의: 히스토리 변경!
# 팀원들에게 즉시 공지 필요

# ===== 방법 3: Feature Flag로 즉시 비활성화 =====

# Feature Flag가 있다면
cat > featureFlags.js << 'EOF'
const featureFlags = {
  newPayment: false  // 즉시 비활성화!
};
EOF

git add featureFlags.js
git commit -m "hotfix: Disable new payment feature"
git push

# PR 생성 및 즉시 merge
gh pr create --title "Disable new payment"
gh pr merge --squash

# ✅ 1분 만에 비활성화!
```

**롤백 의사결정 트리:**
```
버그 발견
  ↓
심각도 판단
  ↓
┌─────────┬─────────┐
│         │         │
P0        P1        P2
(즉시)    (빠르게)  (계획적)
│         │         │
↓         ↓         ↓
Revert   Hotfix   다음 릴리스
즉시      PR       에 포함
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: PR이 너무 큼**

```bash
# 위험한 패턴
git switch -c feature/huge
# 50개 파일 변경, 5000줄
gh pr create  # 리뷰어: "..." (포기)
```

**올바른 방법:**
```bash
# 작게 나누기
git switch -c feature/part1
# 5개 파일, 200줄
gh pr create --title "Part 1/5"

git switch -c feature/part2
# ...
```

---

#### ❌ **실수 2: main에 직접 push**

```bash
# 위험한 패턴
git switch main
git commit -m "Quick fix"
git push origin main  # ❌ PR 없이!
```

**올바른 방법:**
```bash
# 항상 브랜치 + PR
git switch -c hotfix/quick-fix
git commit -m "fix: Quick fix"
git push origin hotfix/quick-fix
gh pr create
```

---

#### ❌ **실수 3: CI 실패한 PR merge**

```bash
# 위험한 패턴
# CI: ❌ Tests failed
# 개발자: "나중에 고치지 뭐"
gh pr merge  # ❌ 위험!
```

**올바른 방법:**
```bash
# CI 통과 필수!
# CI: ❌ Tests failed
# 개발자: 테스트 수정
git commit -m "test: Fix failing tests"
git push
# CI: ✅ All checks passed
gh pr merge  # ✅ 안전!
```

---

#### ❌ **실수 4: 리뷰 없이 merge**

```bash
# 위험한 패턴
gh pr create
# 30초 후...
gh pr merge  # ❌ 리뷰 없이!
```

**올바른 방법:**
```bash
# 최소 1명 Approve 필요
gh pr create
# 리뷰 대기...
# Approve 받은 후
gh pr merge  # ✅
```

---

### 🎯 실습 미션

#### **미션 1: 완전한 GitHub Flow** 🎯

**목표:** 브랜치 → PR → Merge → 배포 전체 경험

```bash
# 준비
mkdir github-flow-practice && cd github-flow-practice
git init
echo "# Project" > README.md
git add README.md
git commit -m "docs: Initial commit"

# main 브랜치로 이름 변경
git branch -M main

# 미션:
# 1. Feature 브랜치 생성 (login)
# 2. 3개 파일 생성 + 커밋
# 3. GitHub에 push (실제 repo 생성)
# 4. PR 생성
# 5. (시뮬레이션) 리뷰 + Merge
# 6. 브랜치 정리

# 힌트:
# gh repo create --public
```

<details>
<summary>정답 보기</summary>

```bash
# 1. GitHub repo 생성
gh repo create github-flow-practice --public --source=. --push

# 2. Feature 브랜치
git switch -c feature/login

# 3. 파일 생성
echo "Login Page" > login.html
git add login.html
git commit -m "feat(auth): Add login HTML"

echo "Login Script" > login.js
git add login.js
git commit -m "feat(auth): Add login JavaScript"

echo "Login Style" > login.css
git add login.css
git commit -m "style(auth): Add login CSS"

# 4. Push
git push -u origin feature/login

# 5. PR 생성
gh pr create \
  --title "feat(auth): Add login page" \
  --body "Add user login functionality"

# 6. (시뮬레이션) 리뷰 및 Approve
gh pr review --approve

# 7. Merge
gh pr merge --squash

# 8. 정리
git switch main
git pull
git branch -d feature/login

# 9. 확인
git log --oneline
# abc123 feat(auth): Add login page
# def456 docs: Initial commit

# ✅ 완벽한 GitHub Flow!
```

</details>

---

#### **미션 2: Feature Flag 실습** 🚩

**목표:** Feature Flag로 안전하게 배포

```bash
# 시나리오:
# 1. Feature Flag 시스템 구축
# 2. 새 기능을 flag 뒤에 숨기기
# 3. main에 merge
# 4. flag 활성화로 기능 켜기
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Feature Flag 시스템
git switch -c feature/feature-flags

cat > featureFlags.js << 'EOF'
const flags = {
  newDashboard: false,
  betaFeatures: false
};

module.exports = flags;
EOF

git add featureFlags.js
git commit -m "feat(config): Add feature flags"
git push origin feature/feature-flags

# PR 생성 및 merge
gh pr create --title "Add feature flags"
gh pr merge --squash

# 2. 새 기능 (flag 뒤에)
git switch main
git pull
git switch -c feature/new-dashboard

cat > dashboard.js << 'EOF'
const flags = require('./featureFlags');

function renderDashboard() {
  if (flags.newDashboard) {
    return 'New Dashboard!';
  } else {
    return 'Old Dashboard';
  }
}

module.exports = { renderDashboard };
EOF

git add dashboard.js
git commit -m "feat(dashboard): Add new dashboard (behind flag)"
git push origin feature/new-dashboard

# 3. PR 및 merge
gh pr create --title "Add new dashboard (behind flag)"
gh pr merge --squash

# 4. Flag 활성화
git switch main
git pull
git switch -c feature/enable-dashboard

cat > featureFlags.js << 'EOF'
const flags = {
  newDashboard: true,  // 활성화!
  betaFeatures: false
};

module.exports = flags;
EOF

git add featureFlags.js
git commit -m "feat(dashboard): Enable new dashboard"
git push origin feature/enable-dashboard

gh pr create --title "Enable new dashboard"
gh pr merge --squash

# ✅ 안전하게 점진적 배포!
```

</details>

---

#### **미션 3: Hotfix 시뮬레이션** 🚨

**목표:** 긴급 버그 수정 프로세스

```bash
# 시나리오:
# 1. "프로덕션" 코드 준비
# 2. 버그 발견!
# 3. Hotfix 브랜치
# 4. 수정 및 빠른 배포
# 5. 검증
```

<details>
<summary>정답 보기</summary>

```bash
# 1. "프로덕션" 준비
git switch main
echo "function login() { retrun true; }" > auth.js  # 오타!
git add auth.js
git commit -m "feat(auth): Add login"
git push

# 2. 버그 발견!
# "retrun" → "return" 오타

# 3. Hotfix
git switch -c hotfix/fix-typo

# 4. 수정
echo "function login() { return true; }" > auth.js
git add auth.js
git commit -m "fix(auth): Fix typo in login function

Critical typo causing syntax error.
'retrun' → 'return'

Fixes #999
Priority: P0"

git push origin hotfix/fix-typo

# 5. PR (긴급)
gh pr create \
  --title "🚨 Hotfix: Fix critical typo" \
  --body "## Problem
Typo causing syntax error in login

## Fix
'retrun' → 'return'

## Impact
Fixes login for all users"

# 6. 빠른 리뷰 및 Merge
gh pr review --approve
gh pr merge --squash

# 7. 검증
git switch main
git pull
cat auth.js
# function login() { return true; }  ✅

# ✅ Hotfix 완료!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 기본 워크플로우 ===
git switch main && git pull
git switch -c feature/name
# ... 개발 ...
git push -u origin feature/name
gh pr create
# ... 리뷰 ...
gh pr merge --squash

# === GitHub CLI ===
gh pr create                # PR 생성
gh pr create --draft        # Draft PR
gh pr list                  # PR 목록
gh pr view 123              # PR 보기
gh pr checkout 123          # PR 체크아웃
gh pr merge 123             # PR merge

# === 리뷰 ===
gh pr review 123 --approve
gh pr review 123 --comment -b "LGTM"
gh pr review 123 --request-changes

# === 롤백 ===
git revert <commit>         # Revert PR
git push --force            # 강제 롤백 (긴급)
```

---

### 🎨 PR 템플릿 예시

```markdown
## What

<!-- 무엇을 변경했나요? -->

## Why

<!-- 왜 필요한가요? -->

## How

<!-- 어떻게 구현했나요? -->

## Testing

- [ ] Unit tests
- [ ] Integration tests
- [ ] Manual testing

## Screenshots

<!-- UI 변경 시 스크린샷 -->

## Checklist

- [ ] Tests pass
- [ ] Documentation updated
- [ ] No breaking changes
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [2.2 Git Flow](./02-gitflow.md)

**다음 학습:**
- [2.4 Trunk-Based Dev](./04-trunk-based.md)
- [2.5 Release 관리](./05-release-management.md)

**관련 주제:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)
- [4.1 Rebase](../04-rebase/01-rebase-basics.md)

---

### 💡 추가 팁

#### **GitHub Actions 예시**

```yaml
# .github/workflows/main.yml
name: CI/CD

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm test
      
  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
```

---

<div align="center">

**🎉 축하합니다! GitHub Flow를 완전히 마스터했습니다!**

이제 빠르고 안전한 배포를 할 수 있습니다!

[⬅️ 이전: Git Flow](./02-gitflow.md) | [README로](../README.md) | [다음: Trunk-Based ➡️](./04-trunk-based.md)

</div>
