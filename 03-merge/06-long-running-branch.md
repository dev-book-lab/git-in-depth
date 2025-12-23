# 3.6 장기 브랜치 통합 (Long-Running Branch Integration)

**난이도: ⭐⭐⭐**

> *"장기 브랜치는 merge의 최종 보스입니다. 전략과 인내가 성공을 만듭니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 장기 브랜치 통합을 알아야 하나?

**실제 개발 시나리오:**
```
상황: 6개월간 분리된 브랜치

main 브랜치:
- 500개 커밋
- 50명 개발자
- 200개 파일 변경

feature-v2 브랜치:
- 300개 커밋
- 10명 개발자
- 새로운 아키텍처

시도:
git merge feature-v2
# CONFLICT in 150 files!
# 😱😱😱

개발자: "이거 어떻게 하죠?"
팀장: "merge 포기?"
CTO: "전략이 필요해요!"
```

**장기 브랜치 통합을 이해하면:**
```bash
전략 없이 (Before):
git merge feature-v2
→ 150개 파일 충돌
→ 3일 소요
→ 버그 다수
→ 스트레스 극심

전략적 접근 (After):
1. 분석 (1일)
2. 점진적 통합 (1주)
3. 충돌 분산 처리
4. 안전한 완료
→ 총 2주, 안정적

✅ 충돌 최소화
✅ 리스크 분산
✅ 안전한 통합
✅ 팀 부담 감소
```

> 💡 **핵심:** 장기 브랜치는 한번에 merge하지 않습니다!
> 단계적, 전략적 접근이 성공의 열쇠입니다!

---

### 📌 장기 브랜치란?

#### **정의**

```
장기 브랜치 (Long-Running Branch):
main에서 분리된 지 오래된 브랜치

기준:
- 시간: 1개월 이상
- 커밋: 100개 이상
- 파일: 50개 이상 변경
- 개발자: 여러 명

특징:
- 대량 충돌 가능
- 아키텍처 차이
- 히스토리 복잡
```

---

#### **발생 원인**

```
원인 1: 대규모 리팩토링
예: 레거시 시스템 재작성
기간: 6개월
결과: 전체 구조 변경

원인 2: 새 기능 개발
예: v2.0 개발
기간: 3개월
결과: 많은 새 기능

원인 3: 실험적 프로젝트
예: 새 기술 스택
기간: 4개월
결과: 완전히 다른 구현

원인 4: 팀 분리
예: 별도 팀이 개발
기간: 2개월
결과: 다른 방향 진화
```

---

### 🎯 위험 요소

#### **1️⃣ 충돌 폭발**

```
문제:
시간이 지날수록 충돌 지수 증가

1개월: ~20개 충돌
2개월: ~60개 충돌
3개월: ~150개 충돌
6개월: ~500개 충돌

이유:
- 양쪽 모두 진화
- 공통 파일 수정
- 아키텍처 분기
```

---

#### **2️⃣ 의도 불명확**

```
문제:
오래전 변경이라 이유 망각

예시:
main: 변수명 user → customer
feature: 변수명 user → account

왜 바꿨지?
누가 바꿨지?
어느 게 맞지?

→ 결정 어려움
```

---

#### **3️⃣ 테스트 폭발**

```
문제:
merge 후 테스트 대량 실패

main: 1000개 테스트
feature: 800개 테스트
merge 후: 400개 실패!

이유:
- API 변경
- 의존성 불일치
- 예상치 못한 상호작용
```

---

#### **4️⃣ 팀 부담**

```
문제:
한 번에 처리하려면 팀 전체 중단

충돌 해결: 3일
테스트 수정: 2일
버그 수정: 1주
→ 총 2주 프로젝트 중단

팀 사기 저하
생산성 급락
```

---

### 🛡️ 해결 전략

#### **1️⃣ 점진적 통합 (Incremental Integration)**

```
전략:
한 번에 merge하지 않고
작은 단위로 나눠서 통합

방법:
main → feature (주기적 rebase)
feature → main (단계적 merge)

효과:
- 충돌 분산
- 리스크 최소화
- 지속적 검증
```

---

#### **2️⃣ 선행 동기화 (Forward Integration)**

```
전략:
merge 전에 최신 main을 가져옴

방법:
git switch feature
git merge main (또는 rebase)

효과:
- feature가 최신 상태
- 충돌 미리 해결
- merge 시 충돌 감소
```

---

#### **3️⃣ 모듈별 통합 (Module-wise Integration)**

```
전략:
모듈/기능 단위로 순차 통합

순서:
1. 공통 유틸리티
2. 데이터 모델
3. 비즈니스 로직
4. API 레이어
5. UI 컴포넌트

효과:
- 의존성 순서 준수
- 단계별 테스트
- 롤백 용이
```

---

#### **4️⃣ 미리보기 Merge (Preview Merge)**

```
전략:
실제 merge 전에 시뮬레이션

방법:
1. 테스트 브랜치 생성
2. 충돌 파악
3. 전략 수립
4. 실제 merge

효과:
- 충돌 예측
- 시간 추정
- 리스크 평가
```

---

### 📊 통합 접근법 비교

| 접근법 | 시간 | 충돌 | 리스크 | 권장 |
|:------|:-----|:-----|:------|:-----|
| **한방 Merge** | 짧음 | 최대 | 높음 | ❌ |
| **점진적 통합** | 보통 | 분산 | 낮음 | ✅ |
| **선행 동기화** | 보통 | 중간 | 낮음 | ✅ |
| **모듈별** | 김 | 최소 | 최저 | ✅✅ |

---

### 🔍 충돌 분석

#### **충돌 예측**

```bash
# 충돌 예측 (merge 전)
git merge --no-commit --no-ff feature
git diff --name-only --diff-filter=U

# 예상 충돌:
# src/api.js
# src/model.js
# tests/api.test.js
# ... (150개)

git merge --abort

# 분석:
# - API 파일: 양쪽 수정
# - 모델: 스키마 변경
# - 테스트: 새 기능
```

---

#### **충돌 분류**

```
충돌 타입별 분류:

Type A: 간단 (공백, 포맷)
→ 자동 해결 가능
→ 80개

Type B: 중간 (로직 변경)
→ 검토 필요
→ 50개

Type C: 복잡 (아키텍처)
→ 팀 논의 필요
→ 20개

전략:
A → 자동화
B → 도구 사용
C → 수동 + 리뷰
```

---

### 📅 통합 타임라인

```
전형적인 장기 브랜치 통합 일정:

Week -2: 준비
- 분석
- 전략 수립
- 팀 교육

Week -1: 동기화
- main → feature
- 충돌 해결
- 테스트

Week 0: 통합
Day 1: Core modules
Day 2: Business logic
Day 3: API layer
Day 4: UI components
Day 5: 통합 테스트

Week +1: 안정화
- 버그 수정
- 성능 최적화
- 문서화

총: 4주
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 분석 명령어

#### **1️⃣ 브랜치 비교**

```bash
# 커밋 차이
git log --oneline main..feature
# feature에만 있는 커밋

git log --oneline feature..main
# main에만 있는 커밋

# 양쪽 비교
git log --oneline --left-right main...feature
# < main의 커밋
# > feature의 커밋

# 통계
git log --oneline main..feature | wc -l
# feature가 main보다 300개 앞섬

git log --oneline feature..main | wc -l
# main이 feature보다 500개 앞섬
```

---

#### **2️⃣ 파일 변경 분석**

```bash
# 변경된 파일 목록
git diff --name-only main...feature

# 통계
git diff --stat main...feature
# src/api.js      | 150 ++++-----
# src/model.js    | 200 ++++++----
# tests/*.js      | 500 +++++++++++
# 50 files changed, 2000 insertions(+), 500 deletions(-)

# 변경 타입별
git diff --name-status main...feature
# M    modified.js
# A    added.js
# D    deleted.js
# R100 old.js -> new.js
```

---

#### **3️⃣ 충돌 예측**

```bash
# 미리보기 merge
git merge --no-commit --no-ff feature

# 충돌 파일 확인
git diff --name-only --diff-filter=U
# api.js
# model.js
# config.js

# 충돌 개수
git diff --name-only --diff-filter=U | wc -l
# 150

# 취소
git merge --abort
```

---

#### **4️⃣ Merge Base 확인**

```bash
# 공통 조상
git merge-base main feature
# abc123...

# 얼마나 오래됐나
git log --oneline abc123..main | wc -l
# 500 (main이 500개 앞섬)

git log --oneline abc123..feature | wc -l
# 300 (feature가 300개 앞섬)

# 시간으로
git log --since="$(git show -s --format=%ci abc123)" main | wc -l
# 6개월 전
```

---

### ⚡ 점진적 통합

#### **1️⃣ 선행 동기화 (Feature → Main)**

```bash
# Feature에서 main 가져오기
git switch feature

# Merge 방식
git merge main
# 충돌 해결
git commit

# Rebase 방식 (더 깔끔)
git rebase main
# 충돌 해결
git rebase --continue

# 주기: 매주 1회 권장
```

---

#### **2️⃣ 부분 Merge**

```bash
# 특정 파일만 merge
git switch main
git checkout feature -- src/utils.js
git add src/utils.js
git commit -m "Merge utils from feature"

# 특정 커밋만 cherry-pick
git cherry-pick abc123 def456
# feature의 특정 커밋만 가져오기

# 디렉토리 단위
git checkout feature -- src/models/
git add src/models/
git commit -m "Merge models from feature"
```

---

#### **3️⃣ Merge 커밋 생성 (히스토리용)**

```bash
# 내용은 main 유지, 히스토리만 연결
git merge -s ours feature
# feature와 연결되지만 코드는 main

# 다음에 실제 merge
git merge feature
# 충돌 최소화됨 (이미 히스토리 연결)
```

---

### 📋 단계별 통합 워크플로우

```bash
# ===== Phase 1: 준비 =====

# 1. 브랜치 동기화
git switch feature
git fetch origin
git merge origin/main

# 2. 충돌 예측
git switch main
git merge --no-commit --no-ff feature
git diff --name-only --diff-filter=U > conflicts.txt
git merge --abort

# 3. 분석
cat conflicts.txt | wc -l
# 150개

# ===== Phase 2: 모듈별 통합 =====

# 통합 브랜치 생성
git switch main
git switch -c integration/feature

# Step 1: Utils (의존성 없음)
git checkout feature -- src/utils/
git add src/utils/
git commit -m "Integrate utils from feature"

# 테스트
npm test -- src/utils/
# ✅ 통과

# Step 2: Models
git checkout feature -- src/models/
git add src/models/
git commit -m "Integrate models from feature"

# 충돌 발생 시
git status
# Unmerged paths

# 수동 해결
vim src/models/User.js
git add src/models/User.js

npm test
# ✅ 통과

# Step 3: Services
git checkout feature -- src/services/
# ... 충돌 해결 ...
git commit -m "Integrate services from feature"

# Step 4: API
git checkout feature -- src/api/
# ... 충돌 해결 ...
git commit -m "Integrate API from feature"

# Step 5: UI
git checkout feature -- src/components/
# ... 충돌 해결 ...
git commit -m "Integrate UI from feature"

# ===== Phase 3: 검증 =====

# 전체 테스트
npm test
npm run lint
npm run build

# 통합 테스트
npm run test:integration

# ===== Phase 4: 완료 =====

# Main으로
git switch main
git merge integration/feature

# 태그
git tag -a v2.0.0 -m "Major release: Feature integration"

# Push
git push origin main --tags
```

---

### 🔍 충돌 최소화 기법

#### **1️⃣ 전략적 Rebase**

```bash
# Feature를 main 위로
git switch feature
git rebase -i main

# Interactive rebase:
# 1. 커밋 정리 (squash)
# 2. 순서 조정
# 3. 충돌 미리 해결

# 결과:
# - 깔끔한 히스토리
# - 충돌 분산 처리
# - Merge 시 충돌 감소
```

---

#### **2️⃣ 3-way Merge 전략**

```bash
# Patience diff (더 나은 알고리즘)
git merge -X patience feature

# 공백 무시
git merge -X ignore-space-change feature

# 결합
git merge -X patience -X ignore-space-change feature
```

---

#### **3️⃣ 파일별 전략**

```bash
# Ours 우선 (main)
git merge -X ours feature

# Theirs 우선 (feature)
git merge -X theirs feature

# 파일별 선택
git merge --no-commit feature
git checkout --ours config.json
git checkout --theirs new-feature.js
git add config.json new-feature.js
git commit
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 6개월 분리 브랜치

**목표:** 대규모 feature 안전하게 통합

#### **Problem: v2.0이 6개월간 분리!**

```bash
# 상황
feature-v2 브랜치:
- 분리: 6개월 전
- 커밋: 300개
- 파일: 200개 변경
- 새 아키텍처

main 브랜치:
- 커밋: 500개
- 파일: 150개 변경
- 버그 수정, 개선

예상 충돌: 150개 파일
```

#### **Solution: 4주 점진적 통합**

```bash
# ===== Week 1: 분석 및 준비 =====

# Day 1: 브랜치 분석
echo "=== Branch Analysis ==="

# 커밋 차이
git log --oneline main..feature-v2 | wc -l
# 300 commits ahead

git log --oneline feature-v2..main | wc -l
# 500 commits behind

# Merge base
MERGE_BASE=$(git merge-base main feature-v2)
git log --oneline -1 $MERGE_BASE
# abc123 (6 months ago)

# 파일 변경
git diff --stat main...feature-v2 > changes.txt
cat changes.txt
# 200 files changed, 10000 insertions(+), 3000 deletions(-)

# Day 2: 충돌 예측
echo "=== Conflict Prediction ==="

# 미리보기
git switch main
git switch -c test-merge
git merge --no-commit --no-ff feature-v2

# 충돌 파일
git diff --name-only --diff-filter=U > conflicts.txt
cat conflicts.txt | wc -l
# 150 conflicts

# 분류
grep "\.js$" conflicts.txt | wc -l
# 100 JavaScript files

grep "\.json$" conflicts.txt | wc -l
# 10 Config files

grep "\.css$" conflicts.txt | wc -l
# 20 Style files

grep "test\." conflicts.txt | wc -l
# 20 Test files

# 취소
git switch main
git branch -D test-merge

# Day 3: 전략 수립
cat > INTEGRATION_PLAN.md << 'EOF'
# Feature v2.0 Integration Plan

## Timeline: 4 weeks

### Week 1: Preparation
- Analysis ✅
- Strategy ✅
- Team training
- Tool setup

### Week 2: Forward Sync
- Merge main → feature-v2
- Resolve conflicts
- Test feature-v2
- Stabilize

### Week 3: Module Integration
Day 1: Utils & Models (40 files)
Day 2: Services (30 files)
Day 3: API Layer (30 files)
Day 4: UI Components (50 files)
Day 5: Tests (50 files)

### Week 4: Validation
- Integration tests
- Performance tests
- Bug fixes
- Documentation

## Conflict Strategy
- Type A (80 files): Auto-resolve (format)
- Type B (50 files): Tool-assisted (logic)
- Type C (20 files): Manual review (architecture)

## Rollback Plan
Tag each phase for easy rollback

## Team Assignment
- Lead: @tech-lead
- Utils/Models: @team-a
- Services/API: @team-b
- UI: @team-c
- Tests: @team-qa
EOF

# Day 4: 도구 설정
echo "=== Tool Setup ==="

# Merge tool 통일
./scripts/setup-merge-tool.sh

# 자동화 스크립트
cat > scripts/integrate-module.sh << 'EOF'
#!/bin/bash

MODULE=$1

echo "=== Integrating $MODULE ==="

# Checkout files
git checkout feature-v2 -- $MODULE

# Status
git status

# Add
git add $MODULE

# Test
npm test -- $MODULE

if [ $? -eq 0 ]; then
  echo "✅ Tests passed"
  git commit -m "Integrate $MODULE from feature-v2"
else
  echo "❌ Tests failed"
  git reset HEAD $MODULE
  git checkout -- $MODULE
  exit 1
fi
EOF

chmod +x scripts/integrate-module.sh

# Day 5: 팀 교육
echo "=== Team Training ==="
# - 통합 계획 공유
# - 도구 사용법
# - 충돌 해결 프로세스

# ===== Week 2: Forward Sync =====

echo "=== Week 2: Forward Synchronization ==="

# Feature-v2를 최신으로
git switch feature-v2
git fetch origin

# Merge main (큰 작업!)
git merge origin/main

# 충돌 해결 (2-3일)
# ... 150개 파일 ...

# 모듈별 해결
for module in utils models services api ui; do
  echo "Resolving $module..."
  # 충돌 해결
  git add src/$module/
done

git commit -m "Merge main into feature-v2

Resolved conflicts:
- Utils: formatting differences
- Models: schema updates  
- Services: API changes
- UI: component updates

All tests passing"

# 테스트
npm test
npm run build

# Push
git push origin feature-v2

# ===== Week 3: Module Integration =====

echo "=== Week 3: Module Integration ==="

# Integration 브랜치
git switch main
git switch -c integration/feature-v2

# Day 1: Utils & Models
./scripts/integrate-module.sh src/utils
./scripts/integrate-module.sh src/models

# 충돌 발생 시
git status
# both modified: src/models/User.js

# 해결
git mergetool

# 테스트
npm test -- src/models/

git commit -m "Integrate utils and models from feature-v2

Changes:
- Updated utility functions
- New data models
- Schema migrations

Tests: All passing"

# Day 2: Services
./scripts/integrate-module.sh src/services

# 충돌 복잡 → 팀 회의
echo "Complex conflicts in services..."
# 아키텍처 논의
# 수동 해결

git commit -m "Integrate services from feature-v2

Architecture changes:
- New service layer
- Updated dependencies
- Backward compatibility maintained"

# Day 3: API Layer
./scripts/integrate-module.sh src/api

# Day 4: UI Components
./scripts/integrate-module.sh src/components

# Day 5: Tests
./scripts/integrate-module.sh tests

# 통합 테스트
npm run test:integration

# ===== Week 4: Validation =====

echo "=== Week 4: Validation ==="

# 전체 테스트
npm test
# ✅ 1500/1500 tests passed

# 성능 테스트
npm run benchmark
# ✅ Performance acceptable

# 빌드
npm run build
# ✅ Build successful

# Lint
npm run lint
# ⚠️ 50 warnings (fixing...)

# 버그 수정 (발견된 것들)
git commit -m "Fix integration bugs

- Fixed API compatibility
- Updated test assertions  
- Resolved dependency conflicts"

# 문서
cat > docs/V2_MIGRATION.md << 'EOF'
# v2.0 Migration Guide

## What's New
- New architecture
- Improved performance
- Modern APIs

## Breaking Changes
- Old API deprecated
- Config format changed

## Migration Steps
1. Update dependencies
2. Migrate config
3. Update API calls

See detailed guide...
EOF

# ===== Final Merge =====

echo "=== Final Merge ==="

# Main으로
git switch main
git merge integration/feature-v2

# Fast-forward (충돌 없음!)
# Updating abc123..def456
# Fast-forward

# 태그
git tag -a v2.0.0 -m "Major release: v2.0

- 6 months of development
- 300 commits integrated
- 200 files changed
- New architecture
- All tests passing

Integration period: 4 weeks
Team effort: 5 developers"

# Push
git push origin main v2.0.0

# ===== 결과 =====

echo "=== Integration Complete! ==="

# 통계
git log --oneline v1.0.0..v2.0.0 | wc -l
# 800 commits (500 main + 300 feature)

git diff --stat v1.0.0 v2.0.0
# 200 files changed, 13000 insertions(+), 3000 deletions(-)

# Before:
# - 예상 시간: 1주 (한방 merge)
# - 충돌: 150개 동시
# - 리스크: 매우 높음
# - 스트레스: 극심

# After:
# - 실제 시간: 4주 (점진적)
# - 충돌: 분산 처리
# - 리스크: 낮음
# - 팀 부담: 관리 가능

# ✅ 안전한 대규모 통합 완료!
```

---

### 💼 Scenario 2: 빅뱅 Merge 실패 후 복구

**목표:** 실패한 merge 체계적으로 복구

#### **Problem: 한방 merge 시도했다가 실패!**

```bash
# 상황
개발자: "merge 해보죠!"
git merge feature-big

# CONFLICT in 200 files!
# 😱 패닉!

개발자: "뭔가 잘못됨..."
# 3시간 충돌 해결...
# 테스트 500개 실패
# 코드 엉망

팀장: "이거 안 되겠는데..."
CTO: "롤백하고 다시!"
```

#### **Solution: 체계적 복구 및 재통합**

```bash
# ===== Step 1: 현재 상태 저장 =====

echo "=== Saving Current State ==="

# 현재 작업 저장
git stash push -m "Failed merge attempt"

# 또는 브랜치로
git switch -c failed-merge-backup

# Main으로 복귀
git switch main
git reset --hard origin/main

# ===== Step 2: 문제 분석 =====

echo "=== Analyzing Failure ==="

# 무엇이 잘못됐나
cat > FAILURE_ANALYSIS.md << 'EOF'
# Merge Failure Analysis

## What Went Wrong
1. Big Bang approach
   - 200 files at once
   - No preparation
   - No conflict prediction

2. No strategy
   - Random conflict resolution
   - No testing between steps
   - No rollback plan

3. Inadequate tools
   - Terminal only
   - No GUI tools
   - No automation

## Lessons Learned
- Never big bang merge
- Need preparation
- Need strategy
- Need tools
- Need time

## New Approach
- 4-week plan
- Module-by-module
- Forward sync first
- Tool-assisted
- Continuous testing
EOF

# ===== Step 3: 올바른 접근 =====

echo "=== Correct Approach ==="

# Phase 1: Forward Sync
git switch feature-big
git merge main
# 충돌 해결 (여유있게)

# 하루 종일 걸려도 OK
# 한 번만 하면 됨

# Phase 2: 모듈 분석
tree src/
# src/
#   ├── core/      (20 files)
#   ├── api/       (30 files)
#   ├── ui/        (50 files)
#   ├── services/  (40 files)
#   └── utils/     (60 files)

# Phase 3: 우선순위
cat > PRIORITY.md << 'EOF'
# Integration Priority

1. utils (독립적)
2. core (의존성 적음)
3. services (core 의존)
4. api (services 의존)
5. ui (모든 것 의존)
EOF

# Phase 4: 점진적 통합
git switch main
git switch -c integration/feature-big-v2

# Step 1: Utils (1일)
echo "Day 1: Utils"
git checkout feature-big -- src/utils/
git add src/utils/
npm test -- src/utils/
git commit -m "Integrate utils"

# Step 2: Core (1일)
echo "Day 2: Core"
git checkout feature-big -- src/core/
# 충돌 발생
git mergetool
git add src/core/
npm test -- src/core/
git commit -m "Integrate core"

# Step 3: Services (2일)
echo "Day 3-4: Services"
git checkout feature-big -- src/services/
# 복잡한 충돌
# 팀 리뷰
git add src/services/
npm test -- src/services/
git commit -m "Integrate services"

# Step 4: API (1일)
echo "Day 5: API"
git checkout feature-big -- src/api/
git add src/api/
npm test -- src/api/
git commit -m "Integrate API"

# Step 5: UI (2일)
echo "Day 6-7: UI"
git checkout feature-big -- src/ui/
# 충돌 + 테스트 수정
git add src/ui/
npm test
git commit -m "Integrate UI"

# ===== Step 4: 검증 =====

# 전체 테스트
npm test
# ✅ All passed

# Main으로
git switch main
git merge integration/feature-big-v2

# ===== 결과 비교 =====

# Big Bang (실패):
# 시간: 3시간 (미완성)
# 충돌: 200개 동시
# 테스트: 500개 실패
# 결과: 포기

# 점진적 (성공):
# 시간: 7일
# 충돌: 분산 처리
# 테스트: 단계별 통과
# 결과: 완료

# ✅ 실패에서 배워 성공!
```

---

### 💼 Scenario 3: 경쟁 브랜치 통합

**목표:** 동시에 개발된 여러 브랜치

#### **Problem: 3개 팀이 동시 개발!**

```bash
# 상황
team-a: feature-a (3개월)
team-b: feature-b (3개월)
team-c: feature-c (3개월)

모두 main에서 분리
모두 대규모 변경
일부 겹치는 파일

어떻게 통합?
```

#### **Solution: 토너먼트 방식**

```bash
# ===== Step 1: 통합 순서 결정 =====

echo "=== Integration Order ==="

# 의존성 분석
cat > DEPENDENCIES.md << 'EOF'
# Feature Dependencies

feature-a: Infrastructure
- Database schema
- API framework
- No dependencies

feature-b: Business Logic
- Depends on: feature-a
- User management
- Payment system

feature-c: UI
- Depends on: feature-a, feature-b
- Frontend components
- User interface

Order: a → b → c
EOF

# ===== Step 2: 선행 동기화 (모든 팀) =====

echo "=== Forward Sync: All Branches ==="

# Team A
git switch feature-a
git merge main
# 충돌 해결
git push origin feature-a

# Team B
git switch feature-b
git merge main
# 충돌 해결
git push origin feature-b

# Team C
git switch feature-c
git merge main
# 충돌 해결
git push origin feature-c

# ===== Step 3: Round 1 (A) =====

echo "=== Round 1: Feature A ==="

# Integration 브랜치
git switch main
git switch -c integration/round1

# Merge feature-a
git merge feature-a
# 충돌 최소 (이미 동기화)

# 테스트
npm test
# ✅ Pass

# Main으로
git switch main
git merge integration/round1
git branch -d integration/round1

git tag -a release/feature-a -m "Feature A integrated"

# ===== Step 4: 중간 동기화 =====

echo "=== Mid Sync: B and C ==="

# Team B: 최신 main (A 포함) 가져오기
git switch feature-b
git merge main
# 충돌 가능 (A와 겹치는 부분)
# 해결
git push origin feature-b

# Team C: 최신 main (A 포함) 가져오기
git switch feature-c
git merge main
# 충돌 가능
# 해결
git push origin feature-c

# ===== Step 5: Round 2 (B) =====

echo "=== Round 2: Feature B ==="

git switch main
git switch -c integration/round2

git merge feature-b
# A 덕분에 충돌 감소

# 테스트
npm test
# ✅ Pass

git switch main
git merge integration/round2
git branch -d integration/round2

git tag -a release/feature-b -m "Feature B integrated"

# ===== Step 6: 최종 동기화 =====

echo "=== Final Sync: C ==="

# Team C: 최신 main (A, B 포함)
git switch feature-c
git merge main
# A, B와 통합
# 충돌 최소
git push origin feature-c

# ===== Step 7: Round 3 (C) =====

echo "=== Round 3: Feature C ==="

git switch main
git switch -c integration/round3

git merge feature-c
# 거의 충돌 없음!

# 테스트
npm test
# ✅ Pass

git switch main
git merge integration/round3
git branch -d integration/round3

git tag -a release/all-features -m "All features integrated"

# ===== 결과 =====

git log --oneline --graph -20
# *   Release all features
# |\  
# | *   Feature C
# | |\  
# * | |   Feature B
# |\ \ \  
# * | | |   Feature A
# ...

# 통계
git diff --stat release/feature-a~1 release/feature-a
# Feature A: 100 files

git diff --stat release/feature-b~1 release/feature-b
# Feature B: 80 files

git diff --stat release/all-features~1 release/all-features
# Feature C: 60 files

# 충돌
# Round 1 (A): 20개
# Round 2 (B): 15개 (A 덕분에 감소)
# Round 3 (C): 5개 (A, B 덕분에 최소)

# 만약 동시 merge:
# 예상 충돌: 100개+

# ✅ 토너먼트 방식으로 충돌 75% 감소!
```

---

### 💼 Scenario 4: 리팩토링 브랜치 통합

**목표:** 전면 리팩토링 안전하게 통합

#### **Problem: 전체 아키텍처 재작성!**

```bash
# 상황
refactor 브랜치:
- 4개월 작업
- 전체 구조 변경
- 패키지 재구성
- 파일 이동 다수

main 브랜치:
- 계속 개발
- 새 기능 추가
- 버그 수정

충돌 예상: 거의 모든 파일
```

#### **Solution: 이중 트랙 전략**

```bash
# ===== Step 1: 분석 =====

echo "=== Refactor Analysis ==="

# 무엇이 바뀌었나
git diff --name-status main refactor > changes.txt

# 이동된 파일
grep "^R" changes.txt > renamed.txt
cat renamed.txt | wc -l
# 150 files renamed

# 수정된 파일
grep "^M" changes.txt > modified.txt
cat modified.txt | wc -l
# 100 files modified

# 새 파일
grep "^A" changes.txt > added.txt

# ===== Step 2: 이동 먼저 (Main에서) =====

echo "=== Step 1: Apply Renames to Main ==="

# Main에 이동 적용
git switch main
git switch -c main-with-renames

# 수동으로 이동 (또는 스크립트)
while read line; do
  old=$(echo $line | awk '{print $2}')
  new=$(echo $line | awk '{print $3}')
  
  git mv $old $new
done < renamed.txt

git commit -m "Refactor: Apply file renames

Preparing for refactor merge:
- 150 files renamed
- Directory structure updated
- No logic changes

This aligns main with refactor structure"

# Main으로
git switch main
git merge main-with-renames

# ===== Step 3: Feature 동기화 =====

echo "=== Step 2: Sync Features to Refactor ==="

# Refactor에 main 병합
git switch refactor
git merge main

# 충돌 (하지만 이동은 이미 해결됨!)
# 로직 변경만 해결
# ... 충돌 해결 ...

git commit -m "Merge main features into refactor"

# ===== Step 4: 점진적 통합 =====

echo "=== Step 3: Gradual Integration ==="

git switch main
git switch -c integration/refactor

# Layer 1: Core (새 아키텍처)
git checkout refactor -- src/core/
git add src/core/
npm test -- src/core/
git commit -m "Integrate refactored core"

# Layer 2: Services
git checkout refactor -- src/services/
git add src/services/
npm test -- src/services/
git commit -m "Integrate refactored services"

# Layer 3: API
git checkout refactor -- src/api/
git add src/api/
npm test -- src/api/
git commit -m "Integrate refactored API"

# Layer 4: Tests
git checkout refactor -- tests/
git add tests/
npm test
git commit -m "Integrate updated tests"

# ===== Step 5: 검증 =====

# 전체 테스트
npm test
# ✅ Pass

# 성능 비교
npm run benchmark > benchmark.txt

cat benchmark.txt
# Before: 150ms average
# After: 80ms average
# 🎉 47% improvement!

# ===== Step 6: 완료 =====

git switch main
git merge integration/refactor

git tag -a v3.0.0 -m "Major refactor

- Architecture overhaul
- Performance improved 47%
- Code organization improved
- All tests passing"

# ===== 결과 =====

# 이중 트랙 전략:
# 1. Main에 구조 먼저 적용
# 2. Refactor에 기능 동기화
# 3. 점진적 통합

# 효과:
# - 충돌 90% 감소
# - 안전한 전환
# - 성능 개선 검증

# ✅ 전면 리팩토링 성공!
```

---

### 💼 Scenario 5: 자동화된 통합 파이프라인

**목표:** CI/CD로 통합 자동화

#### **Problem: 수동 통합이 너무 오래 걸림!**

```bash
# 요구사항
# - 주기적 통합 (매주)
# - 자동 충돌 감지
# - 자동 테스트
# - 알림
```

#### **Solution: 자동화 파이프라인**

```bash
# ===== Step 1: GitHub Actions =====

cat > .github/workflows/integration.yml << 'EOF'
name: Long-Running Branch Integration

on:
  schedule:
    - cron: '0 0 * * 0'  # 매주 일요일
  workflow_dispatch:

jobs:
  # Job 1: 분석
  analyze:
    runs-on: ubuntu-latest
    outputs:
      conflicts: ${{ steps.check.outputs.conflicts }}
    steps:
      - uses: actions/checkout@v3
        with:
          fetch-depth: 0
      
      - name: Check conflicts
        id: check
        run: |
          git merge --no-commit --no-ff feature-long
          conflicts=$(git diff --name-only --diff-filter=U | wc -l)
          echo "conflicts=$conflicts" >> $GITHUB_OUTPUT
          git merge --abort
      
      - name: Report
        run: |
          echo "Conflicts: ${{ steps.check.outputs.conflicts }}"
  
  # Job 2: 선행 동기화
  forward-sync:
    needs: analyze
    if: needs.analyze.outputs.conflicts < 50
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
        with:
          ref: feature-long
          fetch-depth: 0
      
      - name: Merge main
        run: |
          git config user.name "GitHub Actions"
          git config user.email "actions@github.com"
          
          git merge origin/main
          
          # 자동 해결 시도
          git merge -X ours origin/main || true
      
      - name: Test
        run: |
          npm ci
          npm test
      
      - name: Push
        if: success()
        run: |
          git push origin feature-long
  
  # Job 3: 알림
  notify:
    needs: [analyze, forward-sync]
    runs-on: ubuntu-latest
    steps:
      - name: Slack notification
        uses: 8398a7/action-slack@v3
        with:
          status: ${{ job.status }}
          text: |
            Integration Report:
            - Conflicts: ${{ needs.analyze.outputs.conflicts }}
            - Sync: ${{ needs.forward-sync.result }}
          webhook_url: ${{ secrets.SLACK_WEBHOOK }}
EOF

# ===== Step 2: 모니터링 스크립트 =====

cat > scripts/monitor-integration.sh << 'EOF'
#!/bin/bash

echo "=== Integration Monitor ==="

# 브랜치 나이
age=$(git log --format=%cr $(git merge-base main feature-long) -1)
echo "Branch age: $age"

# 커밋 차이
ahead=$(git log --oneline main..feature-long | wc -l)
behind=$(git log --oneline feature-long..main | wc -l)
echo "Ahead: $ahead, Behind: $behind"

# 충돌 예측
git merge --no-commit --no-ff feature-long 2>/dev/null
conflicts=$(git diff --name-only --diff-filter=U | wc -l)
git merge --abort 2>/dev/null

echo "Conflicts: $conflicts"

# 경고
if [ $behind -gt 100 ]; then
  echo "⚠️  WARNING: Too far behind!"
fi

if [ $conflicts -gt 50 ]; then
  echo "⚠️  WARNING: Many conflicts!"
fi
EOF

chmod +x scripts/monitor-integration.sh

# 주간 실행 (crontab)
# 0 9 * * 1 /path/to/monitor-integration.sh

# ===== Step 3: 자동 리포트 =====

cat > scripts/integration-report.sh << 'EOF'
#!/bin/bash

OUTPUT="integration-report-$(date +%Y%m%d).md"

cat > $OUTPUT << 'REPORT'
# Integration Status Report

**Date:** $(date)
**Branch:** feature-long

## Statistics

### Commits
- Feature ahead: $(git log --oneline main..feature-long | wc -l)
- Main ahead: $(git log --oneline feature-long..main | wc -l)

### Conflicts
- Predicted: $(git merge --no-commit --no-ff feature-long 2>&1 | grep -c CONFLICT)

### Files Changed
$(git diff --stat main...feature-long | tail -1)

## Recommendations

$(
if [ $conflicts -lt 20 ]; then
  echo "✅ Ready for integration"
elif [ $conflicts -lt 50 ]; then
  echo "⚠️  Moderate conflicts, proceed with caution"
else
  echo "❌ Too many conflicts, forward sync recommended"
fi
)

## Next Steps
1. Run forward sync
2. Review conflicts
3. Schedule integration

---
*Generated automatically*
REPORT

# 리포트 전송
# mail -s "Integration Report" team@example.com < $OUTPUT
EOF

chmod +x scripts/integration-report.sh

# ===== Step 4: 대시보드 =====

cat > integration-dashboard.html << 'EOF'
<!DOCTYPE html>
<html>
<head>
  <title>Integration Dashboard</title>
  <style>
    .metric { padding: 20px; margin: 10px; border: 1px solid #ddd; }
    .ok { background: #dfd; }
    .warning { background: #ffd; }
    .danger { background: #fdd; }
  </style>
</head>
<body>
  <h1>Long-Running Branch Integration</h1>
  
  <div class="metric ok">
    <h2>Branch Age</h2>
    <p id="age">Loading...</p>
  </div>
  
  <div class="metric warning">
    <h2>Commits Behind</h2>
    <p id="behind">Loading...</p>
  </div>
  
  <div class="metric danger">
    <h2>Predicted Conflicts</h2>
    <p id="conflicts">Loading...</p>
  </div>
  
  <script>
    async function update() {
      // Fetch from API
      const response = await fetch('/api/integration-status');
      const data = await response.json();
      
      document.getElementById('age').textContent = data.age;
      document.getElementById('behind').textContent = data.behind;
      document.getElementById('conflicts').textContent = data.conflicts;
    }
    
    update();
    setInterval(update, 60000); // 1분마다
  </script>
</body>
</html>
EOF

# ===== 결과 =====

# 자동화 효과:
# - 주간 자동 동기화
# - 충돌 조기 감지
# - 자동 리포트
# - 실시간 모니터링

# 수동 (Before):
# - 6개월 방치
# - 갑작스런 통합
# - 대량 충돌

# 자동 (After):
# - 주기적 동기화
# - 충돌 분산
# - 예측 가능

# ✅ 자동화된 통합 파이프라인!
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: "나중에 한번에" 심리**

```bash
# 위험한 생각
"지금은 바쁘니까..."
"기능 다 만들고..."
"나중에 한번에 merge!"

# 6개월 후
# 😱 merge 불가능
```

**올바른 방법:**
```bash
# 주기적 동기화
git switch feature
git merge main  # 매주!

# 작은 충돌 자주 vs 큰 충돌 한번
```

---

#### ❌ **실수 2: 선행 동기화 생략**

```bash
# 위험
git switch main
git merge feature-long
# 충돌 폭탄!
```

**올바른 방법:**
```bash
# 먼저 feature에서 main 가져오기
git switch feature-long
git merge main
# 충돌 해결

# 그 다음 main으로
git switch main
git merge feature-long
# 충돌 최소!
```

---

#### ❌ **실수 3: 테스트 생략**

```bash
# 위험
git merge --no-ff feature
# 빠르게 commit
git push

# 나중에 발견
# 프로덕션 버그!
```

**올바른 방법:**
```bash
# 단계마다 테스트
git merge feature
npm test  # ✅
git commit
```

---

#### ❌ **실수 4: 롤백 계획 없음**

```bash
# 위험
# 통합 시작
# 문제 발생
# 롤백 방법 모름
```

**올바른 방법:**
```bash
# 각 단계 태그
git tag integration/step1
git tag integration/step2

# 문제 시
git reset --hard integration/step1
```

---

### 🎯 실습 미션

#### **미션 1: 점진적 통합 시뮬레이션** 🎯

**목표:** 모듈별 통합 경험

```bash
# 준비
mkdir long-running && cd long-running
git init

# 미션:
# 1. 장기 브랜치 생성
# 2. 모듈별 통합
# 3. 단계별 커밋
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 초기
mkdir -p src/{utils,api,ui}
echo "main" > src/utils/util.js
echo "main" > src/api/api.js
echo "main" > src/ui/ui.js
git add .
git commit -m "Initial"

# 2. Feature 브랜치
git switch -c feature-long

echo "feature utils" > src/utils/util.js
echo "feature api" > src/api/api.js
echo "feature ui" > src/ui/ui.js
git add .
git commit -m "Feature updates"

# 3. Main 계속 개발
git switch main
echo "main updated" >> src/utils/util.js
git add .
git commit -m "Main update"

# 4. 점진적 통합
git switch -c integration

# Step 1: Utils
git checkout feature-long -- src/utils/
git add src/utils/
git commit -m "Integrate utils"

# Step 2: API
git checkout feature-long -- src/api/
git add src/api/
git commit -m "Integrate API"

# Step 3: UI
git checkout feature-long -- src/ui/
git add src/ui/
git commit -m "Integrate UI"

# 5. 완료
git switch main
git merge integration

# ✅ 모듈별 통합 완료!
```

</details>

---

#### **미션 2: 충돌 예측** 🔍

**목표:** Merge 전 충돌 파악

```bash
# 시나리오:
# 큰 브랜치 merge 전
# 충돌 미리 확인
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "Base" > file.txt
git add .
git commit -m "Base"

# Main
echo "Main 1" >> file.txt
git add .
git commit -m "Main 1"

echo "Main 2" >> file.txt
git add .
git commit -m "Main 2"

# Feature
git switch -c feature HEAD~2
echo "Feature 1" >> file.txt
git add .
git commit -m "Feature 1"

echo "Feature 2" >> file.txt
git add .
git commit -m "Feature 2"

# 충돌 예측
git switch main
git merge --no-commit --no-ff feature

# 충돌 확인
git diff --name-only --diff-filter=U
# file.txt

# 충돌 개수
git diff --name-only --diff-filter=U | wc -l
# 1

# 취소
git merge --abort

# ✅ 미리 파악!
```

</details>

---

#### **미션 3: 토너먼트 통합** 🏆

**목표:** 3개 브랜치 순차 통합

```bash
# 시나리오:
# A, B, C 브랜치
# A → B → C 순서로
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "Base" > file.txt
git add .
git commit -m "Base"

# Branch A
git switch -c feature-a
echo "A" >> file.txt
git add .
git commit -m "Feature A"

# Branch B
git switch main
git switch -c feature-b
echo "B" >> file.txt
git add .
git commit -m "Feature B"

# Branch C
git switch main
git switch -c feature-c
echo "C" >> file.txt
git add .
git commit -m "Feature C"

# Round 1: A
git switch main
git merge feature-a
git tag round1

# Round 2: B (A 포함)
git switch feature-b
git merge main  # A 가져오기
# 충돌 해결
git add .
git commit

git switch main
git merge feature-b
git tag round2

# Round 3: C (A, B 포함)
git switch feature-c
git merge main  # A, B 가져오기
# 충돌 해결
git add .
git commit

git switch main
git merge feature-c
git tag round3

# 확인
git log --oneline --graph

# ✅ 토너먼트 완료!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 분석 ===
git log --oneline main..feature      # Feature 커밋
git diff --stat main...feature       # 파일 차이
git merge --no-commit --no-ff feat   # 충돌 예측

# === 동기화 ===
git switch feature
git merge main                       # 선행 동기화

# === 모듈별 통합 ===
git checkout feature -- src/module/  # 모듈 가져오기
git add src/module/
git commit -m "Integrate module"

# === 롤백 ===
git reset --hard integration/step1   # 단계로 복귀
```

---

### 🎨 통합 전략 선택

```
1개월 이하:
→ 일반 merge

1-3개월:
→ 선행 동기화 + merge

3-6개월:
→ 점진적 통합

6개월 이상:
→ 모듈별 통합 + 자동화
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [3.5 Merge Tools](./05-merge-tools.md)

**다음 학습:**
- [4.1 Rebase 기초](../04-rebase/01-rebase-basics.md)

**관련 주제:**
- [3.1 Merge 타입](./01-merge-types.md)
- [3.4 Merge 전략](./04-merge-strategies.md)

---

<div align="center">

**🎉 축하합니다! 장기 브랜치 통합을 완전히 마스터했습니다!**

**🎊 Merge 챕터 완전 정복! 🎊**

이제 어떤 브랜치도 안전하게 통합할 수 있습니다!

[⬅️ 이전: Merge Tools](./05-merge-tools.md) | [README로](../README.md) | [다음: Rebase 기초 ➡️](../04-rebase/01-rebase-basics.md)

</div>
