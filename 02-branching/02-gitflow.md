# 2.2 Git Flow

**난이도: ⭐⭐**

> *"Git Flow는 복잡하지만 강력합니다. 체계적인 릴리스 관리가 필요할 때 최고의 선택입니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Git Flow를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 중견기업 제품 개발팀

현재 문제:
- 개발/스테이징/프로덕션 환경 따로 관리
- 여러 버전 동시 지원 (v1.x, v2.x)
- 긴급 패치 필요 시 혼란
- QA 테스트 타이밍 애매

팀장: "main 브랜치만으로는 한계야..."
개발자: "어떤 브랜치가 배포용인지 헷갈려요"
QA: "테스트할 코드가 계속 바뀌어요!"
운영: "핫픽스를 어디에 배포해야 하죠?"
```

**Git Flow를 사용하면:**
```bash
master (main)
  ↓
프로덕션 환경 (v1.0, v1.1, v2.0...)
  ↓
항상 안정적, 태그로 버전 관리

develop
  ↓
개발 환경
  ↓
다음 릴리스 준비, 통합 테스트

feature/*
  ↓
개별 기능 개발
  ↓
완성되면 develop에 merge

release/*
  ↓
QA/스테이징 환경
  ↓
버그 수정, 최종 검증

hotfix/*
  ↓
긴급 패치
  ↓
master와 develop 동시 적용

✅ 명확한 역할 분담
✅ 체계적인 릴리스
✅ 안정적인 운영
```

> 💡 **핵심:** Git Flow는 5가지 브랜치 타입으로 
> 체계적인 개발/릴리스/운영 프로세스를 구축합니다!

---

### 📌 Git Flow의 5가지 브랜치

```
┌─────────────────────────────────────────────────┐
│              Git Flow 브랜치 구조                  │
├─────────────────────────────────────────────────┤
│                                                 │
│  master (main)     ←─ 프로덕션 (항상 배포 가능)       │
│      ↑                                          │
│      │                                          │
│  develop           ←─ 다음 릴리스 준비              │
│   ↗  ↖                                          │
│  ↗     ↖                                        │
│ feature/*          ←─ 새 기능 개발                 │
│                                                 │
│ release/*          ←─ 릴리스 준비 (QA)             │
│                                                 │
│ hotfix/*           ←─ 긴급 패치                   │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

#### **1️⃣ master (main) - 프로덕션 브랜치**

**역할:** 실제 배포되는 코드

```
특징:
✅ 항상 안정적
✅ 배포 가능 상태
✅ 태그로 버전 관리
✅ 직접 커밋 금지!

라이프사이클:
생성: 프로젝트 시작 시
업데이트: release 또는 hotfix merge 시만
삭제: 절대 안 함

예시:
master
  ↓
v2.0.0 (tag) ← 최신 릴리스
  ↓
v1.1.0 (tag)
  ↓
v1.0.0 (tag)
```

**규칙:**
```bash
# ❌ 절대 안 됨
git switch master
git commit -m "Quick fix"  # 금지!

# ✅ 오직 merge만
git merge release/1.2.0
git merge hotfix/critical-bug
```

---

#### **2️⃣ develop - 개발 통합 브랜치**

**역할:** 다음 릴리스를 위한 개발 통합

```
특징:
✅ 최신 개발 코드
✅ 모든 feature가 여기로 merge
✅ 항상 빌드 가능
✅ 통합 테스트 대상

라이프사이클:
생성: 프로젝트 시작 시 (master에서 분기)
업데이트: feature merge, release 종료 후
삭제: 절대 안 함

히스토리:
develop
  ↓
feat: Add login ← feature/login merge
  ↓
feat: Add payment ← feature/payment merge
  ↓
feat: Add dashboard ← feature/dashboard merge
  ↓
initial commit
```

**규칙:**
```bash
# ✅ feature merge
git switch develop
git merge --no-ff feature/new-feature

# ✅ 직접 커밋도 가능 (작은 수정)
git commit -m "chore: Update dependencies"

# ❌ 불안정한 코드 금지
# 빌드 깨지는 커밋 금지!
```

---

#### **3️⃣ feature/* - 기능 개발 브랜치**

**역할:** 개별 기능 독립 개발

```
특징:
✅ develop에서 분기
✅ develop으로 merge
✅ 기능별로 생성
✅ 완성 후 삭제

네이밍:
feature/user-authentication
feature/payment-integration
feature/social-login
feature/JIRA-123-add-dashboard

라이프사이클:
1. develop에서 생성
2. 기능 개발
3. develop에 merge
4. 삭제
```

**워크플로우:**
```bash
# 1. 생성
git switch develop
git switch -c feature/user-login

# 2. 개발
git commit -m "feat: Add login UI"
git commit -m "feat: Add login API"
git commit -m "test: Add login tests"

# 3. Merge
git switch develop
git merge --no-ff feature/user-login

# 4. 삭제
git branch -d feature/user-login

# 5. 정리 (원격도)
git push origin --delete feature/user-login
```

---

#### **4️⃣ release/* - 릴리스 준비 브랜치**

**역할:** 배포 전 최종 검증

```
특징:
✅ develop에서 분기
✅ master와 develop에 merge
✅ 버그 수정만 가능
✅ 새 기능 추가 금지!

네이밍:
release/1.0.0
release/2024-Q1
release/v2.1.0

라이프사이클:
1. develop에서 생성 (기능 완성 후)
2. QA 테스트
3. 버그 수정
4. master에 merge (배포)
5. develop에도 merge (버그 수정 반영)
6. 삭제
```

**워크플로우:**
```bash
# 1. 생성 (기능 개발 완료 시)
git switch develop
git switch -c release/1.2.0

# 2. 버전 정보 업데이트
echo "1.2.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.2.0"

# 3. QA 테스트 중 버그 발견
git commit -m "fix: Resolve login timeout"
git commit -m "fix: Fix payment calculation"

# 4. Master에 merge (배포!)
git switch master
git merge --no-ff release/1.2.0
git tag -a v1.2.0 -m "Release version 1.2.0"

# 5. Develop에도 merge (버그 수정 반영)
git switch develop
git merge --no-ff release/1.2.0

# 6. 삭제
git branch -d release/1.2.0
```

**중요 규칙:**
```
Release 브랜치에서는:
✅ 버그 수정
✅ 문서 수정
✅ 버전 번호 업데이트
✅ 빌드 스크립트 수정

❌ 새 기능 추가 금지!
❌ 대규모 리팩토링 금지!

이유: 릴리스 범위가 확정되어야 함
```

---

#### **5️⃣ hotfix/* - 긴급 패치 브랜치**

**역할:** 프로덕션 긴급 수정

```
특징:
✅ master에서 분기
✅ master와 develop에 merge
✅ 즉시 배포
✅ 버전 번호 증가 (patch)

네이밍:
hotfix/critical-security-bug
hotfix/payment-failure
hotfix/1.2.1

라이프사이클:
1. master에서 생성
2. 버그 수정
3. master에 merge (즉시 배포)
4. develop에도 merge
5. 삭제
```

**워크플로우:**
```bash
# 1. 프로덕션 버그 발견!
git switch master
git switch -c hotfix/payment-bug

# 2. 긴급 수정
git commit -m "fix: Resolve payment double charge

Fix race condition in payment processing.
Priority: P0, Critical

Fixes #999"

# 3. 버전 업데이트
echo "1.2.1" > VERSION
git commit -m "chore: Bump version to 1.2.1"

# 4. Master에 merge (즉시 배포!)
git switch master
git merge --no-ff hotfix/payment-bug
git tag -a v1.2.1 -m "Hotfix: Payment bug"
git push origin master --tags

# 5. Develop에도 merge
git switch develop
git merge --no-ff hotfix/payment-bug

# 6. 삭제
git branch -d hotfix/payment-bug
```

**Release 중 Hotfix가 필요하면?**
```bash
# Release 브랜치가 있을 때
# develop 대신 release에 merge!

git switch master
git switch -c hotfix/urgent

# ... 수정 ...

git switch master
git merge --no-ff hotfix/urgent

# Develop 대신 Release에!
git switch release/1.3.0
git merge --no-ff hotfix/urgent

# Release 종료 시 develop으로 전달됨
```

---

### 🔄 Git Flow 전체 흐름

```
1. 프로젝트 시작
   master ─→ develop (분기)

2. 기능 개발
   develop ─→ feature/* (분기)
   feature/* ─→ develop (merge)

3. 릴리스 준비
   develop ─→ release/* (분기)
   
4. QA & 버그 수정
   release/* (버그 수정)

5. 배포
   release/* ─→ master (merge + tag)
   release/* ─→ develop (merge)

6. 긴급 패치
   master ─→ hotfix/* (분기)
   hotfix/* ─→ master (merge + tag)
   hotfix/* ─→ develop (merge)
```

**시각화:**
```
master     ─●─────────────●─────●───
            │             │     │
            │         v1.0.0  v1.0.1
            │                 │
develop  ───●─●─●─────●─●─●───●─●─●─
             ↗ ↗ ↗     ↓     ↗
            │ │ │       │   │
feature/*   ●─┘ └───────┘   │
                 │           │
release/*        └───────────┘
                     │
hotfix/*             └───────────────┘
```

---

### 📊 버전 관리 전략

#### **Semantic Versioning (SemVer)**

```
버전 형식: MAJOR.MINOR.PATCH

예시: 1.2.3
      ↑ ↑ ↑
      │ │ └─ PATCH: 버그 수정 (호환성 유지)
      │ └─── MINOR: 새 기능 추가 (호환성 유지)
      └───── MAJOR: 호환성 깨지는 변경

변경 규칙:
- 버그 수정 → PATCH 증가 (1.2.3 → 1.2.4)
- 새 기능 → MINOR 증가 (1.2.3 → 1.3.0)
- Breaking → MAJOR 증가 (1.2.3 → 2.0.0)
```

**Git Flow와 SemVer:**
```
feature/* → develop
  └─ MINOR 버전 증가 예정

release/* → master
  └─ 실제 MINOR 버전 증가
  └─ 태그: v1.3.0

hotfix/* → master
  └─ PATCH 버전 증가
  └─ 태그: v1.3.1
```

---

### 🎯 Git Flow의 장단점

#### **장점 ✅**

```
1. 명확한 역할 분담
   - master: 프로덕션
   - develop: 개발
   - feature: 기능
   - release: QA
   - hotfix: 긴급

2. 체계적인 릴리스
   - 계획된 배포
   - QA 프로세스 명확
   - 버전 관리 체계적

3. 병렬 작업 지원
   - 여러 버전 동시 지원
   - 긴급 패치 독립적
   - 기능 개발 독립적

4. 안정성
   - master 항상 안정적
   - 배포 신뢰도 높음
   - 롤백 쉬움

5. 대규모 팀에 적합
   - 명확한 워크플로우
   - 충돌 최소화
   - 역할 분담 명확
```

---

#### **단점 ❌**

```
1. 복잡성
   - 브랜치 5종류
   - 워크플로우 복잡
   - 학습 곡선 높음

2. 오버헤드
   - merge 작업 많음
   - 브랜치 관리 부담
   - CI/CD 설정 복잡

3. 느린 배포
   - develop → release → master
   - QA 프로세스 필수
   - 빠른 배포 어려움

4. 작은 팀에 과함
   - 2-3명 팀에는 오버킬
   - 스타트업에는 무거움
   - 빠른 실험 어려움

5. Merge 충돌 가능
   - release ↔ develop 동기화
   - hotfix 적용 시 주의
   - 장기 feature 문제
```

---

### 🏢 Git Flow 적합한 프로젝트

#### **✅ Git Flow를 써야 할 때:**

```
1. 명확한 릴리스 주기
   - 월/분기 단위 배포
   - QA 프로세스 필요
   - 계획된 릴리스

2. 여러 버전 지원
   - v1.x, v2.x 동시 지원
   - 장기 지원(LTS) 필요
   - 고객별 버전 관리

3. 엄격한 품질 관리
   - 금융/의료 서비스
   - 대규모 엔터프라이즈
   - 높은 안정성 요구

4. 대규모 팀
   - 10명 이상 개발팀
   - 여러 팀 협업
   - 명확한 역할 필요

예시:
- 패키지 소프트웨어
- 모바일 앱 (스토어 심사)
- 엔터프라이즈 솔루션
- 금융/의료 시스템
```

---

#### **❌ Git Flow를 피해야 할 때:**

```
1. 빠른 배포 필요
   - 하루에 여러 번 배포
   - CI/CD 자동화
   - 빠른 실험

2. 작은 팀
   - 2-5명 팀
   - 스타트업
   - 복잡성 부담

3. 웹 서비스
   - SaaS 플랫폼
   - 사용자 버전 선택 불가
   - 롤백 쉬움

4. 오픈소스
   - 기여자 관리 복잡
   - PR 중심 워크플로우
   - GitHub Flow가 더 적합

대안:
- GitHub Flow: 웹 서비스
- Trunk-Based: CI/CD 중심
- GitLab Flow: 환경별 브랜치
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 Git Flow 설치 및 초기화

#### **1️⃣ Git Flow 확장 도구 설치**

```bash
# macOS
brew install git-flow

# Ubuntu/Debian
apt-get install git-flow

# Windows (Git Bash)
# Git for Windows에 포함됨

# 확인
git flow version
```

---

#### **2️⃣ 저장소 초기화**

```bash
# Git Flow 초기화
git flow init

# 질문에 답변:
# Branch name for production releases: [master]
# Branch name for "next release" development: [develop]
# Feature branches prefix: [feature/]
# Release branches prefix: [release/]
# Hotfix branches prefix: [hotfix/]
# Support branches prefix: [support/]
# Version tag prefix: []

# 기본값 사용 (권장)
git flow init -d
```

**초기화 결과:**
```bash
# 2개 브랜치 생성됨
git branch
# * develop
#   master

# .git/config에 설정 추가됨
[gitflow "branch"]
    master = master
    develop = develop
[gitflow "prefix"]
    feature = feature/
    release = release/
    hotfix = hotfix/
```

---

### 🌟 Feature 브랜치 명령어

```bash
# Feature 시작
git flow feature start user-login
# = git switch -c feature/user-login develop

# Feature 목록
git flow feature list

# Feature 완료 (develop에 merge)
git flow feature finish user-login
# = git switch develop
# = git merge --no-ff feature/user-login
# = git branch -d feature/user-login

# Feature 게시 (원격에 push)
git flow feature publish user-login
# = git push origin feature/user-login

# Feature 가져오기
git flow feature pull origin user-login

# Feature 추적
git flow feature track user-login
# = git switch -b feature/user-login origin/feature/user-login
```

---

### 🚀 Release 브랜치 명령어

```bash
# Release 시작
git flow release start 1.2.0
# = git switch -c release/1.2.0 develop

# Release 게시
git flow release publish 1.2.0

# Release 완료
git flow release finish 1.2.0
# = git switch master
# = git merge --no-ff release/1.2.0
# = git tag -a 1.2.0
# = git switch develop
# = git merge --no-ff release/1.2.0
# = git branch -d release/1.2.0

# Release 완료 (메시지 자동)
git flow release finish -m "Release 1.2.0" 1.2.0
```

---

### 🚨 Hotfix 브랜치 명령어

```bash
# Hotfix 시작
git flow hotfix start 1.2.1
# = git switch -c hotfix/1.2.1 master

# Hotfix 완료
git flow hotfix finish 1.2.1
# = git switch master
# = git merge --no-ff hotfix/1.2.1
# = git tag -a 1.2.1
# = git switch develop
# = git merge --no-ff hotfix/1.2.1
# = git branch -d hotfix/1.2.1

# Hotfix 완료 (메시지 자동)
git flow hotfix finish -m "Hotfix 1.2.1" 1.2.1
```

---

### 📊 명령어 비교표

#### **Git Flow vs 수동 명령어**

| Git Flow | 수동 명령어 | 설명 |
|:---------|:-----------|:-----|
| `git flow feature start X` | `git switch -c feature/X develop` | Feature 시작 |
| `git flow feature finish X` | `git switch develop && git merge --no-ff feature/X && git branch -d feature/X` | Feature 완료 |
| `git flow release start X` | `git switch -c release/X develop` | Release 시작 |
| `git flow release finish X` | `master merge + tag + develop merge + delete` | Release 완료 |
| `git flow hotfix start X` | `git switch -c hotfix/X master` | Hotfix 시작 |

---

#### **브랜치 타입별 명령어**

| 타입 | 시작 | 완료 | 분기점 | Merge 대상 |
|:-----|:-----|:-----|:------|:----------|
| **feature** | `start <name>` | `finish <name>` | develop | develop |
| **release** | `start <version>` | `finish <version>` | develop | master + develop |
| **hotfix** | `start <version>` | `finish <version>` | master | master + develop |

---

### ⚡ 실행 결과 시각화

#### **Scenario: Feature 전체 과정**

**초기:**
```
master  ●
         
develop ●
```

**Feature 시작:**
```bash
git flow feature start login
```

```
master  ●
         
develop ●
        │
feature/login ●
```

**Feature 개발:**
```bash
git commit -m "feat: Add login UI"
git commit -m "feat: Add login API"
```

```
master  ●
         
develop ●
        │
feature/login ●─●
```

**Feature 완료:**
```bash
git flow feature finish login
```

```
master  ●
         
develop ●─●─● (merge from feature/login)
        
(feature/login 삭제됨)
```

---

#### **Scenario: Release + Hotfix**

```
1. Release 시작
master  ●
         
develop ●─●─●
            │
release/1.0 └─●

2. Release 중 버그 수정
master  ●
         
develop ●─●─●
            │
release/1.0 └─●─●

3. Release 완료
master  ●─────●─[v1.0]
                ↗
develop ●─●─●─●

4. Hotfix 필요!
master  ●─────●─[v1.0]
                │
hotfix/1.0.1    └─●

5. Hotfix 완료
master  ●─────●─[v1.0]─●─[v1.0.1]
                        ↓
develop ●─●─●─●─────────●
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 프로젝트 Git Flow 도입

**목표:** 기존 프로젝트에 Git Flow 적용

#### **Problem: 기존에 main 브랜치만 사용하던 프로젝트!**

```bash
# 현재 상태
main ─●─●─●─●─● (모든 커밋이 여기)
```

#### **Solution: Git Flow로 전환**

```bash
# 1. Git Flow 초기화
git flow init -d

# 자동으로 develop 생성됨
# main ─●─●─●─●─●
#                 ↓
#       develop  ─● (같은 위치에서 분기)

# 2. 규칙 정하기
# - main: 배포만
# - develop: 개발 통합
# - feature/*: 기능 개발
# - release/*: QA
# - hotfix/*: 긴급 패치

# 3. 팀원들에게 공지
cat > BRANCHING.md << 'EOF'
# Branching Strategy: Git Flow

## 브랜치 종류
- `main`: 프로덕션 (직접 커밋 금지!)
- `develop`: 개발 통합
- `feature/*`: 새 기능
- `release/*`: 릴리스 준비
- `hotfix/*`: 긴급 패치

## 워크플로우
1. 새 기능 개발:
   ```bash
   git flow feature start my-feature
   # ... 개발 ...
   git flow feature finish my-feature
   ```

2. 릴리스:
   ```bash
   git flow release start 1.0.0
   # ... QA & 버그 수정 ...
   git flow release finish 1.0.0
   ```

3. 긴급 패치:
   ```bash
   git flow hotfix start 1.0.1
   # ... 수정 ...
   git flow hotfix finish 1.0.1
   ```
EOF

git add BRANCHING.md
git commit -m "docs: Add Git Flow guide"

# 4. 현재 작업 중인 기능들을 feature로
git switch develop
git switch -c feature/existing-work

# 기존 커밋들을 feature로 이동 (필요시)
# 또는 새로운 작업만 feature로

# 5. CI/CD 업데이트
# - main: 프로덕션 배포
# - develop: 개발 환경 배포
# - feature/*: PR 테스트만

# ✅ Git Flow 도입 완료!
```

**마이그레이션 전략:**
```
점진적 도입 (권장):
1주차: Git Flow 교육
2주차: develop 브랜치 활성화
3주차: feature 브랜치 사용 시작
4주차: release 프로세스 도입

한번에 전환:
- 작은 팀 (< 5명)
- 새 프로젝트 시작
- 명확한 이해
```

---

### 💼 Scenario 2: 릴리스 주기 관리

**목표:** 2주마다 정기 릴리스

#### **Problem: 2주마다 배포하는데 매번 혼란스럽다!**

```bash
# 현재 문제
# - 언제 feature 개발 멈춰야 하나?
# - QA는 언제 시작?
# - 버그 수정은 어디서?
```

#### **Solution: 체계적인 릴리스 사이클**

```bash
# ===== Week 1-2: 개발 기간 =====
# Feature 개발
git flow feature start feature-a
# ... 개발 ...
git flow feature finish feature-a

git flow feature start feature-b
# ... 개발 ...
git flow feature finish feature-b

# develop 상태:
# ●─● feat: A
#   └─● feat: B

# ===== Week 2 금요일: Feature Freeze =====
# 더 이상 새 feature merge 안 함!
# 다음 릴리스 준비 시작

git flow release start 1.2.0

# VERSION 파일 업데이트
echo "1.2.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.2.0"

# CHANGELOG 업데이트
cat >> CHANGELOG.md << 'EOF'
## [1.2.0] - 2024-12-23

### Added
- Feature A: User authentication
- Feature B: Payment integration

### Changed
- Improved performance

### Fixed
- (버그 수정은 QA 후 추가)
EOF

git add CHANGELOG.md
git commit -m "docs: Update changelog for 1.2.0"

# ===== Week 3: QA 기간 =====
# release/1.2.0에서 QA 진행

# 버그 발견 시 수정
git commit -m "fix: Resolve login timeout"
git commit -m "fix: Fix payment calculation error"

# 긴급 feature 요청 오면?
# → 거절! 다음 릴리스로!
# → 또는 develop에서 시작 (다음 릴리스용)

# ===== Week 3 금요일: 배포 =====
# QA 완료!
git flow release finish -m "Release 1.2.0" 1.2.0

# 자동으로:
# 1. master에 merge
# 2. 태그 생성 (v1.2.0)
# 3. develop에 merge (버그 수정 반영)
# 4. release/1.2.0 삭제

# 배포
git switch main
git push origin main --tags

# ===== Week 4-5: 다음 사이클 시작 =====
git switch develop

# 이제 다음 릴리스 (1.3.0) 개발 시작!
git flow feature start feature-c
# ...

# ✅ 체계적인 2주 사이클!
```

**타임라인:**
```
Week 1-2: 개발
  - Feature 개발
  - develop에 merge
  - 통합 테스트

Week 2 금요일: Feature Freeze
  - Release 브랜치 생성
  - 버전/Changelog 업데이트

Week 3: QA
  - release 브랜치에서 QA
  - 버그 수정
  - 회귀 테스트

Week 3 금요일: 배포
  - master에 merge
  - 태그 생성
  - 프로덕션 배포

반복!
```

---

### 💼 Scenario 3: 긴급 패치 프로세스

**목표:** 프로덕션 버그 빠르게 처리

#### **Problem: 프로덕션에 심각한 버그 발견!**

```bash
# 상황
# - 현재 시간: 월요일 오전 10시
# - 프로덕션 버전: v1.2.0
# - 버그: 결제 시 이중 청구
# - 우선순위: P0 (최고)
# - develop에는 다음 릴리스 개발 중
```

#### **Solution: Hotfix 워크플로우**

```bash
# ===== 10:00 AM - 버그 발견 =====
# 긴급 회의: 즉시 수정 결정

# ===== 10:10 AM - Hotfix 시작 =====
git switch main
git pull  # 최신 main 확인

git flow hotfix start 1.2.1

# ===== 10:15 AM - 버그 재현 =====
# 로컬에서 버그 재현 성공

# ===== 10:30 AM - 수정 시작 =====
# payment.js 수정
git add payment.js
git commit -m "fix(payment): Resolve double charge issue

Fix race condition causing duplicate charges when
users click submit button multiple times rapidly.

Root cause:
- Missing debounce on submit button
- No idempotency check on server

Solution:
- Add client-side debounce (500ms)
- Add server-side idempotency key validation
- Add integration test

Impact: Critical - affects all payment transactions
Priority: P0

Fixes #789
Tested-by: QA Team
Reviewed-by: Tech Lead"

# ===== 11:00 AM - 테스트 =====
# 로컬 테스트 완료
# 스테이징 환경 테스트

# ===== 11:30 AM - 버전 업데이트 =====
echo "1.2.1" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.2.1"

# CHANGELOG 업데이트
cat >> CHANGELOG.md << 'EOF'
## [1.2.1] - 2024-12-23

### Fixed
- **Critical**: Fix double charge in payment processing
  - Add client-side debounce
  - Add server-side idempotency check
  - Fixes #789

### Security
- No security issues
EOF

git add CHANGELOG.md
git commit -m "docs: Update changelog for hotfix 1.2.1"

# ===== 12:00 PM - Hotfix 완료 =====
git flow hotfix finish -m "Hotfix 1.2.1: Payment bug" 1.2.1

# 자동으로:
# 1. master에 merge + 태그 (v1.2.1)
# 2. develop에도 merge
# 3. hotfix/1.2.1 삭제

# ===== 12:10 PM - 배포 =====
git switch main
git push origin main --tags

# CI/CD가 자동 배포
# 또는 수동 배포

# ===== 12:30 PM - 검증 =====
# 프로덕션 모니터링
# 버그 재발 확인

# ===== 1:00 PM - 완료 보고 =====
# 팀에 공지:
# "v1.2.1 hotfix 배포 완료
#  결제 이중 청구 버그 수정됨
#  모니터링 결과 정상"

# ✅ 3시간 만에 긴급 패치 완료!
```

**Hotfix 체크리스트:**
```
□ 버그 재현 확인
□ 코드 수정
□ 로컬 테스트
□ 스테이징 테스트
□ 버전 번호 업데이트
□ CHANGELOG 업데이트
□ 코드 리뷰 (가능하면)
□ master에 merge + 태그
□ develop에도 merge
□ 배포
□ 프로덕션 검증
□ 모니터링
□ 팀 공지
```

---

### 💼 Scenario 4: 여러 버전 동시 지원

**목표:** v1.x와 v2.x 동시 유지보수

#### **Problem: 두 버전을 동시에 지원해야 한다!**

```bash
# 요구사항
# - v1.x: 레거시 고객용 (유지보수만)
# - v2.x: 신규 고객용 (활발한 개발)
# - 각각 독립적 배포
# - 보안 패치는 양쪽 모두 적용
```

#### **Solution: Support 브랜치 활용**

```bash
# ===== 초기 설정 =====
# v1 개발 완료 및 배포
main (v1.3.0)

# v2 개발 시작
git switch develop
# ... v2 기능 개발 ...

# v2.0.0 릴리스
git flow release start 2.0.0
git flow release finish 2.0.0

# 현재 상태:
# main: v2.0.0 (최신)

# ===== v1 지원 브랜치 생성 =====
# v1 마지막 버전에서 분기
git switch main
git checkout tags/v1.3.0
git switch -c support/1.x

# 이제 구조:
# main: v2.0.0 (v2 개발)
# support/1.x: v1.3.0 (v1 유지보수)

# ===== v1 버그 수정 =====
# v1에 버그 발견!
git switch support/1.x
git switch -c hotfix/1.3.1

# 수정
git commit -m "fix: Security patch for v1"

# v1 배포
git switch support/1.x
git merge --no-ff hotfix/1.3.1
git tag -a v1.3.1 -m "v1.3.1 Security patch"
git push origin support/1.x --tags

# v2에도 적용 필요하면
git switch develop
git cherry-pick <commit-hash>

# ===== v2 개발 계속 =====
git switch develop
# ... v2 feature 개발 ...

git flow release start 2.1.0
git flow release finish 2.1.0

# ===== 최종 상태 =====
# main: v2.1.0 (최신)
# support/1.x: v1.3.1 (유지보수)

# CI/CD 설정:
# - main → 프로덕션 (v2 고객)
# - support/1.x → 프로덕션-v1 (v1 고객)
```

**버전별 전략:**
```
v2 (main):
  - 활발한 개발
  - 새 기능 추가
  - Git Flow 완전 적용
  - develop → release → main

v1 (support/1.x):
  - 유지보수만
  - 보안 패치
  - 중요 버그 수정만
  - Hotfix 중심
  - 새 기능 추가 안 함

공통 이슈:
  - 보안 패치는 양쪽 모두
  - Cherry-pick으로 적용
  - 또는 별도 수정
```

---

### 💼 Scenario 5: Git Flow 문제 해결

**목표:** 흔한 Git Flow 문제 처리

#### **Problem 1: Release 중 Feature 추가 요청**

```bash
# 상황
# - release/1.2.0 QA 중
# - 급하게 새 기능 요청
# - "이번 릴리스에 꼭 넣어주세요!"

# ❌ 나쁜 방법
git switch release/1.2.0
# 새 기능 개발...  # 금지!

# ✅ 올바른 방법
# 1. 거절
"이번 릴리스 범위는 확정되었습니다.
다음 릴리스 (1.3.0)에 포함됩니다."

# 2. 정말 중요하면
# Release 취소하고 재시작
git switch develop
git branch -D release/1.2.0

# Feature 추가
git flow feature start urgent-feature
# ... 개발 ...
git flow feature finish urgent-feature

# Release 재시작
git flow release start 1.2.0
```

---

#### **Problem 2: Hotfix와 Release 충돌**

```bash
# 상황
# - release/1.2.0 QA 중
# - 프로덕션에 긴급 버그!
# - Hotfix 필요

# ❌ 나쁜 방법
git flow hotfix start 1.1.1
# ... 수정 ...
git flow hotfix finish 1.1.1
# develop에 merge되는데 release는?

# ✅ 올바른 방법
git flow hotfix start 1.1.1
# ... 수정 ...

# Master에만 merge
git switch main
git merge --no-ff hotfix/1.1.1
git tag v1.1.1

# Release에 적용!
git switch release/1.2.0
git merge --no-ff hotfix/1.1.1

# Release 완료 시 develop으로 전달됨
git flow release finish 1.2.0
```

---

#### **Problem 3: Feature가 너무 오래 걸림**

```bash
# 상황
# - feature/big-feature 개발 중 (2주)
# - develop이 계속 업데이트됨
# - Merge 시 충돌 예상

# ❌ 나쁜 방법
# 2주 후 한번에 merge
git switch develop
git merge feature/big-feature  # 충돌 폭탄!

# ✅ 올바른 방법
# 주기적으로 develop 동기화
git switch feature/big-feature
git merge develop  # 매일 또는 주 1회

# 또는 작게 나누기
git switch develop
git switch -c feature/big-feature-part1
# ... Part 1 완성 ...
git flow feature finish big-feature-part1

git switch -c feature/big-feature-part2
# ... Part 2 개발 ...
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: Master에 직접 커밋**

```bash
# 위험한 패턴
git switch main
# ... 작업 ...
git commit -m "Quick fix"  # ❌ 금지!
```

**올바른 방법:**
```bash
# Hotfix 브랜치 사용!
git flow hotfix start fix
# ... 수정 ...
git flow hotfix finish fix
```

---

#### ❌ **실수 2: Release 브랜치에 기능 추가**

```bash
# 위험한 패턴
git switch release/1.2.0
git commit -m "feat: Add new feature"  # ❌ 금지!
```

**올바른 방법:**
```bash
# Release에서는 버그 수정만!
git commit -m "fix: Bug in feature"  # ✅

# 새 기능은 다음 릴리스로
git switch develop
git flow feature start new-feature
```

---

#### ❌ **실수 3: 태그 없이 배포**

```bash
# 위험한 패턴
git switch main
git merge release/1.2.0
git push  # ❌ 태그 없음!
```

**올바른 방법:**
```bash
# 항상 태그!
git flow release finish 1.2.0
# 자동으로 태그 생성됨

# 수동이면
git tag -a v1.2.0 -m "Release 1.2.0"
git push origin main --tags
```

---

#### ❌ **실수 4: Develop에서 직접 배포**

```bash
# 위험한 패턴
git switch develop
# "개발 완료! 바로 배포하자"
# develop → 프로덕션  # ❌ 위험!
```

**올바른 방법:**
```bash
# 반드시 Release 프로세스!
git flow release start 1.2.0
# QA 테스트
git flow release finish 1.2.0
# 이제 main에서 배포
```

---

### 🎯 실습 미션

#### **미션 1: Git Flow 전체 사이클** 🎯

**목표:** Feature → Release → Hotfix 전체 경험

```bash
# 준비
mkdir gitflow-practice && cd gitflow-practice
git init
git commit --allow-empty -m "Initial commit"
git flow init -d

# 미션:
# 1. Feature 개발 (login, payment)
# 2. Release 1.0.0 (버그 수정 포함)
# 3. Hotfix 1.0.1
# 4. 각 단계마다 히스토리 확인

# 힌트:
# git log --oneline --graph --all
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Feature 개발
git flow feature start login
echo "Login" > login.txt
git add login.txt
git commit -m "feat: Add login"
git flow feature finish login

git flow feature start payment
echo "Payment" > payment.txt
git add payment.txt
git commit -m "feat: Add payment"
git flow feature finish payment

# 2. Release
git flow release start 1.0.0

# 버전 업데이트
echo "1.0.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 1.0.0"

# 버그 발견 및 수정
echo "Bug fixed" >> login.txt
git add login.txt
git commit -m "fix: Resolve login timeout"

git flow release finish -m "Release 1.0.0" 1.0.0

# 3. Hotfix
git flow hotfix start 1.0.1

echo "Critical fix" >> payment.txt
git add payment.txt
git commit -m "fix: Critical payment bug"

echo "1.0.1" > VERSION
git add VERSION
git commit -m "chore: Bump to 1.0.1"

git flow hotfix finish -m "Hotfix 1.0.1" 1.0.1

# 4. 히스토리 확인
git log --oneline --graph --all --decorate

# 출력 예시:
# *   Merge branch 'hotfix/1.0.1' into develop
# |\  
# | *   Merge branch 'hotfix/1.0.1'
# | |\  
# | | * chore: Bump to 1.0.1
# | | * fix: Critical payment bug
# | |/  
# |/|   
# * |   Merge tag '1.0.0' into develop
# |\ \  
# | |/  
# |/|   
# | *   Merge branch 'release/1.0.0'
# | |\  
# | | * chore: Bump version to 1.0.0
# | | * fix: Resolve login timeout
# | |/  
# |/|   
# * | feat: Add payment
# * | feat: Add login

# ✅ 완벽한 Git Flow!
```

</details>

---

#### **미션 2: 릴리스 주기 시뮬레이션** 📅

**목표:** 2주 릴리스 사이클 체험

```bash
# 시나리오:
# Week 1-2: Feature 개발 (3개)
# Week 2 금: Release 시작
# Week 3: QA (버그 2개 수정)
# Week 3 금: 배포

# 준비
mkdir release-cycle && cd release-cycle
git init
git commit --allow-empty -m "Initial"
git flow init -d
```

<details>
<summary>정답 보기</summary>

```bash
# ===== Week 1-2: 개발 =====
# Feature 1
git flow feature start dashboard
echo "Dashboard" > dashboard.txt
git add dashboard.txt
git commit -m "feat: Add dashboard"
git flow feature finish dashboard

# Feature 2
git flow feature start reports
echo "Reports" > reports.txt
git add reports.txt
git commit -m "feat: Add reports"
git flow feature finish reports

# Feature 3
git flow feature start export
echo "Export" > export.txt
git add export.txt
git commit -m "feat: Add export"
git flow feature finish export

# ===== Week 2 금: Feature Freeze =====
git flow release start 1.0.0

echo "1.0.0" > VERSION
git add VERSION
git commit -m "chore: Bump to 1.0.0"

# ===== Week 3: QA =====
# 버그 1
echo "Fix 1" >> dashboard.txt
git add dashboard.txt
git commit -m "fix: Dashboard loading issue"

# 버그 2
echo "Fix 2" >> reports.txt
git add reports.txt
git commit -m "fix: Reports calculation error"

# ===== Week 3 금: 배포 =====
git flow release finish -m "Release 1.0.0" 1.0.0

# 확인
git tag
# 1.0.0

git log --oneline --graph

# ✅ 2주 릴리스 사이클 완료!
```

</details>

---

#### **미션 3: 긴급 패치 시뮬레이션** 🚨

**목표:** 실전 Hotfix 프로세스

```bash
# 시나리오:
# 1. v1.0.0 배포됨
# 2. Develop에 새 기능 개발 중
# 3. 프로덕션 버그 발견!
# 4. Hotfix 1.0.1로 즉시 수정

# 준비
mkdir hotfix-practice && cd hotfix-practice
git init
git flow init -d
echo "App" > app.txt
git add app.txt
git commit -m "feat: Initial app"

# v1.0.0 배포
git switch main
git merge develop
git tag v1.0.0
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Develop에 새 기능 (아직 배포 안 됨)
git switch develop
git flow feature start next-feature
echo "Next Feature" > next.txt
git add next.txt
git commit -m "feat: Next feature WIP"
# finish 안 함 (아직 개발 중)

# 2. 프로덕션 버그 발견!
git switch main

# 3. Hotfix 시작
git flow hotfix start 1.0.1

# 4. 버그 수정
echo "Bug Fixed!" >> app.txt
git add app.txt
git commit -m "fix(critical): Resolve production bug

Fix null pointer exception in payment.
Priority: P0

Fixes #999"

echo "1.0.1" > VERSION
git add VERSION
git commit -m "chore: Bump to 1.0.1"

# 5. Hotfix 완료
git flow hotfix finish -m "Hotfix 1.0.1" 1.0.1

# 6. 확인
git tag
# v1.0.0
# 1.0.1

# Main에 적용됨
git switch main
cat app.txt
# App
# Bug Fixed!

# Develop에도 적용됨
git switch develop
cat app.txt
# App
# Bug Fixed!

# 그리고 next feature는 그대로
git switch feature/next-feature
ls
# app.txt next.txt

# ✅ 완벽한 Hotfix!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 초기화 ===
git flow init -d              # 기본값으로 초기화

# === Feature ===
git flow feature start <n>    # 시작
git flow feature finish <n>   # 완료
git flow feature publish <n>  # 원격에 공유

# === Release ===
git flow release start <v>    # 시작
git flow release finish <v>   # 완료 (merge + tag)

# === Hotfix ===
git flow hotfix start <v>     # 시작
git flow hotfix finish <v>    # 완료 (merge + tag)

# === 상태 확인 ===
git flow feature list         # Feature 목록
git flow release list         # Release 목록
git flow hotfix list          # Hotfix 목록
```

---

### 🎨 버전 번호 가이드

```
Semantic Versioning: MAJOR.MINOR.PATCH

1.0.0 → 첫 정식 릴리스
1.1.0 → 새 기능 추가
1.1.1 → 버그 수정
2.0.0 → 호환성 깨지는 변경

예시:
feature → 1.2.0 (MINOR 증가)
hotfix  → 1.2.1 (PATCH 증가)
Breaking → 2.0.0 (MAJOR 증가)
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [2.1 브랜치 기본](./01-branch-basics.md)

**다음 학습:**
- [2.3 GitHub Flow](./03-github-flow.md)
- [2.4 Trunk-Based Dev](./04-trunk-based.md)

**관련 주제:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)
- [5.1 Release 관리](./05-release-management.md)

---

### 💡 추가 팁

#### **Git Flow 자동화**

```bash
# Husky + Git Flow
# package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "npm test",
      "pre-push": "git flow feature list | grep -v 'No feature'"
    }
  }
}

# CI/CD 설정
# .github/workflows/gitflow.yml
name: Git Flow CI
on:
  push:
    branches:
      - develop
      - 'release/**'
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v2
      - run: npm test
```

---

<div align="center">

**🎉 축하합니다! Git Flow를 완전히 마스터했습니다!**

이제 체계적인 릴리스 관리를 할 수 있습니다!

[⬅️ 이전: 브랜치 기본](./01-branch-basics.md) | [README로](../README.md) | [다음: GitHub Flow ➡️](./03-github-flow.md)

</div>
