# 2.5 Release 관리 (Release Management)

**난이도: ⭐⭐⭐**

> *"Release 관리는 코드를 사용자에게 전달하는 마지막 관문입니다. 체계적인 관리가 신뢰를 만듭니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Release 관리를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 성장하는 SaaS 기업

요구사항:
- 엔터프라이즈 고객: v2.x (안정 버전)
- 일반 고객: v3.x (최신 기능)
- 레거시 고객: v1.x (LTS, 보안 패치만)
- 각 버전별 독립 배포
- 긴급 패치는 모든 버전에

문제:
개발자: "어느 브랜치에 패치해야 하죠?"
PM: "v2.5 고객만 업데이트 가능한가요?"
지원팀: "v1.x는 언제까지 지원하나요?"
CTO: "버전 관리 전략이 필요해요!"
```

**체계적 Release 관리를 하면:**
```bash
main
  └─ v3.x (최신)

release/2.x (LTS)
  └─ v2.5, v2.6... (엔터프라이즈)

release/1.x (유지보수)
  └─ v1.10, v1.11... (보안만)

✅ 명확한 버전 전략
✅ 독립적 릴리스
✅ Backport 프로세스
✅ LTS 지원 계획
✅ 고객별 맞춤 서비스
```

> 💡 **핵심:** Release 관리는 여러 버전을 동시에 
> 안정적으로 운영하는 전략과 프로세스입니다!

---

### 📌 Release 관리의 핵심 개념

```
┌─────────────────────────────────────────────────┐
│           Release 관리 구조                       │
├─────────────────────────────────────────────────┤
│                                                 │
│  main (trunk)                                   │
│    ↓                                            │
│  최신 개발 (v4.0-dev)                             │
│                                                 │
│  release/3.x (Current)                          │
│    ↓                                            │
│  현재 프로덕션 (v3.2, v3.3...)                     │
│                                                 │
│  release/2.x (LTS)                              │
│    ↓                                            │
│  장기 지원 (v2.8, v2.9...)                        │
│                                                 │
│  release/1.x (Maintenance)                      │
│    ↓                                            │
│  유지보수만 (v1.15, v1.16...)                      │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

### 🔢 버전 관리 전략

#### **1️⃣ Semantic Versioning (SemVer)**

```
형식: MAJOR.MINOR.PATCH

예시: 2.5.3
      ↑ ↑ ↑
      │ │ └─ PATCH: 버그 수정 (하위 호환)
      │ └─── MINOR: 기능 추가 (하위 호환)
      └───── MAJOR: Breaking Changes

변경 규칙:
1. 버그 수정 → PATCH 증가
   2.5.3 → 2.5.4

2. 새 기능 (호환) → MINOR 증가
   2.5.3 → 2.6.0

3. Breaking Change → MAJOR 증가
   2.5.3 → 3.0.0

4. Pre-release → 접미사
   3.0.0-alpha.1
   3.0.0-beta.2
   3.0.0-rc.1
```

---

#### **2️⃣ Calendar Versioning (CalVer)**

```
형식: YYYY.MM.PATCH 또는 YY.MM.PATCH

예시:
Ubuntu: 24.04, 24.10 (년.월)
Pylons: 20.7.1 (년.월.수정)

장점:
✅ 출시 시기 명확
✅ 오래된 버전 식별 쉬움
✅ 정기 릴리스에 적합

사용:
- Ubuntu (OS)
- Pylons (Python)
- Wine (호환성 레이어)
```

---

#### **3️⃣ Incremental Versioning**

```
형식: 단순 증가

예시:
Chrome: 120, 121, 122...
Firefox: 115, 116, 117...

특징:
✅ 매우 단순
✅ Breaking Change 구분 없음
✅ 빠른 릴리스 주기

사용:
- 웹 브라우저
- 모바일 앱 (자동 업데이트)
```

---

### 🌳 브랜치 기반 Release 전략

#### **1️⃣ Release 브랜치 전략**

```
main
  ↓
  ●───────────────●────────────●───
  │               │            │
  │         v3.0.0 출시   v4.0.0 출시
  │
  └──→ release/3.x
         ↓
         ●──●──●──●
         │  │  │  │
      3.0 3.1 3.2 3.3
```

**특징:**
```
✅ 버전별 독립 브랜치
✅ 각 브랜치에서 패치
✅ 명확한 버전 분리
✅ Backport 용이

사용:
- Git Flow
- GitLab Flow
```

---

#### **2️⃣ Tag 기반 Release 전략**

```
main
  ↓
  ●──●──●──●──●──●──●
  │  │  │  │  │  │  │
  v1 v2 v3 v4 v5 v6 v7
```

**특징:**
```
✅ 브랜치 최소화
✅ 태그로만 관리
✅ 단순한 구조
✅ GitHub Flow, Trunk-Based

주의:
⚠️ 여러 버전 지원 어려움
⚠️ Backport 복잡
```

---

### 🔄 Backport 전략

#### **Backport란?**

```
정의:
새로운 버전의 수정사항을
오래된 버전에 적용하는 것

예시:
main (v4.0) → 버그 수정
  ↓
release/3.x (v3.5)로 이식
  ↓
release/2.x (v2.10)로 이식
```

---

#### **Backport 프로세스**

```bash
# 1. main에서 버그 수정
git switch main
git commit -m "fix: Critical security bug"
# Commit: abc123

# 2. release/3.x로 cherry-pick
git switch release/3.x
git cherry-pick abc123
git tag v3.5.1
git push origin release/3.x --tags

# 3. release/2.x로 cherry-pick
git switch release/2.x
git cherry-pick abc123
# 충돌 해결...
git tag v2.10.5
git push origin release/2.x --tags

# 4. 각 버전 배포
# v3.5.1 → 프로덕션 (v3 고객)
# v2.10.5 → 프로덕션 (v2 고객)
```

---

#### **Backport 전략**

| 전략 | 설명 | 장점 | 단점 |
|:-----|:-----|:-----|:-----|
| **Selective** | 중요한 것만 | 효율적 | 일관성 떨어짐 |
| **Automatic** | 모든 수정 | 일관성 | 작업량 많음 |
| **On-demand** | 요청 시만 | 유연함 | 누락 가능 |
| **Time-based** | 정기적 | 예측 가능 | 비효율적 |

---

### 📅 LTS (Long Term Support) 관리

#### **LTS란?**

```
정의:
장기간 유지보수를 보장하는 버전

특징:
✅ 안정성 최우선
✅ 장기 보안 패치
✅ 최소 기능 추가
✅ 예측 가능한 수명

예시:
Node.js:
- v20 LTS (2023-2026)
- v18 LTS (2022-2025)

Ubuntu:
- 24.04 LTS (2029까지)
- 22.04 LTS (2027까지)
```

---

#### **LTS 전략**

```
버전 수명 주기:

v3.0 (Active)
  ↓
6개월: 새 기능 활발
  ↓
v3.1, v3.2, v3.3 출시
  ↓
v4.0 출시
  ↓
v3.x → LTS 전환
  ↓
2년: 보안 패치만
  ↓
v3.x → EOL (End of Life)

타임라인:
├─────────┬─────────────┬─────────┤
2024      2024.6        2026.6    EOL
Active    Maintenance   Security
(6mo)     (1yr)         (1yr)
```

---

### 🎯 Release 타입

#### **1️⃣ Major Release**

```
정의: 호환성 깨지는 큰 변경

예시: v2.x → v3.0.0

포함:
✅ Breaking Changes
✅ 새로운 아키텍처
✅ 마이그레이션 가이드
✅ 긴 베타 기간

주기: 1-2년
```

---

#### **2️⃣ Minor Release**

```
정의: 하위 호환 기능 추가

예시: v3.2.0 → v3.3.0

포함:
✅ 새 기능
✅ 성능 개선
✅ Deprecation 경고
✅ 짧은 베타

주기: 1-3개월
```

---

#### **3️⃣ Patch Release**

```
정의: 버그 수정

예시: v3.3.1 → v3.3.2

포함:
✅ 버그 수정
✅ 보안 패치
✅ 문서 수정
❌ 새 기능 없음

주기: 수시 (필요 시)
```

---

#### **4️⃣ Pre-release**

```
순서:
alpha → beta → rc → stable

alpha:
- 내부 테스트
- 불안정
- API 변경 가능

beta:
- 공개 테스트
- 기능 완성
- 버그 수정 중

rc (Release Candidate):
- 출시 직전
- 최종 검증
- 버그만 수정

stable:
- 정식 출시
- 프로덕션 사용
```

---

### 📊 Release 계획

#### **Release Train 모델**

```
정기적 릴리스 (예: 6주마다)

Week 1-4: 개발
  └─ 새 기능 추가

Week 5: Feature Freeze
  └─ 기능 동결, 테스트

Week 6: Release
  └─ 출시, 다음 Train 시작

타임라인:
├──────┬──────┬──────┬──────┬──────┬──────┤
Week 1  Week 2  Week 3  Week 4  Week 5  Week 6
[───────── Dev ─────────][─Test─][Release]
                          └─ Next Train 시작

장점:
✅ 예측 가능
✅ 계획 수립 쉬움
✅ 정기적 배포
```

---

#### **Feature-based Release**

```
기능 완성 시 릴리스

Feature A 완성 → v3.1 출시
Feature B 완성 → v3.2 출시
Feature C 완성 → v3.3 출시

장점:
✅ 유연함
✅ 빠른 출시
✅ 기능 중심

단점:
❌ 예측 어려움
❌ 일정 불확실
```

---

### 🔒 보안 패치 관리

#### **보안 패치 프로세스**

```
1. 보안 취약점 발견
   ↓
2. 비공개 수정
   ↓
3. 영향받는 모든 버전 패치
   ↓
4. 동시 출시
   ↓
5. CVE 공개
   ↓
6. 사용자 알림

예시:
CVE-2024-12345 발견
  ↓
v4.2.1 (main)
v3.8.5 (LTS)
v2.15.3 (maintenance)
  ↓
동시 출시
  ↓
보안 공지
```

---

#### **보안 버전 번호**

```
긴급도에 따라:

Critical:
즉시 PATCH 증가
2.5.3 → 2.5.4

High:
다음 정기 릴리스에 포함
2.5.3 → 2.6.0

Medium/Low:
계획된 릴리스에 포함
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 Release 브랜치 관리

#### **1️⃣ Release 브랜치 생성**

```bash
# v3.0 출시 준비
git switch main
git switch -c release/3.x

# 버전 번호 설정
echo "3.0.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 3.0.0"

# 태그 생성
git tag -a v3.0.0 -m "Release version 3.0.0

## What's New
- Feature A
- Feature B
- Feature C

## Breaking Changes
- API endpoint changed
- Configuration format updated

## Migration Guide
See docs/migration-v3.md"

# Push
git push origin release/3.x
git push origin v3.0.0
```

---

#### **2️⃣ 패치 릴리스**

```bash
# release/3.x에서 버그 수정
git switch release/3.x

# 수정
git commit -m "fix: Critical bug in payment"

# 버전 증가 (PATCH)
echo "3.0.1" > VERSION
git add VERSION
git commit -m "chore: Bump version to 3.0.1"

# 태그
git tag -a v3.0.1 -m "Release version 3.0.1

## Bug Fixes
- Fix payment processing bug
- Fix memory leak

Fixes #123"

# Push
git push origin release/3.x
git push origin v3.0.1
```

---

#### **3️⃣ Backport (Cherry-pick)**

```bash
# main의 수정을 release/3.x로
git switch release/3.x

# Cherry-pick (단일 커밋)
git cherry-pick abc123

# Cherry-pick (여러 커밋)
git cherry-pick abc123 def456 ghi789

# 충돌 발생 시
# ... 충돌 해결 ...
git add .
git cherry-pick --continue

# 중단하려면
git cherry-pick --abort

# 커밋 메시지 수정
git cherry-pick abc123 -e
```

---

#### **4️⃣ LTS 브랜치 관리**

```bash
# v2.x를 LTS로 전환
git switch release/2.x

# README 업데이트
cat >> README.md << 'EOF'

## Version 2.x LTS

This is a Long Term Support version.

- Support Period: 2024-2026
- Security Patches: Until 2027
- New Features: No
- Bug Fixes: Critical only

For latest features, upgrade to v3.x
EOF

git add README.md
git commit -m "docs: Mark v2.x as LTS"

# LTS 태그
git tag -a lts/2.x -m "LTS: Version 2.x

Support until 2027
Security patches only"

git push origin release/2.x
git push origin lts/2.x
```

---

### 📋 버전 관리 명령어

#### **1️⃣ 버전 번호 자동화**

```bash
# npm version (Node.js)
npm version patch   # 3.0.0 → 3.0.1
npm version minor   # 3.0.1 → 3.1.0
npm version major   # 3.1.0 → 4.0.0

# 자동으로:
# - package.json 업데이트
# - Git 커밋 생성
# - Git 태그 생성

# 예시
npm version patch -m "Release v%s"
# → v3.0.1 태그 생성
```

---

#### **2️⃣ Changelog 생성**

```bash
# Conventional Commits로 자동 생성
npm install -g conventional-changelog-cli

# CHANGELOG.md 생성
conventional-changelog -p angular -i CHANGELOG.md -s

# 결과:
# ## [3.0.1] - 2024-12-23
# 
# ### Bug Fixes
# - Fix payment bug ([abc123])
# 
# ### Features
# - Add dark mode ([def456])
```

---

#### **3️⃣ Release Note 자동화**

```bash
# GitHub Release Notes 생성
gh release create v3.0.1 \
  --title "Release v3.0.1" \
  --notes "$(cat CHANGELOG.md)" \
  --target release/3.x

# 바이너리 첨부
gh release create v3.0.1 \
  --title "Release v3.0.1" \
  --notes-file RELEASE_NOTES.md \
  dist/*.zip
```

---

### 📊 명령어 비교표

#### **Release 명령어**

| 작업 | Git Flow | GitHub Flow | Trunk-Based |
|:-----|:---------|:-----------|:-----------|
| **Release 시작** | `git flow release start` | Branch + Tag | Tag only |
| **Release 완료** | `git flow release finish` | Merge + Tag | Tag |
| **Hotfix** | `git flow hotfix start` | PR + Tag | Cherry-pick |
| **Backport** | Manual cherry-pick | Manual | Manual |

---

#### **버전 전략 비교**

| 전략 | 적합한 경우 | 복잡도 | 예시 |
|:-----|:-----------|:------|:-----|
| **SemVer** | 라이브러리, API | 중간 | npm, pip |
| **CalVer** | OS, 정기 릴리스 | 낮음 | Ubuntu |
| **Incremental** | 브라우저, 앱 | 낮음 | Chrome |
| **Hybrid** | 대규모 프로젝트| 높음 | Kubernetes |

---

### ⚡ 실행 결과 시각화

#### **Scenario: v3.x LTS 지원**

```
2024년 초:
main ─●─●─●─●─
      │
      └──→ release/3.x
             v3.0

2024년 중반:
main ─●─●─●─●─●─●─●─
      │           │
      │           └──→ release/4.x
      │                  v4.0
      └──→ release/3.x (LTS)
             v3.0, v3.1, v3.2

2025년:
main ─●─●─●─●─
      │
      └──→ release/5.x
             v5.0

      release/4.x
        v4.0, v4.1, v4.2

      release/3.x (LTS, 보안만)
        v3.3, v3.4 (보안 패치)

2026년:
      release/3.x → EOL
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 멀티 버전 Release 전략

**목표:** 3개 버전 동시 지원

#### **Problem: 여러 고객 버전을 어떻게 관리하나?**

```bash
# 요구사항
# - v4.x: 최신 (일반 고객)
# - v3.x: LTS (엔터프라이즈)
# - v2.x: 유지보수만 (레거시)
# - 보안 패치는 모든 버전
```

#### **Solution: 브랜치 기반 멀티 버전**

```bash
# ===== 초기 설정 (v2.x 시대) =====

# v2.0 출시 완료
git switch main
git tag v2.0.0
git switch -c release/2.x
git push origin release/2.x

# ===== v3.0 개발 시작 =====

# main에서 v3 개발
git switch main

# Breaking changes
git commit -m "feat!: New API design (BREAKING)"
git commit -m "feat!: Change config format (BREAKING)"

# v3.0 출시
git tag v3.0.0
git switch -c release/3.x
git push origin release/3.x v3.0.0

# ===== v3.x LTS 전환 =====

# 1년 후 v4.0 출시
git switch main
git tag v4.0.0
git switch -c release/4.x

# v3.x를 LTS로 선언
git switch release/3.x

cat > LTS.md << 'EOF'
# Version 3.x LTS

## Support Timeline
- Active Support: 2024-01 to 2024-12
- LTS Period: 2025-01 to 2027-12
- EOL: 2027-12-31

## What's Supported
✅ Security patches
✅ Critical bug fixes
❌ New features
❌ Non-critical bugs

## Upgrade Path
v3.x → v4.x (See migration guide)
EOF

git add LTS.md
git commit -m "docs: Declare v3.x as LTS"
git tag lts/3.x
git push origin release/3.x lts/3.x

# ===== 보안 패치 발생 =====

# CVE-2024-12345: SQL Injection

# 1. main에서 수정
git switch main
git commit -m "fix(security): Prevent SQL injection

CVE-2024-12345
Priority: Critical

Sanitize user input in query builder."
# Commit: abc123

git tag v4.1.1
git push origin main v4.1.1

# 2. v3.x (LTS)로 backport
git switch release/3.x
git cherry-pick abc123
# 코드 차이로 충돌 발생
# ... 수동 해결 ...
git add .
git cherry-pick --continue

git tag v3.8.1
git push origin release/3.x v3.8.1

# 3. v2.x (유지보수)로 backport
git switch release/2.x
git cherry-pick abc123
# ... 수동 해결 ...
git add .
git cherry-pick --continue

git tag v2.15.1
git push origin release/2.x v2.15.1

# 4. 보안 공지
cat > SECURITY_ADVISORY.md << 'EOF'
# Security Advisory: CVE-2024-12345

## Severity
Critical (CVSS 9.8)

## Affected Versions
- v4.1.0 and earlier
- v3.8.0 and earlier
- v2.15.0 and earlier

## Fixed Versions
- v4.1.1
- v3.8.1 (LTS)
- v2.15.1 (Maintenance)

## Action Required
Upgrade immediately to fixed versions.

## Details
SQL injection vulnerability in query builder.
Attacker could execute arbitrary SQL.

## Credit
Reported by security researcher John Doe
EOF

# 5. 동시 배포
# - v4.1.1 → 일반 고객
# - v3.8.1 → 엔터프라이즈 (LTS)
# - v2.15.1 → 레거시 고객

# ===== v2.x EOL 선언 =====

# 2년 후
git switch release/2.x

cat > EOL.md << 'EOF'
# Version 2.x End of Life

## Final Release
v2.18.0 (2026-12-31)

## Support Ended
2026-12-31

## Upgrade Required
All users must upgrade to v3.x or v4.x

## Archive
This branch is archived.
No further updates will be provided.
EOF

git add EOL.md
git commit -m "docs: Declare v2.x EOL"
git tag eol/2.x
git push origin release/2.x eol/2.x

# GitHub에서 브랜치 보호
# "This branch is archived"

# ✅ 완료!
```

**버전별 지원 매트릭스:**

| 버전 | 상태 | 지원 내용 | EOL | 고객 |
|:-----|:-----|:----------|:----|:-----|
| v4.x | Active | 전체 지원 | 2027+ | 일반 |
| v3.x | LTS | 보안+Critical | 2027 | 엔터프라이즈 |
| v2.x | EOL | 없음 | 2026 | 레거시 (마이그레이션 중) |

---

### 💼 Scenario 2: Release Train 구현

**목표:** 6주마다 정기 릴리스

#### **Problem: 릴리스가 불규칙하고 예측 불가!**

```bash
# 현재 문제
# - 릴리스 일정 불명확
# - 기능 완성 기다림
# - 출시 날짜 계속 연기
# - 고객 불만
```

#### **Solution: Release Train 모델**

```bash
# ===== Release Train 설정 =====

# 1. 6주 주기 정의
cat > RELEASE_SCHEDULE.md << 'EOF'
# Release Schedule

## 2024 Trains
- Train 1: Jan 1 - Feb 11 → v3.1 (Feb 12)
- Train 2: Feb 12 - Mar 24 → v3.2 (Mar 25)
- Train 3: Mar 25 - May 5 → v3.3 (May 6)
- Train 4: May 6 - Jun 16 → v3.4 (Jun 17)
- Train 5: Jun 17 - Jul 28 → v3.5 (Jul 29)
- Train 6: Jul 29 - Sep 8 → v3.6 (Sep 9)
- Train 7: Sep 9 - Oct 20 → v3.7 (Oct 21)
- Train 8: Oct 21 - Dec 1 → v3.8 (Dec 2)

## Train Process
Week 1-4: Development
Week 5: Feature Freeze + Testing
Week 6: Release + Next Train Starts
EOF

# ===== Train 1: v3.1 =====

# Week 1 (Jan 1): Train 시작
git switch main
git switch -c train/3.1

echo "Train 3.1 Started" > TRAIN.md
git add TRAIN.md
git commit -m "chore: Start Train 3.1"

# Week 1-4: 개발
git switch main

# Feature A (Week 1)
git commit -m "feat(ui): Add dark mode"

# Feature B (Week 2)
git commit -m "feat(api): Add GraphQL support"

# Feature C (Week 3)
git commit -m "feat(analytics): Add dashboard"

# Week 4: 개발 마무리
git commit -m "feat(payment): Add Stripe integration"

# Week 5 (Feb 5): Feature Freeze
git switch -c release/3.1

echo "3.1.0-rc.1" > VERSION
git commit -m "chore: Feature Freeze for 3.1"

# RC 태그
git tag v3.1.0-rc.1
git push origin release/3.1 v3.1.0-rc.1

# QA 테스트
# - 통합 테스트
# - 성능 테스트
# - 보안 스캔

# 버그 발견 → 수정
git commit -m "fix: Dark mode flickering"
git tag v3.1.0-rc.2

git commit -m "fix: GraphQL query timeout"
git tag v3.1.0-rc.3

# Week 6 (Feb 12): Release!
echo "3.1.0" > VERSION
git commit -m "chore: Release 3.1.0"
git tag v3.1.0

# CHANGELOG 생성
cat > CHANGELOG.md << 'EOF'
# v3.1.0 (2024-02-12)

## Features
- Add dark mode support
- Add GraphQL API
- Add analytics dashboard
- Add Stripe payment integration

## Bug Fixes
- Fix dark mode flickering
- Fix GraphQL query timeout

## Breaking Changes
None

## Migration Guide
No migration needed
EOF

git add CHANGELOG.md
git commit -m "docs: Add CHANGELOG for 3.1.0"

# Merge to main
git switch main
git merge release/3.1 --no-ff
git push origin main v3.1.0

# Release 브랜치 정리 (선택)
# git branch -d release/3.1

# ===== 동시에 Train 2 시작! =====

# Week 6 (Feb 12): Train 2 시작
git switch main
git switch -c train/3.2

echo "Train 3.2 Started" > TRAIN.md
git commit -m "chore: Start Train 3.2"

# ... Train 2 개발 (6주) ...

# ===== 긴급 패치 (Train과 별개) =====

# Train 1 출시 2주 후 버그 발견
git switch main
git commit -m "fix(critical): Payment validation bug"
# Commit: xyz789

# 즉시 패치 릴리스
git switch release/3.1
git cherry-pick xyz789

echo "3.1.1" > VERSION
git commit -m "chore: Patch release 3.1.1"
git tag v3.1.1
git push origin release/3.1 v3.1.1

# main에도 merge
git switch main
git merge release/3.1

# Train 2는 계속 진행 (영향 없음)

# ===== 결과 =====

# 2024년 릴리스:
# v3.1 - Feb 12
# v3.1.1 - Feb 26 (Patch)
# v3.2 - Mar 25
# v3.3 - May 6
# v3.4 - Jun 17
# v3.5 - Jul 29
# v3.6 - Sep 9
# v3.7 - Oct 21
# v3.8 - Dec 2

# ✅ 예측 가능한 릴리스!
# ✅ 정기적인 배포!
# ✅ 고객 만족도 향상!
```

**Release Train 장점:**
```
✅ 예측 가능
   - 고객이 계획 수립 가능
   - 마케팅 준비
   
✅ 품질 향상
   - 정해진 QA 기간
   - 충분한 테스트

✅ 개발 리듬
   - 명확한 데드라인
   - Feature Freeze 규칙

✅ 유연성
   - 기능 못 따라잡으면 다음 Train
   - 긴급 패치 별도 가능
```

---

### 💼 Scenario 3: 자동화된 Release 파이프라인

**목표:** Release 과정 완전 자동화

#### **Problem: 수동 Release가 너무 복잡하고 실수가 많다!**

```bash
# 수동 프로세스 문제점
# 1. 버전 번호 수동 업데이트 (실수 빈번)
# 2. CHANGELOG 수동 작성 (누락)
# 3. 태그 수동 생성 (오타)
# 4. Release Note 수동 작성 (시간 소요)
# 5. 바이너리 수동 빌드 (환경 차이)
# 6. 배포 수동 실행 (인적 오류)
```

#### **Solution: CI/CD 기반 자동화**

```bash
# ===== GitHub Actions 워크플로우 =====

# .github/workflows/release.yml
cat > .github/workflows/release.yml << 'EOF'
name: Release

on:
  push:
    branches:
      - main
      - 'release/**'
    tags:
      - 'v*'

jobs:
  # 1. 버전 검증
  validate:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Validate version
        run: |
          VERSION=$(cat VERSION)
          echo "Version: $VERSION"
          
          # SemVer 형식 확인
          if ! [[ $VERSION =~ ^[0-9]+\.[0-9]+\.[0-9]+$ ]]; then
            echo "Invalid version format"
            exit 1
          fi
  
  # 2. 테스트
  test:
    needs: validate
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-node@v3
      
      - run: npm ci
      - run: npm test
      - run: npm run lint
      
      - name: Security audit
        run: npm audit --production
  
  # 3. 빌드
  build:
    needs: test
    runs-on: ubuntu-latest
    strategy:
      matrix:
        os: [linux, macos, windows]
        arch: [x64, arm64]
    steps:
      - uses: actions/checkout@v3
      
      - name: Build binary
        run: |
          npm run build:${{ matrix.os }}-${{ matrix.arch }}
      
      - name: Upload artifact
        uses: actions/upload-artifact@v3
        with:
          name: binary-${{ matrix.os }}-${{ matrix.arch }}
          path: dist/*
  
  # 4. CHANGELOG 생성
  changelog:
    needs: test
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Generate CHANGELOG
        run: |
          npm install -g conventional-changelog-cli
          conventional-changelog -p angular -i CHANGELOG.md -s
      
      - name: Commit CHANGELOG
        run: |
          git config user.name "GitHub Actions"
          git config user.email "actions@github.com"
          git add CHANGELOG.md
          git commit -m "docs: Update CHANGELOG" || true
          git push
  
  # 5. GitHub Release 생성
  release:
    needs: [build, changelog]
    if: startsWith(github.ref, 'refs/tags/v')
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Download artifacts
        uses: actions/download-artifact@v3
      
      - name: Create Release
        uses: softprops/action-gh-release@v1
        with:
          files: |
            binary-*/
          body_path: CHANGELOG.md
          draft: false
          prerelease: false
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
  
  # 6. 배포
  deploy:
    needs: release
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      
      - name: Deploy to production
        run: |
          # Docker 이미지 빌드
          docker build -t myapp:${{ github.ref_name }} .
          
          # Registry에 push
          docker push myapp:${{ github.ref_name }}
          
          # Kubernetes 업데이트
          kubectl set image deployment/myapp \
            myapp=myapp:${{ github.ref_name }}
      
      - name: Verify deployment
        run: |
          kubectl rollout status deployment/myapp
  
  # 7. 알림
  notify:
    needs: deploy
    runs-on: ubuntu-latest
    steps:
      - name: Slack notification
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            Release ${{ github.ref_name }} deployed!
            
            Changelog: ${{ github.server_url }}/${{ github.repository }}/releases/tag/${{ github.ref_name }}
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
EOF

# ===== 사용 방법 =====

# 1. 개발 완료
git commit -m "feat: Add new feature"
git push

# 2. 버전 업데이트
echo "3.2.0" > VERSION
git add VERSION
git commit -m "chore: Bump version to 3.2.0"

# 3. 태그 생성 (트리거!)
git tag v3.2.0
git push origin v3.2.0

# 4. GitHub Actions 자동 실행
# ✅ 테스트 실행
# ✅ 빌드 (6개 플랫폼)
# ✅ CHANGELOG 생성
# ✅ GitHub Release 생성
# ✅ 바이너리 첨부
# ✅ 프로덕션 배포
# ✅ Slack 알림

# ===== Backport 자동화 =====

# .github/workflows/backport.yml
cat > .github/workflows/backport.yml << 'EOF'
name: Backport

on:
  pull_request:
    types: [closed]
    branches: [main]

jobs:
  backport:
    if: github.event.pull_request.merged == true
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Check labels
        id: check
        run: |
          LABELS="${{ join(github.event.pull_request.labels.*.name, ',') }}"
          echo "labels=$LABELS" >> $GITHUB_OUTPUT
      
      - name: Backport to 3.x
        if: contains(steps.check.outputs.labels, 'backport-3.x')
        run: |
          git switch release/3.x
          git cherry-pick ${{ github.event.pull_request.merge_commit_sha }}
          git push origin release/3.x
      
      - name: Backport to 2.x
        if: contains(steps.check.outputs.labels, 'backport-2.x')
        run: |
          git switch release/2.x
          git cherry-pick ${{ github.event.pull_request.merge_commit_sha }}
          git push origin release/2.x
      
      - name: Comment on PR
        uses: actions/github-script@v6
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: '✅ Automatically backported to release branches'
            })
EOF

# 사용: PR에 라벨 추가
# - backport-3.x
# - backport-2.x
# → Merge 시 자동 backport!

# ✅ 완전 자동화 완료!
```

**자동화 효과:**
```
수동 (Before):
- 시간: 2-3시간
- 실수율: 15%
- 스트레스: 높음

자동화 (After):
- 시간: 5-10분
- 실수율: 0%
- 스트레스: 낮음

추가 장점:
✅ 일관성
✅ 추적 가능
✅ 롤백 쉬움
✅ 문서화 자동
```

---

### 💼 Scenario 4: Hotfix 우선순위 관리

**목표:** 긴급도에 따른 Hotfix 전략

#### **Problem: 모든 버그가 긴급하다고 주장!**

```bash
# 상황
# 지원팀: "고객이 화났어요! 긴급!"
# PM: "이거 오늘 중 수정해야 해요!"
# 영업: "계약 걸린 이슈예요!"
# 개발자: "어느 것부터...?"
```

#### **Solution: 우선순위 매트릭스**

```bash
# ===== 우선순위 정의 =====

cat > HOTFIX_PRIORITY.md << 'EOF'
# Hotfix Priority Matrix

## P0 - Critical (즉시)
- 서비스 다운
- 데이터 손실
- 보안 취약점 (Critical)
- 결제 불가

처리: 즉시 (1시간 내)
배포: 모든 버전

## P1 - High (당일)
- 핵심 기능 불가
- 다수 사용자 영향
- 보안 취약점 (High)

처리: 4시간 내
배포: 현재 + LTS

## P2 - Medium (주간)
- 일부 기능 불가
- 소수 사용자 영향
- Workaround 있음

처리: 1주 내
배포: 다음 정기 릴리스

## P3 - Low (월간)
- UI 버그
- 문서 오류
- 마이너 불편

처리: 다음 릴리스
배포: 계획된 릴리스
EOF

# ===== P0 예시: 결제 시스템 다운 =====

# 11:00 - 발견
# 고객이 결제 불가!

# 11:05 - P0 확인
cat > issues/ISSUE-P0-001.md << 'EOF'
# P0: Payment System Down

## Impact
- All payments failing
- Affecting 100% users
- Revenue impact: $10k/hour

## Root Cause
Database connection pool exhausted

## Fix
Increase pool size from 10 to 50
EOF

# 11:10 - 수정 시작
git switch main
git switch -c hotfix/payment-pool

# 수정
echo "POOL_SIZE=50" >> config.env
git commit -m "fix(payment): Increase DB pool size

P0 - Critical
Fixes payment system downtime

Root cause: Pool exhaustion under load
Solution: Increase pool 10 → 50

Affects: All users
Impact: $10k/hour revenue loss

Fixes #P0-001"

# 11:30 - 테스트
npm test
npm run test:integration

# 11:45 - 모든 버전에 backport

# main (v4.x)
git switch main
git merge hotfix/payment-pool
git tag v4.2.1
git push origin main v4.2.1

# release/3.x (LTS)
git switch release/3.x
git cherry-pick <commit>
git tag v3.8.2
git push origin release/3.x v3.8.2

# release/2.x (유지보수)
git switch release/2.x
git cherry-pick <commit>
git tag v2.15.2
git push origin release/2.x v2.15.2

# 12:00 - 배포 (자동)
# CI/CD가 3개 버전 동시 배포

# 12:15 - 검증
# ✅ 결제 정상화
# ✅ 에러율 0%

# 12:30 - Postmortem
cat > postmortems/2024-12-23-payment-down.md << 'EOF'
# Postmortem: Payment System Downtime

## Incident
Date: 2024-12-23
Duration: 11:00 - 12:15 (75 minutes)
Severity: P0

## Impact
- 100% payment failures
- Revenue loss: ~$12,500
- Customer complaints: 150+

## Timeline
11:00 - First report
11:05 - P0 declared
11:10 - Fix started
11:45 - Fix deployed (all versions)
12:15 - Fully recovered

## Root Cause
DB connection pool (size: 10) exhausted
under Black Friday traffic (10x normal)

## Fix
Increased pool size to 50
Deployed to v4.2.1, v3.8.2, v2.15.2

## Prevention
- [ ] Add pool size monitoring
- [ ] Auto-scaling pool size
- [ ] Load testing before events
- [ ] Alert at 80% pool usage

## Lessons Learned
✅ P0 process worked well (75min TTR)
✅ Multi-version backport successful
⚠️ Need better capacity planning
EOF

# ===== P1 예시: 로그인 느림 =====

# 09:00 - 발견
# 로그인이 30초 걸림

# 09:15 - P1 확인
# 핵심 기능이지만 서비스는 동작

# 09:30 - 수정
git switch -c fix/login-slow
git commit -m "perf(auth): Optimize login query

P1 - High
Fixes slow login (30s → 2s)

Root cause: N+1 query
Solution: Add query optimization

Affects: All users
Impact: UX degradation"

# 13:00 - 테스트 및 리뷰
# PR 생성 및 리뷰

# 14:00 - 배포
# 현재 버전 + LTS만
git tag v4.2.2
git tag v3.8.3

# ===== P2 예시: UI 버그 =====

# 10:00 - 발견
# 다크모드에서 텍스트 안 보임

# 10:15 - P2 확인
# Workaround 있음 (라이트모드 사용)

# 다음 정기 릴리스에 포함
# 급하지 않음

# ✅ 우선순위 명확!
```

**우선순위 결정 트리:**
```
버그 발견
  ↓
서비스 다운? → Yes → P0 (즉시)
  ↓ No
보안 취약점? → Yes → P0/P1 (즉시/당일)
  ↓ No
결제/핵심 기능? → Yes → P1 (당일)
  ↓ No
다수 사용자? → Yes → P1 (당일)
  ↓ No
Workaround? → No → P2 (주간)
  ↓ Yes
  P3 (월간)
```

---

### 💼 Scenario 5: Deprecation 관리

**목표:** 기능 제거를 안전하게

#### **Problem: 오래된 API를 제거하고 싶은데 고객이 사용 중!**

```bash
# 상황
# - 오래된 API v1
# - 새 API v2 완성
# - v1 제거하고 싶음
# - 하지만 고객이 아직 사용
```

#### **Solution: 3단계 Deprecation**

```bash
# ===== Phase 1: Deprecation 선언 (v3.0) =====

# v3.0.0 릴리스
git switch main

# API에 Deprecation 경고 추가
cat > api/v1/users.js << 'EOF'
/**
 * @deprecated since v3.0.0
 * Use /api/v2/users instead
 * Will be removed in v4.0.0 (2025-06-01)
 */
router.get('/api/v1/users', (req, res) => {
  // 경고 로그
  console.warn('DEPRECATED: /api/v1/users used. Migrate to /api/v2/users');
  
  // 응답 헤더에 경고
  res.setHeader('Deprecation', 'true');
  res.setHeader('Sunset', '2025-06-01');
  res.setHeader('Link', '</api/v2/users>; rel="alternate"');
  
  // 메트릭 수집
  metrics.track('deprecated_api_v1_users');
  
  // 기존 로직 유지 (아직 동작!)
  const users = await User.findAll();
  res.json(users);
});
EOF

# 문서 업데이트
cat > docs/DEPRECATION.md << 'EOF'
# Deprecation Notice

## API v1 (/api/v1/*)

**Deprecated:** v3.0.0 (2024-01-01)
**Removal:** v4.0.0 (2025-06-01)
**Alternative:** /api/v2/*

### Timeline
- 2024-01-01: Deprecation announced
- 2024-06-01: Grace period ends (6 months)
- 2025-01-01: Warning emails sent
- 2025-06-01: Removed in v4.0.0

### Migration Guide
```js
// Before (v1)
fetch('/api/v1/users')

// After (v2)
fetch('/api/v2/users', {
  headers: { 'API-Version': '2' }
})
```

### Support
Contact support@example.com for help
EOF

git add api/ docs/
git commit -m "feat: Deprecate API v1

Deprecate /api/v1/* in favor of /api/v2/*

Timeline:
- Now (v3.0): Deprecated but functional
- v4.0 (2025-06): Removed

Migration guide: docs/DEPRECATION.md

BREAKING CHANGE: API v1 will be removed in v4.0"

git tag v3.0.0
git push origin main v3.0.0

# 고객 이메일
cat > email_deprecation.txt << 'EOF'
Subject: Action Required: API v1 Deprecation

Dear Customer,

We're writing to inform you about an important change:

## What's Changing
API v1 (/api/v1/*) is deprecated and will be removed 
in 18 months (June 2025).

## What You Need to Do
Migrate to API v2 (/api/v2/*) before June 2025.

## Timeline
- Now: v1 works with deprecation warnings
- June 2024: Grace period ends (6 months)
- June 2025: v1 completely removed

## Migration Guide
See: https://docs.example.com/deprecation

## Need Help?
Contact: support@example.com

Thank you,
Product Team
EOF

# ===== Phase 2: 모니터링 (6개월) =====

# Deprecation 사용량 추적
cat > scripts/deprecation-report.js << 'EOF'
const analytics = require('./analytics');

async function generateDeprecationReport() {
  const usage = await analytics.query({
    metric: 'deprecated_api_v1_users',
    timeRange: 'last_30_days'
  });
  
  console.log('=== API v1 Usage Report ===');
  console.log(`Total calls: ${usage.total}`);
  console.log(`Unique users: ${usage.uniqueUsers}`);
  console.log(`Top users:`);
  
  usage.topUsers.forEach(user => {
    console.log(`  ${user.email}: ${user.calls} calls`);
  });
  
  // 이메일 알림 (많이 사용하는 고객)
  if (usage.calls > 1000) {
    sendReminderEmail(user.email);
  }
}
EOF

# 월별 리포트
# Month 1: 10,000 calls/day
# Month 2: 8,000 calls/day ↓
# Month 3: 5,000 calls/day ↓
# Month 4: 2,000 calls/day ↓
# Month 5: 500 calls/day ↓
# Month 6: 100 calls/day ↓ (거의 없음)

# ===== Phase 3: 제거 (v4.0) =====

# v4.0.0 개발
git switch main

# API v1 완전 제거
rm -rf api/v1/
git commit -m "feat!: Remove deprecated API v1

BREAKING CHANGE: API v1 has been removed

Deprecated since: v3.0.0 (2024-01-01)
Grace period: 18 months
Removed in: v4.0.0 (2025-06-01)

Alternatives: API v2 (/api/v2/*)
Migration guide: docs/DEPRECATION.md

Current usage: <100 calls/day
Affected users: Contacted directly"

git tag v4.0.0
git push origin main v4.0.0

# Release Notes
cat > RELEASE_v4.0.0.md << 'EOF'
# v4.0.0 - Major Release

## Breaking Changes

### API v1 Removed ⚠️

API v1 has been completely removed after 18-month 
deprecation period.

**If you're still using API v1:**
- Your application will break
- Migrate to API v2 immediately
- See: docs/DEPRECATION.md

**Timeline:**
- 2024-01: Deprecated
- 2025-06: Removed (now)

## New Features
- API v2 enhancements
- Performance improvements

## Migration Guide
Full guide: https://docs.example.com/v4-migration
EOF

# 남은 소수 사용자에게 직접 연락
cat > email_final_notice.txt << 'EOF'
Subject: URGENT: API v1 Removed

Dear Customer,

API v1 has been removed in v4.0.0 released today.

## Immediate Action Required
Your application may be broken.
Migrate to API v2 now.

## Emergency Support
Call: +1-555-0123
Email: emergency@example.com

We're here to help 24/7.

Apologies for any inconvenience,
Product Team
EOF

# ✅ 안전한 Deprecation 완료!
```

**Deprecation 베스트 프랙티스:**
```
Timeline:
✅ 18-24개월 예고
✅ 6개월마다 알림
✅ 3개월 전 최종 경고

Communication:
✅ Release notes
✅ 문서
✅ 이메일 (여러 번)
✅ API 응답 헤더
✅ 로그 경고

Monitoring:
✅ 사용량 추적
✅ 사용자 식별
✅ 마이그레이션 진행률

Support:
✅ 마이그레이션 가이드
✅ 자동화 도구
✅ 개인 지원
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: 버전 번호 일관성 없음**

```bash
# 위험한 패턴
v3.0.0
v3.1
v3.02  # ❌ 잘못된 형식
v3.10.0
v3.2  # ❌ 순서 엉킴
```

**올바른 방법:**
```bash
# SemVer 준수
v3.0.0
v3.1.0  # ✅ PATCH도 항상 포함
v3.2.0
v3.10.0  # ✅ 10 > 2 (숫자 비교)

# 자동화로 강제
npm version patch  # 자동 증가
```

---

#### ❌ **실수 2: LTS 버전에 새 기능 추가**

```bash
# 위험한 패턴
git switch release/3.x  # LTS
git commit -m "feat: Add new dashboard"  # ❌ 새 기능!
```

**올바른 방법:**
```bash
# LTS는 보안+Critical만!
git switch release/3.x
git commit -m "fix(security): CVE-2024-001"  # ✅
git commit -m "fix(critical): Payment bug"  # ✅

# 새 기능은 main (v4.x)로
git switch main
git commit -m "feat: Add new dashboard"  # ✅
```

---

#### ❌ **실수 3: EOL 버전에 계속 패치**

```bash
# 낭비
git switch release/1.x  # EOL 2023
git commit -m "fix: Bug"  # ❌ 의미 없음
```

**올바른 방법:**
```bash
# EOL 이후는 패치 중단
# 고객에게 업그레이드 권장

# EOL 선언
cat > EOL_NOTICE.md << 'EOF'
v1.x reached End of Life on 2023-12-31

No further updates.
Upgrade to v2.x or v3.x required.
EOF
```

---

#### ❌ **실수 4: Changelog 없이 릴리스**

```bash
# 나쁜 예
git tag v3.5.0
git push origin v3.5.0
# 고객: "뭐가 바뀐 거죠?"
```

**올바른 방법:**
```bash
# Changelog 작성
conventional-changelog -p angular -i CHANGELOG.md -s

# 확인
cat CHANGELOG.md

# Commit
git add CHANGELOG.md
git commit -m "docs: Update changelog for v3.5.0"

# 태그
git tag v3.5.0
git push origin main v3.5.0
```

---

### 🎯 실습 미션

#### **미션 1: 멀티 버전 Release** 🎯

**목표:** 3개 버전 동시 관리 실습

```bash
# 준비
mkdir release-management && cd release-management
git init
git branch -M main

# 미션:
# 1. v2.x LTS 브랜치 생성
# 2. v3.x Current 브랜치 생성
# 3. main에서 v4.x 개발
# 4. 보안 패치를 모든 버전에 backport
# 5. 각 버전 태그 생성
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 초기 커밋
echo "App v1" > app.js
git add app.js
git commit -m "feat: Initial version"

# 2. v2.x LTS
git tag v2.0.0
git switch -c release/2.x
echo "LTS Branch" > LTS.md
git add LTS.md
git commit -m "docs: Mark as LTS"
git push origin release/2.x v2.0.0

# 3. v3.x Current
git switch main
git tag v3.0.0
git switch -c release/3.x
git push origin release/3.x v3.0.0

# 4. v4.x (main)
git switch main
echo "v4 feature" >> app.js
git add app.js
git commit -m "feat: v4 feature"

# 5. 보안 패치
git commit -m "fix(security): Critical patch"
# Commit: abc123

# 6. Backport to v3.x
git switch release/3.x
git cherry-pick abc123
git tag v3.0.1
git push origin release/3.x v3.0.1

# 7. Backport to v2.x
git switch release/2.x
git cherry-pick abc123
git tag v2.0.1
git push origin release/2.x v2.0.1

# 8. 확인
git tag
# v2.0.0
# v2.0.1
# v3.0.0
# v3.0.1

# ✅ 멀티 버전 관리 완료!
```

</details>

---

#### **미션 2: Changelog 자동 생성** 📝

**목표:** Conventional Commits로 Changelog 자동화

```bash
# 시나리오:
# 1. Conventional Commits으로 커밋
# 2. conventional-changelog로 생성
# 3. 버전 태그
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Conventional Commits
git commit -m "feat(auth): Add login"
git commit -m "feat(api): Add GraphQL"
git commit -m "fix(payment): Fix timeout"
git commit -m "docs: Update README"

# 2. Changelog 생성
npm install -g conventional-changelog-cli
conventional-changelog -p angular -i CHANGELOG.md -s

# 3. 확인
cat CHANGELOG.md
# ## [3.1.0] - 2024-12-23
# 
# ### Features
# - **auth:** Add login
# - **api:** Add GraphQL
# 
# ### Bug Fixes
# - **payment:** Fix timeout

# 4. Commit
git add CHANGELOG.md
git commit -m "docs: Update CHANGELOG for v3.1.0"

# 5. 태그
git tag v3.1.0
git push origin main v3.1.0

# ✅ 자동화 완료!
```

</details>

---

#### **미션 3: Deprecation 프로세스** ⚠️

**목표:** 기능 Deprecation부터 제거까지

```bash
# 시나리오:
# 1. 오래된 API v1 deprecate
# 2. v2 제공
# 3. 경고 추가
# 4. 다음 버전에서 제거
```

<details>
<summary>정답 보기</summary>

```bash
# 1. API v1 (기존)
cat > api-v1.js << 'EOF'
function getUsersV1() {
  return ['user1', 'user2'];
}
module.exports = { getUsersV1 };
EOF

git add api-v1.js
git commit -m "feat: Add API v1"
git tag v3.0.0

# 2. API v2 (새 버전)
cat > api-v2.js << 'EOF'
function getUsersV2() {
  return [
    { id: 1, name: 'user1' },
    { id: 2, name: 'user2' }
  ];
}
module.exports = { getUsersV2 };
EOF

git add api-v2.js
git commit -m "feat: Add API v2"

# 3. v1 Deprecate
cat > api-v1.js << 'EOF'
/**
 * @deprecated Use getUsersV2 from api-v2.js
 * Will be removed in v4.0.0
 */
function getUsersV1() {
  console.warn('DEPRECATED: getUsersV1 will be removed in v4.0.0');
  return ['user1', 'user2'];
}
module.exports = { getUsersV1 };
EOF

cat > DEPRECATION.md << 'EOF'
# Deprecation Notice

## API v1

**Deprecated:** v3.1.0
**Removal:** v4.0.0
**Alternative:** API v2

See migration guide...
EOF

git add api-v1.js DEPRECATION.md
git commit -m "feat: Deprecate API v1

BREAKING CHANGE: API v1 deprecated, use v2"

git tag v3.1.0

# 4. v1 제거 (v4.0.0)
rm api-v1.js
git add api-v1.js
git commit -m "feat!: Remove API v1

BREAKING CHANGE: API v1 removed
Use API v2 instead"

git tag v4.0.0

# ✅ Deprecation 프로세스 완료!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === Release 브랜치 ===
git switch -c release/3.x        # 생성
git tag -a v3.0.0 -m "Release"   # 태그
git push origin release/3.x      # Push

# === Backport ===
git cherry-pick <commit>         # 이식
git cherry-pick <c1> <c2>        # 여러 개
git cherry-pick --abort          # 취소

# === 버전 관리 ===
npm version patch                # 3.0.0 → 3.0.1
npm version minor                # 3.0.1 → 3.1.0
npm version major                # 3.1.0 → 4.0.0

# === Changelog ===
conventional-changelog -p angular -i CHANGELOG.md -s

# === GitHub Release ===
gh release create v3.0.0 \
  --title "Release v3.0.0" \
  --notes-file CHANGELOG.md
```

---

### 🎨 Release Timeline 템플릿

```
v3.0.0 Release Plan

Week -6: Feature Freeze
  └─ No new features
  └─ Bug fixes only

Week -4: Beta
  └─ v3.0.0-beta.1
  └─ Public testing

Week -2: RC
  └─ v3.0.0-rc.1
  └─ Final testing

Week 0: Release
  └─ v3.0.0
  └─ Production

Week +0: LTS
  └─ v2.x becomes LTS
  └─ Security only

Week +52: EOL
  └─ v1.x End of Life
  └─ No more support
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [2.4 Trunk-Based](./04-trunk-based.md)

**다음 학습:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)

**관련 주제:**
- [1.4 커밋 해부](../01-fundamentals/04-commit-anatomy.md)
- [4.1 Rebase](../04-rebase/01-rebase-basics.md)

---

<div align="center">

**🎉 축하합니다! Release 관리를 완전히 마스터했습니다!**

**🎊 브랜치 전략 5부작 완전 정복! 🎊**

[⬅️ 이전: Trunk-Based](./04-trunk-based.md) | [README로](../README.md) | [다음: Merge 타입 ➡️](../03-merge/01-merge-types.md)

</div>
