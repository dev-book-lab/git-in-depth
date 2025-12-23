# 2.4 Trunk-Based Development

**난이도: ⭐⭐⭐**

> *"Trunk-Based Development는 극한의 단순함입니다. 모든 개발자가 하나의 브랜치에서 일합니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 Trunk-Based Development를 알아야 하나?

**실제 개발 시나리오:**
```
상황: 글로벌 테크 기업 (Google, Facebook, Netflix)

요구사항:
- 하루 100번+ 배포
- 수천 명의 개발자
- 최소한의 머지 충돌
- 빠른 피드백 루프
- 지속적 통합 (CI) 필수

팀장: "브랜치가 많으면 통합이 느려져..."
개발자: "긴 브랜치는 충돌 지옥이에요"
DevOps: "배포 파이프라인이 복잡해요"
CTO: "경쟁사보다 10배 빠르게 배포해야 해요!"
```

**Trunk-Based Development를 사용하면:**
```bash
trunk (main) - 단 하나의 브랜치!
  ↓
모든 개발자가 직접 커밋 (또는 초단기 브랜치)
  ↓
1-2일 안에 trunk로 merge
  ↓
자동화된 테스트 + 배포
  ↓
Feature Toggles로 미완성 기능 숨김

✅ 극한의 단순함
✅ 최소 충돌
✅ 빠른 통합
✅ 지속적 배포
✅ 대규모 팀 지원
```

> 💡 **핵심:** Trunk-Based Development는 하나의 브랜치(trunk)에
> 모든 개발자가 빈번하게 통합하는 극한의 CI 전략입니다!

---

### 📌 Trunk-Based Development의 핵심 개념

```
┌─────────────────────────────────────────────────┐
│       Trunk-Based Development 핵심               │
├─────────────────────────────────────────────────┤
│                                                 │
│  trunk (main)                                   │
│    ↓                                            │
│  모든 개발자의 작업이 여기로                           │
│    ↓                                            │
│  브랜치 수명: < 1-2일                              │
│    ↓                                            │
│  하루에 여러 번 통합                                │
│    ↓                                            │
│  Feature Toggles 필수                            │
│    ↓                                            │
│  자동화된 테스트 필수                                │
│                                                 │
└─────────────────────────────────────────────────┘
```

---

#### **원칙 1: 하나의 Trunk (주 브랜치)**

```
전통적 방식:
master, develop, feature/*, release/*
(여러 장기 브랜치)

Trunk-Based:
trunk (또는 main)
(단 하나의 진실의 원천)

특징:
✅ 모든 개발자가 동일한 브랜치
✅ 장기 브랜치 없음
✅ 항상 최신 상태
✅ 통합 지연 없음
```

---

#### **원칙 2: 짧은 수명 브랜치**

```
브랜치 규칙:
- 수명: 1-2일 최대
- 크기: 작은 변경
- 목적: 단일 기능/버그

허용되는 브랜치:
✅ 초단기 feature 브랜치 (1-2일)
✅ 즉시 merge되는 hotfix

금지:
❌ 일주일 이상 브랜치
❌ 여러 기능 포함
❌ 장기 feature 브랜치
```

**브랜치 수명 비교:**
```
Git Flow:
feature/* - 1-2주

GitHub Flow:
feature/* - 3-7일

Trunk-Based:
feature/* - 1-2일 (또는 직접 커밋)
```

---

#### **원칙 3: 빈번한 통합**

```
통합 빈도:
- 최소: 하루 1회
- 권장: 하루 여러 번
- 이상적: 커밋마다

방법:
1. 직접 trunk에 커밋
2. 초단기 브랜치 → 즉시 merge

이유:
✅ 충돌 최소화
✅ 빠른 피드백
✅ 진정한 CI
```

---

#### **원칙 4: Feature Toggles (필수!)**

```
역할:
- 미완성 기능 숨기기
- 점진적 롤아웃
- A/B 테스트
- 즉시 롤백

중요성:
Trunk-Based에서는 필수!
→ trunk에 미완성 코드가 들어감
→ Toggle로 숨겨야 안전

예시:
if (featureToggles.newCheckout) {
  // 새 체크아웃 (개발 중)
} else {
  // 기존 체크아웃 (안정)
}
```

---

#### **원칙 5: 자동화된 테스트**

```
필수 자동화:
✅ 단위 테스트 (80%+ 커버리지)
✅ 통합 테스트
✅ E2E 테스트 (핵심 플로우)
✅ 성능 테스트
✅ 보안 스캔

실행 시점:
- 커밋마다
- PR 생성 시
- Merge 전
- 배포 전

이유:
trunk = production
→ 테스트 없으면 재앙!
```

---

#### **원칙 6: 지속적 통합/배포 (CI/CD)**

```
파이프라인:
커밋
  ↓
자동 테스트
  ↓
자동 빌드
  ↓
자동 배포 (staging)
  ↓
검증
  ↓
자동 배포 (production)

속도:
- 커밋 → 배포: 10-30분
- 하루 배포 횟수: 10-100회+

필수 요소:
✅ 빠른 빌드 (< 10분)
✅ 빠른 테스트 (< 5분)
✅ 자동 롤백
✅ 모니터링
```

---

### 🌳 Trunk-Based의 두 가지 스타일

#### **1️⃣ 직접 Trunk 커밋 (Commit to Trunk)**

**대규모 팀, 성숙한 프로세스**

```bash
# 직접 trunk에 커밋
git switch trunk
git pull --rebase
# ... 작업 ...
git add .
git commit -m "feat: Add feature"
git push

# CI가 자동 실행
# 테스트 통과 → 자동 배포
```

**특징:**
```
✅ 가장 빠른 통합
✅ 브랜치 없음
✅ 충돌 최소
✅ 극한의 단순함

⚠️ 요구사항:
- 매우 높은 테스트 커버리지
- 숙련된 개발자
- 강력한 CI
- Feature Toggles 필수
```

---

#### **2️⃣ 초단기 브랜치 (Short-Lived Branches)**

**중규모 팀, 코드 리뷰 필요**

```bash
# 1일짜리 브랜치
git switch -c feature/quick-feature
# ... 작업 (몇 시간) ...
git commit -m "feat: Add feature"
git push

# PR 생성
gh pr create

# 리뷰 (30분-2시간)
# Merge (즉시)

# 브랜치 삭제
```

**특징:**
```
✅ 코드 리뷰 가능
✅ 여전히 빠름
✅ 품질 보장
✅ 유연함

규칙:
- 수명: 1-2일 최대
- 크기: 작은 변경
- 리뷰: 빠르게 (2-4시간)
- Merge: 즉시
```

---

### 📊 브랜치 전략 비교

| 특성 | Git Flow | GitHub Flow | Trunk-Based |
|:-----|:---------|:-----------|:-----------|
| **브랜치 수** | 5종류 | 2종류 | 1종류 (+초단기) |
| **브랜치 수명** | 1-4주 | 3-7일 | 1-2일 |
| **통합 빈도** | 주/월 | 일 | 시간/일 |
| **충돌 위험** | 높음 | 중간 | 낮음 |
| **배포 속도** | 느림 | 빠름 | 매우 빠름 |
| **복잡도** | 높음 | 중간 | 낮음 |
| **학습 곡선** | 높음 | 낮음 | 중간 |
| **자동화 요구** | 낮음 | 중간 | 매우 높음 |
| **팀 크기** | 중-대 | 소-중 | 소-대 |

---

### 🎯 Trunk-Based의 장단점

#### **장점 ✅**

```
1. 극한의 빠른 통합
   - 하루 여러 번 통합
   - 충돌 최소화
   - 진정한 CI

2. 최소 브랜치 복잡도
   - trunk 하나만
   - 관리 부담 없음
   - 헷갈릴 일 없음

3. 빠른 피드백
   - 커밋 → 테스트: 분 단위
   - 문제 조기 발견
   - 빠른 수정

4. 대규모 팀 지원
   - Google, Facebook 사용
   - 수천 명 개발자
   - 확장성 뛰어남

5. 지속적 배포 최적
   - 하루 100번+ 배포
   - 작은 변경
   - 쉬운 롤백

6. 머지 지옥 없음
   - 작고 자주 통합
   - 충돌 거의 없음
   - 스트레스 감소
```

---

#### **단점 ❌**

```
1. 높은 진입 장벽
   - 문화 변화 필요
   - 숙련도 필요
   - 학습 시간 소요

2. 자동화 필수
   - 테스트 자동화
   - CI/CD 구축
   - 모니터링
   - 초기 투자 큼

3. Feature Toggles 복잡
   - Toggle 관리
   - 기술 부채 가능
   - 제거 필요

4. 테스트 커버리지 필수
   - 80%+ 필요
   - 작성 시간 소요
   - 유지보수 부담

5. 코드 리뷰 어려움
   - 직접 커밋 시 리뷰 없음
   - 빠른 리뷰 필요
   - 사후 리뷰

6. 미완성 코드 위험
   - trunk에 WIP 코드
   - Toggle 누락 시 위험
   - 품질 관리 어려움
```

---

### 🏢 Trunk-Based 적합한 프로젝트

#### **✅ Trunk-Based를 써야 할 때:**

```
1. 대규모 웹 서비스
   - Google, Facebook, Netflix
   - 수백-수천 명 개발자
   - 하루 수백 번 배포

2. 성숙한 자동화
   - CI/CD 완벽 구축
   - 테스트 커버리지 높음
   - 모니터링 완비

3. 빠른 배포 필수
   - 경쟁 우위
   - 빠른 실험
   - A/B 테스트 빈번

4. 숙련된 팀
   - Senior 개발자 다수
   - DevOps 문화
   - 자율성 높음

5. 단일 프로덕션
   - SaaS 플랫폼
   - 웹 애플리케이션
   - API 서비스

예시:
- Google (Search, Gmail)
- Facebook (Facebook.com)
- Netflix (Streaming)
- Amazon (Retail)
- Uber (App)
```

---

#### **❌ Trunk-Based를 피해야 할 때:**

```
1. 여러 버전 지원
   - v1.x, v2.x, v3.x
   - 고객별 버전
   - 장기 지원

2. 자동화 부족
   - 수동 테스트
   - 수동 배포
   - CI/CD 없음

3. 느린 릴리스
   - 월/분기 배포
   - 긴 QA 프로세스
   - 승인 절차 복잡

4. 주니어 팀
   - 경험 부족
   - 트레이닝 필요
   - 실수 위험

5. 복잡한 승인
   - 여러 단계 승인
   - 규제 산업
   - 보수적 문화

대안:
- Git Flow: 여러 버전
- GitHub Flow: 코드 리뷰
- GitLab Flow: 환경별 브랜치
```

---

### 🔑 Trunk-Based 성공 요소

#### **1️⃣ 작은 변경 (Small Batches)**

```
원칙:
- 한 번에 하나씩
- 작게 나누기
- 빠른 통합

크기 가이드:
✅ 1개 파일 변경
✅ 100줄 이하
✅ 1-2시간 작업

❌ 10개 파일 변경
❌ 1000줄 변경
❌ 1주일 작업

이유:
- 충돌 최소화
- 빠른 리뷰
- 쉬운 롤백
- 이해 쉬움
```

---

#### **2️⃣ 빠른 빌드 (< 10분)**

```
목표:
커밋 → 빌드 완료: 10분 이내

최적화 방법:
✅ 병렬 빌드
✅ 캐싱
✅ 증분 빌드
✅ 분산 빌드

느리면:
- 피드백 느림
- 개발 흐름 끊김
- 생산성 하락
```

---

#### **3️⃣ 빠른 테스트 (< 5분)**

```
테스트 전략:
1. Commit Stage (1-2분)
   - 단위 테스트
   - Lint
   - 빠른 검증

2. Acceptance Stage (3-5분)
   - 통합 테스트
   - API 테스트
   - 핵심 E2E

3. 전체 E2E (nightly)
   - 모든 시나리오
   - 성능 테스트
   - 보안 테스트

피라미드:
    ▲ E2E (적음)
   ◆ Integration (중간)
  ◆◆ Unit (많음)
```

---

#### **4️⃣ Feature Toggles 전략**

```
Toggle 타입:
1. Release Toggles
   - 미완성 기능 숨김
   - 수명: 2주-2개월
   
2. Ops Toggles
   - 성능 조절
   - Kill switch
   - 수명: 영구

3. Experiment Toggles
   - A/B 테스트
   - 수명: 실험 기간

4. Permission Toggles
   - 사용자별 권한
   - 수명: 영구

관리:
✅ Toggle 인벤토리
✅ 자동 제거 알림
✅ 테스트 모든 조합
✅ 기본값 신중
```

---

#### **5️⃣ 모니터링 & 관찰성**

```
필수 메트릭:
✅ 배포 빈도
✅ Lead Time
✅ MTTR (복구 시간)
✅ Change Failure Rate

관찰성:
✅ 로그 집계
✅ 분산 추적
✅ 메트릭 대시보드
✅ 알람

목적:
- 문제 조기 발견
- 빠른 롤백
- 데이터 기반 결정
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 Trunk-Based 워크플로우 명령어

#### **1️⃣ 직접 Trunk 커밋 스타일**

```bash
# trunk 최신화
git switch trunk
git pull --rebase origin trunk

# 작업 (작게!)
echo "Small change" > file.txt
git add file.txt
git commit -m "feat: Add small feature"

# Push (CI 트리거)
git push origin trunk

# CI가 자동으로:
# 1. 테스트 실행
# 2. 빌드
# 3. 배포 (자동)
```

---

#### **2️⃣ 초단기 브랜치 스타일**

```bash
# 1. trunk 최신화
git switch trunk
git pull --rebase origin trunk

# 2. 브랜치 생성 (짧은 이름)
git switch -c quick-fix

# 3. 작업 (몇 시간)
echo "Quick fix" > file.txt
git add file.txt
git commit -m "fix: Quick bug fix"

# 4. trunk 동기화 (중요!)
git switch trunk
git pull --rebase
git switch quick-fix
git rebase trunk

# 5. Push
git push origin quick-fix

# 6. PR 생성 (간단하게)
gh pr create --title "Quick fix" --body "Fixes #123"

# 7. 리뷰 (빠르게: 30분-2시간)
# 팀원 리뷰...

# 8. Merge (즉시!)
gh pr merge --squash

# 9. 정리
git switch trunk
git pull
git branch -d quick-fix

# ✅ 총 소요: 반나절
```

---

#### **3️⃣ Feature Toggle 적용**

```bash
# 1. Toggle 시스템 추가 (최초 1회)
cat > featureToggles.js << 'EOF'
class FeatureToggles {
  constructor() {
    this.toggles = {
      newCheckout: process.env.NEW_CHECKOUT === 'true',
      betaFeatures: this.getUserBeta(),
      experimentA: Math.random() < 0.1  // 10% 롤아웃
    };
  }
  
  isEnabled(toggle) {
    return this.toggles[toggle] || false;
  }
  
  getUserBeta() {
    // 사용자별 베타 체크
    return false;
  }
}

module.exports = new FeatureToggles();
EOF

git add featureToggles.js
git commit -m "feat: Add feature toggle system"
git push origin trunk

# 2. 미완성 기능 개발 (Toggle 뒤에!)
cat > checkout.js << 'EOF'
const toggles = require('./featureToggles');

function processCheckout(cart) {
  if (toggles.isEnabled('newCheckout')) {
    return newCheckoutFlow(cart);  // 개발 중!
  } else {
    return oldCheckoutFlow(cart);   // 안정적
  }
}
EOF

git add checkout.js
git commit -m "feat: Add new checkout (behind toggle)"
git push origin trunk

# ✅ trunk에 merge됐지만 사용자는 영향 없음!

# 3. 계속 개발
cat > newCheckoutFlow.js << 'EOF'
function newCheckoutFlow(cart) {
  // 새로운 체크아웃 로직
  // 아직 완성 안 됨...
}
EOF

git add newCheckoutFlow.js
git commit -m "feat: Implement new checkout flow"
git push origin trunk

# 4. 완성 후 Toggle 활성화 (점진적)
# ENV: NEW_CHECKOUT=true (5% 사용자)
# 테스트 → 25% → 50% → 100%

# 5. Toggle 제거 (완전 안정 후)
cat > checkout.js << 'EOF'
function processCheckout(cart) {
  return newCheckoutFlow(cart);  // 이제 이것만!
}
EOF

git add checkout.js
git commit -m "refactor: Remove checkout toggle (100% rollout)"
git push origin trunk
```

---

#### **4️⃣ 브랜치 동기화 (중요!)**

```bash
# trunk가 계속 업데이트됨
# 자주 동기화 필요!

# 방법 1: Rebase (권장)
git switch my-branch
git fetch origin
git rebase origin/trunk

# 충돌 시
git rebase --continue

# 방법 2: Merge (간단)
git switch my-branch
git merge origin/trunk

# 권장 빈도:
# - 하루 1회 이상
# - 작업 시작 시
# - Merge 직전
```

---

### 📊 명령어 비교표

#### **워크플로우 비교**

| 단계 | Git Flow | GitHub Flow | Trunk-Based |
|:-----|:---------|:-----------|:-----------|
| **브랜치 생성** | `git flow feature start` | `git switch -c feature` | `git switch -c quick` |
| **브랜치 수명** | 1-2주 | 3-7일 | 1-2일 |
| **동기화** | 거의 안 함 | 가끔 | 하루 여러 번 |
| **Merge** | develop → release → master | PR → main | PR → trunk (즉시) |
| **배포** | Release 완료 시 | Merge 후 자동 | Merge 후 즉시 |

---

#### **브랜치 전략 선택 기준**

| 요구사항 | Git Flow | GitHub Flow | Trunk-Based |
|:---------|:---------|:-----------|:-----------|
| 여러 버전 지원 | ✅ 최적 | ❌ 불가 | ❌ 불가 |
| 빠른 배포 | ❌ 느림 | ✅ 빠름 | ✅ 최고 |
| 대규모 팀 | ✅ 가능 | ⚠️ 중간 | ✅ 최적 |
| 자동화 필요 | ⚠️ 중간 | ✅ 높음 | ✅ 매우 높음 |
| 학습 난이도 | ⚠️ 높음 | ✅ 낮음 | ⚠️ 중간 |

---

### ⚡ 실행 결과 시각화

#### **Scenario: Trunk-Based 하루**

**오전 9시:**
```
trunk  ─●─
        ↓
    (현재 상태)
```

**오전 10시: 개발자 A**
```bash
git switch -c fix-bug
# ... 수정 ...
git push
gh pr create
gh pr merge --squash
```

```
trunk  ─●─●─
          ↑
       fix-bug
```

**오전 11시: 개발자 B**
```bash
git switch -c add-feature
# ... 개발 ...
git push
gh pr create
gh pr merge --squash
```

```
trunk  ─●─●─●─
            ↑
        add-feature
```

**오후 2시: 개발자 C**
```bash
# 직접 trunk에
git commit -m "refactor: Clean up"
git push
```

```
trunk  ─●─●─●─●─
              ↑
          (직접 커밋)
```

**오후 5시:**
```
trunk  ─●─●─●─●─●─●─
        ↑
    오늘 6개 커밋!
    각 커밋마다 배포됨
```

---

#### **Scenario: 장기 Feature with Toggles**

```
Day 1:
trunk  ─●─ (toggle 시스템 추가)

Day 2-10:
trunk  ─●─●─●─●─●─●─
        ↑ ↑ ↑ ↑ ↑ ↑
        새 기능 개발 (Toggle OFF)
        사용자는 영향 없음!

Day 11:
trunk  ─●─ (Toggle ON, 5% 사용자)

Day 12:
trunk  ─●─ (25% 사용자)

Day 13:
trunk  ─●─ (100% 사용자)

Day 14:
trunk  ─●─ (Toggle 제거)
```

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: Trunk-Based 도입

**목표:** 기존 팀을 Trunk-Based로 전환

#### **Problem: GitHub Flow에서 Trunk-Based로!**

```bash
# 현재 상태
# - GitHub Flow 사용 중
# - 브랜치 3-7일 수명
# - 하루 2-3번 배포
# - 더 빠른 배포 원함

# 목표
# - 브랜치 1-2일로 단축
# - 하루 10번+ 배포
# - 충돌 최소화
```

#### **Solution: 점진적 전환**

```bash
# ===== Phase 1: 준비 (Week 1) =====

# 1. Feature Toggle 시스템 구축
cat > config/featureToggles.js << 'EOF'
class FeatureToggles {
  constructor() {
    this.config = require('./toggles.json');
  }
  
  isEnabled(toggleName, userId = null) {
    const toggle = this.config[toggleName];
    if (!toggle) return false;
    
    // 환경 변수 체크
    if (process.env[toggleName.toUpperCase()] === 'true') {
      return true;
    }
    
    // 사용자 기반 롤아웃
    if (toggle.rollout && userId) {
      return this.isInRollout(userId, toggle.rollout);
    }
    
    return toggle.enabled;
  }
  
  isInRollout(userId, percentage) {
    const hash = this.hashUserId(userId);
    return hash % 100 < percentage;
  }
}

module.exports = new FeatureToggles();
EOF

cat > config/toggles.json << 'EOF'
{
  "newPaymentFlow": {
    "enabled": false,
    "rollout": 0,
    "description": "New payment processing flow"
  },
  "betaFeatures": {
    "enabled": false,
    "rollout": 0,
    "description": "Beta features for testing"
  }
}
EOF

git add config/
git commit -m "feat: Add feature toggle system"
git push origin main

# 2. CI/CD 최적화
cat > .github/workflows/ci.yml << 'EOF'
name: CI/CD
on:
  push:
    branches: [main]
  pull_request:

jobs:
  test:
    runs-on: ubuntu-latest
    timeout-minutes: 10  # 빠른 피드백!
    steps:
      - uses: actions/checkout@v2
      - uses: actions/cache@v2  # 캐싱으로 속도 향상
        with:
          path: node_modules
          key: ${{ runner.os }}-node-${{ hashFiles('package-lock.json') }}
      - run: npm ci
      - run: npm run test:unit -- --maxWorkers=4  # 병렬
      - run: npm run test:integration

  deploy:
    needs: test
    if: github.ref == 'refs/heads/main'
    runs-on: ubuntu-latest
    steps:
      - run: npm run deploy
EOF

# 3. 팀 교육
# - Trunk-Based 개념
# - Feature Toggles 사용법
# - 작은 커밋 연습

# ===== Phase 2: 시범 운영 (Week 2-3) =====

# 규칙 설정
cat > DEVELOPMENT.md << 'EOF'
# Development Guidelines

## Branch Lifetime
- Maximum: 2 days
- Ideal: Same day
- Large features: Use feature toggles

## Commit Frequency
- Minimum: Once per day
- Ideal: Multiple times per day
- Push often!

## PR Size
- Maximum: 200 lines
- Ideal: 100 lines
- Break down large changes

## Code Review
- Target: 2 hours
- Maximum: 1 business day
- Focus on critical issues only

## Feature Toggles
- Use for features > 1 day
- Document in toggles.json
- Remove after full rollout
EOF

# 첫 Toggle 기반 개발
git switch -c new-dashboard

cat > components/Dashboard.js << 'EOF'
const toggles = require('../config/featureToggles');

function Dashboard({ user }) {
  if (toggles.isEnabled('newDashboard', user.id)) {
    return <NewDashboard user={user} />;
  }
  return <OldDashboard user={user} />;
}
EOF

git add components/Dashboard.js
git commit -m "feat: Add new dashboard (behind toggle)"
git push origin new-dashboard

# PR 생성 및 빠른 merge
gh pr create --title "New dashboard (toggle)"
# 리뷰 (2시간)
gh pr merge --squash

# ✅ 미완성이지만 안전하게 main에!

# ===== Phase 3: 점진적 확대 (Week 4-6) =====

# 브랜치 수명 단축 모니터링
git log --all --oneline --since="1 week ago" \
  | grep -E "Merge|feat|fix" \
  | wc -l
# 목표: 주당 50+ 커밋

# Toggle 활성화 (점진적)
# Week 4: 5% 사용자
# Week 5: 25% 사용자
# Week 6: 100% 사용자

cat > config/toggles.json << 'EOF'
{
  "newDashboard": {
    "enabled": true,
    "rollout": 100,
    "description": "New dashboard - fully rolled out"
  }
}
EOF

# ===== Phase 4: 최적화 (Week 7-8) =====

# 메트릭 수집
cat > scripts/metrics.sh << 'EOF'
#!/bin/bash

echo "=== Trunk-Based Metrics ==="

# 배포 빈도
echo "Deploys today:"
git log --since="1 day ago" --oneline --first-parent main | wc -l

# Lead Time (커밋→배포)
echo "Average lead time: ~20 minutes"

# 브랜치 수명
echo "Active branches:"
git branch -r | grep -v "main" | wc -l

# 평균 PR 크기
echo "Average PR size:"
gh pr list --state closed --limit 10 --json additions,deletions \
  | jq '.[] | .additions + .deletions' \
  | awk '{sum+=$1} END {print sum/NR " lines"}'
EOF

chmod +x scripts/metrics.sh

# ===== 결과 =====
# Before (GitHub Flow):
# - 브랜치 수명: 3-7일
# - 하루 배포: 2-3번
# - 충돌: 주 2-3회

# After (Trunk-Based):
# - 브랜치 수명: 1-2일
# - 하루 배포: 10-15번
# - 충돌: 주 0-1회

# ✅ 성공적 전환!
```

**전환 체크리스트:**
```
□ Feature Toggle 시스템
□ CI/CD 최적화 (< 10분)
□ 테스트 커버리지 (80%+)
□ 팀 교육
□ 가이드라인 문서화
□ 시범 운영
□ 메트릭 수집
□ 점진적 확대
□ 지속적 개선
```

---

### 💼 Scenario 2: 대규모 기능 개발

**목표:** 2주 걸리는 기능을 Trunk-Based로

#### **Problem: 큰 기능을 어떻게 작게 나누나?**

```bash
# 요구사항
# - 새로운 주문 시스템 (2주 소요)
# - 여러 컴포넌트 연관
# - trunk에 매일 통합해야 함!
```

#### **Solution: 수평 분할 + Toggle**

```bash
# ===== 분석: 기능 분해 =====

# 큰 기능:
# "새로운 주문 시스템"

# 수평 분할 (레이어별):
# 1. 데이터 모델
# 2. API 엔드포인트
# 3. 비즈니스 로직
# 4. UI 컴포넌트
# 5. 통합

# ===== Day 1: Feature Toggle 설정 =====

cat > config/toggles.json << 'EOF'
{
  "newOrderSystem": {
    "enabled": false,
    "rollout": 0,
    "description": "New order processing system"
  }
}
EOF

git add config/toggles.json
git commit -m "feat(orders): Add feature toggle for new system"
git push origin trunk

# ===== Day 2-3: 데이터 모델 =====

cat > models/Order.js << 'EOF'
class Order {
  constructor(data) {
    this.id = data.id;
    this.items = data.items;
    this.total = data.total;
    // New fields for new system
    this.processingStatus = data.processingStatus;
    this.metadata = data.metadata;
  }
  
  // New methods (won't be called yet)
  async processOrder() {
    // New processing logic
  }
}

module.exports = Order;
EOF

git add models/Order.js
git commit -m "feat(orders): Add new order model

Add new fields and methods for new order system.
Behind feature toggle - not used yet.

Part 1/5"
git push origin trunk

# ✅ trunk에 merge!
# ✅ 아무 영향 없음 (아직 안 쓰임)

# ===== Day 4-5: API 엔드포인트 =====

cat > routes/orders.js << 'EOF'
const toggles = require('../config/featureToggles');
const OldOrderService = require('../services/OldOrderService');
const NewOrderService = require('../services/NewOrderService');

router.post('/orders', async (req, res) => {
  const service = toggles.isEnabled('newOrderSystem')
    ? NewOrderService
    : OldOrderService;
    
  const order = await service.createOrder(req.body);
  res.json(order);
});
EOF

cat > services/NewOrderService.js << 'EOF'
class NewOrderService {
  async createOrder(data) {
    // New order creation logic
    const order = new Order(data);
    await order.processOrder();
    return order;
  }
}
EOF

git add routes/orders.js services/NewOrderService.js
git commit -m "feat(orders): Add new order API endpoint

Add new order service and endpoint.
Routed based on feature toggle.

Part 2/5"
git push origin trunk

# ===== Day 6-8: 비즈니스 로직 =====

cat > services/OrderProcessor.js << 'EOF'
class OrderProcessor {
  async process(order) {
    // Validate
    await this.validate(order);
    
    // Calculate
    await this.calculateTotals(order);
    
    // Apply discounts
    await this.applyDiscounts(order);
    
    // Finalize
    await this.finalize(order);
    
    return order;
  }
  
  // ... methods ...
}
EOF

git add services/OrderProcessor.js
git commit -m "feat(orders): Add order processing logic

Implement core order processing logic:
- Validation
- Calculation
- Discounts
- Finalization

Part 3/5"
git push origin trunk

# ===== Day 9-11: UI 컴포넌트 =====

cat > components/OrderForm.js << 'EOF'
const toggles = require('../config/featureToggles');

function OrderForm() {
  const useNewSystem = toggles.isEnabled('newOrderSystem');
  
  if (useNewSystem) {
    return <NewOrderForm />;
  }
  return <OldOrderForm />;
}
EOF

git add components/
git commit -m "feat(orders): Add new order UI components

Add new order form and components.
Toggle-based routing.

Part 4/5"
git push origin trunk

# ===== Day 12-13: 통합 테스트 =====

cat > tests/integration/orders.test.js << 'EOF'
describe('New Order System', () => {
  beforeEach(() => {
    process.env.NEW_ORDER_SYSTEM = 'true';
  });
  
  it('should process order correctly', async () => {
    const order = await createOrder(testData);
    expect(order.processingStatus).toBe('completed');
  });
  
  // More tests...
});
EOF

git add tests/
git commit -m "test(orders): Add integration tests

Add comprehensive tests for new order system.

Part 5/5 - Ready for rollout!"
git push origin trunk

# ===== Day 14: 점진적 롤아웃 =====

# 내부 테스트
cat > config/toggles.json << 'EOF'
{
  "newOrderSystem": {
    "enabled": true,
    "rollout": 0,  # 개발팀만
    "description": "New order processing system"
  }
}
EOF

# 문제 없으면 5% → 25% → 50% → 100%

# ===== Week 3: Toggle 제거 =====

# 완전 안정화 후
git switch -c cleanup-order-toggle

# Old system 제거
rm services/OldOrderService.js
rm components/OldOrderForm.js

# Toggle 코드 제거
cat > routes/orders.js << 'EOF'
router.post('/orders', async (req, res) => {
  const order = await NewOrderService.createOrder(req.body);
  res.json(order);
});
EOF

git add .
git commit -m "refactor(orders): Remove old order system

Remove old order system after successful rollout.
New system now serves 100% of users.

Cleanup toggle and legacy code."
git push origin cleanup-order-toggle

gh pr create --title "Cleanup: Remove old order system"
gh pr merge --squash

# ✅ 완료!
# ✅ 2주 동안 매일 trunk에 통합
# ✅ 안전한 롤아웃
```

**분할 전략:**
```
수평 분할 (레이어):
✅ 데이터 → API → 로직 → UI
✅ 각 레이어 독립 개발
✅ 점진적 통합

수직 분할 (기능):
✅ 핵심 기능 먼저
✅ 부가 기능 나중
✅ MVP 접근

Dark Launching:
✅ 코드는 배포
✅ 사용자 영향 없음
✅ Toggle로 제어
```

---

### 💼 Scenario 3: 빠른 실험과 A/B 테스트

**목표:** Feature Toggle로 A/B 테스트

#### **Problem: 새 UI가 나을지 확신이 없다!**

```bash
# 요구사항
# - 체크아웃 UI 2가지 버전
# - A/B 테스트 2주
# - 데이터 기반 결정
```

#### **Solution: Toggle 기반 실험**

```bash
# ===== 1. 실험 Toggle 설정 =====

cat > config/experiments.js << 'EOF'
class ExperimentManager {
  constructor() {
    this.experiments = {
      checkoutUIv2: {
        name: 'Checkout UI V2',
        rollout: 50,  // 50% A, 50% B
        metrics: ['conversion_rate', 'cart_abandonment']
      }
    };
  }
  
  getVariant(experimentName, userId) {
    const exp = this.experiments[experimentName];
    if (!exp) return 'control';
    
    // Consistent hashing
    const hash = this.hashUserId(userId);
    return hash % 100 < exp.rollout ? 'treatment' : 'control';
  }
  
  trackMetric(experimentName, variant, metric, value) {
    // Analytics 전송
    analytics.track({
      experiment: experimentName,
      variant: variant,
      metric: metric,
      value: value
    });
  }
}

module.exports = new ExperimentManager();
EOF

# ===== 2. 두 버전 구현 =====

cat > components/Checkout.js << 'EOF'
const experiments = require('../config/experiments');

function Checkout({ user, cart }) {
  const variant = experiments.getVariant('checkoutUIv2', user.id);
  
  // Track assignment
  experiments.trackMetric('checkoutUIv2', variant, 'assigned', 1);
  
  if (variant === 'treatment') {
    return <CheckoutV2 user={user} cart={cart} />;
  }
  return <CheckoutV1 user={user} cart={cart} />;
}
EOF

# V1: 기존 (여러 페이지)
cat > components/CheckoutV1.js << 'EOF'
function CheckoutV1({ user, cart }) {
  const [step, setStep] = useState(1);
  
  return (
    <div>
      {step === 1 && <ShippingInfo />}
      {step === 2 && <PaymentInfo />}
      {step === 3 && <Review />}
    </div>
  );
}
EOF

# V2: 새 버전 (단일 페이지)
cat > components/CheckoutV2.js << 'EOF'
function CheckoutV2({ user, cart }) {
  return (
    <div className="single-page-checkout">
      <ShippingInfo />
      <PaymentInfo />
      <Review />
    </div>
  );
}
EOF

git add components/
git commit -m "feat(checkout): Add A/B test for UI v2

Implement single-page checkout as experiment.
50/50 split between v1 and v2.

Tracking:
- Conversion rate
- Cart abandonment
- Time to complete"
git push origin trunk

# ===== 3. 메트릭 수집 =====

cat > analytics/checkoutMetrics.js << 'EOF'
const experiments = require('../config/experiments');

function trackCheckoutStart(user, cart, variant) {
  experiments.trackMetric('checkoutUIv2', variant, 'started', 1);
}

function trackCheckoutComplete(user, order, variant, timeSpent) {
  experiments.trackMetric('checkoutUIv2', variant, 'completed', 1);
  experiments.trackMetric('checkoutUIv2', variant, 'time_spent', timeSpent);
}

function trackCheckoutAbandoned(user, cart, variant, step) {
  experiments.trackMetric('checkoutUIv2', variant, 'abandoned', 1);
  experiments.trackMetric('checkoutUIv2', variant, 'abandoned_step', step);
}
EOF

# ===== 4. 2주 후 분석 =====

# 데이터 수집
cat > scripts/analyzeExperiment.js << 'EOF'
const analytics = require('../analytics');

async function analyzeCheckoutExperiment() {
  const results = await analytics.getExperimentResults('checkoutUIv2');
  
  console.log('=== Checkout UI V2 Results ===');
  console.log('Control (V1):');
  console.log(`  Conversion: ${results.control.conversion}%`);
  console.log(`  Abandonment: ${results.control.abandonment}%`);
  console.log(`  Avg Time: ${results.control.avgTime}s`);
  
  console.log('Treatment (V2):');
  console.log(`  Conversion: ${results.treatment.conversion}%`);
  console.log(`  Abandonment: ${results.treatment.abandonment}%`);
  console.log(`  Avg Time: ${results.treatment.avgTime}s`);
  
  console.log('Statistical Significance:');
  console.log(`  p-value: ${results.pValue}`);
  console.log(`  Significant: ${results.pValue < 0.05 ? 'Yes' : 'No'}`);
}
EOF

# 결과 (예시):
# Control (V1):
#   Conversion: 68%
#   Abandonment: 32%
#   Avg Time: 180s
#
# Treatment (V2):
#   Conversion: 75%  ← 7% 증가!
#   Abandonment: 25%  ← 7% 감소!
#   Avg Time: 120s  ← 60s 빠름!
#
# Statistical Significance: Yes (p < 0.001)

# ===== 5. 결정: V2 승리! =====

# 100% 롤아웃
cat > config/experiments.js << 'EOF'
{
  checkoutUIv2: {
    rollout: 100,  // 모든 사용자
    winner: 'treatment'
  }
}
EOF

git commit -m "feat(checkout): Roll out V2 to 100%

Based on A/B test results:
- +7% conversion rate
- -7% abandonment rate
- -60s average time

Statistical significance: p < 0.001"
git push origin trunk

# ===== 6. 정리 (1주 후) =====

# V1 제거
rm components/CheckoutV1.js

cat > components/Checkout.js << 'EOF'
function Checkout({ user, cart }) {
  return <CheckoutV2 user={user} cart={cart} />;
}
EOF

git commit -m "refactor(checkout): Remove V1 after successful rollout"
git push origin trunk

# ✅ 데이터 기반 결정!
# ✅ 안전한 실험
# ✅ 빠른 롤백 가능 (필요 시)
```

**실험 프로세스:**
```
1. 가설 수립
   "단일 페이지 체크아웃이 전환율을 높일 것"

2. 메트릭 정의
   - 전환율
   - 이탈률
   - 완료 시간

3. Toggle 구현
   - A: 기존 (Control)
   - B: 새 버전 (Treatment)

4. 데이터 수집 (2주)
   - 충분한 샘플 크기
   - 통계적 유의성 확인

5. 분석 및 결정
   - 데이터 분석
   - 승자 선택

6. 롤아웃
   - 50% → 100%
   - 모니터링

7. 정리
   - 패배한 버전 제거
   - Toggle 제거
```

---

### 💼 Scenario 4: 대규모 팀 조율

**목표:** 50명 팀의 충돌 최소화

#### **Problem: 개발자가 많으면 충돌도 많다!**

```bash
# 문제
# - 50명 개발자
# - 하루 100+ 커밋
# - 충돌 빈번
# - 통합 지연
```

#### **Solution: 전략적 조율**

```bash
# ===== 전략 1: 코드 오너십 (CODEOWNERS) =====

cat > .github/CODEOWNERS << 'EOF'
# Global owners
* @dev-team

# Frontend
/frontend/**/*.js @frontend-team
/frontend/**/*.css @frontend-team

# Backend
/backend/**/*.java @backend-team
/backend/**/*.sql @database-team

# Infrastructure
/.github/** @devops-team
/docker/** @devops-team
/k8s/** @devops-team

# Mobile
/mobile/ios/** @ios-team
/mobile/android/** @android-team

# Critical paths (need 2 approvals)
/backend/payment/** @backend-team @security-team
/backend/auth/** @backend-team @security-team
EOF

# 장점:
# - 명확한 책임
# - 빠른 리뷰 라우팅
# - 충돌 영역 식별

# ===== 전략 2: 모듈 분리 =====

# 프로젝트 구조
# monorepo/
# ├── packages/
# │   ├── frontend/     ← Frontend 팀
# │   ├── backend-api/  ← Backend 팀
# │   ├── mobile-app/   ← Mobile 팀
# │   └── shared/       ← 공통 (조심!)

# 각 팀은 자기 모듈에서 작업
# → 충돌 최소화

# ===== 전략 3: 작업 조율 =====

cat > scripts/checkConflicts.sh << 'EOF'
#!/bin/bash

# 현재 작업 중인 영역 확인
echo "=== Active Work Areas ==="

# 모든 feature 브랜치 체크
for branch in $(git branch -r | grep -v "trunk"); do
  echo "Branch: $branch"
  
  # 변경된 파일
  git diff trunk..$branch --name-only | head -5
  echo "---"
done

# 겹치는 파일 경고
echo "=== Potential Conflicts ==="
git diff trunk --name-only \
  | sort | uniq -d \
  | while read file; do
    echo "⚠️  Multiple branches touching: $file"
  done
EOF

# 매일 아침 실행
# → 충돌 가능성 사전 파악

# ===== 전략 4: 통합 순서 =====

# 큰 변경 먼저 통합
# 작은 변경은 rebase

# 예시:
# 오전: 대규모 리팩토링 merge
# 오후: 작은 feature들 merge

# ===== 전략 5: 페어/몹 프로그래밍 =====

# 복잡한 영역은 함께 작업
# → 충돌 없음 (같은 코드!)

# 예시:
# 결제 모듈 리팩토링
# → 3명이 함께 한 화면 보며 작업
# → trunk에 직접 커밋

# ===== 전략 6: Feature Freeze Window =====

cat > scripts/freezeWindow.sh << 'EOF'
#!/bin/bash

# 매일 배포 시간 전 30분
# 새로운 merge 중단
# 안정화 시간

hour=$(date +%H)

if [ $hour -eq 14 ] || [ $hour -eq 15 ]; then
  echo "⚠️  Feature Freeze Window"
  echo "No new merges. Stabilization time."
  exit 1
fi
EOF

# 매일 14:00-16:00
# → 배포 준비
# → 충돌 해결
# → 안정화

# ===== 결과 =====

# Before:
# - 하루 평균 충돌: 15회
# - 충돌 해결 시간: 2-3시간
# - 통합 지연: 자주

# After:
# - 하루 평균 충돌: 2-3회
# - 충돌 해결 시간: 10-20분
# - 통합 지연: 거의 없음

# ✅ 50명 팀도 원활하게!
```

**대규모 팀 베스트 프랙티스:**
```
1. 명확한 오너십
   - CODEOWNERS
   - 팀별 영역

2. 모듈화
   - 독립적 모듈
   - 최소 의존성

3. 통신
   - Daily sync
   - 작업 영역 공유

4. 자동화
   - 충돌 감지
   - 자동 merge

5. 전략적 타이밍
   - 큰 변경 우선
   - Freeze window

6. 페어/몹 프로그래밍
   - 복잡한 영역
   - 지식 공유
```

---

### 💼 Scenario 5: 롤백과 복구

**목표:** 문제 발생 시 빠른 대응

#### **Problem: 배포 후 치명적 버그!**

```bash
# 상황
# - 새 기능 배포 10분 전
# - 에러율 급증: 1% → 45%
# - 사용자 불만 쇄도
# - 즉시 롤백 필요!
```

#### **Solution: 3단계 롤백 전략**

```bash
# ===== Level 1: Feature Toggle (최고!) =====

# 1분 만에 롤백
cat > config/toggles.json << 'EOF'
{
  "newFeature": {
    "enabled": false,  # OFF!
    "rollout": 0
  }
}
EOF

git add config/toggles.json
git commit -m "hotfix: Disable new feature due to critical bug"
git push origin trunk

# CI/CD가 즉시 배포
# → 1-2분 내 적용
# → 에러율 정상화

# ✅ 가장 빠르고 안전!

# ===== Level 2: Revert Commit =====

# Toggle이 없는 경우
# 문제 커밋 찾기
git log --oneline -10
# abc123 feat: Add new feature
# def456 fix: Bug fix
# ...

# Revert PR 생성
git switch -c revert-new-feature
git revert abc123 -m "Revert: New feature

Reverting due to critical production bug:
- Error rate: 1% → 45%
- Payment failures
- User complaints

Issue: #1234"

git push origin revert-new-feature

# PR 생성 및 즉시 merge
gh pr create --title "🔥 URGENT: Revert new feature"
gh pr merge --squash

# ✅ 5-10분 내 롤백

# ===== Level 3: 이전 커밋 배포 (긴급) =====

# 가장 극단적인 경우
# 마지막 정상 커밋으로 강제 배포

# 1. 정상 커밋 찾기
git log --oneline
# abc123 (현재, 버그)
# def456 (이전, 정상) ← 여기로!

# 2. 배포 태그 이동
git tag -f production def456
git push origin production --force

# 3. CI/CD가 production 태그 배포
# → 이전 버전으로 롤백

# ⚠️ 주의: 데이터베이스 마이그레이션!
# → 호환성 확인 필수

# ===== 복구 프로세스 =====

# 1. 롤백 완료 확인
# 모니터링:
# - 에러율 정상화? ✅
# - 핵심 기능 동작? ✅
# - 사용자 피드백? ✅

# 2. 근본 원인 분석
cat > postmortem.md << 'EOF'
# Postmortem: Feature X Rollback

## Timeline
14:00 - Deployed new feature
14:10 - Error rate spike detected
14:12 - Rollback initiated (toggle)
14:15 - Rollback completed
14:20 - Service stabilized

## Root Cause
Null pointer exception in edge case:
- User without email address
- Missed in testing

## Action Items
1. Add test case for users without email
2. Add null checks
3. Improve monitoring
4. Update deployment checklist

## Prevention
- Enhanced test coverage
- Better edge case testing
- Staged rollout (not 100% immediately)
EOF

# 3. 수정 및 재배포
git switch -c fix-null-pointer

# 수정
cat > feature.js << 'EOF'
function processUser(user) {
  if (!user || !user.email) {
    throw new Error('Invalid user');
  }
  // ... rest of code
}
EOF

# 테스트 추가
cat > feature.test.js << 'EOF'
test('handles user without email', () => {
  expect(() => processUser({ id: 1 }))
    .toThrow('Invalid user');
});
EOF

git add .
git commit -m "fix: Add null check for user email

Fixes issue that caused rollback.
Added test case to prevent regression.

Related: #1234"
git push origin fix-null-pointer

# PR 및 merge
gh pr create
gh pr merge --squash

# 4. 점진적 재배포
# Toggle로 천천히
# 5% → 25% → 50% → 100%

# ✅ 완료!
```

**롤백 결정 트리:**
```
버그 발견
  ↓
심각도?
  ↓
├─ Critical (P0)
│   ↓
│   Feature Toggle 있음?
│   ↓
│   ├─ Yes → Toggle OFF (1분)
│   └─ No  → Revert (5분)
│
├─ High (P1)
│   ↓
│   Hotfix PR (1시간)
│
└─ Normal (P2)
    ↓
    다음 배포에 수정
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: Toggle 정리 안 함**

```bash
# 위험한 패턴
# Toggle이 계속 쌓임
toggles.json:
{
  "feature1": true,  # 6개월 전
  "feature2": false, # 3개월 전
  "feature3": true,  # 1년 전
  ...
  # 100개 Toggles! ❌
}
```

**올바른 방법:**
```bash
# Toggle 수명 관리
cat > scripts/cleanToggles.sh << 'EOF'
#!/bin/bash

# 90일 이상 된 Toggle 찾기
echo "=== Old Toggles (90+ days) ==="
git log --all --format="%H %ai" \
  -- config/toggles.json \
  | awk '{print $1}' \
  | while read commit; do
    # 분석...
  done

# 제거 후보 리스트
echo "Toggles to remove:"
echo "- feature1 (enabled 6 months)"
echo "- feature2 (never enabled)"
EOF

# 정기적 정리 (월 1회)
```

---

#### ❌ **실수 2: 테스트 없이 trunk 푸시**

```bash
# 위험한 패턴
git add .
git commit -m "Quick fix"
git push origin trunk  # ❌ 테스트 안 함!

# 결과: CI 실패 → 모두 블록됨
```

**올바른 방법:**
```bash
# 로컬 테스트 먼저!
npm test          # ✅
npm run lint      # ✅
npm run build     # ✅

# 통과 후 push
git push origin trunk
```

---

#### ❌ **실수 3: 브랜치가 너무 오래**

```bash
# 위험한 패턴
git switch -c big-feature
# ... 1주일 작업 ...
# trunk는 계속 진행
# 충돌 폭탄! ❌
```

**올바른 방법:**
```bash
# 매일 rebase
git fetch origin
git rebase origin/trunk

# 또는 작게 나누기
# big-feature → part1, part2, part3
```

---

#### ❌ **실수 4: Toggle 기본값 잘못**

```bash
# 위험한 패턴
{
  "newFeature": {
    "enabled": true,  # ❌ 기본값이 true!
    "rollout": 100
  }
}

# 결과: Toggle 없는 것과 같음
```

**올바른 방법:**
```bash
# 항상 기본값 false!
{
  "newFeature": {
    "enabled": false,  # ✅ 안전
    "rollout": 0
  }
}

# 의도적으로 활성화
```

---

### 🎯 실습 미션

#### **미션 1: Feature Toggle 구현** 🚩

**목표:** Toggle 시스템 처음부터 구축

```bash
# 준비
mkdir trunk-based-practice && cd trunk-based-practice
git init
git branch -M trunk

# 미션:
# 1. Feature Toggle 시스템 구축
# 2. 새 기능 Toggle 뒤에 개발
# 3. 점진적 활성화 (0% → 50% → 100%)
# 4. Toggle 제거

# 힌트:
# - toggles.json
# - getVariant(userId)
# - if (toggle) { new } else { old }
```

<details>
<summary>정답 보기</summary>

```bash
# 1. Toggle 시스템
mkdir config
cat > config/featureToggles.js << 'EOF'
class FeatureToggles {
  constructor() {
    this.config = require('./toggles.json');
  }
  
  isEnabled(name, userId = null) {
    const toggle = this.config[name];
    if (!toggle) return false;
    
    if (!toggle.enabled) return false;
    
    if (userId && toggle.rollout < 100) {
      const hash = this.hash(userId) % 100;
      return hash < toggle.rollout;
    }
    
    return true;
  }
  
  hash(str) {
    let hash = 0;
    for (let i = 0; i < str.length; i++) {
      hash = ((hash << 5) - hash) + str.charCodeAt(i);
    }
    return Math.abs(hash);
  }
}

module.exports = new FeatureToggles();
EOF

cat > config/toggles.json << 'EOF'
{
  "newDashboard": {
    "enabled": false,
    "rollout": 0,
    "description": "New dashboard UI"
  }
}
EOF

git add config/
git commit -m "feat: Add feature toggle system"

# 2. Toggle 뒤에 개발
cat > dashboard.js << 'EOF'
const toggles = require('./config/featureToggles');

function renderDashboard(userId) {
  if (toggles.isEnabled('newDashboard', userId)) {
    return 'New Dashboard!';
  }
  return 'Old Dashboard';
}

module.exports = { renderDashboard };
EOF

git add dashboard.js
git commit -m "feat: Add new dashboard (behind toggle)"

# 3. 점진적 활성화
# 50%
cat > config/toggles.json << 'EOF'
{
  "newDashboard": {
    "enabled": true,
    "rollout": 50
  }
}
EOF

git add config/toggles.json
git commit -m "feat: Enable new dashboard for 50%"

# 100%
cat > config/toggles.json << 'EOF'
{
  "newDashboard": {
    "enabled": true,
    "rollout": 100
  }
}
EOF

git add config/toggles.json
git commit -m "feat: Enable new dashboard for 100%"

# 4. Toggle 제거
cat > dashboard.js << 'EOF'
function renderDashboard(userId) {
  return 'New Dashboard!';
}

module.exports = { renderDashboard };
EOF

git add dashboard.js
git commit -m "refactor: Remove dashboard toggle"

# ✅ 완료!
```

</details>

---

#### **미션 2: 초단기 브랜치 실습** ⚡

**목표:** 1일 브랜치 워크플로우

```bash
# 시나리오:
# 오전: 브랜치 생성, 개발
# 점심: trunk 동기화
# 오후: PR, 리뷰, Merge
# 저녁: 정리

# 3개 브랜치 연속으로!
```

<details>
<summary>정답 보기</summary>

```bash
# Branch 1 (오전)
git switch -c fix-typo
echo "Fixed typo" > file1.txt
git add file1.txt
git commit -m "fix: Fix typo in docs"
git push origin fix-typo

gh pr create --title "Fix typo"
gh pr merge --squash

git switch trunk
git pull
git branch -d fix-typo

# Branch 2 (점심)
git switch -c add-feature
echo "New feature" > file2.txt
git add file2.txt
git commit -m "feat: Add feature"
git push origin add-feature

gh pr create --title "Add feature"
gh pr merge --squash

git switch trunk
git pull
git branch -d add-feature

# Branch 3 (오후)
git switch -c refactor-code
echo "Refactored" > file3.txt
git add file3.txt
git commit -m "refactor: Clean up code"
git push origin refactor-code

gh pr create --title "Refactor"
gh pr merge --squash

git switch trunk
git pull
git branch -d refactor-code

# ✅ 하루에 3개 완료!
```

</details>

---

#### **미션 3: 롤백 시뮬레이션** 🔄

**목표:** 문제 발생 → 롤백 → 수정 → 재배포

```bash
# 시나리오:
# 1. 버그 있는 기능 배포
# 2. 문제 발견
# 3. Toggle로 즉시 롤백
# 4. 수정
# 5. 재배포
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 버그 있는 기능
cat > payment.js << 'EOF'
function processPayment(amount) {
  return amount * 2;  // 버그: 2배 청구!
}
EOF

cat > config/toggles.json << 'EOF'
{
  "newPayment": {
    "enabled": true,
    "rollout": 100
  }
}
EOF

git add .
git commit -m "feat: Add new payment (with bug!)"
git push origin trunk

# 2. 문제 발견!
# "사용자가 2배 청구됨!"

# 3. 즉시 롤백 (Toggle)
cat > config/toggles.json << 'EOF'
{
  "newPayment": {
    "enabled": false,
    "rollout": 0
  }
}
EOF

git add config/toggles.json
git commit -m "hotfix: Disable new payment (bug)"
git push origin trunk

# 4. 수정
cat > payment.js << 'EOF'
function processPayment(amount) {
  return amount;  // 수정: 정상 청구
}
EOF

git add payment.js
git commit -m "fix: Correct payment calculation"
git push origin trunk

# 5. 재배포 (점진적)
# 5%
cat > config/toggles.json << 'EOF'
{
  "newPayment": {
    "enabled": true,
    "rollout": 5
  }
}
EOF

git add config/toggles.json
git commit -m "feat: Re-enable payment (5%)"
git push origin trunk

# 모니터링... 문제 없음!

# 100%
cat > config/toggles.json << 'EOF'
{
  "newPayment": {
    "enabled": true,
    "rollout": 100
  }
}
EOF

git add config/toggles.json
git commit -m "feat: Re-enable payment (100%)"
git push origin trunk

# ✅ 안전한 복구!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 직접 Trunk 커밋 ===
git switch trunk
git pull --rebase
# ... 작업 ...
git push

# === 초단기 브랜치 ===
git switch -c quick-feature
# ... 작업 (1-2일) ...
git rebase trunk
git push
gh pr merge --squash

# === Feature Toggle ===
# 개발
if (toggles.isEnabled('feature')) {
  newCode();
} else {
  oldCode();
}

# 활성화
toggles.feature.enabled = true

# === 롤백 ===
# Level 1: Toggle
toggles.feature.enabled = false

# Level 2: Revert
git revert <commit>

# Level 3: Force deploy
git tag -f production <good-commit>
```

---

### 🎨 메트릭 대시보드

```bash
# 핵심 메트릭
✅ 배포 빈도: 10-100회/일
✅ Lead Time: < 1시간
✅ MTTR: < 30분
✅ Change Failure Rate: < 15%

# 브랜치 메트릭
✅ 평균 수명: 1-2일
✅ 활성 브랜치: < 10개
✅ 평균 PR 크기: < 200줄

# Toggle 메트릭
✅ 활성 Toggles: < 20개
✅ 평균 수명: < 60일
✅ 제거율: > 80%
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [2.3 GitHub Flow](./03-github-flow.md)

**다음 학습:**
- [2.5 Release 관리](./05-release-management.md)

**관련 주제:**
- [3.1 Merge 타입](../03-merge/01-merge-types.md)
- [4.1 Rebase](../04-rebase/01-rebase-basics.md)

---

<div align="center">

**🎉 축하합니다! Trunk-Based Development를 완전히 마스터했습니다!**

이제 가장 빠른 개발 속도를 낼 수 있습니다!

[⬅️ 이전: GitHub Flow](./03-github-flow.md) | [README로](../README.md) | [다음: Release 관리 ➡️](./05-release-management.md)

</div>
