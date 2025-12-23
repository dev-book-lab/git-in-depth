# 3.3 복잡한 충돌 (Complex Conflicts)

**난이도: ⭐⭐⭐**

> *"복잡한 충돌도 원리를 알면 단순합니다. 하나씩 정복하면 됩니다"*

---

## 📑 목차

- [STEP 1: 내부 원리 이해하기](#step-1-내부-원리-이해하기-)
- [STEP 2: 명령어 완전 정복](#step-2-명령어-완전-정복-)
- [STEP 3: 실전 시나리오 마스터](#step-3-실전-시나리오-마스터-)
- [BONUS: 빠른 참조](#bonus-빠른-참조-)

---

## STEP 1: 내부 원리 이해하기 🧠

### 🎯 왜 복잡한 충돌을 알아야 하나?

**실제 개발 시나리오:**
```
상황: 대규모 프로젝트 Merge

git merge feature
# CONFLICT (modify/delete): image.png
# CONFLICT (binary): logo.svg
# CONFLICT (submodule): lib/auth
# CONFLICT (rename/rename): config.js

개발자: "이게 뭐야? 😱"
주니어: "바이너리 충돌은 어떻게...?"
시니어: "서브모듈 충돌 처음 봐요"
팀장: "이거 해결 가능한가요?"

공통 문제:
→ 텍스트 충돌만 알고 있음
→ 특수 케이스 경험 없음
→ 해결 방법을 몰라서 당황
```

**복잡한 충돌을 이해하면:**
```bash
바이너리 충돌:
# CONFLICT: image.png
해결: 하나 선택 또는 새 파일
git checkout --ours image.png ✅

삭제된 파일:
# CONFLICT (modify/delete)
해결: 삭제 유지 또는 수정 채택
git rm old.js 또는 git add old.js ✅

서브모듈:
# CONFLICT (submodule)
해결: 버전 선택
git add lib/auth ✅

✅ 당황하지 않음
✅ 체계적 해결
✅ 모든 충돌 대응
```

> 💡 **핵심:** 복잡해 보이지만 원리는 같습니다!
> 하나씩 이해하면 모두 해결할 수 있습니다!

---

### 📌 복잡한 충돌의 5가지 타입

```
┌─────────────────────────────────────────────────┐
│          복잡한 충돌 타입                           │
├─────────────────────────────────────────────────┤
│                                                 │
│  1. 바이너리 충돌                                  │
│     - 이미지, 비디오, 압축 파일                      │
│     - Diff 불가, 선택만 가능                        │
│                                                 │
│  2. 삭제 충돌                                     │
│     - 한쪽 삭제, 한쪽 수정                          │
│     - Modify/Delete                             │
│                                                 │
│  3. 이름 변경 충돌                                  │
│     - Rename/Rename                              │
│     - Rename/Delete                              │
│                                                  │
│  4. 서브모듈 충돌                                   │
│     - 서브모듈 버전 충돌                             │
│     - 포인터 충돌                                   │
│                                                  │
│  5. 모드 충돌                                      │
│     - 실행 권한 충돌                                │
│     - Symlink vs 일반 파일                         │
│                                                  │
└──────────────────────────────────────────────────┘
```

---

### 🖼️ 바이너리 충돌

#### **정의**

```
바이너리 파일:
- 이미지 (.png, .jpg, .gif)
- 비디오 (.mp4, .avi)
- 오디오 (.mp3, .wav)
- 문서 (.pdf, .docx)
- 압축 (.zip, .tar.gz)
- 실행 파일 (.exe, .so)

특징:
- Diff 불가 (텍스트 아님)
- Merge 불가
- 선택만 가능
```

---

#### **발생 원인**

```
Base: logo.png (v1)
Ours: logo.png (v2 - 색상 변경)
Theirs: logo.png (v3 - 크기 변경)

Git:
"둘 다 바이너리 파일이고
 자동 merge 불가능!
 어느 버전을 선택할까요?"

# CONFLICT (content): Merge conflict in logo.png
```

---

#### **Git의 저장 방식**

```bash
# 3개 버전 모두 저장
git ls-files -s logo.png

# 100644 abc123... 1    logo.png  (Base)
# 100644 def456... 2    logo.png  (Ours)
# 100644 ghi789... 3    logo.png  (Theirs)

# 추출 가능
git show :1:logo.png > logo.base.png
git show :2:logo.png > logo.ours.png
git show :3:logo.png > logo.theirs.png
```

---

#### **해결 방법**

```
방법 1: Ours 선택
git checkout --ours logo.png
git add logo.png

방법 2: Theirs 선택
git checkout --theirs logo.png
git add logo.png

방법 3: 새 파일
# 둘 다 추출
git show :2:logo.png > logo.ours.png
git show :3:logo.png > logo.theirs.png

# 외부 도구로 결합 (Photoshop 등)
# 새 파일 생성

cp final-logo.png logo.png
git add logo.png

방법 4: 둘 다 유지
mv logo.png logo-v2.png
git show :3:logo.png > logo-v3.png
git add logo-v2.png logo-v3.png
git rm logo.png
```

---

### 🗑️ 삭제 충돌 (Modify/Delete)

#### **정의**

```
Modify/Delete Conflict:
한쪽은 파일을 수정
한쪽은 파일을 삭제

Base: file.txt 존재
Ours: file.txt 삭제
Theirs: file.txt 수정

Git:
"삭제할까요? 수정을 유지할까요?"
```

---

#### **발생 시나리오**

```
시나리오 1: 마이그레이션
Main: old-api.js 삭제 (새 API로 교체)
Feature: old-api.js 개선 (버그 수정)

시나리오 2: 리팩토링
Main: utils.js 삭제 (여러 파일로 분산)
Feature: utils.js 수정 (새 함수 추가)

시나리오 3: 정리
Main: deprecated.js 삭제
Feature: deprecated.js 사용 (아직 몰랐음)
```

---

#### **Git 메시지**

```bash
git merge feature

# CONFLICT (modify/delete): old-api.js deleted in HEAD
# and modified in feature. Version feature of old-api.js
# left in tree.

상태:
git status
# deleted by us:   old-api.js

또는

# CONFLICT (modify/delete): config.js deleted in feature
# and modified in HEAD. Version HEAD of config.js
# left in tree.

상태:
# deleted by them:   config.js
```

---

#### **해결 방법**

```
방법 1: 삭제 유지
git rm old-api.js
git commit

방법 2: 수정 유지
git add old-api.js
git commit

방법 3: 이식 후 삭제
# 수정 내용을 새 파일로
git show :3:old-api.js > temp.js
# 새 파일에 적용
vim new-api.js  # temp.js 내용 이식
git add new-api.js
git rm old-api.js
git commit
```

---

### 🔄 이름 변경 충돌

#### **1️⃣ Rename/Rename**

```
같은 파일을 다른 이름으로 변경

Base: api.js
Ours: api-v1.js
Theirs: api.prod.js

# CONFLICT (rename/rename):
# Rename api.js -> api-v1.js in HEAD
# Rename api.js -> api.prod.js in feature
```

**해결:**
```bash
# 하나 선택
git add api-v1.js
git rm api.prod.js

# 또는 둘 다
git add api-v1.js api.prod.js

# 또는 새 이름
git mv api-v1.js api.config.js
git rm api.prod.js
git add api.config.js
```

---

#### **2️⃣ Rename/Delete**

```
한쪽은 이름 변경, 한쪽은 삭제

Base: old.js
Ours: new.js (이름 변경)
Theirs: (삭제)

# CONFLICT (rename/delete):
# old.js deleted in feature and renamed in HEAD.
```

**해결:**
```bash
# 이름 변경 유지
git add new.js

# 또는 삭제 유지
git rm new.js
```

---

#### **3️⃣ Rename/Modify**

```
한쪽은 이름 변경, 한쪽은 수정

Base: old.js
Ours: new.js (이름 변경)
Theirs: old.js (수정)

Git이 자동 추적:
- 내용 유사도 > 50%
- 자동 merge 시도
```

**결과:**
```bash
# 성공:
# Renamed old.js -> new.js
# 수정사항 자동 적용

# 실패:
# CONFLICT in new.js
# 일반 충돌로 해결
```

---

### 📦 서브모듈 충돌

#### **정의**

```
서브모듈:
다른 Git 저장소를 포함

예시:
main-project/
  .git/
  .gitmodules
  lib/
    auth/  ← 서브모듈
      .git/

서브모듈 충돌:
다른 커밋을 가리킴
```

---

#### **발생 원인**

```
Base: lib/auth → commit abc123
Ours: lib/auth → commit def456
Theirs: lib/auth → commit ghi789

# CONFLICT (submodule): Merge conflict in lib/auth
```

---

#### **충돌 확인**

```bash
git status
# both modified:   lib/auth

cat .git/modules/lib/auth/HEAD
# def456 (Ours)

cd lib/auth
git log --oneline
# ghi789 (Theirs의 커밋도 있음)
```

---

#### **해결 방법**

```bash
# 방법 1: Ours 선택
git checkout --ours lib/auth
git add lib/auth

# 방법 2: Theirs 선택
git checkout --theirs lib/auth
git add lib/auth

# 방법 3: 수동 선택
cd lib/auth
git checkout def456  # 또는 ghi789
cd ../..
git add lib/auth

# 방법 4: 최신 버전
cd lib/auth
git pull origin main
cd ../..
git add lib/auth
```

---

### ⚙️ 모드 충돌

#### **1️⃣ 실행 권한 충돌**

```
Base: script.sh (644, 일반)
Ours: script.sh (755, 실행 가능)
Theirs: script.sh (644, 일반)

# CONFLICT (mode): script.sh
```

**해결:**
```bash
# 실행 권한 부여
chmod +x script.sh
git add script.sh

# 또는 제거
chmod -x script.sh
git add script.sh
```

---

#### **2️⃣ Symlink vs 파일**

```
Base: config (파일)
Ours: config (파일)
Theirs: config (symlink → prod.config)

# CONFLICT (symlink/file): config
```

**해결:**
```bash
# 파일 유지
git checkout --ours config
git add config

# Symlink 유지
git checkout --theirs config
git add config
```

---

### 🔬 3-way Diff 복잡한 케이스

#### **연속된 충돌**

```javascript
<<<<<<< HEAD
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
||||||| Base
function add(a, b) {
  return a + b;
}
=======
function add(x, y) {
  return x + y;
}

function multiply(x, y) {
>>>>>>> feature
  return a - b;
}

해석:
Base: add만
Ours: add + subtract 추가
Theirs: add (파라미터 변경) + multiply 추가

해결:
function add(x, y) {
  return x + y;
}

function subtract(a, b) {
  return a - b;
}

function multiply(x, y) {
  return x * y;
}
```

---

#### **중첩된 충돌**

```javascript
<<<<<<< HEAD
class User {
  constructor(name) {
<<<<<<< HEAD
    this.name = name;
=======
    this.username = name;
>>>>>>> feature-1
  }
=======
class Person {
  constructor(name) {
    this.name = name;
  }
>>>>>>> feature-2
}

문제:
- 2개 merge가 겹침
- feature-1과 feature-2 동시

해결:
# 하나씩 해결
git merge --abort

# feature-1 먼저
git merge feature-1
# 해결

# feature-2 나중
git merge feature-2
# 해결
```

---

## STEP 2: 명령어 완전 정복 💻

### 🔧 바이너리 충돌 해결

#### **버전 추출**

```bash
# 모든 버전 추출
git show :1:image.png > image.base.png
git show :2:image.png > image.ours.png
git show :3:image.png > image.theirs.png

# 메타데이터 확인
file image.*.png
# image.base.png: PNG image data, 100x100
# image.ours.png: PNG image data, 200x200
# image.theirs.png: PNG image data, 150x150

# 크기 확인
ls -lh image.*.png
```

---

#### **선택 및 해결**

```bash
# Ours 선택
git checkout --ours image.png
git add image.png

# Theirs 선택
git checkout --theirs image.png
git add image.png

# 새 파일 사용
cp new-image.png image.png
git add image.png

# 확인
git status
# Changes to be committed:
#   modified: image.png
```

---

### 🗑️ 삭제 충돌 해결

#### **상황 확인**

```bash
git status
# deleted by us:   old.js

# 파일 존재 여부
ls old.js
# old.js (여전히 존재, Theirs 버전)

# 내용 확인
cat old.js
# Theirs의 수정 내용
```

---

#### **해결 선택**

```bash
# 삭제 유지 (Ours)
git rm old.js

# 수정 유지 (Theirs)
git add old.js

# 상태 확인
git status
# Changes to be committed:
#   deleted: old.js
# 또는
#   modified: old.js
```

---

#### **이식 후 삭제**

```bash
# Theirs 버전 저장
cp old.js old.js.backup

# 새 파일에 반영
vim new.js
# old.js.backup의 수정사항 이식

# 삭제 완료
git rm old.js
git add new.js

git commit -m "Merge: Migrated changes from old.js to new.js"
```

---

### 🔄 이름 변경 충돌 해결

#### **Rename/Rename**

```bash
git status
# both modified:   api-v1.js
# both modified:   api.prod.js

# 내용 비교
diff api-v1.js api.prod.js

# 하나 선택
git add api-v1.js
git rm api.prod.js

# 또는 둘 다
git add api-v1.js api.prod.js

# Commit
git commit
```

---

#### **Rename/Delete**

```bash
git status
# renamed: old.js -> new.js
# deleted by them: old.js

# 이름 변경 유지
git add new.js

# 또는 삭제 유지
git rm new.js

# Commit
git commit
```

---

### 📦 서브모듈 충돌 해결

#### **상태 확인**

```bash
git status
# both modified:   lib/auth

# 서브모듈 상태
git submodule status
# +def456 lib/auth (Ours)
# +ghi789 lib/auth (Theirs)

# 충돌 확인
cat .gitmodules
# [submodule "lib/auth"]
#   path = lib/auth
#   url = https://github.com/user/auth.git
```

---

#### **버전 선택**

```bash
# Ours 선택
git checkout --ours lib/auth
git add lib/auth

# Theirs 선택
git checkout --theirs lib/auth
git add lib/auth

# 수동 선택
cd lib/auth
git fetch
git log --oneline --graph --all
# 원하는 커밋 선택
git checkout abc123
cd ../..
git add lib/auth

# 최신 버전
cd lib/auth
git pull origin main
cd ../..
git add lib/auth
```

---

#### **서브모듈 업데이트**

```bash
# 모든 서브모듈 업데이트
git submodule update --remote --merge

# 특정 서브모듈
git submodule update --remote --merge lib/auth

# 상태 확인
git submodule status
# abc123 lib/auth (heads/main)
```

---

### ⚙️ 모드 충돌 해결

#### **실행 권한**

```bash
# 현재 권한 확인
ls -l script.sh
# -rw-r--r--  (644)

# 실행 권한 부여
chmod +x script.sh
ls -l script.sh
# -rwxr-xr-x  (755)

git add script.sh

# 또는 제거
chmod -x script.sh
git add script.sh
```

---

#### **Symlink 충돌**

```bash
# Symlink 확인
ls -l config
# lrwxrwxrwx config -> prod.config

# 파일로 변경
rm config
cp prod.config config
git add config

# 또는 Symlink 유지
git checkout --theirs config
git add config
```

---

### 📊 명령어 비교표

#### **충돌 타입별 해결**

| 충돌 타입 | 명령어 | 추가 작업 |
|:---------|:------|:---------|
| **바이너리** | `--ours/--theirs` | 외부 도구 결합 가능 |
| **Modify/Delete** | `git rm` 또는 `git add` | 이식 고려 |
| **Rename/Rename** | `git add` + `git rm` | 내용 비교 |
| **서브모듈** | `--ours/--theirs` | 버전 확인 필수 |
| **모드** | `chmod` + `git add` | 권한 정책 확인 |

---

#### **버전 추출**

| 대상 | 명령어 | 출력 |
|:-----|:------|:-----|
| **Base** | `git show :1:file` | 공통 조상 |
| **Ours** | `git show :2:file` | 현재 브랜치 |
| **Theirs** | `git show :3:file` | Merge 브랜치 |
| **파일로 저장** | `> file.version` | 외부 비교 |

---

## STEP 3: 실전 시나리오 마스터 🔥

### 💼 Scenario 1: 이미지 파일 충돌

**목표:** 바이너리 충돌 완벽 해결

#### **Problem: 디자이너들이 같은 로고를 수정!**

```bash
git merge design-team
# CONFLICT (content): Merge conflict in logo.png
# CONFLICT (content): Merge conflict in banner.jpg
```

#### **Solution: 시각적 비교 후 선택**

```bash
# ===== Step 1: 상황 파악 =====

git status
# both modified:   logo.png
# both modified:   banner.jpg

# ===== Step 2: 버전 추출 =====

# 모든 버전 추출
mkdir conflict-review

git show :1:logo.png > conflict-review/logo.base.png
git show :2:logo.png > conflict-review/logo.ours.png
git show :3:logo.png > conflict-review/logo.theirs.png

git show :1:banner.jpg > conflict-review/banner.base.jpg
git show :2:banner.jpg > conflict-review/banner.ours.jpg
git show :3:banner.jpg > conflict-review/banner.theirs.jpg

# ===== Step 3: 메타데이터 확인 =====

# 파일 정보
file conflict-review/logo.*.png
# logo.base.png: PNG, 200x200, 8-bit/color
# logo.ours.png: PNG, 300x300, 8-bit/color (확대)
# logo.theirs.png: PNG, 200x200, 8-bit/color (색상 변경)

# 크기 확인
ls -lh conflict-review/logo.*.png
# logo.base.png: 45K
# logo.ours.png: 92K (크기 증가)
# logo.theirs.png: 48K (거의 같음)

# ===== Step 4: 시각적 비교 =====

# 이미지 뷰어로 열기
open conflict-review/logo.*.png

# 또는 비교 도구
code --diff conflict-review/logo.ours.png \
            conflict-review/logo.theirs.png

# 관찰:
# Ours: 크기 확대 (300x300)
# Theirs: 색상 변경 (파란색 → 초록색)

# ===== Step 5: 결정 =====

# 옵션 1: Ours (크기)
git checkout --ours logo.png
git add logo.png

# 옵션 2: Theirs (색상)
git checkout --theirs logo.png
git add logo.png

# 옵션 3: 둘 다 반영 (최선!)
# 외부 도구 사용
# Photoshop/GIMP:
# 1. Theirs 열기 (새 색상)
# 2. 300x300으로 확대
# 3. 새 파일 저장

cp final-logo.png logo.png
git add logo.png

# ===== Step 6: Banner 해결 =====

# 간단한 경우
git checkout --theirs banner.jpg
git add banner.jpg

# ===== Step 7: Commit =====

git commit -m "Merge design: Resolved logo and banner conflicts

logo.png:
- Applied new green color (from design-team)
- Maintained size increase to 300x300 (from main)
- Combined both improvements

banner.jpg:
- Used design-team version (updated image)

Reviewed-by: Design Lead"

# ===== Step 8: 정리 =====

rm -rf conflict-review

git log --oneline -3
# abc123 Merge design: Resolved conflicts
# def456 Update logo size
# ghi789 Update logo color

# ✅ 완벽한 해결!

# ===== 배운 점 =====
# 1. 바이너리는 추출 후 비교
# 2. 메타데이터 확인 중요
# 3. 외부 도구로 결합 가능
# 4. 시각적 확인 필수
```

---

### 💼 Scenario 2: 대규모 리팩토링 충돌

**목표:** Modify/Delete 대량 처리

#### **Problem: 리팩토링으로 30개 파일 삭제/수정!**

```bash
git merge refactor
# CONFLICT (modify/delete): utils/helper1.js
# CONFLICT (modify/delete): utils/helper2.js
# ... (30개 파일)
```

#### **Solution: 체계적 분류 및 처리**

```bash
# ===== Step 1: 충돌 목록 =====

git status | grep "deleted by us" > deleted-conflicts.txt
cat deleted-conflicts.txt
# deleted by us:   utils/helper1.js
# deleted by us:   utils/helper2.js
# ... (30개)

# ===== Step 2: 컨텍스트 조사 =====

# Main의 변경사항
git log --oneline main --since="1 week ago"
# abc123 refactor: Split utils into modules
# def456 refactor: Remove deprecated helpers

# Feature의 변경사항
git log --oneline feature --since="1 week ago"
# ghi789 feat: Add error handling to helpers
# jkl012 feat: Improve helper performance

# 이해:
# Main: utils 분산 (새 구조)
# Feature: utils 개선 (기능 추가)

# ===== Step 3: 분류 =====

# 각 파일 확인
for file in $(cat deleted-conflicts.txt | awk '{print $4}'); do
  echo "=== $file ==="
  
  # Theirs 버전 확인
  git show :3:$file | head -20
  
  echo "Delete? Keep? Migrate? [d/k/m]"
  read answer
  
  case $answer in
    d) echo $file >> to-delete.txt ;;
    k) echo $file >> to-keep.txt ;;
    m) echo $file >> to-migrate.txt ;;
  esac
done

# 결과:
# to-delete.txt: 10개 (더 이상 불필요)
# to-keep.txt: 5개 (여전히 사용)
# to-migrate.txt: 15개 (새 구조로 이식)

# ===== Step 4: 삭제 =====

# 삭제할 파일들
cat to-delete.txt | xargs git rm

# ===== Step 5: 유지 =====

# 유지할 파일들
cat to-keep.txt | xargs git add

# ===== Step 6: 이식 =====

# 이식할 파일들
while read file; do
  echo "Migrating $file..."
  
  # 새 위치 결정
  new_file=$(echo $file | sed 's|utils/|modules/|')
  
  # 디렉토리 생성
  mkdir -p $(dirname $new_file)
  
  # Theirs 버전 추출
  git show :3:$file > $file.backup
  
  # 새 파일에 이식
  if [ -f $new_file ]; then
    # 이미 존재하면 병합
    echo "// Migrated from $file" >> $new_file
    cat $file.backup >> $new_file
  else
    # 새로 생성
    cp $file.backup $new_file
  fi
  
  # 정리
  rm $file.backup
  git add $new_file
  git rm $file
  
done < to-migrate.txt

# ===== Step 7: 테스트 =====

# 빌드 확인
npm run build
# ✅ 성공

# 테스트 실행
npm test
# ✅ 통과

# ===== Step 8: Commit =====

git commit -m "Merge refactor: Resolved 30 file conflicts

Classification:
- Deleted: 10 files (deprecated)
- Kept: 5 files (still in use)
- Migrated: 15 files (moved to modules/)

Migration details:
- utils/helper*.js → modules/helpers/
- Applied feature improvements
- Maintained new structure

Tests: All passing
Build: Successful

Related: #refactor-123"

# ===== Step 9: 정리 =====

rm to-delete.txt to-keep.txt to-migrate.txt deleted-conflicts.txt

# ===== 결과 =====

git diff --stat HEAD~1
# 10 files deleted
# 5 files modified
# 15 files renamed and modified

# ✅ 30개 파일 체계적 해결!

# ===== 자동화 스크립트 =====

cat > resolve-deleted-conflicts.sh << 'EOF'
#!/bin/bash

echo "=== Deleted Conflicts Resolver ==="

# 충돌 목록
conflicts=$(git status | grep "deleted by us" | awk '{print $4}')

for file in $conflicts; do
  echo ""
  echo "File: $file"
  echo "1. Delete (keep deletion)"
  echo "2. Keep (restore file)"
  echo "3. Migrate (move to new location)"
  read -p "Choice [1/2/3]: " choice
  
  case $choice in
    1) git rm $file ;;
    2) git add $file ;;
    3) 
      read -p "New location: " new_loc
      mkdir -p $(dirname $new_loc)
      git show :3:$file > $new_loc
      git add $new_loc
      git rm $file
      ;;
  esac
done

echo "Done! Run 'git commit' to complete."
EOF

chmod +x resolve-deleted-conflicts.sh

# 사용:
# ./resolve-deleted-conflicts.sh
```

---

### 💼 Scenario 3: 서브모듈 버전 충돌

**목표:** 서브모듈 충돌 안전하게 해결

#### **Problem: 라이브러리 버전이 다르다!**

```bash
git merge feature
# CONFLICT (submodule): Merge conflict in lib/ui
# CONFLICT (submodule): Merge conflict in lib/auth
```

#### **Solution: 의존성 분석 후 선택**

```bash
# ===== Step 1: 상황 파악 =====

git status
# both modified:   lib/ui
# both modified:   lib/auth

# 서브모듈 상태
git submodule status
# +abc123 lib/ui (v2.5.0)
# +def456 lib/auth (v1.3.0)

# ===== Step 2: 버전 확인 =====

# Ours 버전
git show :2:.gitmodules | grep -A2 "lib/ui"
# [submodule "lib/ui"]
#   path = lib/ui
#   commit = abc123

# Theirs 버전
git show :3:.gitmodules | grep -A2 "lib/ui"
# [submodule "lib/ui"]
#   path = lib/ui
#   commit = ghi789

# ===== Step 3: 서브모듈 내부 확인 =====

# lib/ui 분석
cd lib/ui
git fetch
git log --oneline abc123..ghi789
# ghi789 feat: Add dark mode
# jkl012 fix: Button styling
# mno345 feat: New components

# Ours: v2.5.0
# Theirs: v2.7.0 (새 기능들!)

cd ../..

# lib/auth 분석
cd lib/auth
git fetch
git log --oneline def456..pqr678
# pqr678 security: Fix vulnerability
# stu901 feat: SSO support

# Ours: v1.3.0
# Theirs: v1.4.0 (보안 패치!)

cd ../..

# ===== Step 4: 의존성 확인 =====

# 주 프로젝트의 의존성
cat package.json | grep -A5 dependencies
# "lib-ui": "^2.5.0",  # Ours
# "lib-auth": "^1.3.0"  # Ours

# Theirs 버전 호환성 확인
# lib-ui v2.7.0: 호환 (MINOR 버전)
# lib-auth v1.4.0: 호환 (MINOR 버전)

# Breaking changes 확인
cd lib/ui
git log --oneline --grep="BREAKING" abc123..ghi789
# (출력 없음 = Breaking 없음)
cd ../..

# ===== Step 5: 테스트 환경 =====

# Theirs 버전으로 테스트
git checkout --theirs lib/ui
git checkout --theirs lib/auth

# 서브모듈 업데이트
git submodule update --init --recursive

# 테스트
npm test
# ✅ 통과!

# ===== Step 6: 최종 선택 =====

# 옵션 1: Theirs (새 기능 + 보안)
git add lib/ui lib/auth

# 옵션 2: Ours (안정성)
git checkout --ours lib/ui lib/auth
git add lib/ui lib/auth

# 옵션 3: 최신 버전 (권장!)
cd lib/ui
git checkout v2.8.0  # 최신!
cd ../..

cd lib/auth
git checkout v1.5.0  # 최신!
cd ../..

git add lib/ui lib/auth

# ===== Step 7: 문서화 =====

cat > SUBMODULE_UPDATE.md << 'EOF'
# Submodule Update

## lib/ui
- Before: v2.5.0
- After: v2.8.0
- Changes:
  - Added dark mode
  - New components
  - Button styling fixes

## lib/auth
- Before: v1.3.0
- After: v1.5.0
- Changes:
  - Security fix (CVE-2024-001)
  - SSO support
  - Performance improvements

## Testing
- All tests passing
- No breaking changes
- Backward compatible

## Rollback
If issues occur:
```bash
cd lib/ui && git checkout v2.5.0
cd lib/auth && git checkout v1.3.0
git add lib/ui lib/auth
git commit -m "Rollback submodules"
```
EOF

git add SUBMODULE_UPDATE.md

# ===== Step 8: Commit =====

git commit -m "Merge: Updated submodules to latest versions

lib/ui: v2.5.0 → v2.8.0
- Dark mode support
- New components
- Bug fixes

lib/auth: v1.3.0 → v1.5.0
- Critical security fix (CVE-2024-001)
- SSO support

Testing: All passed
Breaking changes: None
Backward compatible: Yes

See: SUBMODULE_UPDATE.md"

# ===== Step 9: 검증 =====

# 서브모듈 상태
git submodule status
# abc789 lib/ui (v2.8.0)
# def012 lib/auth (v1.5.0)

# 빌드 확인
npm run build
# ✅ 성공

# ✅ 안전한 업데이트 완료!

# ===== 자동화 스크립트 =====

cat > resolve-submodule-conflicts.sh << 'EOF'
#!/bin/bash

echo "=== Submodule Conflict Resolver ==="

# 충돌 확인
submodules=$(git status | grep "both modified" | grep -E "(lib|vendor)" | awk '{print $3}')

for sm in $submodules; do
  echo ""
  echo "Submodule: $sm"
  
  # 버전 정보
  ours=$(git ls-files -s $sm | grep "^160000" | awk '{print $2}' | head -1)
  theirs=$(git ls-files -s $sm | grep "^160000" | awk '{print $2}' | tail -1)
  
  echo "Ours: $ours"
  echo "Theirs: $theirs"
  
  # 선택
  echo "1. Keep Ours"
  echo "2. Use Theirs"
  echo "3. Update to latest"
  read -p "Choice [1/2/3]: " choice
  
  case $choice in
    1) git checkout --ours $sm ;;
    2) git checkout --theirs $sm ;;
    3)
      cd $sm
      git fetch
      latest=$(git describe --tags --abbrev=0)
      echo "Latest: $latest"
      git checkout $latest
      cd -
      ;;
  esac
  
  git add $sm
done

echo "Done! Run 'git commit' to complete."
EOF

chmod +x resolve-submodule-conflicts.sh
```

---

### 💼 Scenario 4: 모드 충돌 배치 처리

**목표:** 여러 스크립트의 권한 충돌 해결

#### **Problem: 10개 스크립트 실행 권한 충돌!**

```bash
git merge scripts-update
# CONFLICT (mode): script1.sh
# CONFLICT (mode): script2.sh
# ... (10개)
```

#### **Solution: 정책 기반 일괄 처리**

```bash
# ===== Step 1: 충돌 목록 =====

git status | grep "mode" > mode-conflicts.txt
cat mode-conflicts.txt
# both modified:   scripts/script1.sh
# both modified:   scripts/script2.sh
# ... (10개)

# ===== Step 2: 현재 권한 확인 =====

ls -l scripts/*.sh
# -rw-r--r-- script1.sh  # 644 (Ours)
# -rwxr-xr-x script2.sh  # 755 (Theirs)
# -rw-r--r-- script3.sh
# ...

# ===== Step 3: 정책 결정 =====

# 정책: 모든 .sh 파일은 실행 가능
# 이유: 스크립트는 실행되어야 함

# ===== Step 4: 배치 처리 =====

# 모든 .sh 파일에 실행 권한
chmod +x scripts/*.sh

# 확인
ls -l scripts/*.sh
# -rwxr-xr-x script1.sh  # 755
# -rwxr-xr-x script2.sh  # 755
# ...

# Git에 추가
git add scripts/*.sh

# ===== Step 5: 다른 케이스 =====

# Python 스크립트는?
ls -l scripts/*.py
# -rw-r--r-- deploy.py

# 정책: .py는 shebang 있으면 실행 가능
if head -1 scripts/deploy.py | grep -q "^#!/"; then
  chmod +x scripts/deploy.py
fi

git add scripts/*.py

# ===== Step 6: 문서화 =====

cat > PERMISSIONS.md << 'EOF'
# File Permissions Policy

## Scripts (.sh)
All shell scripts must be executable (755)

```bash
chmod +x script.sh
```

## Python scripts (.py)
Executable only if shebang present

```python
#!/usr/bin/env python3
```

## Data files (.json, .txt, .md)
Not executable (644)

## Configuration files
Not executable (644)
Sensitive files (600)

## Automation
See scripts/fix-permissions.sh
EOF

git add PERMISSIONS.md

# ===== Step 7: 자동화 스크립트 =====

cat > scripts/fix-permissions.sh << 'EOF'
#!/bin/bash

echo "Fixing file permissions..."

# Shell scripts
find . -name "*.sh" -exec chmod +x {} \;

# Python with shebang
find . -name "*.py" -exec sh -c '
  if head -1 "$1" | grep -q "^#!/"; then
    chmod +x "$1"
  fi
' sh {} \;

# Remove execute from data files
find . -name "*.json" -exec chmod -x {} \;
find . -name "*.txt" -exec chmod -x {} \;
find . -name "*.md" -exec chmod -x {} \;

echo "Done!"
EOF

chmod +x scripts/fix-permissions.sh
git add scripts/fix-permissions.sh

# ===== Step 8: Commit =====

git commit -m "Merge: Standardized file permissions

Applied permission policy:
- .sh files: executable (755)
- .py files: executable if shebang
- Data files: not executable (644)

Affected files: 15
Policy document: PERMISSIONS.md
Automation: scripts/fix-permissions.sh

Resolves: #permissions-001"

# ===== Step 9: Pre-commit Hook =====

cat > .git/hooks/pre-commit << 'EOF'
#!/bin/bash

echo "Checking file permissions..."

# Shell scripts must be executable
non_exec=$(find . -name "*.sh" ! -perm -u=x)
if [ -n "$non_exec" ]; then
  echo "❌ These .sh files are not executable:"
  echo "$non_exec"
  exit 1
fi

# Data files must not be executable
exec_data=$(find . -name "*.json" -perm -u=x)
if [ -n "$exec_data" ]; then
  echo "❌ These data files should not be executable:"
  echo "$exec_data"
  exit 1
fi

echo "✅ Permissions OK"
EOF

chmod +x .git/hooks/pre-commit

# ✅ 정책 기반 해결 완료!
```

---

### 💼 Scenario 5: 복합 충돌 종합 해결

**목표:** 여러 타입의 충돌 동시 처리

#### **Problem: 바이너리 + 삭제 + 서브모듈 충돌!**

```bash
git merge complex-feature
# CONFLICT (content): logo.png (binary)
# CONFLICT (modify/delete): old-api.js
# CONFLICT (submodule): lib/utils
# CONFLICT (rename/rename): config.js
```

#### **Solution: 우선순위 기반 해결**

```bash
# ===== Step 1: 충돌 분류 =====

git status
# both modified:   logo.png (binary)
# deleted by us:   old-api.js (modify/delete)
# both modified:   lib/utils (submodule)
# both modified:   config.prod.js (rename/rename)
# both modified:   config.staging.js

echo "=== Conflict Classification ==="
echo "1. Binary: logo.png"
echo "2. Delete: old-api.js"
echo "3. Submodule: lib/utils"
echo "4. Rename: config.js → config.prod.js / config.staging.js"

# ===== Step 2: 우선순위 =====

# 우선순위:
# 1. 서브모듈 (의존성 중요)
# 2. 삭제 충돌 (구조 영향)
# 3. 이름 변경 (네이밍 결정)
# 4. 바이너리 (시각적 확인)

# ===== Step 3: 서브모듈 해결 =====

echo "=== Resolving Submodule ==="

cd lib/utils
git fetch
git log --oneline HEAD..FETCH_HEAD
# Latest: v3.2.1 (bug fixes)

git checkout v3.2.1
cd ../..
git add lib/utils

echo "✅ Submodule resolved (v3.2.1)"

# ===== Step 4: 삭제 충돌 해결 =====

echo "=== Resolving Delete Conflict ==="

# 조사
git log --oneline -- old-api.js
# Main: Removed (migrated to new-api.js)
# Feature: Added error handling

# 이식
git show :3:old-api.js | grep -A20 "error handling" > error-handling.patch

# 새 파일에 적용
patch new-api.js error-handling.patch

# 삭제 완료
git rm old-api.js
git add new-api.js

echo "✅ Delete conflict resolved (migrated to new-api.js)"

# ===== Step 5: 이름 변경 해결 =====

echo "=== Resolving Rename Conflict ==="

# 비교
diff config.prod.js config.staging.js
# 내용 유사, 환경 설정만 다름

# 결정: 둘 다 유지
git add config.prod.js config.staging.js

# Base 제거
# (이미 rename되어 없음)

echo "✅ Rename resolved (both environments kept)"

# ===== Step 6: 바이너리 해결 =====

echo "=== Resolving Binary Conflict ==="

# 버전 추출
git show :2:logo.png > logo.ours.png
git show :3:logo.png > logo.theirs.png

# 시각적 비교
open logo.ours.png logo.theirs.png

# 결정: Theirs (더 나은 디자인)
git checkout --theirs logo.png
git add logo.png

# 정리
rm logo.ours.png logo.theirs.png

echo "✅ Binary resolved (theirs selected)"

# ===== Step 7: 검증 =====

echo "=== Verification ==="

# 빌드 테스트
npm run build
if [ $? -eq 0 ]; then
  echo "✅ Build successful"
else
  echo "❌ Build failed"
  exit 1
fi

# 테스트 실행
npm test
if [ $? -eq 0 ]; then
  echo "✅ Tests passed"
else
  echo "❌ Tests failed"
  exit 1
fi

# ===== Step 8: Commit =====

git commit -m "Merge complex-feature: Resolved 4 types of conflicts

1. Submodule (lib/utils)
   - Updated to v3.2.1 (latest stable)
   - Includes bug fixes

2. Modify/Delete (old-api.js)
   - Removed old-api.js (as planned)
   - Migrated error handling to new-api.js

3. Rename (config.js)
   - Split into config.prod.js and config.staging.js
   - Both environments now supported

4. Binary (logo.png)
   - Used design-team version (better design)

Testing:
- Build: ✅ Successful
- Tests: ✅ All passed

Related: #complex-merge-001"

# ===== Step 9: 문서화 =====

cat > MERGE_NOTES.md << 'EOF'
# Merge Notes: complex-feature

## Conflicts Resolved

### 1. Submodule Conflict
**File:** lib/utils
**Resolution:** Updated to v3.2.1
**Reason:** Latest stable version with bug fixes

### 2. Modify/Delete Conflict
**File:** old-api.js
**Resolution:** Deleted, migrated to new-api.js
**Reason:** API migration completed

### 3. Rename Conflict
**File:** config.js
**Resolution:** Split into prod and staging
**Reason:** Multi-environment support

### 4. Binary Conflict
**File:** logo.png
**Resolution:** Used design-team version
**Reason:** Better visual design

## Verification
- Build: Successful
- Tests: All passed
- No regressions

## Rollback Plan
If issues occur:
```bash
git revert HEAD
```
EOF

git add MERGE_NOTES.md
git commit --amend --no-edit

# ===== Step 10: 통지 =====

echo "=== Merge Complete ==="
echo ""
echo "Resolved conflicts:"
echo "  ✅ Submodule (lib/utils)"
echo "  ✅ Modify/Delete (old-api.js)"
echo "  ✅ Rename (config.js)"
echo "  ✅ Binary (logo.png)"
echo ""
echo "See MERGE_NOTES.md for details"

# ✅ 복합 충돌 완벽 해결!
```

---

### 🚨 함정 & 실수 방지

#### ❌ **실수 1: 바이너리 파일 수동 편집**

```bash
# 위험한 패턴
vim logo.png
# 바이너리 데이터 깨짐!

git add logo.png
git commit
# 파일 손상!
```

**올바른 방법:**
```bash
# 버전 선택 또는 새 파일
git checkout --ours logo.png
# 또는
cp new-logo.png logo.png
git add logo.png
```

---

#### ❌ **실수 2: 서브모듈 직접 수정**

```bash
# 위험한 패턴
cd lib/utils
# ... 직접 수정 ...
cd ../..
git add lib/utils
# 서브모듈 상태 불일치!
```

**올바른 방법:**
```bash
# 버전 선택 후 추가
cd lib/utils
git checkout v3.2.0
cd ../..
git add lib/utils

# 또는 upstream 업데이트
cd lib/utils
git pull origin main
cd ../..
git add lib/utils
```

---

#### ❌ **실수 3: 이식 없이 삭제**

```bash
# 위험한 패턴
git rm old.js
# 수정사항 손실!
```

**올바른 방법:**
```bash
# 수정사항 확인
git show :3:old.js

# 이식 필요 여부 판단
if [important changes]; then
  # 이식 후 삭제
  git show :3:old.js > temp
  # new.js에 반영
  git rm old.js
else
  # 바로 삭제
  git rm old.js
fi
```

---

#### ❌ **실수 4: 테스트 생략**

```bash
# 위험한 패턴
# 복잡한 충돌 해결
git add .
git commit
# 테스트 안 함!

# 배포 후 발견...
```

**올바른 방법:**
```bash
# 해결 후 반드시 테스트
git add .

# 테스트
npm test
npm run lint
npm run build

# 통과 후 commit
git commit
```

---

### 🎯 실습 미션

#### **미션 1: 바이너리 충돌 실습** 🖼️

**목표:** 이미지 충돌 경험

```bash
# 준비
mkdir binary-conflict && cd binary-conflict
git init

# 미션:
# 1. 이미지 파일로 충돌 만들기
# 2. 버전 추출
# 3. 선택 및 해결
```

<details>
<summary>정답 보기</summary>

```bash
# 1. 초기 이미지
echo "Base Image" > image.txt
git add image.txt
git commit -m "Add image"

# 2. Feature 브랜치
git switch -c feature
echo "Feature Image (updated)" > image.txt
git add image.txt
git commit -m "Update image"

# 3. Main 브랜치
git switch main
echo "Main Image (different update)" > image.txt
git add image.txt
git commit -m "Update image differently"

# 4. Merge (충돌!)
git merge feature
# CONFLICT!

# 5. 버전 추출
git show :1:image.txt > image.base.txt
git show :2:image.txt > image.ours.txt
git show :3:image.txt > image.theirs.txt

# 6. 비교
cat image.*.txt

# 7. 선택 (Theirs)
git checkout --theirs image.txt
git add image.txt

# 8. Commit
git commit -m "Resolved: Used feature version"

# ✅ 완료!
```

</details>

---

#### **미션 2: Modify/Delete 실습** 🗑️

**목표:** 삭제 충돌 해결

```bash
# 시나리오:
# Main: 파일 삭제
# Feature: 파일 수정
# 해결: 이식 후 삭제
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "function old() { }" > old.js
echo "function new() { }" > new.js
git add .
git commit -m "Initial"

# Feature
git switch -c feature
echo "function old() { return 'improved'; }" > old.js
git add old.js
git commit -m "Improve old function"

# Main
git switch main
git rm old.js
git commit -m "Remove old.js"

# Merge (충돌!)
git merge feature
# CONFLICT (modify/delete)!

# 이식
git show :3:old.js > temp.js
cat temp.js >> new.js

# 삭제
git rm old.js
git add new.js

# Commit
git commit -m "Merged improvements into new.js"

cat new.js
# function new() { }
# function old() { return 'improved'; }

# ✅ 완료!
```

</details>

---

#### **미션 3: 복합 충돌 실습** 🔥

**목표:** 여러 타입 동시 해결

```bash
# 시나리오:
# - Binary (image.txt)
# - Delete (old.js)
# - Rename (config.js)
```

<details>
<summary>정답 보기</summary>

```bash
# 준비
git init
echo "Image" > image.txt
echo "Old" > old.js
echo "Config" > config.js
git add .
git commit -m "Initial"

# Feature
git switch -c feature
echo "Image-Feature" > image.txt
echo "Old-Improved" > old.js
git mv config.js config.prod.js
git add .
git commit -m "Feature updates"

# Main
git switch main
echo "Image-Main" > image.txt
git rm old.js
git mv config.js config.staging.js
git add .
git commit -m "Main updates"

# Merge (3가지 충돌!)
git merge feature
# CONFLICT!

# 1. Binary
git checkout --theirs image.txt
git add image.txt

# 2. Delete
git rm old.js

# 3. Rename
git add config.prod.js config.staging.js

# Commit
git commit -m "Resolved 3 conflicts"

# 확인
ls
# config.prod.js
# config.staging.js
# image.txt (feature 버전)

# ✅ 완료!
```

</details>

---

## BONUS: 빠른 참조 📌

### 📋 Cheat Sheet

```bash
# === 바이너리 충돌 ===
git show :2:file > file.ours        # Ours 추출
git show :3:file > file.theirs      # Theirs 추출
git checkout --ours file            # Ours 선택
git checkout --theirs file          # Theirs 선택

# === 삭제 충돌 ===
git rm file                         # 삭제 유지
git add file                        # 수정 유지

# === 서브모듈 ===
git checkout --ours submodule       # Ours 버전
cd submodule && git checkout v1.0   # 특정 버전

# === 모드 충돌 ===
chmod +x file                       # 실행 권한
chmod -x file                       # 권한 제거
```

---

### 🎨 충돌 타입 결정 트리

```
충돌 발생
  ↓
파일 타입?
├─ 텍스트 → 일반 충돌 해결
├─ 바이너리 → 버전 선택
├─ 삭제됨 → 삭제 or 유지
├─ 이름변경 → 이름 결정
├─ 서브모듈 → 버전 선택
└─ 모드 → 권한 결정
```

---

### 🔗 관련 문서 링크

**이전 학습:**
- [3.2 충돌의 원리](./02-conflict-basics.md)

**다음 학습:**
- [3.4 Merge 전략](./04-merge-strategies.md)

**관련 주제:**
- [3.1 Merge 타입](./01-merge-types.md)
- [3.5 Merge Tools](./05-merge-tools.md)

---

<div align="center">

**🎉 축하합니다! 복잡한 충돌을 완전히 마스터했습니다!**

이제 어떤 충돌도 두렵지 않습니다!

[⬅️ 이전: 충돌의 원리](./02-conflict-basics.md) | [README로](../README.md) | [다음: Merge 전략 ➡️](./04-merge-strategies.md)

</div>
